# SIM7080 Zephyr Offload 数据收发流程分析

> 基于 `zephyr/drivers/modem/simcom/sim7080/` 源码分析，涵盖 TX/RX 全链路及 AT 命令匹配机制。

---

## 1. 整体架构

```
应用层 (WebSocket / TCP 标准 POSIX API)
    ↕  zsock_send / zsock_recv / write / read
Zephyr Socket 层（fdtable + socket_op_vtable 分发）
    ↕  offload_socket_fd_op_vtable
SIM7080 Offload 驱动层（sim7080_sock.c）
    ↕  AT 命令（串口）
SIM7080 硬件模组（内置 TCP/IP 协议栈）
    ↕  LTE 网络
```

---

## 2. socket_op_vtable 接口说明

```c
const struct socket_op_vtable offload_socket_fd_op_vtable = {
    .fd_vtable = {
        .read  = offload_read,   // POSIX read()/recv() 走 VFS 路径时触发
        .write = offload_write,  // POSIX write()/send() 走 VFS 路径时触发
        .close = offload_close,
        .ioctl = offload_ioctl,
    },
    .connect  = offload_connect,   // AT+CAOPEN
    .sendto   = offload_sendto,    // 真正实现：AT+CASEND 两阶段握手
    .recvfrom = offload_recvfrom,  // 真正实现：等通知 → AT+CARECV → 拷贝
    .sendmsg  = offload_sendmsg,   // 遍历 iov 调 sendto
};
```

| 接口 | 触发路径 | 实质 |
|------|---------|------|
| `.write` | `write()` / `send()` 走 VFS | 封装 → `offload_sendto(flags=0, addr=NULL)` |
| `.sendto` | `sendto()` / `sendmsg()` 走 socket 层 | **真正实现**：AT+CASEND 两阶段握手 |
| `.read` | `read()` / `recv()` 走 VFS | 封装 → `offload_recvfrom(flags=0, addr=NULL)` |
| `.recvfrom` | `recvfrom()` 走 socket 层 | **真正实现**：等通知 → AT+CARECV |

`read`/`write` 是 POSIX 文件 I/O 入口，`sendto`/`recvfrom` 是 socket 语义入口，两者最终汇聚到同一套 AT 命令实现。

---

## 3. TX 路径：发送一包数据

### 3.1 应用层（以 WebSocket 为例）

```
websocket_send_msg(ws_sock, payload, len, BINARY, mask=true)
  → 构建 WS 帧头（FIN + opcode + payload_len + mask_key）
  → 对 payload 做 XOR mask
  → websocket_prepare_and_send()
      → 组装 net_msghdr（header iov + payload iov）
      → sendmsg_all(ctx->real_sock, msg, ...)
          → zsock_sendmsg(real_sock, ...)
```

`real_sock` 是底层的 offload TCP socket。

### 3.2 Zephyr Socket 层

```
zsock_sendmsg()
  → fdtable 查找 real_sock 绑定的 vtable
  → offload_socket_fd_op_vtable.sendmsg = offload_sendmsg
```

### 3.3 offload_sendmsg

遍历 `msg_iov`，对每个 iov 循环调用 `offload_sendto()`，直到本 iov 数据全部发完（因每次最多发 `tx_space_avail` 字节）。

### 3.4 offload_sendto —— 核心 AT 交互（两阶段）

**阶段一：查询发送缓冲区可用空间**

```
→ 发送 "AT+CASEND=<id>\r\n"
← 解析 "+CASEND: <space>"  →  on_cmd_casend() 存入 mdata.tx_space_avail
```

**阶段二：发送数据（持有 sem_tx_lock 互斥锁）**

```
→ 发送 "AT+CASEND=<id>,<len>\r\n"
← 等待 '> '（sem_tx_ready）
      modem_rx 线程解析到 '>' → on_cmd_tx_ready() → k_sem_give()
→ modem_cmd_send_data_nolock() 写入原始二进制数据
← 等待 "OK"（sem_response）
      modem_rx 线程解析到 "OK" → on_cmd_ok() → k_sem_give()
→ 释放 sem_tx_lock
→ 返回实际写入字节数
```

### 3.5 底层 UART 写

`iface->write` 实现（`modem_iface_uart_interrupt.c`）：

```c
// 逐字节轮询写 UART，无 DMA，无 TX 中断
do {
    uart_poll_out(iface->dev, *buf++);
} while (--size);
```

---

## 4. RX 路径：从 UART 中断到应用层的完整数据路径

### 4.1 层次总览

```
层次                  关键数据结构                           阻塞/唤醒机制
──────────────────────────────────────────────────────────────────────────
UART 硬件 FIFO         无（硬件）                            触发 UART RX 中断
  ↓
[ISR] UART RX ISR      iface_uart_data.rx_rb（ring_buf，1024 B）  k_sem_give(rx_sem)
  ↓
modem_rx 线程          net_buf pool（最多 30 片 × 1024 B）   k_sem_take(rx_sem)
  ↓
cmd_handler 解析       match_buf（AT 行缓冲）
  ↓
URC +CADATAIND         modem_socket.packet_sizes[]（dummy=1）  k_sem_give(sem_data_ready)
  ↓                                                            k_poll_signal_raise(sig_data_ready)
offload_recvfrom()     sock_data->recv_buf（= 应用传入 buf）   k_sem_take(sem_data_ready)
  ↓
net_buf_linearize()    应用层缓冲区（唯一一次内存拷贝）
  ↓
应用层 recv()/read()
```

### 4.2 第一阶段：UART 中断 → ring_buf（ISR 上下文）

```c
// modem_iface_uart_isr()  ——  在 UART 中断上下文中运行
while (uart_irq_update(dev) && uart_irq_rx_ready(dev)) {
    // 向 ring_buf 申请连续可写空间（zero-copy claim/finish）
    partial_size = ring_buf_put_claim(&data->rx_rb, &dst, UINT32_MAX);
    if (!partial_size) {
        // ring_buf 满：开启硬件流控则关闭 RX 中断等待排水
        //              否则丢弃数据并打印错误
        break;
    }
    // 从 UART FIFO 批量读取到 ring_buf 申请的内存
    rx = uart_fifo_read(dev, dst, partial_size);
    dst += rx;  total_size += rx;  partial_size -= rx;
}
ring_buf_put_finish(&data->rx_rb, total_size);  // 提交实际写入字节数
if (total_size > 0) {
    k_sem_give(&data->rx_sem);  // 唤醒 modem_rx 线程
}
```

`rx_rb` 的后备存储是 `iface_rb_buf[MDM_MAX_DATA_LENGTH=1024]`，定义在 `sim7080_data` 中。
ISR 做最少工作：写 ring_buf + give 一个信号量，不做任何 AT 解析。

### 4.3 第二阶段：modem_rx 线程 → net_buf 链（线程上下文）

```c
// sim7080.c
static void modem_rx(void *p1, void *p2, void *p3)
{
    while (true) {
        // 等待 ISR 通知有新数据（k_sem_take rx_sem）
        modem_iface_uart_rx_wait(&mctx.iface, K_FOREVER);
        modem_cmd_handler_process(&mctx.cmd_handler, &mctx.iface);
    }
}
```

`modem_cmd_handler_process()` 循环调用两个子函数，**直到 ring_buf 为空**：

**子函数 A：`cmd_handler_process_iface_data()`** —— ring_buf → net_buf 链

```c
// 若 rx_buf 为空，先分配第一个 fragment
if (!data->rx_buf)
    data->rx_buf = net_buf_alloc(buf_pool, alloc_timeout);

while (true) {
    frag_room = net_buf_tailroom(last_frag);
    if (!frag_room) {
        // 当前 fragment 写满，分配新 fragment 追加到链表尾部
        frag = net_buf_alloc(buf_pool, alloc_timeout);
        net_buf_frag_insert(last, frag);
        last = frag;
    }
    // iface->read() 内部调用 ring_buf_get()，将字节从 ring_buf 移到 net_buf
    ret = iface->read(iface, net_buf_tail(frag), frag_room, &bytes_read);
    if (ret < 0 || bytes_read == 0) break;  // ring_buf 空，退出循环
    net_buf_add(frag, bytes_read);           // 更新 fragment 有效长度
}
```

数据从 ring_buf **再度拷贝**进入 net_buf 链（`data->rx_buf`）。
pool 上限：`MDM_RECV_MAX_BUF=30` 个 fragment × `MDM_RECV_BUF_SIZE=1024` B = 最多 30 KB 缓冲。

**子函数 B：`cmd_handler_process_rx_buf()`** —— 逐行解析 AT 响应

```c
while (data->rx_buf && data->rx_buf->len) {
    skipcrlf(data);   // 跳过行首 \r\n

    // 1. 优先尝试 DIRECT 命令匹配（无 CRLF，如 '>'）
    cmd = find_cmd_direct_match(data);
    if (cmd) { cmd->func(...); continue; }

    // 2. 找到行尾 CRLF 的位置
    len = findcrlf(data, &frag, &offset);
    if (!frag) break;  // 行不完整，等下次数据到来

    // 3. 将本行内容线性化到 match_buf（最大 match_buf_len-1 字节）
    match_len = net_buf_linearize(data->match_buf, match_buf_len-1,
                                  data->rx_buf, 0, len);

    // 4. 三槽匹配（strncmp）：CMD_RESP(0) → CMD_UNSOL(1) → CMD_HANDLER(2)
    cmd = find_cmd_match(data);
    if (cmd) process_cmd(cmd, match_len, data);
    //  process_cmd 内：skip 命令前缀 → 按分隔符解析 argv → 调用 cmd->func()

    // 5. 跳过已处理的行（回收 net_buf fragment）
    while (frag && data->rx_buf != frag)
        data->rx_buf = net_buf_frag_del(NULL, data->rx_buf);
    net_buf_pull(data->rx_buf, offset);
}
```

### 4.4 第三阶段：+CADATAIND URC → 通知应用层

模组 TCP 数据到达时，先异步发出 URC（**不含数据本身，只是通知**）：
`+CADATAIND: 0\r\n`

```
match_buf = "+CADATAIND: 0"
  → find_cmd_match(): 命中槽1（CMD_UNSOL）中的 "+CADATAIND: "
  → on_urc_cadataind(argv[0]="0")
      → sim7080_handle_sock_data_indication(sock_fd=0)
          → sock = modem_socket_from_fd(cfg, 0)
          → modem_socket_packet_size_update(cfg, sock, +1)
              // 内部：sock->packet_sizes[packet_count++] = 1  (dummy，仅标记有数据)
              // 若 packet_sizes[0] > 0：
              //   k_poll_signal_raise(&sock->sig_data_ready, 0)
              //   → 唤醒正在 zsock_poll() 阻塞的线程（POLLIN 事件）
          → modem_socket_data_ready(cfg, sock)
              // 内部：if (sock->is_waiting)
              //           sock->is_waiting = false
              //           k_sem_give(&sock->sem_data_ready)
              //   → 唤醒阻塞在 modem_socket_wait_data() 的应用线程
```

此时 **TCP 数据仍在模组内部缓冲区**，还未发往主机。

### 4.5 第四阶段：应用层 offload_recvfrom() → AT+CARECV 拉取数据

```c
static ssize_t offload_recvfrom(void *obj, void *buf, size_t max_len, ...)
{
    // 1. 检查是否有待读数据（packet_size 是 dummy 值，>0 即"有数据"）
    packet_size = modem_socket_next_packet_size(cfg, sock);
    if (!packet_size) {
        if (flags & DONTWAIT) { errno = EAGAIN; return -1; }
        // 阻塞直到 +CADATAIND → k_sem_give(sem_data_ready) 唤醒
        modem_socket_wait_data(cfg, sock);
        //  内部：sock->is_waiting = true
        //        k_sem_take(&sock->sem_data_ready, K_FOREVER)
    }

    // 2. 截断到接收上限（MDM_MAX_DATA_LENGTH = 1024 字节）
    if (max_len > MDM_MAX_DATA_LENGTH) max_len = MDM_MAX_DATA_LENGTH;

    // 3. 将应用 buf 挂到 sock->data（供 modem_rx 线程写入）
    sock_data.recv_buf     = buf;       // ← 直接指向应用层缓冲区！
    sock_data.recv_buf_len = max_len;
    sock->data = &sock_data;
    mdata.current_sock_fd = sock->sock_fd;

    // 4. 发 AT+CARECV=0,1024，注入临时 CMD_HANDLER
    snprintk(sendbuf, sizeof(sendbuf), "AT+CARECV=%d,%zd", sock->id, max_len);
    struct modem_cmd data_cmd[] = { MODEM_CMD("+CARECV: ", on_cmd_carecv, 1U, ",") };
    modem_cmd_send(&mctx.iface, &mctx.cmd_handler,
                   data_cmd, 1,           // → 写入 cmds[CMD_HANDLER=2]
                   sendbuf, &mdata.sem_response, MDM_CMD_TIMEOUT);
    // modem_cmd_send 内部 k_sem_take(sem_response)，阻塞等待 OK

    // 5. modem_cmd_send 返回后，recv_read_len 已由 sockread_common 填写
    ret = sock_data.recv_read_len;

    // 6. 清理 dummy packet_size 记账
    // （sockread_common 内部已调用 modem_socket_packet_size_update(sock, -packet_size)）

    sock->data = NULL;
    return ret;
}
```

### 4.6 第五阶段：+CARECV 响应解析 → net_buf_linearize → 写入应用 buf

模组响应：`+CARECV: 66,<66字节原始二进制>\r\nOK\r\n`

```
UART ISR 把 +CARECV 响应写入 ring_buf
  → rx_sem → modem_rx 线程唤醒
      → cmd_handler_process_iface_data(): ring_buf → net_buf 链
      → cmd_handler_process_rx_buf():
            findcrlf 找到 "+CARECV: 66" 行尾
            match_buf = "+CARECV: 66"
            → find_cmd_match(): 命中槽2（CMD_HANDLER）中的 "+CARECV: "
            → process_cmd():
                  跳过命令前缀 "+CARECV: "（10字节）
                  argv[0] = "66"（按 "," 分割的第一个参数）
                  data->rx_buf 现在指向逗号后的 66 字节原始 TCP 数据
                  调用 on_cmd_carecv(data, match_len, argv, argc=1)

on_cmd_carecv:
  → sockread_common(current_sock_fd, data, atoi("66")=66, len)
      sock_data = sock->data   // 取回指向应用 buf 的指针
      ret = net_buf_linearize(
                sock_data->recv_buf,    // ← 目标：应用层 buf
                sock_data->recv_buf_len,
                data->rx_buf,           // ← 源：net_buf 当前位置（66 字节 TCP 数据）
                0,                      // src_offset
                66);                    // bytes to copy
      // TCP 原始二进制直接从 net_buf 复制到应用 buf，无中间缓冲区
      data->rx_buf = net_buf_skip(data->rx_buf, ret);  // 消费掉 66 字节
      sock_data->recv_read_len = 66;

      // 更新 packet_size 记账（减掉 dummy sentinel）
      packet_size = modem_socket_next_packet_size(cfg, sock);   // = 1 (dummy)
      modem_socket_packet_size_update(cfg, sock, -packet_size); // packet_count = 0

modem_rx 线程继续处理 "OK\r\n"
  → on_cmd_ok() → k_sem_give(&mdata.sem_response)
      → modem_cmd_send() 中的 k_sem_take 返回
      → CMD_HANDLER 槽被清空（cmds[2] = NULL）
```

**全路径内存拷贝次数**：2 次：
1. ISR：`UART FIFO → ring_buf`
2. `modem_rx` 线程：`ring_buf → net_buf`
3. `sockread_common`：`net_buf → 应用 buf`（`net_buf_linearize`）

> 无法避免第 2、3 次拷贝：net_buf 是 cmd_handler 的必要中间层（用于行匹配解析）。

### 4.7 第六阶段：数据返回应用层（WebSocket 路径）

```
offload_recvfrom() 返回 66（字节数）
  → zsock_recvfrom() 返回 66
  → websocket_recv_msg() 解析 WS 帧头（FIN/opcode/payload_len/mask）
      → XOR unmask payload（in-place，无拷贝）
      → 返回 payload 指针给应用回调
  → 应用处理业务数据（音频帧、JSON 消息等）
```

### 4.8 关键尺寸参数汇总

| 参数 | 值 | 作用范围 |
|------|----|---------|
| `MDM_MAX_DATA_LENGTH` | **1024** | AT+CARECV 单次最多请求字节数（**仅接收**） |
| `MDM_RECV_BUF_SIZE` | 1024 | 每个 net_buf fragment 容量 |
| `MDM_RECV_MAX_BUF` | 30 | net_buf pool 最大 fragment 数（30 KB 总缓冲） |
| `iface_rb_buf` | 1024 | UART RX ring_buf 大小（ISR 直接写） |
| TX 上限 | **动态** | `AT+CASEND=<id>` 返回 `tx_space_avail`，**驱动无硬编码上限** |

---

## 5. AT 命令响应匹配机制

### 5.1 三槽设计

`modem_cmd_handler_data` 内有三个命令槽（`data->cmds[0..2]`），每次接收到一行数据时按 `j=0,1,2` 顺序遍历匹配（先匹配先赢）：

| 槽 | 宏常量 | 注入时机 | 内容 |
|----|--------|---------|------|
| 0 | `CMD_RESP` | `modem_cmd_handler_init` 初始化时 | `response_cmds`：OK / ERROR / +CME ERROR / `>` |
| 1 | `CMD_UNSOL` | `modem_cmd_handler_init` 初始化时 | `unsolicited_cmds`：+CADATAIND / +CASTATE / RDY 等 URC |
| 2 | `CMD_HANDLER` | 每次 `modem_cmd_send` 调用时临时注入 | 当前命令期望的特定响应，如 +CARECV / +CASEND / +CAOPEN |

### 5.2 `modem_cmd_handler_init` 固定注入（槽 0、1）

```c
// modem_init() 中
const struct modem_cmd_handler_config cmd_handler_config = {
    .response_cmds     = response_cmds,       // → cmds[CMD_RESP=0]，永久有效
    .response_cmds_len = ARRAY_SIZE(response_cmds),
    .unsol_cmds        = unsolicited_cmds,    // → cmds[CMD_UNSOL=1]，永久有效
    .unsol_cmds_len    = ARRAY_SIZE(unsolicited_cmds),
};
```

### 5.3 `modem_cmd_send` 临时注入（槽 2）

```c
// offload_recvfrom 中
struct modem_cmd data_cmd[] = { MODEM_CMD("+CARECV: ", on_cmd_carecv, 1U, ",") };
modem_cmd_send(&mctx.iface, &mctx.cmd_handler,
               data_cmd, ARRAY_SIZE(data_cmd),   // ← 写入 cmds[CMD_HANDLER=2]
               sendbuf, &mdata.sem_response, MDM_CMD_TIMEOUT);
// 函数返回前会自动调用 modem_cmd_handler_update_cmds(NULL, 0) 清空槽 2
```

`modem_cmd_send_ext` 内部完整逻辑：
1. 取 `sem_tx_lock`
2. `modem_cmd_handler_update_cmds(data, handler_cmds, ...)` → 写 `cmds[2]`
3. `iface->write()` 发 AT 字符串 + EOL（`\r\n`）
4. `k_sem_take(sem, timeout)` 阻塞等待
5. `modem_cmd_handler_update_cmds(NULL, 0)` → **清空 `cmds[2]`**
6. 释放 `sem_tx_lock`

### 5.4 直接匹配（MODEM_CMD_DIRECT）

`>` 提示符用 `MODEM_CMD_DIRECT` 定义，原因是它**没有 `\r\n` 结尾**，无法走普通的 `findcrlf()` 路径。解析器会在 `cmd_handler_process_rx_buf()` 中优先对 ring buffer 头部做前缀匹配（`starts_with()`），匹配成功后直接跳过对应字节，不等行尾。

```c
// sim7080.c response_cmds 中
MODEM_CMD_DIRECT(">", on_cmd_tx_ready),

// on_cmd_tx_ready
MODEM_CMD_DIRECT_DEFINE(on_cmd_tx_ready)
{
    k_sem_give(&mdata.sem_tx_ready);  // 通知 offload_sendto 可以发数据了
    return len;
}
```

---

## 6. 关键信号量一览

| 信号量 | 用途 |
|--------|------|
| `sem_tx_lock` | 互斥锁，保证同一时刻只有一个 AT 命令在飞 |
| `sem_response` | 等待命令响应（OK/ERROR/特定响应） |
| `sem_tx_ready` | 等待 `>` 提示符（模组准备好接收数据） |
| `sem_data_ready`（per socket） | 等待 +CADATAIND 通知有新数据 |
| `sem_dns` | 等待 DNS 查询结果 |

---

## 7. 完整时序图

### TX（发送）

```
应用线程                          modem_rx 线程              模组 UART
    |                                   |                        |
    |-- websocket_send_msg() ---------->|                        |
    |-- offload_sendto()                |                        |
    |   |-- AT+CASEND=id\r\n ---------->|----UART-TX------------>|
    |   |<-- wait sem_response          |   (parse "+CASEND: N") |
    |   |                               |<---"+CASEND: N\r\n"---|
    |   |                               |-- on_cmd_casend()      |
    |   |                               |-- k_sem_give(response) |
    |   |<-- sem_response given --------|                        |
    |   |                               |                        |
    |   |-- AT+CASEND=id,len\r\n ------>|----UART-TX------------>|
    |   |<-- wait sem_tx_ready          |<---"> "----------------|
    |   |                               |-- on_cmd_tx_ready()    |
    |   |                               |-- k_sem_give(tx_ready) |
    |   |<-- sem_tx_ready given --------|                        |
    |   |-- raw_data -------------------|-UART-TX--------------->|
    |   |<-- wait sem_response          |<---"OK\r\n"------------|
    |   |                               |-- on_cmd_ok()          |
    |   |                               |-- k_sem_give(response) |
    |   |<-- sem_response given --------|                        |
    |<-- return bytes_written           |                        |
```

### RX（接收）

```
应用线程（recv）            modem_rx 线程               UART ISR             模组
    |                           |                          |                   |
    |                           |                          |<---+CADATAIND:0---|
    |                           |                          |  uart_fifo_read() |
    |                           |                          |  ring_buf_put()   |
    |                           |                          |  k_sem_give(rx)   |
    |                           |<-- rx_sem wakeup --------|                   |
    |                           |-- ring_buf→net_buf       |                   |
    |                           |-- match "+CADATAIND: "   |                   |
    |                           |-- packet_size_update(+1) |                   |
    |                           |-- k_poll_signal_raise()  |                   |
    |                           |-- k_sem_give(data_rdy)   |                   |
    |<-- (if blocked) wakeup ---|                          |                   |
    |                           |                          |                   |
    |-- offload_recvfrom() ---->|                          |                   |
    |   wait sem_data_ready     |                          |                   |
    |   (already given)         |                          |                   |
    |   AT+CARECV=0,1024 ------>|---UART poll_out--------->|------------------>|
    |   wait sem_response       |                          |<---+CARECV:66,<data>|
    |                           |                          |  ring_buf_put()   |
    |                           |                          |  k_sem_give(rx)   |
    |                           |<-- rx_sem wakeup --------|                   |
    |                           |-- ring_buf→net_buf       |                   |
    |                           |-- match "+CARECV: "      |                   |
    |                           |-- on_cmd_carecv()        |                   |
    |                           |-- net_buf_linearize()    |                   |
    |                           |   (data → 应用 buf)      |                   |
    |                           |-- recv_read_len=66       |                   |
    |                           |<---"OK\r\n"--------------|-------------------|
    |                           |-- k_sem_give(response)   |                   |
    |<-- sem_response given ----|                          |                   |
    |<-- return 66              |                          |                   |
    |                           |                          |                   |
    |-- websocket parse frame   |                          |                   |
    |-- return payload to app   |                          |                   |
```
