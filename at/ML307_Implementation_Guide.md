# ML307 Cat.1 模块 AT命令实现指南

> 基于 `78/esp-ml307` 源码分析 + 官方文档（TCP_IP用户手册_V5.1.5、扩展AT用户手册_4G系列_V1.7.4、SSL用户手册_V5.4.5）
>
> 本文档面向需要在其他平台/框架上重新实现相同功能的开发者。

---

## 目录

1. [硬件与UART配置](#1-硬件与uart配置)
2. [上电与波特率自适应](#2-上电与波特率自适应)
3. [SIM卡检测](#3-sim卡检测)
4. [网络注册（CEREG）](#4-网络注册cereg)
5. [PDP上下文激活与IP获取（MIPCALL）](#5-pdp上下文激活与ip获取mipcall)
6. [TCP连接建立](#6-tcp连接建立)
7. [SSL(TLS)连接建立](#7-ssltls连接建立)
8. [WebSocket握手（软件实现）](#8-websocket握手软件实现)
9. [Opus音频数据发送](#9-opus音频数据发送)
10. [Opus音频数据接收](#10-opus音频数据接收)
11. [连接断开与重连处理](#11-连接断开与重连处理)
12. [URC处理总表](#12-urc处理总表)
13. [关键参数与注意事项](#13-关键参数与注意事项)
14. [完整AT命令时序示意图](#14-完整at命令时序示意图)

---

## 1. 硬件与UART配置

### 1.1 物理连接

| ML307引脚 | MCU功能 |
|-----------|---------|
| TXD       | MCU RXD |
| RXD       | MCU TXD |
| GND       | 共地    |
| VCC       | 供电（查阅模块规格书）|
| RESET/PWR | GPIO（可选，用于硬件复位）|

### 1.2 UART参数

| 参数 | 值 |
|------|----|
| 目标波特率 | 921600 |
| 初始探测波特率 | 见下表 |
| 数据位 | 8 |
| 停止位 | 1 |
| 奇偶校验 | 无 |
| 流控 | 无（软件层面控制发送节奏）|

**波特率探测顺序：**
```
115200 → 921600 → 460800 → 230400 → 57600 → 38400 → 19200 → 9600
```

---

## 2. 上电与波特率自适应

### 2.1 完整流程

```
┌─────────────────────────────────────────────────────────────────┐
│                     上电/硬件复位                               │
│                        ↓                                        │
│  ML307 → MCU: +MATREADY（可选，首次上电时出现，代码不等待它）  │
│                                                                 │
│  [波特率检测循环，尝试列表中每个波特率，每轮之间延迟1000ms]    │
│  MCU → ML307: AT\r\n                 （20ms超时）               │
│  ML307 → MCU: OK                    （匹配成功）                │
│                        ↓                                        │
│  MCU → ML307: AT+IPR=921600\r\n     （设定目标波特率）          │
│  ML307 → MCU: OK                                                │
│  MCU: 本地切换到921600波特率                                    │
│                        ↓                                        │
│  MCU → ML307: AT+CGMR\r\n           （读取固件版本，识别型号）  │
│  ML307 → MCU: ML307_xxx\r\nOK                                   │
│                        ↓                                        │
│  [初始化清理：删除残留HTTP实例，共4个实例id=0~3]               │
│  MCU → ML307: AT+MHTTPDEL=0\r\n → OK                           │
│  MCU → ML307: AT+MHTTPDEL=1\r\n → OK                           │
│  MCU → ML307: AT+MHTTPDEL=2\r\n → OK                           │
│  MCU → ML307: AT+MHTTPDEL=3\r\n → OK                           │
└─────────────────────────────────────────────────────────────────┘
```

### 2.2 关键说明

- **+MATREADY** 是 ML307 上电/重启后的**一次性**主动上报，不会周期性出现。  
  官方文档（扩展AT用户手册 3.1节）定义：`+MATREADY`：模组开机主动上报信息，标识模组AT已初始化完成。  
  **注意**：代码（`AtModem::Detect()`）**不等待** `+MATREADY` 再开始波特率检测，而是直接轮询 `AT`；`+MATREADY` 仅在 `HandleUrc` 中处理，用于检测运行中模组的意外重启并触发重新初始化流程。

- `AT+IPR=<baudrate>` 切换波特率立即生效，MCU侧需在收到OK后再切换本机波特率。

- `AT+CGMR` 返回固件版本字符串，包含 "ML307" 子串，可用于确认模组型号。

- **初始化清理（`AT+MHTTPDEL=0~3`）**：对应源码 `Ml307AtModem::ResetConnections()`，在 `Ml307AtModem` 构造函数中被调用（即 `AtModem::Detect()` 识别到 ML307 型号、创建实例后立即执行）。  
  ML307 最多支持 4 个并发 HTTP 实例（id 0~3），若上次程序崩溃或异常重启时未能正常调用 `AT+MHTTPDEL` 关闭，这些实例会在模组内存中**持续残留**，导致下次创建 HTTP 连接时返回错误（实例已占满）。批量删除所有实例可确保从已知干净状态启动，即使模组未重启也同样有效。

---

## 3. SIM卡检测

### 3.1 AT命令

```
AT+CPIN?
```

**成功响应：**
```
+CPIN: READY
OK
```

**失败响应：**
```
+CME ERROR: 10    ← SIM卡未插入
ERROR             ← 模组未就绪，需等待重试
```

### 3.2 重试策略

代码实现的重试策略（`at_modem.cc::WaitForNetworkReady`）：
- 最多重试 **10次**
- 每次间隔 **1秒**
- 若返回 `+CME ERROR: 10`（未插卡）则立即终止并上报错误

### 3.3 URC处理

```
+CPIN: READY    ← SIM卡热插拔后模块主动上报，仅更新内部标志位
```

> 注意：收到 `+CPIN: READY` URC 时，代码仅更新 `pin_ready_` 标志，**不触发重连**。

---

## 4. 网络注册（CEREG）

### 4.1 AT命令序列

```
# 步骤1：开启扩展URC（包含位置信息）
MCU → ML307: AT+CEREG=2\r\n
ML307 → MCU: OK

# 步骤2：查询当前状态快照（必须执行）
MCU → ML307: AT+CEREG?\r\n
ML307 → MCU: +CEREG: 2,<stat>[,<tac>,<ci>,<AcT>]\r\n
             OK
# 注意：+CEREG: 响应与后续URC走同一套HandleUrc处理逻辑（AtUart将所有
# +CEREG:行都路由至URC回调，无论是命令响应还是主动上报）。
# 若此时 stat=1 或 5，AT_EVENT_NETWORK_READY 事件位立即被置位。

# 步骤3：阻塞等待事件位（单次等待，不轮询）
# 若步骤2已触发 AT_EVENT_NETWORK_READY，则立即返回。
# 若尚未注册，则等待后续 +CEREG URC 异步通知，直到触发事件位。
```

**stat 触发的事件位：**
```
stat=1 或 5 → xEventGroupSetBits(AT_EVENT_NETWORK_READY)
stat=3      → xEventGroupSetBits(AT_EVENT_NETWORK_ERROR)
其他 stat   → 不触发任何事件位，继续等待
```

### 4.2 `<stat>` 状态码含义

| stat | 含义 |
|------|------|
| 0 | 未注册，未搜网 |
| 1 | **注册成功（归属网络）** |
| 2 | 未注册，正在搜网 |
| 3 | 注册被拒绝 |
| 4 | 未知 |
| 5 | **注册成功（漫游）** |

### 4.3 实现要点（严格依据源码）

源码（`at_modem.cc::WaitForNetworkReady`）的实际执行顺序：

```
1. 重置状态：network_ready_=false，清除事件位
2. 循环最多10次：发送 AT+CPIN?（SIM卡检测）
3. 发送 AT+CEREG=2（开启URC）
4. 发送 AT+CEREG?（查询当前状态，必不可少）
   └─ 响应 +CEREG: 通过 HandleUrc 处理：
      ├─ stat=1/5 → network_ready_=true，置 AT_EVENT_NETWORK_READY 位
      └─ stat=3   → 置 AT_EVENT_NETWORK_ERROR 位
5. xEventGroupWaitBits 阻塞等待（单次，不循环发 AT+CEREG?）：
   ├─ 若步骤4已置位 → 立即返回
   └─ 否则等待后续 +CEREG URC 触发（可设置 timeout_ms 超时）
```

**关键结论：**
- `AT+CEREG?` **必须发送**，不能省略——它是获取当前状态快照的唯一手段。
- `AT+CEREG?` 的响应（`+CEREG:`）与后续URC使用**完全相同**的处理逻辑。
- `AT+CEREG?` **不在循环中重复发送**，只发一次，剩余等待由 `xEventGroupWaitBits` 完成。
- 若超时（`timeout_ms`），返回 `NetworkStatus::ErrorTimeout`。

---

## 5. PDP上下文激活与IP获取（MIPCALL）

### 5.1 AT命令（查询方式）

```
MCU → ML307: AT+MIPCALL?\r\n

响应（未激活）:
+MIPCALL: 0,0
OK

响应（已激活）:
+MIPCALL: 0,1,"10.x.x.x"
OK
```

### 5.2 参数说明

| 参数 | 含义 |
|------|------|
| `<cid>` | PDP上下文ID，通常为0或1 |
| `<status>` | 0=未连接，1=已连接 |
| `<IP1>` | 分配的IPv4地址 |
| `<IP2>` | IPv6地址（可选，双栈时返回）|

> **官方文档说明（扩展AT用户手册 4.17节）**：发起建立连接请求，如果此contextID已建立连接，则直接返回OK；执行结果以URC形式上报，连接成功返回连接成功状态及本地的IP地址。

### 5.3 重试策略（轮询等待激活）

代码使用**指数退避**轮询：
```
延迟序列: 10ms → 20ms → 40ms → 80ms → 160ms → 320ms → 640ms → 1000ms（上限）
最多重试: 10次
```

### 5.4 替代方案（主动激活）

若模块未自动激活PDP，可以主动发送：
```
AT+MIPCALL=1,<cid>\r\n    ← 指定cid主动激活（operation=1=建立连接）
```
例：`AT+MIPCALL=1,1` = 激活cid=1的PDP上下文，成功后URC上报：
```
+MIPCALL: 1,1,"10.81.41.153"
```

---

## 6. TCP连接建立

### 6.1 完整AT命令序列（普通TCP，无SSL）

```
# 步骤1：查询当前连接状态（必须执行，源码强制等待 ML307_TCP_INITIALIZED 位）
MCU → ML307: AT+MIPSTATE=0\r\n
ML307 → MCU: +MIPSTATE: 0,<proto>,<addr>,<port>,<state>  ← URC（任何状态都会返回）
             OK
# 若 state="INITIAL"：instance_active_=false，无需关闭
# 若 state="CONNECTED"：instance_active_=true，后续会先发 AT+MIPCLOSE 关闭
# 注意：代码检查 arguments[4].string_value，即第5个字段为 state（共5字段，无 lport）

# 步骤2：关闭SSL（明确指定不用SSL）
MCU → ML307: AT+MIPCFG="ssl",0,0,0\r\n
ML307 → MCU: OK

# 步骤3：设置数据编码格式（收发均使用HEX编码）
MCU → ML307: AT+MIPCFG="encoding",0,1,1\r\n
ML307 → MCU: OK

# 步骤4：建立TCP连接
MCU → ML307: AT+MIPOPEN=0,"TCP","example.com",8080,,0\r\n
ML307 → MCU: OK
             （异步等待URC）
ML307 → MCU: +MIPOPEN: 0,0\r\n   ← result=0 表示连接成功
             或 +MIPOPEN: 0,<非0> ← 连接失败
```

### 6.2 HEX编码说明

#### 为什么需要HEX编码

AT命令是**纯文本协议**，命令行以 `\r\n`（`0x0D 0x0A`）作为结束标志。若将原始二进制数据直接塞进 `AT+MIPSEND` 命令行：

- 一旦数据中出现字节 `0x0D 0x0A`（CR LF），ML307 的 AT 解析器会误认为命令结束，命令被截断，后续字节被当作新命令处理
- `0x00`（null字节）、`0x1B`（ESC，AT命令取消符）等控制字符同样会干扰 AT 协议

**HEX编码的解决方案**：把每个字节映射为 `0-9 A-F` 这16个安全的**可打印ASCII字符**，彻底消除所有控制字符，AT解析器不会产生歧义。

#### 编码规则

| 原始字节 | HEX字符串 | UART实际传输 |
|---------|----------|------------|
| `0xAB`（1字节）| `"AB"`（2字节）| `0x41`（`'A'`）、`0x42`（`'B'`）|
| `0x01 0xAB 0xFF`（3字节）| `"01ABFF"`（6字节）| 6个ASCII字节 |
| 10字节数据 | 20字节HEX字符串 | 20字节 |

常用HEX字符对应的ASCII码：

| HEX字符 | ASCII码（十进制）| ASCII码（十六进制）|
|---------|----------------|------------------|
| `'0'` | 48 | 0x30 |
| `'9'` | 57 | 0x39 |
| `'A'` | 65 | 0x41 |
| `'B'` | 66 | 0x42 |
| `'F'` | 70 | 0x46 |

> **代价**：数据量翻倍，这是HEX编码相对于原始二进制的唯一代价。730字节原始数据 → 1460字节HEX，恰为 AT+MIPSEND 命令行上限。

**发送时**（`send_format=1`）：MCU将二进制数据HEX编码后写入AT命令字符串，ML307从中还原二进制后通过TCP发送。  
**接收时**（`recv_format=1`）：ML307将收到的TCP二进制数据HEX编码后放入URC字符串，MCU从中还原二进制。

源码中对应实现（`ml307_tcp.cc`）：
```cpp
// 发送：EncodeHexAppend 将原始字节追加为HEX字符
at_uart_->EncodeHexAppend(command, data.data() + total_sent, chunk_size);
// 接收：DecodeHex 将HEX字符串还原为原始字节
stream_callback_(at_uart_->DecodeHex(arguments[3].string_value));
```

### 6.3 AT+MIPCFG="encoding" 参数详解

**命令格式：**
```
AT+MIPCFG="encoding",<connect_id>,<send_format>,<recv_format>
```

| 参数 | 值 | 含义 |
|------|-----|------|
| `<connect_id>` | 0~5 | 连接实例ID |
| `<send_format>` | 0 | ASCII原始数据（默认）|
| `<send_format>` | **1** | **HEX字符串**（代码使用此值）|
| `<send_format>` | 2 | 带转义字符串 |
| `<recv_format>` | 0 | ASCII原始数据（默认）|
| `<recv_format>` | **1** | **HEX字符串**（代码使用此值）|

> **关键**：使用HEX编码时，发送数据长度变为原始数据的2倍。例如10字节数据要写20个HEX字符。

### 6.4 AT+MIPOPEN 参数详解

**命令格式：**
```
AT+MIPOPEN=<connect_id>,<proto_type>,<address>,<remote_port>[,<timeout>[,<access_mode>[,<local_port>]]]
```

| 参数 | 值 | 含义 |
|------|-----|------|
| `<connect_id>` | 0~5 | 连接实例ID |
| `<proto_type>` | "TCP" / "UDP" | 协议类型 |
| `<address>` | 字符串 | 服务器IP或域名 |
| `<remote_port>` | 0~65535 | 服务器端口 |
| `<timeout>` | 1~180，默认60 | 连接超时（秒）|
| `<access_mode>` | **0** | **普通模式**（代码使用此值）|
| `<access_mode>` | 1 | 透传模式 |
| `<access_mode>` | 2 | 流缓存模式 |
| `<local_port>` | 0 | 系统自动分配本地端口（默认）|

**URC结果：**
```
+MIPOPEN: <connect_id>,<result>
```
- `result=0`：连接成功
- `result≠0`：连接失败，错误码参见附录

---

## 7. SSL(TLS)连接建立

### 7.1 完整AT命令序列（wss:// 连接）

```
# 步骤1：查询当前连接状态（必须执行，与TCP连接相同，等待 ML307_TCP_INITIALIZED 位）
MCU → ML307: AT+MIPSTATE=0\r\n
ML307 → MCU: +MIPSTATE: 0,...  ← URC
             OK

# 步骤2：配置SSL认证方式（不验证服务器证书）
MCU → ML307: AT+MSSLCFG="auth",0,0\r\n
ML307 → MCU: OK

# 步骤3：为此连接启用SSL
MCU → ML307: AT+MIPCFG="ssl",0,1,0\r\n
ML307 → MCU: OK

# 步骤4：设置HEX编码
MCU → ML307: AT+MIPCFG="encoding",0,1,1\r\n
ML307 → MCU: OK

# 步骤5：建立TCP连接（内部自动进行TLS握手）
MCU → ML307: AT+MIPOPEN=0,"TCP","example.com",443,,0\r\n
ML307 → MCU: OK
             （等待URC）
ML307 → MCU: +MIPOPEN: 0,0\r\n
```

### 7.2 AT+MSSLCFG="auth" 参数详解

**命令格式（官方文档 SSL用户手册 3.1节）：**
```
AT+MSSLCFG="auth",<ssl_id>,<cert_verify>
```

| 参数 | 值 | 含义 |
|------|-----|------|
| `<ssl_id>` | 0~5 | SSL上下文ID（与MIPCFG中的ssl_id对应）|
| `<cert_verify>` | **0** | **无身份认证**（代码使用，不验证服务器证书）|
| `<cert_verify>` | 1 | 单向认证（验证服务器证书）|
| `<cert_verify>` | 2 | 双向认证（互相验证证书）|

### 7.3 AT+MIPCFG="ssl" 参数详解

**命令格式：**
```
AT+MIPCFG="ssl",<connect_id>,<ssl_enable>,<ssl_id>
```

| 参数 | 值 | 含义 |
|------|-----|------|
| `<connect_id>` | 0~5 | 连接实例ID |
| `<ssl_enable>` | **0** | 关闭SSL |
| `<ssl_enable>` | **1** | 启用SSL |
| `<ssl_id>` | 0~5 | 关联的SSL上下文ID |

> `AT+MIPCFG="ssl",0,1,0` 表示：连接0启用SSL，使用ssl_id=0的SSL上下文配置（即刚才 MSSLCFG 设置的auth=0不验证证书的上下文）。

---

## 8. WebSocket握手（软件实现）

ML307 本身不支持WebSocket协议，需要在应用层通过AT+MIPSEND手动发送HTTP Upgrade请求。

### 8.1 握手请求内容

```http
GET /xiaozhi/v1/ HTTP/1.1\r\n
Host: example.com\r\n
Upgrade: websocket\r\n
Connection: Upgrade\r\n
Sec-WebSocket-Key: <base64_random_16bytes>\r\n
Sec-WebSocket-Version: 13\r\n
Protocol-Version: 3\r\n
\r\n
```

### 8.2 发送握手请求（HEX编码）

```
# 将HTTP Upgrade请求字符串转为HEX后发送
MCU → ML307: AT+MIPSEND=0,<data_len>,<HEX_DATA>\r\n
ML307 → MCU: +MIPSEND: 0,<data_len>
             OK
```

> `<data_len>` 为原始数据字节数（非HEX字符串长度），HEX数据**无引号**。

### 8.3 等待握手响应

```
ML307 → MCU: +MIPURC: "rtcp",0,<len>,"<HEX_DATA>"\r\n

HEX解码后内容为:
HTTP/1.1 101 Switching Protocols\r\n
Upgrade: websocket\r\n
Connection: Upgrade\r\n
Sec-WebSocket-Accept: ...\r\n
\r\n
```

收到 `101 Switching Protocols` 响应即表示WebSocket连接建立成功。

### 8.4 Sec-WebSocket-Accept 验证（可选）

标准要求验证：
```
Accept = base64(SHA1(Key + "258EAFA5-E914-47DA-95CA-C5AB0DC85B11"))
```
代码中实现了完整的RFC 6455验证逻辑。

---

## 9. Opus音频数据发送

### 9.1 数据封装层次（从内到外）

```
[1] Opus裸数据（约100~300字节/帧，60ms/帧，16kHz单声道）
     ↓
[2] BinaryProtocol帧头（v2: 16字节，v3: 4字节）
     ↓
[3] WebSocket帧（RFC 6455，opcode=0x02二进制帧，客户端须掩码）
     ↓
[4] HEX编码（每字节 → 2个ASCII字符，数据量翻倍）
     ↓
[5] AT+MIPSEND命令（每次最多730字节原始数据 = 1460字节HEX）
```

### 9.2 BinaryProtocol v2 帧头格式（16字节）

```
字节偏移   字段           类型      说明
+0~1       version        uint16    协议版本 = 2（大端）
+2~3       type           uint16    0=音频（OPUS），1=JSON控制（大端）
+4~7       reserved       uint32    保留，填0
+8~11      timestamp      uint32    时间戳（ms，大端）
+12~15     payload_size   uint32    有效载荷长度（不含头部，大端）
```

> 源码定义（`main/protocols/protocol.h`）：`uint16_t version; uint16_t type; uint32_t reserved; uint32_t timestamp; uint32_t payload_size;`，共16字节。

### 9.3 BinaryProtocol v3 帧头格式（4字节）

```
字节偏移  字段           类型      说明
+0        type           uint8     0=音频（OPUS），1=JSON控制
+1        reserved       uint8     保留，填0
+2~3      payload_size   uint16    有效载荷长度（不含头部，大端）
```

### 9.4 WebSocket帧格式（RFC 6455）

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-------+-+-------------+-------------------------------+
|F|R|R|R| opcode|M| Payload len |    Extended payload length    |
|I|S|S|S|  (4)  |A|     (7)     |             (16/64)           |
|N|V|V|V|       |S|             |   (if payload len==126/127)   |
| |1|2|3|       |K|             |                               |
+-+-+-+-+-------+-+-------------+-------------------------------+
|     Extended payload length continued, if payload len == 127  |
+---------------------------------------------------------------+
|                     Masking-key (4 bytes)                     |
+---------------------------------------------------------------+
|                    Masked Payload Data                         |
+---------------------------------------------------------------+
```

关键字段：
- `FIN=1`（单帧消息）
- `opcode=0x02`（二进制帧）
- `MASK=1`（客户端发送必须掩码）
- `Masking-key`：4字节随机数
- `Payload`：与掩码做XOR后的数据

### 9.5 AT+MIPSEND 发送命令

**命令格式：**
```
AT+MIPSEND=<connect_id>,<send_length>,<data>
```

| 参数 | 值 | 含义 |
|------|-----|------|
| `<connect_id>` | 0~5 | 连接实例ID |
| `<send_length>` | 0~730 | **原始数据字节数**（非HEX字符串长度）|
| `<data>` | HEX字符串 | 要发送的数据（HEX编码，**无引号**）|

**重要限制（来源：源码 `ml307_tcp.cc::Send()`）**：

```cpp
const size_t MAX_PACKET_SIZE = 1460 / 2;  // = 730字节
// command += std::to_string(chunk_size);  // 传入原始数据长度，最大730
// at_uart_->EncodeHexAppend(command, ...);  // HEX数据紧跟其后，无引号
```
`<send_length>` 填入**原始数据字节数**（最大730）。ML307收到后将HEX解码，实际通过TCP传输 730 字节，HEX字符串占命令行 1460 字符。

$$\text{命令行HEX字符数} = <send\_length> \times 2 \leq 1460$$

**响应：**
```
+MIPSEND: <connect_id>,<send_length>
OK
```

> `+MIPSEND` URC 表示数据已成功**进入协议栈发送缓存**，并不代表对端已收到。

### 9.6 分块发送示例

```
原始Opus帧: 200字节
↓ 封装BinaryProtocol v2头（16字节）→ 216字节
↓ 封装WebSocket帧 → 224字节
  （216 > 125，WS帧头：1B opcode + 1B 0xFE + 2B扩展长度 + 4B mask = 8字节头）
↓ HEX编码 → 448字节HEX字符串

AT+MIPSEND=0,224,<448字符HEX数据>\r\n
+MIPSEND: 0,224     ← 确认224字节进入协议栈
OK
```

若数据超过730字节原始（1460字节HEX），需**分多次**发送：
```
# 第1块：730字节原始数据 → 1460字节HEX
AT+MIPSEND=0,730,<1460字符HEX>\r\n
+MIPSEND: 0,730
OK

# 第2块：剩余数据
AT+MIPSEND=0,<remaining_len>,<HEX>\r\n
+MIPSEND: 0,<remaining_len>
OK
```

---

## 10. Opus音频数据接收

### 10.1 接收URC格式

**官方文档（TCP_IP用户手册 3.20节）普通模式下TCP接收：**
```
+MIPURC: "rtcp",<connect_id>,<recv_length>,<data>
```

| 参数 | 含义 |
|------|------|
| `<connect_id>` | 连接实例ID（0~5）|
| `<recv_length>` | 接收到的**原始数据字节数**（HEX编码前）|
| `<data>` | HEX字符串（recv_format=1时），长度为recv_length×2 |

### 10.2 数据解析流程

```
+MIPURC: "rtcp",0,224,"448字符HEX数据"
           ↓
[1] HEX解码 → 224字节二进制数据
           ↓
[2] WebSocket帧解析（RFC 6455）
    - 验证FIN、opcode
    - 读取Payload长度
    - 服务端数据无掩码，直接读取payload
           ↓
[3] BinaryProtocol解析（v2/v3）
    - 读取帧头，确定type和payload_size
    - type=0x01: 音频数据
    - type=0x02: JSON控制消息
           ↓
[4] Opus解码
    - 输入: Opus裸数据（约100~300字节）
    - 参数: 16kHz, 1声道, 60ms帧长
    - 输出: PCM数据（16位有符号，960采样/帧）
           ↓
[5] 送入扬声器播放
```

### 10.3 粘包处理

TCP是流式协议，单个URC可能包含多个WebSocket帧，也可能一个帧被分成多个URC。需要维护**接收缓冲区**和**帧状态机**：

```
pseudocode:
buffer.append(new_data)
while buffer has enough data for WebSocket frame header:
    frame_len = parse_ws_frame_length(buffer)
    if buffer.size >= frame_len:
        frame = buffer.consume(frame_len)
        process_ws_frame(frame)
    else:
        break   # 等待更多数据
```

---

## 11. 连接断开与重连处理

### 11.1 断开相关URC

```
# 远端/异常断开（TCP连接层）
+MIPURC: "disconn",<connect_id>,<connect_state>
    connect_state: 1=服务器关闭连接, 2=连接异常, 3=PDP去激活

# 连接已关闭（资源已释放）
+MIPCLOSE: <connect_id>[,<ret_code>]
    ret_code: 0=正常关闭, 1=超时, 2=其他原因

# 模组重启（需重新走全部初始化流程）
+MATREADY
```

### 11.2 主动关闭连接

```
MCU → ML307: AT+MIPCLOSE=0\r\n
ML307 → MCU: OK
             （异步）
ML307 → MCU: +MIPCLOSE: 0
```

### 11.3 重连策略建议

```
状态机:
  INIT → SIM_CHECK → NETWORK_REG → IP_ACTIVE → TCP_CONN → WS_CONN → RUNNING
  
触发重连的事件:
  +MATREADY         → 回到 INIT（模组重启，全部重来）
  +MIPURC: "disconn" → 回到 TCP_CONN（重建TCP连接）
  +MIPCLOSE         → 回到 TCP_CONN
  WebSocket断开     → 回到 WS_CONN（重发Upgrade请求）
  IP丢失             → 回到 IP_ACTIVE（等待PDP重新激活）
```

---

## 12. URC处理总表

| URC | 触发条件 | 建议处理 |
|-----|---------|---------|
| `+MATREADY` | 模组上电/重启完成 | 触发全流程重新初始化 |
| `+CPIN: READY` | SIM卡就绪 | 更新SIM就绪标志 |
| `+CEREG: n,stat,...` | 网络注册状态变化 | stat=1/5→设置网络就绪标志 |
| `+MIPCALL: cid,1,"IP"` | PDP激活成功 | 记录IP，设置IP就绪标志 |
| `+MIPOPEN: id,0` | TCP连接成功 | 开始WebSocket握手 |
| `+MIPOPEN: id,非0` | TCP连接失败 | 延迟后重试 |
| `+MIPSEND: id,len` | 数据进入发送缓存确认 | 释放发送等待信号量 |
| `+MIPURC: "rtcp",id,len,data` | 收到TCP数据 | HEX解码→WebSocket解析→处理 |
| `+MIPURC: "disconn",id,state` | 连接异常断开 | 触发TCP层重连 |
| `+MIPCLOSE: id` | 连接资源已释放 | 清理连接实例状态 |

---

## 13. 关键参数与注意事项

### 13.1 HEX编码规则

```
原始字节: 0x48 0x65 0x6C 0x6C 0x6F
HEX字符串: "48656C6C6F"   （大写或小写均可发送，接收时统一处理大写）
长度关系: HEX字符串长度 = 原始字节数 × 2
```

### 13.2 AT+MIPSEND 数据长度限制

| 类型 | 限制 |
|------|------|
| 命令行直接输入最大HEX字符串长度 | **1460字节** |
| 对应最大原始数据 | **730字节** |
| 单次超过730字节须分块发送 | |

### 13.3 Opus编解码参数

| 参数 | 值 |
|------|----|
| 采样率 | 16000 Hz |
| 声道数 | 1（单声道）|
| 帧长 | 60ms（每帧960个采样） |
| 编码复杂度 | 0（最低，适合实时通信）|
| 应用类型 | OPUS_APPLICATION_VOIP 或 OPUS_APPLICATION_AUDIO |

### 13.4 多连接实例

ML307支持最多6路并发TCP/UDP连接（connect_id 0~5），每路独立配置。代码中使用 `connect_id=0` 作为主连接。

### 13.5 AT命令响应超时建议

| 命令 | 建议超时 |
|------|---------|
| `AT` / `AT+IPR` | 20ms |
| `AT+CPIN?` | 5s |
| `AT+CEREG?` | 3s |
| `AT+MIPCALL?` | 5s |
| `AT+MIPCFG` | 3s |
| `AT+MSSLCFG` | 3s |
| `AT+MIPOPEN` URC等待 | 60s |
| `AT+MIPSEND` 响应 | 5s |
| `AT+MIPCLOSE` URC等待 | 10s |

### 13.6 AT命令格式注意事项

- 所有命令以 `\r\n` 结尾（CR+LF，即 `0x0D 0x0A`）
- 响应以 `\r\n` 为行分隔符
- HEX字符串参数可用双引号括起，也可不括（读取方式一致）
- 在收到前一条命令的 OK/ERROR 之前，不建议发送下一条命令

---

## 14. 完整AT命令时序示意图

```
时间
 │
 │  ML307上电
 │  └─→ [URC] +MATREADY
 │
 │  [波特率检测]
 │  MCU→: AT\r\n
 │  ←ML307: OK
 │
 │  [设置波特率]
 │  MCU→: AT+IPR=921600\r\n
 │  ←ML307: OK
 │  本地切换921600bps
 │
 │  [型号识别]
 │  MCU→: AT+CGMR\r\n
 │  ←ML307: ML307_xxx\r\nOK
 │
 │  [清理HTTP实例（防止崩溃重启后残留实例占满4个slot）]
 │  MCU→: AT+MHTTPDEL=0\r\n → ←OK
 │  MCU→: AT+MHTTPDEL=1\r\n → ←OK
 │  MCU→: AT+MHTTPDEL=2\r\n → ←OK
 │  MCU→: AT+MHTTPDEL=3\r\n → ←OK
 │
 │  [SIM卡检测，最多重试10次]
 │  MCU→: AT+CPIN?\r\n
 │  ←ML307: +CPIN: READY\r\nOK
 │
 │  [开启网络注册URC]
 │  MCU→: AT+CEREG=2\r\n
 │  ←ML307: OK
 │  MCU→: AT+CEREG?\r\n
 │  ←ML307: +CEREG: 2,2,...\r\nOK   （搜网中）
 │  ...等待...
 │  ←ML307: [URC] +CEREG: 2,1,...   （注册成功）
 │
 │  [等待IP激活，指数退避最多10次]
 │  MCU→: AT+MIPCALL?\r\n
 │  ←ML307: +MIPCALL: 0,1,"10.x.x.x"\r\nOK
 │
 │  ┌─── [TCP连接]
 │  │  MCU→: AT+MIPSTATE=0\r\n
 │  │  ←ML307: +MIPSTATE: 0,...\r\nOK   （必须等待此URC，设置初始化标志）
 │  │  MCU→: AT+MSSLCFG="auth",0,0\r\n → ←OK   （仅SSL需要）
 │  │  MCU→: AT+MIPCFG="ssl",0,1,0\r\n → ←OK   （仅SSL需要）
 │  │  MCU→: AT+MIPCFG="encoding",0,1,1\r\n → ←OK
 │  │  MCU→: AT+MIPOPEN=0,"TCP","host",port,,0\r\n
 │  │  ←ML307: OK
 │  │  ←ML307: [URC] +MIPOPEN: 0,0
 │  └───
 │
 │  ┌─── [WebSocket握手]
 │  │  MCU→: AT+MIPSEND=0,<data_len>,<HEX_HTTP_REQUEST>\r\n
 │  │  ←ML307: +MIPSEND: 0,<data_len>\r\nOK
 │  │  ←ML307: [URC] +MIPURC: "rtcp",0,<len>,"<HEX_HTTP_101>"
 │  │  解析到"101 Switching Protocols"
 │  └───
 │
 │  ┌─── [音频通信循环]
 │  │
 │  │  发送路径（每60ms触发一次）：
 │  │  麦克风PCM → Opus编码 → BinaryProtocol封帧 → WebSocket封帧 → HEX
 │  │  MCU→: AT+MIPSEND=0,<data_len>,<HEX>\r\n
 │  │  ←ML307: +MIPSEND: 0,<len>\r\nOK
 │  │
 │  │  接收路径（随时可能到来）：
 │  │  ←ML307: [URC] +MIPURC: "rtcp",0,<len>,"<HEX>"
 │  │  HEX解码 → WebSocket解帧 → BinaryProtocol解帧 → Opus解码 → 扬声器PCM
 │  │
 │  └───
 │
 │  [断开情况处理]
 │  ←ML307: [URC] +MIPURC: "disconn",0,1  → 触发重连
 │  或 ←ML307: [URC] +MATREADY             → 触发全流程重新初始化
```

---

## 附录A：AT命令速查表

| AT命令 | 功能 | 关键参数 |
|--------|------|---------|
| `AT` | 通信测试 | 无 |
| `AT+IPR=<baud>` | 设置波特率 | 921600 |
| `AT+CGMR` | 读取固件版本 | 无 |
| `AT+MHTTPDEL=<id>` | 删除HTTP实例 | id: 0~3 |
| `AT+CPIN?` | 查询SIM卡状态 | 无 |
| `AT+CEREG=2` | 开启网络注册URC（含位置信息）| 模式2 |
| `AT+CEREG?` | 查询当前网络注册状态 | 无 |
| `AT+MIPCALL?` | 查询PDP上下文激活状态及IP | 无 |
| `AT+MIPCALL=1,<cid>` | 主动激活PDP上下文 | cid通常为1 |
| `AT+MIPSTATE=<id>` | 查询TCP连接状态 | connect_id |
| `AT+MSSLCFG="auth",<ssl_id>,<verify>` | 配置SSL认证方式 | verify=0无认证 |
| `AT+MIPCFG="ssl",<id>,<enable>,<ssl_id>` | 启用/关闭SSL | enable=1启用 |
| `AT+MIPCFG="encoding",<id>,<sf>,<rf>` | 设置数据编码格式 | sf=rf=1: HEX |
| `AT+MIPOPEN=<id>,"TCP",<host>,<port>,,<mode>` | 建立TCP连接 | mode=0普通 |
| `AT+MIPSEND=<id>,<len>,<data>` | 发送数据（HEX）| len为原始字节数，最大730；对应HEX字符最大1460 |
| `AT+MIPCLOSE=<id>` | 关闭TCP连接 | 无 |

---

## 附录B：错误处理参考

| 错误 | 含义 | 建议处理 |
|------|------|---------|
| `+CME ERROR: 10` | SIM卡未插入 | 上报错误，停止重试 |
| `+CME ERROR: 3` | 操作不允许（可能未注册到网络）| 检查网络注册状态 |
| `+MIPOPEN: 0,非0` | TCP连接失败 | 等待5s后重试 |
| `+MIPURC: "disconn",0,2` | 连接异常 | 关闭并重建TCP |
| `+MIPURC: "disconn",0,3` | PDP去激活 | 等待IP重新激活后重连 |

---

*文档版本: 基于 ESP32 xiaozhi-esp32 项目分析 + ML307官方文档V5.1.5/V1.7.4/V5.4.5*
