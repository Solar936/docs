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

## 4. RX 路径：接收数据到应用层

### 4.1 第一阶段：被动通知（modem_rx 线程，异步）

```
UART 中断 ISR
  → uart_fifo_read() 读数据
  → ring_buf_put() 写入环形缓冲区（iface_rb_buf，1024 字节）
  → k_sem_give(&data->rx_sem)  ← 唤醒 modem_rx 线程

modem_rx 线程（sim7080.c）
  → modem_iface_uart_rx_wait()  ←  阻塞在 rx_sem
  → modem_cmd_handler_process()
      → cmd_handler_process_iface_data()
            ring_buf_get() → net_buf 链表（最多 30 个 buf × 1024 字节）
      → cmd_handler_process_rx_buf()
            匹配 URC "+CADATAIND: <cid>"
            → on_urc_cadataind()
            → sim7080_handle_sock_data_indication(fd)
                → modem_socket_packet_size_update(sock, 1)  // dummy=1，表示"有数据"
                → modem_socket_data_ready()
                    → k_sem_give(&sock->sem_data_ready)     // 唤醒等待 recvfrom 的线程
                    → k_poll_signal_raise(&sock->sig_data_ready)  // 唤醒 poll
```

> **注意**：模组只告知"有数据到达"（+CADATAIND），不告知长度，因此 packet_size 设为 dummy=1。

### 4.2 第二阶段：主动拉取（应用线程，同步）

```
websocket_recv_msg(ws_sock, buf, len)
  → zsock_recvfrom(ctx->real_sock, ...)
  → offload_socket_fd_op_vtable.recvfrom = offload_recvfrom()
```

`offload_recvfrom()` 内部流程：

```
modem_socket_next_packet_size() == 0?
  ├─ DONTWAIT: 返回 EAGAIN
  └─ 否则: modem_socket_wait_data() 阻塞在 sem_data_ready
              ← 被 on_urc_cadataind 唤醒后继续

构造 "AT+CARECV=<id>,<max_len>\r\n"
modem_cmd_send(..., data_cmd, ...)  ←  临时注入 "+CARECV: " 的解析规则

modem_rx 线程收到 "+CARECV: <len>,<data>"
  → on_cmd_carecv()
  → sockread_common()
      → net_buf_linearize() 从 rx_buf 拷贝到 sock_data->recv_buf（即应用 buf）
      → 更新 sock_data->recv_read_len

offload_recvfrom() 返回 recv_read_len

websocket_recv_msg() 解析 WS 帧头，剥掉协议头后返回给应用
```

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
应用线程                          modem_rx 线程              模组 UART
    |                                   |                        |
    |                                   |<---"+CADATAIND: 0\r\n"-|
    |                                   |-- on_urc_cadataind()   |
    |                                   |-- packet_size_update(1)|
    |                                   |-- k_sem_give(data_rdy) |
    |                                   |                        |
    |-- offload_recvfrom() ------------>|                        |
    |   |-- wait sem_data_ready         |                        |
    |   |<-- (already given) -----------|                        |
    |   |-- AT+CARECV=id,len\r\n ------>|----UART-TX------------>|
    |   |<-- wait sem_response          |<---"+CARECV:N,data\r\n"|
    |   |                               |-- on_cmd_carecv()      |
    |   |                               |-- net_buf_linearize()  |
    |   |                               |-- (数据已拷到 buf)      |
    |   |                               |<---"OK\r\n"------------|
    |   |                               |-- on_cmd_ok()          |
    |   |                               |-- k_sem_give(response) |
    |   |<-- sem_response given --------|                        |
    |<-- return recv_read_len           |                        |
    |-- websocket 解析帧头，返回 payload->|                        |
```
