# ML307C Zephyr Offload 实现方案

> 参考来源：
> - `zephyr/samples/drivers/modem/uart_raw_test/src/main.c`（v9，已验证可与小智服务器完成 WebSocket/HTTPS 通信）
> - `docs/at/TCP_IP用户手册_V5.1.5.md`
> - `docs/at/ML307_Implementation_Guide.md`
> - `zephyr/drivers/modem/simcom/sim7080/`（对比参考）

---

## 1. ML307 vs sim7080 核心差异对比

| 特性 | sim7080 | ML307 |
|------|---------|-------|
| **TX 准备步骤** | `AT+CASEND=<id>` 查可用空间 | **不需要**，直接发数据 |
| **TX 触发符** | 等 `>` 提示符（DIRECT 匹配，无 CRLF）| **无** `>` 提示符 |
| **TX 命令格式** | `AT+CASEND=<id>,<len>` → 等 `>` → 原始二进制 + `\x1A` | `AT+MIPSEND=<id>,<len>,<HEX数据>`（内联 HEX，单条命令）|
| **TX 最大块** | **动态**（`AT+CASEND=<id>` 查询 `tx_space_avail`，驱动无硬编码上限；接收侧 CARECV 上限为 1024） | **730 字节原始**（HEX 后 1460 字符），超出需分块 |
| **TX 确认** | `OK` | `+MIPSEND: <id>,<len>` + `OK` |
| **数据编码** | 原始二进制 | **HEX 字符串**（必须用 `AT+MIPCFG="encoding"` 先开启）|
| **RX 通知** | `+CADATAIND: <fd>`（无数据，仅通知）| `+MIPURC: "rtcp",<id>,<len>,<HEX数据>`（URC **直接携带数据**）|
| **RX 拉数据** | 必须主动 `AT+CARECV=<id>,<len>` | **access_mode=0 无需拉取**，URC 就是数据 |
| **断连通知** | `+CASTATE: <id>,0` | `+MIPURC: "disconn",<id>,<state>` |
| **PDP 激活** | 独立 sim7080_pdp 驱动（CNACT/CNCFG）| `AT+MIPCALL?` 轮询 / `AT+MIPCALL=1,1` 激活 |
| **TLS** | 内置（Mbed TLS on-chip）| `AT+MSSLCFG="auth",0,<verify>` + `AT+MIPCFG="ssl",<id>,1,0` |

---

## 2. HEX 编码原因

AT 协议是文本协议。WebSocket/TLS 数据流是二进制，其中包含 `0x0D`（`\r`）、`0x0A`（`\n`）、`0x00`、`0x1A` 等字节，这些字节会被 AT 解析器误认为命令结束符或 CTRL-Z，从而破坏协议。

ML307 通过在连接上配置 `AT+MIPCFG="encoding",<id>,1,1`，要求应用层将所有二进制数据转为大写 HEX ASCII 再发送，接收到的 `+MIPURC: "rtcp"` URC 的数据字段同样是 HEX 格式，驱动负责解码。

```
原始字节: 0x47 0x45 0x54 0x20 0x2F 0x0D 0x0A
HEX串:    "4745542F 0D0A"
```

---

## 3. offload_connect 实现方案

```
1. AT+MIPSTATE=<id>               检查实例状态（已连接则先关闭）
2. AT+MIPCFG="ssl",<id>,<en>,0    TLS 开关（不需要 TLS 时 en=0）
       - 若 TLS：先 AT+MSSLCFG="auth",0,<verify>  (0=不验证, 1=验证)
3. AT+MIPCFG="encoding",<id>,1,1  HEX 编解码（TX 和 RX 都 HEX）
4. AT+MIPOPEN=<id>,"TCP",<ip>,<port>,,0   mode=0（普通模式）
5. 等待 URC：
       - 普通 TCP：+MIPOPEN: <id>,0           （0=success）
       - TLS 连接：+IPOPEN:  <id>,0           （同含义，前缀不同！）
```

**注意**：uart_raw_test 实测证明 TLS 连接时 URC 前缀为 `+IPOPEN:` 而非 `+MIPOPEN:`，驱动需用 `urc_wait2()` 同时匹配两种前缀。

---

## 4. offload_sendto 实现方案

无 `>` 提示符，**单条命令**完成发送：

```
AT+MIPSEND=<id>,<data_len>,<HEX_data>\r\n
```

- `<data_len>`：原始字节数，最大 **730**
- `<HEX_data>`：`data_len * 2` 个大写 ASCII 字符（无引号）
- 超过 730 字节：循环切块，每块分别发一条 `AT+MIPSEND`
- 响应序列：`+MIPSEND: <id>,<len>\r\nOK\r\n`（15 s 超时，TLS 握手可能延迟）

sim7080 对比：
```
sim7080: AT+CASEND=0           → +CASEND: N（N=tx_space_avail，模组动态返回）
         AT+CASEND=0,<len>     → 等 >（sem_tx_ready）
                                → 写原始二进制字节 → 写 0x1A（Ctrl-Z）
                                → OK
ML307:   AT+MIPSEND=0,<len>,<HEX> → +MIPSEND:0,<len> → OK
```

> **注**：sim7080 TX 无驱动侧硬编码上限，每次由 `AT+CASEND=<id>` 动态查询可用缓冲区大小（`mdata.tx_space_avail`）；`MDM_MAX_DATA_LENGTH=1024` 是 `AT+CARECV` **接收侧**的截断值，与 TX 无关。

---

## 5. offload_recvfrom 实现方案

### 5.1 数据到达路径（URC 驱动，access_mode=0）

```
UART ISR
 └─ ring_buf_put() → k_sem_give(rx_sem)
     └─ modem_rx 线程
         └─ modem_cmd_handler_process()
             └─ find_cmd_match() 命中 "+MIPURC: \"rtcp\"" URC handler
                 └─ on_urc_mipurc_rtcp()
                     ├─ HEX 解码  argv[3] → binary
                     ├─ ring_buf_put(&sd->rx_rb, decoded, n)   写入 per-socket ring buf
                     ├─ k_sem_give(&sd->rx_sem)                唤醒 blocked recv()
                     └─ modem_socket_packet_size_update()      更新 poll() 信号
```

### 5.2 offload_recvfrom 执行路径

```c
pkt_size = modem_socket_next_packet_size(sock);
if (!pkt_size) {
    if (flags & DONTWAIT)  { errno = EAGAIN; return -1; }
    k_sem_take(&sd->rx_sem, K_FOREVER);   // URC handler 触发后唤醒
}
// 从 per-socket ring buf 拷到用户 buf
n = ring_buf_get(&sd->rx_rb, buf, max_len);
modem_socket_packet_size_update(sock, -(int)n);
return n;
```

**与 sim7080 recvfrom 的关键差异**：

| | sim7080 | ML307 |
|--|---------|-------|
| 等数据 | `modem_socket_wait_data()` → `k_sem_take(sem_data_ready)` | `k_sem_take(&sd->rx_sem)` |
| 数据来源 | 收到 `+CADATAIND` → 发 `AT+CARECV` → CMD handler 解析 → `net_buf_linearize` 到 `sock_data->recv_buf` | URC handler 直接解码 HEX → `ring_buf_put` |
| 返回给调用者 | `sock_data->recv_read_len`（单次 AT 结果） | `ring_buf_get()` 返回字节数 |

### 5.3 sim7080 recvfrom 完整数据回传路径（参考）

```
+CADATAIND: <fd>
  └─ on_urc_cadataind()
      └─ sim7080_handle_sock_data_indication(fd)
          ├─ modem_socket_packet_size_update(sock, +1)  → k_poll_signal_raise(&sig_data_ready, 0)
          └─ modem_socket_data_ready()                  → k_sem_give(&sem_data_ready)

offload_recvfrom():
  1. modem_socket_next_packet_size() == 0 → modem_socket_wait_data() → k_sem_take(sem_data_ready)
  2. snprintk("AT+CARECV=%d,%zd", id, max_len)
  3. modem_cmd_send() → on_cmd_carecv() → sockread_common()
       └─ net_buf_linearize(sock_data->recv_buf, max_len, data->rx_buf, ...)
           → sock_data->recv_read_len = ret
  4. return sock_data->recv_read_len
```

---

## 6. k_poll_signal_raise 的作用

`k_poll_signal_raise(&sock->sig_data_ready, 0)` 由 `modem_socket_packet_size_update()` 调用（在 `modem_socket.c:134`）。

作用：**唤醒正在调用 `zsock_poll()` / `select()` 的线程**，通知"此 socket 有可读数据"。

机制：
```
offload_poll_prepare():
    k_poll_event_init(*pev,
        K_POLL_TYPE_SIGNAL,         // 监听 k_poll_signal_t
        K_POLL_MODE_NOTIFY_ONLY,
        &sock->sig_data_ready);     // 绑定到这个 signal

当 modem_socket_packet_size_update() 设置 sig_data_ready 时：
    k_poll_signal_raise() → 唤醒 k_poll() → zsock_poll() 返回 POLLIN
```

与 `sem_data_ready` 的区别：
- `sem_data_ready`：用于 **`recv()` 阻塞等待**（`modem_socket_wait_data()`）
- `sig_data_ready`：用于 **`poll()` / `select()` 监听**（`zsock_poll()`，如 WebSocket 事件循环）

---

## 7. sim7080_pdp 的作用及 ML307 等效方案

### sim7080_pdp 做的事

`sim7080_pdp_activate()` 执行以下步骤：
1. **频段配置**：`AT+CNMP` / `AT+CMNB` / `AT+CBANDCFG`
2. **RSSI 等待**：`AT+CSQ` 轮询直到有效信号值
3. **GPRS 附着确认**：`AT+CGATT?` 轮询直到 `+CGATT: 1`
4. **网络注册等待**：`AT+CEREG?` / `AT+CREG?` 轮询直到 stat=1 或 5
5. **PDP 上下文配置**：`AT+CNCFG=0,0`（双栈 IPv4/IPv6）
6. **PDP 激活**：`AT+CNACT=0,1` → 等待 URC `+APP PDP: ACTIVE`（通过 `pdp_sem`）

### ML307 等效流程（来自 uart_raw_test v9）

ML307 没有独立 PDP 驱动，全部在 modem init 序列中完成：

| 步骤 | sim7080_pdp | ML307（uart_raw_test） |
|------|-------------|----------------------|
| 频段配置 | `AT+CNMP` / `AT+CBANDCFG` | **不需要**（ML307 内部自动） |
| 深睡眠禁用 | 无（sim7080 无深睡眠问题） | `AT+MLPMCFG="sleepmode",0,1`（**必须 permanent=1 写 NV**） |
| RSSI | `AT+CSQ` 轮询 | `AT+CSQ`（查询即可，无需等待循环） |
| 网络注册 | `AT+CGATT?` + `AT+CEREG?` 轮询 | `AT+CEREG=2` → 等待 `+CEREG: 1/5` **URC**（不轮询） |
| PDP 上下文 | `AT+CNCFG=0,0`（必须配置） | **不需要**（ML307 内置） |
| PDP 激活 | `AT+CNACT=0,1` → URC 确认 | `AT+MIPCALL?` 指数退避轮询 → 失败则 `AT+MIPCALL=1,1` |

**关键结论**：ML307 **不需要** `sim7080_pdp` 这样的独立驱动文件。PDP 激活逻辑直接放在 modem `init_thread` 的启动序列（Step 8）中即可。

---

## 8. ML307 完整 AT 命令启动序列（来自 uart_raw_test v9）

```
Step 0:  AT                               ← 探测，重试最多 30 次（间隔 1s）
Step 1:  AT+MLPMCFG="sleepmode",0,1       ← 禁用深睡眠（permanent=1，写 NV）
Step 2:  ATE0                             ← 关闭回显
Step 3:  AT+CGMR                          ← 读固件版本
Step 4:  AT+CPIN? (x10)                   ← 等 SIM READY
Step 5:  AT+CEREG=2                       ← 开启网络注册 URC（含位置信息）
Step 6:  AT+CEREG? + 等 +CEREG: 1/5 URC  ← 等网络注册（最多 60s，被动 URC）
Step 7:  AT+CSQ                           ← 信号质量
Step 8:  AT+MIPCALL? (指数退避 x10)       ← 等 PDP 激活；失败则 AT+MIPCALL=1,1
Step 9:  AT+CGSN=1                        ← IMEI
Step 10: AT+ICCID                         ← ICCID
Step 11: AT+COPS?                         ← 运营商名称
→ state = NETWORKING
```

**关键易错点**：

1. `AT+MLPMCFG="sleepmode",0,1` 的第三个参数必须是 `1`（写 NV）。若为 `0`（不写 NV），每次深睡唤醒后睡眠模式恢复，modem 反复重启发送 `+MATREADY`，`AT+CEREG?` 永远超时。

2. Step 6 网络注册等待**不能用 `rx_drain()` 清缓冲区**后再轮询。`+CEREG: 1` URC 是异步到来的，如果在它到达时恰好调用 `rx_drain()` 就会丢失。正确做法：持续积累所有字节，对每个字节调用 `cereg_registered()` 扫描。

3. `AT+MIPCALL?` 响应含 `,1,` 说明 PDP 已激活；若不含则等待后重试。

---

## 9. 完整 TypeC Socket 数据流时序图（ML307）

### TX 路径

```
App 调用 zsock_send(fd, buf, len, 0)
  └─ offload_sendto(obj, buf, len, ...)
      └─ mipsend(sock->id, buf, len)
          ├─ （若 len > 730）循环切块
          └─ 对每块：
              ├─ snprintk("AT+MIPSEND=%d,%zu,", id, chunk)    构造 header
              ├─ hex_encode(chunk_data, chunk, header_end)      追加 HEX
              └─ modem_cmd_send()
                  ├─ iface.write() → uart_poll_out() 逐字节 TX
                  ├─ k_sem_take(sem_response, 15s)
                  └─ on_cmd_mipsend URC handler → LOG
```

### RX 路径

```
模组推送 URC（access_mode=0，无需主动拉取）：
+MIPURC: "rtcp",0,66,"4745542F484F535..."

UART ISR
  └─ ring_buf_put(modem rx ring) → k_sem_give(rx_sem)
      └─ modem_rx 线程
          └─ modem_cmd_handler_process()
              └─ find_cmd_match: "+MIPURC: \"rtcp\""
                  └─ on_urc_mipurc_rtcp(argv[0..3])
                      ├─ id      = atoi(argv[1])   连接 ID
                      ├─ recvlen = atoi(argv[2])   原始字节数
                      ├─ hex_decode(argv[3]) → decoded[]
                      ├─ ring_buf_put(&sock_data[id].rx_rb, decoded, n)
                      ├─ k_sem_give(&sock_data[id].rx_sem)
                      └─ modem_socket_packet_size_update(sock, +n)
                          └─ k_poll_signal_raise(&sig_data_ready, 0)

App 调用 zsock_recv(fd, buf, max_len, 0)
  └─ offload_recvfrom(obj, buf, max_len, ...)
      ├─ modem_socket_next_packet_size() == 0?
      │   └─ k_sem_take(&sd->rx_sem, K_FOREVER)   ← 阻塞直到上面 give
      └─ ring_buf_get(&sd->rx_rb, buf, max_len)
          └─ return n
```

### poll() 路径（WebSocket 事件循环）

```
App 调用 zsock_poll(&pfd, 1, timeout)
  └─ offload_poll_prepare()
      └─ k_poll_event_init(K_POLL_TYPE_SIGNAL, &sock->sig_data_ready)
          ← 绑定到信号

数据到来时 modem_socket_packet_size_update() 调用：
  k_poll_signal_raise(&sig_data_ready, 0)
    └─ k_poll() 返回 → zsock_poll() 返回 POLLIN

App 收到 POLLIN → 调用 zsock_recv()
```

---

## 10. URC 完整表（ML307 TCP/IP 相关）

| URC | 触发时机 | 处理方式 |
|-----|---------|---------|
| `+MIPURC: "rtcp",<id>,<len>,"<HEX>"` | access_mode=0，收到 TCP 数据 | HEX 解码 → 写 per-socket ring buf |
| `+MIPURC: "disconn",<id>,<state>` | 远端关闭、网络断开 | 置 `remote_closed=true`，`k_sem_give(rx_sem)` |
| `+MIPOPEN: <id>,<result>` | MIPOPEN 完成 | 检查 result==0，`k_sem_give(sem_open)` |
| `+IPOPEN: <id>,<result>` | TLS 连接完成（前缀不同！） | 同上 |
| `+MIPCLOSE: <id>` | 连接关闭完成 | 清除连接状态 |
| `+CEREG: <stat>` | 网络注册状态变化 | 更新注册状态标志 |
| `+MATREADY` | 模组启动/从深睡唤醒 | 检测是否模组重启（与深睡眠相关） |
| `+MIPCALL: <id>,<state>,<ip>` | PDP 激活/去激活结果 | 更新 PDP 状态 |
