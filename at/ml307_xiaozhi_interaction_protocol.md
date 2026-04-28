# ML307 + 小智 WebSocket 交互协议分析

> 数据来源：`小智回复opus`（uart_raw_test 运行日志）、`tx-decoder--260426-141855.csv`（逻分 TX 抓取，8582 字节）、`rx-decoder--260426-141855.csv`（逻分 RX 抓取，21343 字节）

---

## 一、Modem 初始化序列

### Step 0：波特率自动检测

| TX | RX | 备注 |
|---|---|---|
| `AT\r\n` | `\r\nOK\r\n` | 921600 baud 命中（sweep 1） |

### Step 1：检查/关闭深度睡眠

```
TX: AT+MLPMCFG="sleepmode"\r\n
RX: \r\n+MLPMCFG: "sleepmode",0\r\n\r\nOK\r\n   (33 bytes)
```

- 若返回 `0` → 已禁用，跳过；若非 `0` → 需发 `AT+MLPMCFG="sleepmode",0,1` + `AT+CFUN=1,1` 重启

### Step 2：关闭回显

```
TX: ATE0\r\n
RX: \r\nOK\r\n   (6 bytes)
```

### Step 3：查询固件版本

```
TX: AT+CGMR\r\n
RX: \r\nML307R-DL-MBRH0S01\r\n\r\nOK\r\n   (28 bytes)
```

### Step 4：SIM 卡就绪检查（最多 10 次重试）

```
TX: AT+CPIN?\r\n
RX: \r\n+CPIN: READY\r\n\r\nOK\r\n   (22 bytes)
```

### Step 5：配置网络注册 URC 格式

```
TX: AT+CEREG=2\r\n
RX: \r\nOK\r\n   (6 bytes)
```

### Step 6：等待网络注册

```
TX: AT+CEREG?\r\n
RX: \r\n+CEREG: 2,1,"5141","0521870C",7\r\n\r\nOK\r\n   (41 bytes)
```

stat=1 表示已注册；stat 定义：0=未注册，1=注册本地，2=搜索，3=被拒，5=漫游

### Step 7：信号质量

```
TX: AT+CSQ\r\n
RX: \r\n+CSQ: 20,99\r\n\r\nOK\r\n   (21 bytes)
```

CSQ 20 ≈ -73 dBm

### Step 8：PDP 上下文激活检查（指数退避，最多 10 次）

```
TX: AT+MIPCALL?\r\n
RX: \r\n+MIPCALL: 1,1,"10.148.158.125","2409:8D21:DE2:E858::1"\r\n\r\nOK\r\n   (64 bytes)
```

格式：`+MIPCALL: <cid>,<status>,<ipv4>,<ipv6>`，status=1 表示已激活

### Step 9–12：设备信息查询

| 命令 | 响应示例 |
|---|---|
| `AT+CGSN=1` | `+CGSN: 864098089417629` (IMEI) |
| `AT+ICCID` | `+ICCID: 89860413162580182798` |
| `AT+COPS?` | `+COPS: 0,0,"CHINA MOBILE",7` |
| `AT+CGMI` | `CMCC` |
| `AT+CGMM` | `ML307R` |

---

## 二、设备激活 HTTP POST（Step 13a–g）

### 2.1 TLS/SSL 配置

```
AT+MSSLCFG="auth",0,0          → 不验证服务器证书
AT+MIPCFG="ssl",0,1,0          → socket 0 启用 SSL，TLS 1.2
AT+MIPCFG="encoding",0,1,1     → socket 0 数据使用 HEX 编码
```

**关键**：`encoding=1,1` 表示发送和接收均使用 HEX 字符串。所有 `AT+MIPSEND` 的数据和 `+MIPURC` 的数据均为 ASCII HEX 字符串（每字节 2 个十六进制字符）。

### 2.2 建立 TCP 连接

```
TX: AT+MIPOPEN=0,"TCP","api.tenclass.net",443,,0\r\n
RX: \r\nOK\r\n   (等待 URC)

URC: +MIPOPEN: 0,0     → 连接成功（约 1.4 秒后）
```

格式：`AT+MIPOPEN=<cid>,"TCP",<host>,<port>,,<ssl_flag>`，ssl_flag=0（SSL 由 MIPCFG 控制）

### 2.3 发送 HTTP POST 请求

```
TX: AT+MIPSEND=0,523,<HEX_523bytes>\r\n
RX: \r\n+MIPSEND: 0\r\n\r\nOK\r\n   (25 bytes, "+MIPSEND OK")
```

HTTP 请求体（235 字节）：JSON，含设备 MAC/IMEI/ICCID 等信息，发往 `POST /xiaozhi/v1/ HTTP/1.1`

**注意**：523 字节是 HEX 编码后的长度（实际数据 ≈ 261 字节）。实际数据用 HEX 编码，所以 AT 命令参数长度 = 实际字节数 × 2 + 帧头字节数。

### 2.4 接收激活响应

```
URC: +MIPURC: "recv",0,<len>\r\n<HEX_data>
```

解码后得 HTTP 200 响应 JSON：

```json
{
  "mqtt": { "endpoint": "mqtt.xiaozhi.me", "client_id": "...", "publish_topic": "device-server", ... },
  "websocket": { "url": "wss://api.tenclass.net/xiaozhi/v1/", "token": "test-token" },
  "server_time": { "timestamp": 1777184349041, "timezone_offset": 480 },
  "firmware": { "version": "1.0.0", "url": "" }
}
```

关键字段：
- `websocket.url`：后续 WebSocket 连接地址
- `websocket.token`：WebSocket 鉴权 token
- `mqtt.*`：MQTT 备用通道配置（本流程不用）

### 2.5 关闭 HTTP 连接

```
TX: AT+MIPCLOSE=0\r\n
RX: 返回 ERROR（服务端已主动断开），视为正常
```

---

## 三、WebSocket 连接建立（Step 15–16）

### 3.1 重新建立 TCP/TLS 连接

```
TX: AT+MIPSTATE=0\r\n
RX: +MIPSTATE: 0,,,,"INITIAL"    → 确认 socket 处于 INITIAL 状态

TX: AT+MIPOPEN=0,"TCP","api.tenclass.net",443,,0\r\n
RX: OK → 等待 +MIPOPEN URC（约 500ms）
```

### 3.2 WebSocket HTTP Upgrade

```
TX: AT+MIPSEND=0,293,<HEX_293bytes>\r\n   (实际 HTTP 请求约 146 字节)
RX: +MIPSEND: 0 OK
```

HEX 解码后为标准 HTTP/1.1 Upgrade 请求：

```http
GET /xiaozhi/v1/ HTTP/1.1\r\n
Host: api.tenclass.net\r\n
Upgrade: websocket\r\n
Connection: Upgrade\r\n
Sec-WebSocket-Key: <base64_16bytes>\r\n
Sec-WebSocket-Version: 13\r\n
Authorization: Bearer test-token\r\n
\r\n
```

### 3.3 接收 101 Switching Protocols

```
URC 中包含: HTTP/1.1 101 Switching Protocols
           Upgrade: websocket
           Connection: Upgrade
           Sec-WebSocket-Accept: <hash>
```

约 130ms 后收到 101，WebSocket 升级完成。

---

## 四、小智协议握手（Step 17–18）

### 4.1 发送 Hello

WebSocket 文本帧，JSON payload：

```json
{
  "type": "hello",
  "version": 1,
  "features": { "mcp": true },
  "transport": "websocket",
  "audio_params": {
    "format": "opus",
    "sample_rate": 16000,
    "channels": 1,
    "frame_duration": 60
  }
}
```

封装为 WebSocket 帧后 HEX 编码，`AT+MIPSEND=0,170,<HEX>`（实际帧约 85 字节）。

### 4.2 接收 Hello 响应

约 130ms 后收到服务端 hello（166 字节 WS 帧）：

```json
{
  "type": "hello",
  "version": 3,
  "transport": "websocket",
  "audio_params": {
    "format": "opus",
    "sample_rate": 24000,
    "channels": 1,
    "frame_duration": 60
  },
  "session_id": "c029ff80"
}
```

**注意**：服务端 hello 中 `sample_rate=24000`，与客户端 `sample_rate=16000` 不同，说明服务端要求 24kHz 输出采样率（TTS 音频为 24kHz）。`session_id` 用于后续所有消息。

---

## 五、语音对话流程（Step 19–22）

### 5.1 Listen Start

```json
{"session_id":"c029ff80","type":"listen","state":"start","mode":"manual"}
```

`AT+MIPSEND=0,79,<HEX>`（WS 帧 ~39 字节）

### 5.2 发送 Opus 音频帧

**编码规则**：
- Opus 帧封装为 WebSocket 二进制帧
- WS 帧头：固定 `\x82\x7E`（binary, 2字节长度）+ 2字节长度 + 4字节掩码 + 数据
- 每帧 60ms，16kHz，1ch
- 帧间隔约 20ms（定时发送）

**帧大小分布**（29帧）：

| 帧编号 | Opus 字节 | WS 帧字节 | HEX len（AT+MIPSEND 参数） |
|---|---|---|---|
| 0 | 20 | 30 | 30 |
| 1 | 82 | 92 | 92 |
| 2-3 | ~148~178 | ~160~190 | ~160~190 |
| 14–28 | 21 | 31 | 31（尾部均为21字节静音帧） |

**WS 二进制帧格式（HEX 编码前）**：

```
82 7E [len_hi] [len_lo] [mask0] [mask1] [mask2] [mask3] [masked_opus_data...]
```

实际数据长度 = 6 + opus_size 字节（帧头 2 + 长度 2 + 掩码 4）。

### 5.3 Listen Stop

```json
{"session_id":"c029ff80","type":"listen","state":"stop"}
```

`AT+MIPSEND=0,62,<HEX>`

### 5.4 接收服务端响应

服务端通过 `+MIPURC: "recv",0,<len>\r\n<HEX>` URC 推送数据。

**消息类型及时序**（以本次"你好"为例）：

| 时间 | 消息类型 | 内容 |
|---|---|---|
| +0ms (send stop) | — | — |
| +194ms | `tts` start | TTS 开始播放标记 |
| +212ms | `stt` | `"你好。"` 识别结果 |
| +514ms | `llm` | emotion=happy |
| +632ms | `tts` text | `"你好呀，今天过得怎么样？"` |
| +893ms | audio frame #0 | opus 121字节，24kHz，60ms |
| +926ms | audio frame #1 | opus 213字节 |
| ... | audio frames #2–N | 后续 TTS 音频帧 |

**TTS 音频 URC 特点**：
- 每帧约 120~190ms 间隔
- 每次 URC 可能含多段 `+MIPURC` chunk（逻分可见每次 IDLE 产生 2~3 个连续 chunk，总 256~500 字节）
- 单个 WS 音频帧约 120~230 字节（opus 数据，24kHz 60ms/帧）

**UART RX chunk 规律**（逻分观察）：
- DMA buffer 满 256 字节触发一次 TC 中断（`need_buf_switch`）
- UART IDLE 后触发 IDLE ISR 报告剩余字节
- 典型模式：`[256 bytes] + [N bytes IDLE]` 组成一个完整消息片段

---

## 六、AT 命令速查表

| 命令 | 方向 | 功能 |
|---|---|---|
| `AT+MLPMCFG="sleepmode"` | Q | 查询睡眠模式 |
| `AT+MLPMCFG="sleepmode",0,1` | W | 永久禁用深度睡眠 |
| `AT+CFUN=1,1` | W | 软重启（配合 MLPMCFG） |
| `AT+MIPCALL?` | Q | 查询 PDP 上下文状态 |
| `AT+MIPCALL=1` | W | 激活 PDP |
| `AT+MSSLCFG="auth",<cid>,<mode>` | W | SSL 认证模式（0=不验证） |
| `AT+MIPCFG="ssl",<cid>,1,0` | W | socket 启用 SSL |
| `AT+MIPCFG="encoding",<cid>,1,1` | W | 收发均使用 HEX 编码 |
| `AT+MIPSTATE=<cid>` | Q | 查询 socket 状态 |
| `AT+MIPOPEN=<cid>,"TCP",<host>,<port>,,0` | W | 建立 TCP(SSL) 连接 |
| `AT+MIPSEND=<cid>,<len>,<hex>` | W | 发送 HEX 编码数据 |
| `AT+MIPCLOSE=<cid>` | W | 关闭 socket |
| `+MIPOPEN: <cid>,<err>` | URC | TCP 连接结果（0=成功） |
| `+MIPURC: "recv",<cid>,<len>` + HEX | URC | 接收到数据 |
| `+MIPURC: "closed",<cid>` | URC | 连接被远端关闭 |
| `+MIPSEND: <cid>` | RSP | 数据发送成功 |

---

## 七、数据流总结

```
MCU                              ML307                         Server
 |                                  |                             |
 |--- AT+MIPOPEN TCP TLS -------->  |                             |
 |<-- OK + +MIPOPEN URC ----------  |-- TLS Handshake ---------->|
 |                                  |<-- TLS OK ------------------|
 |--- AT+MIPSEND HTTP Upgrade -->   |                             |
 |<-- +MIPSEND OK ---------------   |--- HTTP GET Upgrade ------->|
 |<-- +MIPURC recv (101) --------   |<-- 101 Switching Proto -----|
 |                                  |                             |
 |--- AT+MIPSEND WS hello ------->  |--- WS hello --------------->|
 |<-- +MIPURC recv (hello) ------   |<-- WS hello (session_id) ---|
 |                                  |                             |
 |--- AT+MIPSEND listen start --->  |--- WS listen start -------->|
 |--- AT+MIPSEND opus[0] -------->  |--- WS binary opus[0] ------>|
 |--- AT+MIPSEND opus[1..N] ----->  |--- WS binary opus[1..N] --->|
 |--- AT+MIPSEND listen stop ---->  |--- WS listen stop --------->|
 |                                  |                             |
 |<-- +MIPURC recv (tts start) --   |<-- WS tts/stt/llm/audio ----|
 |<-- +MIPURC recv (stt text) ---   |                             |
 |<-- +MIPURC recv (tts text) ---   |                             |
 |<-- +MIPURC recv (audio[0]) ---   |<-- WS binary opus[] --------|
 |<-- +MIPURC recv (audio[1..]) -   |                             |
```

---

## 八、websocket_test 对照分析（待填充）

> 等待 websocket_test 运行日志后补充。
> 
> 预期关注点：
> 1. 是否正确执行了 Step 13a–c（ssl+encoding 配置）？
> 2. WebSocket upgrade 请求格式是否正确（Host、token、key）？
> 3. Hello 消息的 audio_params 是否匹配？
> 4. +MIPURC 数据接收和解析逻辑是否正确处理分片 HEX 数据？
> 5. 大数据量发送时（HEX > 256 字节）是否命中了 DMA TX 256 字节分块限制？
