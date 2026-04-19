# ML307C AT 指令序列：从波特率检测到收到小智服务器 Opus 数据

**源文件**：`niu/samples/drivers/modem/uart_raw_test/src/main.c`

---

## Step 0：波特率检测

停止 DMA RX，切换为 poll 模式逐一探测。候选波特率顺序：`115200 / 921600 / 460800 / 230400 / 57600 / 38400 / 19200 / 9600`。

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 0.1 | `AT` | → | poll 模式发送，20ms 超时，扫到返回 `OK` 为止（最多 10 轮） |
| 0.2 | `AT+IPR=921600` | → | 若检测波特率 ≠ 921600，切换模组到高速率 |
| 0.3 | `AT` | → | 验证 921600 下通信正常 |

切换成功后重启 DMA 异步 RX。

---

## Step 1：关闭深度睡眠

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 1.1 | `AT+MLPMCFG="sleepmode"` | → | 查询当前睡眠模式 |
| 1.2 | `AT+MLPMCFG="sleepmode",0,1` | → | 禁止睡眠并写入 NV（permanent=1），已为 0 则跳过 |
| 1.3 | `AT+CFUN=1,1` | → | 软复位使 NV 设置生效（仅在 1.2 执行后才发） |
| — | `+MATREADY` | ← URC | 等待模组重新就绪（超时 30 s） |

---

## Step 2：关闭回显

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 2.1 | `ATE0` | → | 关闭命令回显 |
| — | `OK` | ← | — |

---

## Step 3：固件版本

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 3.1 | `AT+CGMR` | → | 查询固件版本，结果存入 `g_fw_revision` |
| — | `OK` | ← | — |

---

## Step 4：SIM 卡状态

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 4.1 | `AT+CPIN?` | → | 查询 SIM 状态，最多重试 10 次（每次间隔 500 ms） |
| — | `+CPIN: READY` / `OK` | ← | READY 即停止重试 |

---

## Step 5：开启网络注册 URC

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 5.1 | `AT+CEREG=2` | → | 开启扩展网络注册 URC（含位置信息） |
| — | `OK` | ← | — |

---

## Step 6：等待网络注册

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 6a | `AT+CEREG?` | → | 一次性初始查询（已注册则直接通过） |
| — | `+CEREG: 2,1,...` / `+CEREG: 2,5,...` | ← | stat=1 归属地注册，stat=5 漫游注册 |
| 6b | *(被动等待)* | ← URC | 持续积累字节，检测 `+CEREG: 1` 或 `+CEREG: 5` |
| 6b fallback | `AT+CEREG?` | → | 每 10 s 主动补充查询一次（超时总计 60 s） |

---

## Step 7：信号质量

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 7.1 | `AT+CSQ` | → | 查询信号强度，RSSI 存入 `g_csq` |
| — | `+CSQ: <rssi>,<ber>` / `OK` | ← | — |

---

## Step 8：PDP 上下文状态

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 8.1 | `AT+MIPCALL?` | → | 指数退避查询（10→1000 ms，最多 10 次） |
| — | `+MIPCALL: 1,1,...` / `OK` | ← | 含 `,1,` 表示 PDP 已激活 |

若 PDP 未激活，追加以下指令：

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 8b | `AT+CGDCONT=1,"IPV4V6","cmnet"` | → | 配置 APN |
| 8c | `AT+MIPCALL=1,1` | → | 激活 PDP，等待 `+MIPCALL:` URC（超时 35 s） |
| 8d | `AT` | → | 等待模组响应（最多轮询 30 次，每次间隔 1 s） |

---

## Step 9：IMEI

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 9.1 | `AT+CGSN=1` | → | 查询 IMEI，结果存入 `g_imei` |
| — | `+CGSN: <imei>` / `OK` | ← | — |

---

## Step 10：ICCID

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 10.1 | `AT+ICCID` | → | 查询 SIM 卡 ICCID，结果存入 `g_iccid` |
| — | `+ICCID: <iccid>` / `OK` | ← | — |

---

## Step 11：运营商 / 模块信息

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 11.1 | `AT+COPS?` | → | 查询运营商名称，结果存入 `g_carrier` |
| 11.2 | `AT+CGMI` | → | 查询制造商 |
| 12.1 | `AT+CGMM` | → | 查询模块型号 |

---

## Step 13：HTTPS 连接小智 OTA 服务器

### 13a–13c：SSL + 编码配置（一次性，断电前不需重复）

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 13a | `AT+MSSLCFG="auth",0,0` | → | SSL 上下文 0，不验证服务器证书 |
| 13b | `AT+MIPCFG="ssl",0,1,0` | → | 套接字 0 启用 TLS，使用 SSL 上下文 0 |
| 13c | `AT+MIPCFG="encoding",0,1,1` | → | 收发均使用 HEX 编码（防止二进制数据污染 AT 解析器） |

### 13d：检查套接字状态

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 13d.1 | `AT+MIPSTATE=0` | → | 查询套接字 0 状态 |
| 13d.2 | `AT+MIPCLOSE=0` | → | 若状态为 CONNECTED 则先关闭 |
| — | `+MIPCLOSE: 0` | ← URC | 等待关闭确认 |

### 13e：建立 TLS 连接

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 13e.1 | `AT+MIPOPEN=0,"TCP","api.tenclass.net",443,,0` | → | 发起 TLS TCP 连接（非透传模式） |
| — | `OK` | ← | 立即返回，DNS + TLS 握手异步进行 |
| — | `+IPOPEN: 0,0` 或 `+MIPOPEN: 0,0` | ← URC | TLS 连接成功（超时 60 s） |

### 13f：发送 HTTP POST（OTA 版本查询）

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 13f.1 | `AT+MIPSEND=0,<len>,<HEX>` | → | HEX 编码的 HTTP POST 请求：`POST /xiaozhi/ota/ HTTP/1.1` |
| — | `+MIPSEND: 0,<len>` + `OK` | ← | 发送确认（超时 15 s） |

OTA 请求头包含：`Device-Id`、`Client-Id`、`User-Agent`、`Accept-Language`、`Activation-Version: 1`。  
Body JSON 包含：固件版本、IMEI、ICCID、运营商、信号强度等信息。

### 13g：接收服务器响应

| # | 事件 | 方向 | 说明 |
|---|------|------|------|
| — | `+MIPURC: "rtcp",0,<len>,<HEX>` | ← URC | 每条 URC 携带一个 TCP 分段，HEX 解码后拼接 HTTP 响应 |
| — | `+MIPURC: "disconn"` 或 `+MIPCLOSE: 0` | ← URC | 服务器关闭连接，响应接收完毕 |

### 13h：关闭连接

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 13h.1 | `AT+MIPCLOSE=0` | → | 关闭套接字 0 |
| — | `+MIPCLOSE: 0` | ← URC | 关闭确认 |

---

## Step 14（可选循环）：设备激活

若 OTA 响应中含有激活码（`code` 字段），需要轮询 `/xiaozhi/ota/activate` 接口，每次激活的 AT 流程如下（与 Step 13d–13h 相同，URL 和 Body 不同）：

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| — | `AT+MIPSTATE=0` | → | 检查套接字状态 |
| — | `AT+MIPCLOSE=0`（如需） | → | 预关闭 |
| — | `AT+MIPOPEN=0,"TCP","api.tenclass.net",443,,0` | → | 建立 TLS 连接 |
| — | `+IPOPEN: 0,0` / `+MIPOPEN: 0,0` | ← URC | 连接成功 |
| — | `AT+MIPSEND=0,2,<HEX("{}") >` | → | POST /xiaozhi/ota/activate，Body 为 `{}` |
| — | `+MIPURC: "rtcp"` | ← URC | 接收 HTTP 响应（200=已激活，202=等待用户绑定） |
| — | `AT+MIPCLOSE=0` | → | 关闭连接 |

激活成功后重新执行 Step 13（OTA 版本查询），此时响应中将携带 WebSocket URL 和 token。

---

## Step 15：建立 WebSocket TLS 连接

### 15a：预关闭套接字

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 15a.1 | `AT+MIPSTATE=0` | → | 查询套接字状态 |
| 15a.2 | `AT+MIPCLOSE=0`（如需） | → | 预关闭 |

### 15b：连接 WebSocket 服务器

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 15b.1 | `AT+MIPOPEN=0,"TCP","<ws_host>",443,,0` | → | 连接 OTA 响应中返回的 WebSocket 服务器 host:443 |
| — | `OK` | ← | — |
| — | `+IPOPEN: 0,0` 或 `+MIPOPEN: 0,0` | ← URC | TLS 连接成功（超时 60 s） |

---

## Step 16：WebSocket HTTP 升级握手

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 16.1 | `AT+MIPSEND=0,<len>,<HEX>` | → | HEX 编码的 WebSocket 升级请求 |
| — | `+MIPSEND: 0,<len>` + `OK` | ← | 发送确认 |
| — | `+MIPURC: "rtcp",0,<len>,<HEX>` | ← URC | HEX 解码后包含 `HTTP/1.1 101 Switching Protocols` |

升级请求额外头：`Upgrade: websocket`、`Sec-WebSocket-Version: 13`、`Protocol-Version: 3`、`Device-Id`、`Client-Id`、`Authorization: <token>`。

---

## Step 17：发送 hello 消息

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 17.1 | `AT+MIPSEND=0,<len>,<HEX>` | → | HEX 编码的 RFC 6455 masked WebSocket 文本帧，载荷为 hello JSON |
| — | `+MIPSEND: 0,<len>` + `OK` | ← | 发送确认 |

Hello JSON：
```json
{"type":"hello","version":1,"features":{"mcp":true},
 "transport":"websocket",
 "audio_params":{"format":"opus","sample_rate":16000,"channels":1,"frame_duration":60}}
```

---

## Step 18：接收服务器 hello 响应

| # | 事件 | 方向 | 说明 |
|---|------|------|------|
| — | `+MIPURC: "rtcp",0,<len>,<HEX>` | ← URC | HEX 解码 → WebSocket 文本帧 → JSON `{"type":"hello","session_id":"...","audio_params":{...}}` |

---

## Step 19：发送 listen start

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 19.1 | `AT+MIPSEND=0,<len>,<HEX>` | → | masked WebSocket 文本帧，JSON 内容：`{"session_id":"...","type":"listen","state":"start","mode":"manual"}` |
| — | `OK` | ← | — |

---

## Step 20：发送 Opus 音频帧

每帧音频封装格式：`BinaryProtocol3 头（4 字节）+ Opus 裸数据`，再封装为 masked WebSocket 二进制帧（opcode=0x02），最后通过 `AT+MIPSEND` HEX 编码发送。

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 20.x | `AT+MIPSEND=0,<len>,<HEX>` | → | 每帧重复一次，帧间间隔 20 ms |
| — | `+MIPSEND: 0,<len>` + `OK` | ← | 每帧发送确认 |

BinaryProtocol3 头格式：`[0x00][0x00][opus_size_hi][opus_size_lo]`

---

## Step 21：发送 listen stop

| # | 指令 | 方向 | 说明 |
|---|------|------|------|
| 21.1 | `AT+MIPSEND=0,<len>,<HEX>` | → | masked WebSocket 文本帧，JSON：`{"session_id":"...","type":"listen","state":"stop"}` |
| — | `OK` | ← | — |

---

## Step 22：接收小智服务器 Opus 回复

| # | 事件 | 方向 | 说明 |
|---|------|------|------|
| — | `+MIPURC: "rtcp",0,<len>,<HEX>` | ← URC | 持续接收，HEX 解码后拼入 WebSocket 流缓冲区 |
| — | WebSocket 文本帧（opcode=0x01） | ← | JSON 类型：`stt`（识别结果）/ `tts`（合成状态）/ `llm`（情绪） |
| — | WebSocket 二进制帧（opcode=0x02） | ← **Opus 数据** | BinaryProtocol3 头 + Opus 数据，`payload[2:4]` 为 BE opus 长度 |
| — | WebSocket Close 帧（opcode=0x08） | ← | 服务器关闭 |
| — | `+MIPCLOSE: 0` | ← URC | 连接断开 |

收到二进制帧后，提取 `payload[4:]` 即为 Opus 编码音频，调用 `opus_decode()` 解码为 PCM，送入扬声器播放队列。收到 `tts state=stop` 或连接断开时结束接收循环（超时 30 s）。

---

## AT 指令汇总

| 指令 | 用途 |
|------|------|
| `AT` | 波特率探测 / 模组活性检测 |
| `AT+IPR=921600` | 设置模组串口波特率 |
| `AT+MLPMCFG="sleepmode"` | 查询睡眠模式 |
| `AT+MLPMCFG="sleepmode",0,1` | 永久禁止深度睡眠 |
| `AT+CFUN=1,1` | 软复位 |
| `ATE0` | 关闭回显 |
| `AT+CGMR` | 固件版本 |
| `AT+CPIN?` | SIM 卡状态 |
| `AT+CEREG=2` | 开启扩展网络注册 URC |
| `AT+CEREG?` | 查询注册状态 |
| `AT+CSQ` | 信号强度 |
| `AT+MIPCALL?` | 查询 PDP 激活状态 |
| `AT+CGDCONT=1,"IPV4V6","cmnet"` | 配置 APN |
| `AT+MIPCALL=1,1` | 激活 PDP 上下文 |
| `AT+CGSN=1` | 查询 IMEI |
| `AT+ICCID` | 查询 ICCID |
| `AT+COPS?` | 查询运营商 |
| `AT+CGMI` | 查询制造商 |
| `AT+CGMM` | 查询模块型号 |
| `AT+MSSLCFG="auth",0,0` | 配置 SSL 上下文（不验证证书） |
| `AT+MIPCFG="ssl",0,1,0` | 套接字 0 启用 TLS |
| `AT+MIPCFG="encoding",0,1,1` | 收发 HEX 编码 |
| `AT+MIPSTATE=0` | 查询套接字 0 状态 |
| `AT+MIPOPEN=0,"TCP",<host>,<port>,,0` | 建立 TCP/TLS 连接 |
| `AT+MIPSEND=0,<len>,<HEX>` | 发送 HEX 编码数据 |
| `AT+MIPCLOSE=0` | 关闭连接 |

**关键 URC**：

| URC | 含义 |
|-----|------|
| `+MATREADY` | 模组 AT 初始化完成 |
| `+CEREG: 1` / `+CEREG: 5` | 网络注册成功（归属地/漫游） |
| `+IPOPEN: 0,0` / `+MIPOPEN: 0,0` | TCP/TLS 连接成功 |
| `+MIPSEND: 0,<len>` | 数据发送确认 |
| `+MIPURC: "rtcp",0,<len>,<HEX>` | 收到 TCP 数据（HEX 编码） |
| `+MIPURC: "disconn"` / `+MIPURC: "closed"` | 远端关闭连接 |
| `+MIPCLOSE: 0` | 套接字关闭完成 |
