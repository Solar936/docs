# ML307 AT 命令完整交互时序

## 概述

本文档记录 xiaozhi-esp32（ESP32S3 + ML307 4G 模组）从开机到用户说"你好小智"、服务器返回响应的完整 AT 命令交互序列。

**数据来源**：`decoder--260411-205244.csv`，从第 3690 行起，共 186 条 AT 消息。

**重要说明**：
- 捕获为 **TX 方向**（ESP32→ML307），模组响应（URCs）不可见
- ML307 返回的 URC（如 `+CEREG`、`+MIPCALL`、`+MIPOPEN`、`+MIPURC`、`+MHTTPURC` 等）由代码中 URC 回调处理
- 时间戳为相对于捕获起点的毫秒数

---

## 阶段一：模组检测与初始化

**代码路径**：`at_modem.cc → AtModem::Detect()` → `ml307_at_modem.cc → Ml307AtModem()` 构造函数

```
AtModem::Detect():
  → SendCommand("AT+CGMR")           // 获取模组版本，检测是否包含 "ML307"
  → new Ml307AtModem(...)            // 创建 ML307 实例
    → Ml307AtModem::ResetConnections():
      → SendCommand("AT+MHTTPDEL=0/1/2/3")  // 清理残留 HTTP 会话
```

| # | 时间(ms) | AT 命令 | 说明 | 期待响应 |
|---|---------|---------|------|---------|
| 001 | 104669.2 | `AT` | AtUart 初始化唤醒字节 | `OK` |
| 002 | 104682.8 | `AT+CGMR` | `AtModem::Detect()` 获取版本，响应含"ML307"则创建 `Ml307AtModem` | 版本字符串（含"ML307"） |
| 003 | 104684.7 | `AT+MHTTPDEL=0` | `Ml307AtModem::ResetConnections()` 清理 HTTP 实例 0 | `OK` |
| 004 | 104685.8 | `AT+MHTTPDEL=1` | 清理 HTTP 实例 1 | `OK` |
| 005 | 104686.9 | `AT+MHTTPDEL=2` | 清理 HTTP 实例 2 | `OK` |
| 006 | 104688.1 | `AT+MHTTPDEL=3` | 清理 HTTP 实例 3 | `OK` |

---

## 阶段二：SIM 卡与网络注册

**代码路径**：`at_modem.cc → AtModem::WaitForNetworkReady()`

```cpp
// at_modem.cc: AtModem::WaitForNetworkReady()
SendCommand("AT+CPIN?");         // 检查 SIM 卡 → 等待 "+CPIN: READY"
SendCommand("AT+CEREG=2");       // 开启网络注册 URC（含 LAC/CellID）
SendCommand("AT+CEREG?");        // 查询当前注册状态 → 等待 "+CEREG: 2,1,..." URC
```

| # | 时间(ms) | AT 命令 | 说明 | 期待响应 |
|---|---------|---------|------|---------|
| 007 | 104696.2 | `AT+CPIN?` | 检查 SIM 卡就绪状态 | `+CPIN: READY` |
| 008 | 104698.8 | `AT+CEREG=2` | 启用带位置信息的注册状态变化 URC | `OK` |
| 009 | 104700.3 | `AT+CEREG?` | 查询当前注册状态，等待 stat=1（已注册本网） | `+CEREG: 2,1,...` |

---

## 阶段三：获取 IP 地址

**代码路径**：`ml307_at_modem.cc → Ml307AtModem::WaitForNetworkReady()`

```cpp
// ml307_at_modem.cc: Ml307AtModem::WaitForNetworkReady()
// 在基类 WaitForNetworkReady() 成功后，轮询 IP 地址（最多 10 次）
for (int i = 0; i < 10; i++) {
    SendCommand("AT+MIPCALL?");  // → 等待 "+MIPCALL: 0,1,<IP>" 表示已获取 IP
    if (已获取IP) break;
    vTaskDelay(delay_ms);
    delay_ms = std::min(delay_ms * 2, 1000);
}
```

| # | 时间(ms) | AT 命令 | 说明 | 期待响应 |
|---|---------|---------|------|---------|
| 010 | 104718.3 | `AT+MIPCALL?` | 轮询获取 IP 地址（第 1 次，本次即成功） | `+MIPCALL: 0,1,<IP地址>` |

---

## 阶段四：设备信息采集

**代码路径**：`ota.cc → Ota::CheckVersion()` 调用 `Board::GetSystemInfoJson()`，该函数调用 `at_modem.cc` 中各 `Get*()` 方法

```cpp
// at_modem.cc: 各 Get*() 方法均带缓存，首次调用才发 AT 命令
GetModuleRevision()  → SendCommand("AT+CGMR")     // 模组版本
GetCsq()             → SendCommand("AT+CSQ")       // 信号强度
GetImei()            → SendCommand("AT+CGSN=1")   // IMEI 号
GetIccid()           → SendCommand("AT+ICCID")    // SIM 卡 ICCID
GetCarrierName()     → SendCommand("AT+COPS?")    // 运营商名称
GetRegistrationState()→SendCommand("AT+CEREG?")   // 注册状态（用于 OTA JSON）
```

| # | 时间(ms) | AT 命令 | 说明 | 期待响应 |
|---|---------|---------|------|---------|
| 011 | 104733.2 | `AT+CGMR` | `GetModuleRevision()` → 缓存至 `module_revision_` | 版本字符串 |
| 012 | 104734.6 | `AT+CSQ` | `GetCsq()` → 信号强度 | `+CSQ: <rssi>,<ber>` |
| 013 | 104736.1 | `AT+CGSN=1` | `GetImei()` → IMEI 号 | IMEI 字符串 |
| 014 | 104741.5 | `AT+ICCID` | `GetIccid()` → SIM ICCID | ICCID 字符串 |
| 015 | 104753.7 | `AT+CGMR` | 第二次调用 `GetModuleRevision()`（另一调用方） | 版本字符串（已缓存） |
| 016 | 104755.2 | `AT+COPS?` | `GetCarrierName()` → 运营商名称 | `+COPS: 0,0,"<运营商>"` |
| 017 | 104757.1 | `AT+CSQ` | 第二次信号强度查询 | `+CSQ: <rssi>,<ber>` |
| 018 | 104758.9 | `AT+ICCID` | 第二次 ICCID 查询 | ICCID 字符串 |
| 019 | 104853.1 | `AT+CEREG?` | `GetRegistrationState()` → 获取注册状态写入 OTA JSON 体 | `+CEREG: 2,1,...` |

---

## 阶段五：HTTP OTA 版本检查

**代码路径**：`ota.cc → Ota::CheckVersion()` → `Ota::SetupHttp()` → `ml307_http.cc → Ml307Http::Open()`

```cpp
// ota.cc: Ota::SetupHttp() 设置 HTTP 请求头
http_->SetHeader("Activation-Version", "1");
http_->SetHeader("Client-Id", uuid);
http_->SetHeader("Content-Type", "application/json");
http_->SetHeader("Device-Id", mac_address);
http_->SetHeader("User-Agent", "bread-compact-ml307/2.2.5");
http_->SetHeader("Accept-Language", "zh-CN");

// ota.cc: Ota::CheckVersion() 调用
http_->Open("POST", "https://api.tenclass.net/xiaozhi/ota/");

// ml307_http.cc: Ml307Http::Open() 内部流程
SendCommand("AT+MHTTPCREATE=\"<base_url>\"")        // 创建 HTTP 实例
SendCommand("AT+MHTTPCFG=\"ssl\",0,1,0")            // SSL on (HTTPS)
SendCommand("AT+MHTTPCFG=\"encoding\",0,0,0")       // HEX 编码 OFF（设置 header 阶段）
for each header:
    SendCommand("AT+MHTTPHEADER=0,<1=more/0=last>,<len>,\"<key>: <value>\"")
SendCommandWithData("AT+MHTTPCONTENT=0,0,<len>", body_bytes)  // POST 体
SendCommand("AT+MHTTPCFG=\"encoding\",0,1,1")       // HEX 编码 ON
SendCommand("AT+MHTTPREQUEST=0,2,0,<HEX(path)>")    // method=2(POST)
```

| # | 时间(ms) | AT 命令 | 说明 | 期待响应 |
|---|---------|---------|------|---------|
| 020 | 104855.2 | `AT+MHTTPCREATE="https://api.tenclass.net"` | 创建 HTTP 实例，获取实例 ID | `+MHTTPCREATE: 0` |
| 021 | 104857.8 | `AT+MHTTPCFG="ssl",0,1,0` | 启用 SSL/TLS | `OK` |
| 022 | 104859.4 | `AT+MHTTPCFG="encoding",0,0,0` | 关闭 HEX 编码（Header 明文传输） | `OK` |
| 023 | 104860.7 | `AT+MHTTPHEADER=0,1,22,"Accept-Language: zh-CN"` | 设置请求头（还有更多） | `OK` |
| 024 | 104862.6 | `AT+MHTTPHEADER=0,1,21,"Activation-Version: 1"` | 设置请求头 | `OK` |
| 025 | 104864.1 | `AT+MHTTPHEADER=0,1,47,"Client-Id: 03b1bf69-4509-42c8-890a-c25dc6bb7832"` | 设置请求头 | `OK` |
| 026 | 104865.9 | `AT+MHTTPHEADER=0,1,30,"Content-Type: application/json"` | 设置请求头 | `OK` |
| 027 | 104867.5 | `AT+MHTTPHEADER=0,1,28,"Device-Id: d0:cf:13:15:94:94"` | 设置请求头 | `OK` |
| 028 | 104869.1 | `AT+MHTTPHEADER=0,0,37,"User-Agent: bread-compact-ml307/2.2.5"` | 设置最后一个请求头（0=last） | `OK` |
| 029 | 104871.2 | `AT+MHTTPCONTENT=0,0,1232` + 紧接 1232 字节 JSON 体 | 设置 POST 体（设备信息 JSON，含芯片型号/版本/MAC/UUID/分区表等） | `OK` |
| 030 | ~104872 | `AT+MHTTPCFG="encoding",0,1,1` | 开启 HEX 编码（发送请求用） | `OK` |
| 031 | 104901.8 | `AT+MHTTPREQUEST=0,2,0,2F7869616F7A68692F6F74612F` | 发起 POST 请求（`/xiaozhi/ota/` 的 HEX 编码） | 等待 `+MHTTPURC: "header",0,200,...` |
| 032 | 105579.6 | `AT+CSQ` | 等待 HTTP 响应期间的周期信号查询（678ms 后） | `+CSQ: <rssi>,<ber>` |
|  | ~106000 | *(等待 HTTP 响应体)* | 等待 `+MHTTPURC: "content",0,...` 携带 OTA 检查结果 | `+MHTTPURC: "content",0,...` |
| 033 | 106931.4 | `AT+MHTTPDEL=0` | `Ml307Http::Close()` 释放 HTTP 实例 | `OK` |

> **等待 ~5.4 秒**：OTA 应答处理（解析版本信息、状态机转换）

---

## 阶段六：WebSocket TCP/SSL 连接

**代码路径**：`ml307_tcp.cc → Ml307Tcp::Connect()` + `ml307_ssl.cc → Ml307Ssl::ConfigureSsl()`

```cpp
// ml307_tcp.cc: Ml307Tcp::Connect()
SendCommand("AT+MIPSTATE=<id>")              // 查询已有连接状态
ssl_->ConfigureSsl();                        // → ssl.cc
SendCommand("AT+MIPCFG=\"encoding\",1,1,1") // HEX 编码 ON
SendCommand("AT+MIPOPEN=1,\"TCP\",\"<host>\",<port>,,0")  // 建立 TCP 连接

// ml307_ssl.cc: Ml307Ssl::ConfigureSsl()
SendCommand("AT+MSSLCFG=\"auth\",0,0")      // SSL 认证模式（不验证服务器证书）
SendCommand("AT+MIPCFG=\"ssl\",1,1,0")      // 为 TCP 实例启用 SSL
```

| # | 时间(ms) | AT 命令 | 说明 | 期待响应 |
|---|---------|---------|------|---------|
| 034 | 112340.1 | `AT+MIPSTATE=1` | 查询 TCP 实例 1 的当前状态 | `+MIPSTATE: 1,0` (未连接) |
| 035 | 112342.3 | `AT+MSSLCFG="auth",0,0` | 配置 SSL：不验证服务器证书 | `OK` |
| 036 | 112343.8 | `AT+MIPCFG="ssl",1,1,0` | 为 TCP 实例 1 启用 SSL，不验证主机名 | `OK` |
| 037 | 112345.3 | `AT+MIPCFG="encoding",1,1,1` | 启用 HEX 编码（收发均 HEX） | `OK` |
| 038 | 112346.7 | `AT+MIPOPEN=1,"TCP","api.tenclass.net",443,,0` | 发起 TCP 连接（含 SSL 握手） | 等待 `+MIPOPEN: 1,0` |

> **等待 ~490ms**：TCP 三次握手 + TLS 握手

---

## 阶段七：WebSocket HTTP 升级握手

**代码路径**：`web_socket.cc → WebSocket::Connect()` → `tcp_->Send(http_request)`

```cpp
// web_socket.cc: WebSocket::Connect()
// 解析 URI，设置请求头，构造 HTTP Upgrade 请求，调用 tcp_->Send()
// tcp_->Send() 在 ml307_tcp.cc 中分块发送（≤730字节/块）：
//   AT+MIPSEND=<id>,<len>,<HEX编码数据>
// 等待服务器返回 "101 Switching Protocols" 的 URC
```

| # | 时间(ms) | AT 命令 | 说明 | 期待响应 |
|---|---------|---------|------|---------|
| 039 | 112836.3 | `AT+MIPSEND=1,300,<hex>` | 发送 HTTP Upgrade 请求（300 字节，HEX 编码） | `+MIPSEND: 1,0` + 等待 `+MIPURC: "rtcp"...` |

**解码后的 HTTP 请求（300 字节）**：
```
GET /xiaozhi/v1/ HTTP/1.1
Host: api.tenclass.net
Authorization: Bearer test-token
Client-Id: 03b1bf69-4509-42c8-890a-c25dc6bb7832
Connection: Upgrade
Device-Id: d0:cf:13:15:94:94
Protocol-Version: 1
Sec-WebSocket-Key: Lc9GKQS0eNhop/8/K/H82Q==
Sec-WebSocket-Version: 13
Upgrade: websocket
```

> **等待 ~537ms**：服务器返回 `HTTP/1.1 101 Switching Protocols`，通过 `+MIPURC: "rtcp"` URC 触发 `HANDSHAKE_SUCCESS_BIT`

---

## 阶段八：WebSocket Hello 握手

**代码路径**：`websocket_protocol.cc → WebsocketProtocol::OpenAudioChannel()` → `SendText(GetHelloMessage())`

```cpp
// websocket_protocol.cc: GetHelloMessage()
cJSON_AddStringToObject(root, "type", "hello");
cJSON_AddNumberToObject(root, "version", version_);   // version=1
cJSON_AddItemToObject(root, "features", features);    // {"mcp": true}
cJSON_AddStringToObject(root, "transport", "websocket");
// audio_params: format=opus, sample_rate=16000, channels=1, frame_duration=60

// web_socket.cc: WebSocket::Send(text)
// 构造 WS 文本帧（opcode=0x01，带 4 字节掩码）
// → tcp_->Send(ws_frame)
// → AT+MIPSEND=1,<len>,<HEX>
```

| # | 时间(ms) | AT 命令 | 说明 | 期待响应 |
|---|---------|---------|------|---------|
| 040 | 113373.7 | `AT+MIPSEND=1,170,<hex>` | 发送客户端 Hello（WS 文本帧，162 字节 payload） | `+MIPSEND: 1,0` + 等待服务器 Hello URC |

**解码后的 WebSocket 文本帧 payload（162 字节）**：
```json
{"type":"hello","version":1,"features":{"mcp":true},"transport":"websocket","audio_params":{"format":"opus","sample_rate":16000,"channels":1,"frame_duration":60}}
```

**服务器响应（RX，通过 URC 接收）**：
```
+MIPURC: "rtcp",1,<len>,<hex>
```
解码后为服务器 Hello JSON（示例）：
```json
{"type":"hello","session_id":"f97c3401","transport":"websocket","audio_params":{"sample_rate":24000,"frame_duration":60}}
```

```cpp
// websocket_protocol.cc: ParseServerHello()
session_id_ = json["session_id"];                 // → "f97c3401"
server_sample_rate_ = json["audio_params"]["sample_rate"];
server_frame_duration_ = json["audio_params"]["frame_duration"];
// 设置 WEBSOCKET_PROTOCOL_SERVER_HELLO_EVENT，解除阻塞
```

---

## 阶段九：发送唤醒词前缓冲音频

**代码路径**：`websocket_protocol.cc → WebsocketProtocol::SendAudio()` → `websocket_->Send(data, len, binary=true)`

```cpp
// websocket_protocol.cc: SendAudio() （version=1 路径）
// 直接将 Opus 编码帧作为 WS 二进制帧发送（无额外协议头）

// web_socket.cc: WebSocket::Send(binary=true)
// 构造 WS 二进制帧（opcode=0x02，带 4 字节掩码）
// → tcp_->Send(ws_frame)
// → AT+MIPSEND=1,<len>,<HEX>
```

| # | 时间范围(ms) | AT 命令 | 说明 |
|---|------------|---------|------|
| 041–075 | 113510 – 113723 | `AT+MIPSEND=1,1xx,<hex>` × **35帧** | 发送唤醒词前缓冲音频（WS 二进制帧，Opus 编码） |

> **35 帧 × 60ms/帧 = 2100ms** 的音频，包含"你好小智"唤醒词

---

## 阶段十：唤醒词检测上报 + 启动监听

**代码路径**：`protocol.cc → Protocol::SendWakeWordDetected()` + `Protocol::SendStartListening()`

```cpp
// protocol.cc: SendWakeWordDetected()
json["type"] = "listen";
json["state"] = "detect";
json["text"] = wake_word;    // "你好小智"
json["session_id"] = session_id_;

// protocol.cc: SendStartListening(kListeningModeAutoStop)
json["type"] = "listen";
json["state"] = "start";
json["mode"] = "auto";       // kListeningModeAutoStop → "auto"
json["session_id"] = session_id_;
```

| # | 时间(ms) | AT 命令 | 说明 |
|---|---------|---------|------|
| 076 | 113723.0 | `AT+MIPSEND=1,86,<hex>` | 唤醒词检测上报（WS 文本帧） |
| 077 | 113750.2 | `AT+MIPSEND=1,77,<hex>` | 启动自动停止监听模式（WS 文本帧） |
| 078 | 113889.9 | `AT+MIPSEND=1,151,<hex>` | 继续发送音频（WS 二进制帧） |

**[076] 解码 payload（78 字节）**：
```json
{"session_id":"f97c3401","type":"listen","state":"detect","text":"你好小智"}
```

**[077] 解码 payload（69 字节）**：
```json
{"session_id":"f97c3401","type":"listen","state":"start","mode":"auto"}
```

> **等待 ~2164ms**：服务器进行语音识别，处理中

---

## 阶段十一：唤醒词二次检测中止 + 重启监听

**代码路径**：`protocol.cc → Protocol::SendAbortSpeaking()` + `Protocol::SendStartListening()`

> 在 auto 监听模式中，设备仍在持续收音，缓冲区中的"你好小智"音频再次触发唤醒词检测，设备中止当前会话并重新开始监听。

```cpp
// protocol.cc: SendAbortSpeaking(kAbortReasonWakeWordDetected)
json["type"] = "abort";
json["reason"] = "wake_word_detected";
json["session_id"] = session_id_;
```

| # | 时间(ms) | AT 命令 | 说明 |
|---|---------|---------|------|
| 079 | 116053.6 | `AT+MIPSEND=1,76,<hex>` | 中止当前响应（WS 文本帧） |
| 080 | 116112.5 | `AT+MIPSEND=1,77,<hex>` | 重新启动自动停止监听（WS 文本帧） |

**[079] 解码 payload（68 字节）**：
```json
{"session_id":"f97c3401","type":"abort","reason":"wake_word_detected"}
```

**[080] 解码 payload（69 字节）**：
```json
{"session_id":"f97c3401","type":"listen","state":"start","mode":"auto"}
```

---

## 阶段十二：持续发送语音指令音频

**代码路径**：`websocket_protocol.cc → WebsocketProtocol::SendAudio()`（同阶段九）

| # | 时间范围(ms) | AT 命令 | 说明 |
|---|------------|---------|------|
| 081–086 | 116260 – 116559 | `AT+MIPSEND=1,1xx,<hex>` × **6帧** | WS 二进制音频帧 |
| 087 | 116579.6 | `AT+CSQ` | 周期性信号强度查询 | 
| 088–165 | 116582 – ~132000 | `AT+MIPSEND=1,xxx,<hex>` × **78帧** | 持续发送用户语音（WS 二进制帧） |

> **服务器处理（RX 方向，通过 URC 接收，代码不可见）**：
> 1. 服务器识别语音 → STT 结果
> 2. LLM 推理生成回复文本
> 3. TTS 合成语音，分帧发送 WebSocket 二进制帧（Opus 编码）
> 4. 模组通过 `+MIPURC: "rtcp",1,<len>,<hex>` URC 上报
> 5. `web_socket.cc` 在 `OnTcpData` 回调中解析 WS 二进制帧
> 6. 调用 `on_incoming_audio_`，播放 TTS 音频

---

## 阶段十三：服务器响应完成，进入下一轮监听

**代码路径**：`protocol.cc → Protocol::SendStartListening()`

| # | 时间(ms) | AT 命令 | 说明 |
|---|---------|---------|------|
| 166 | 132442.6 | `AT+MIPSEND=1,77,<hex>` | 服务器响应播放完毕，重新进入监听（WS 文本帧） |
| 167–186 | ~132500 – 133700 | `AT+MIPSEND=1,xxx,<hex>` × **20帧** | 新一轮监听音频帧 |

**[166] 解码 payload（69 字节）**：
```json
{"session_id":"f97c3401","type":"listen","state":"start","mode":"auto"}
```

> 此消息标志着本次"你好小智"交互完整结束，设备重新进入等待唤醒或语音指令状态。

---

## 完整时序总览

```
T=104669ms  [001] AT                          ← 模组初始化
T=104683ms  [002] AT+CGMR                     ← 检测 ML307
T=104684ms  [003-006] AT+MHTTPDEL=0/1/2/3    ← 清残留 HTTP
            ─────── SIM/网络 ───────
T=104696ms  [007] AT+CPIN?                    ← SIM 就绪检查
T=104699ms  [008] AT+CEREG=2                  ← 启用注册 URC
T=104700ms  [009] AT+CEREG?                   ← 网络注册状态
T=104718ms  [010] AT+MIPCALL?                 ← 获取 IP 地址
            ─────── 设备信息 ───────
T=104733ms  [011-019] AT+CGMR/CSQ/CGSN/ICCID/COPS/CEREG  ← 采集设备信息
            ─────── HTTP OTA ───────
T=104855ms  [020] AT+MHTTPCREATE              ← 创建 HTTP 实例
T=104858ms  [021] AT+MHTTPCFG="ssl"           ← 开启 SSL
T=104859ms  [022] AT+MHTTPCFG="encoding"      ← 关闭编码（设头）
T=104861ms  [023-028] AT+MHTTPHEADER ×6       ← 设置请求头
T=104871ms  [029] AT+MHTTPCONTENT             ← 发送 POST 体
            [030] AT+MHTTPCFG="encoding" ON   ← 开启 HEX 编码
T=104902ms  [031] AT+MHTTPREQUEST             ← POST /xiaozhi/ota/
T=105580ms  [032] AT+CSQ                      ← 等待期间信号查询
T=106931ms  [033] AT+MHTTPDEL=0               ← 关闭 HTTP
            ═══════ 等待 OTA 处理 ~5.4s ═══════
            ─────── TCP/SSL ─────
T=112340ms  [034] AT+MIPSTATE=1               ← 查询连接状态
T=112342ms  [035] AT+MSSLCFG="auth",0,0       ← SSL 认证配置
T=112344ms  [036] AT+MIPCFG="ssl",1,1,0       ← 启用 SSL
T=112345ms  [037] AT+MIPCFG="encoding",1,1,1  ← HEX 编码
T=112347ms  [038] AT+MIPOPEN=1,"TCP","api.tenclass.net",443 ← 建立连接
            ═══════ TCP+TLS 握手 ~490ms ═══════
            ─────── WS 握手 ─────
T=112836ms  [039] AT+MIPSEND GET /xiaozhi/v1/ ← HTTP Upgrade
            ═══════ 等待 101 ~537ms ═══════
            ─────── WS Hello ────
T=113374ms  [040] AT+MIPSEND WS-TEXT hello    ← 发送客户端 Hello
            ←← (RX) 服务器 hello, session_id=f97c3401 ←←
            ─────── 唤醒词音频 ──
T=113510ms  [041-075] AT+MIPSEND WS-BIN ×35  ← 发送 2100ms 音频
T=113723ms  [076] AT+MIPSEND WS-TEXT listen/detect "你好小智"
T=113750ms  [077] AT+MIPSEND WS-TEXT listen/start auto
T=113890ms  [078] AT+MIPSEND WS-BIN           ← 1帧音频
            ═══════ 服务器 STT 处理 ~2164ms ═══════
            ─────── 中止重启 ────
T=116054ms  [079] AT+MIPSEND WS-TEXT abort wake_word_detected
T=116113ms  [080] AT+MIPSEND WS-TEXT listen/start auto
            ─────── 持续发送音频 ─
T=116260ms  [081-165] AT+MIPSEND WS-BIN ×84  ← 持续发送语音
            ←← (RX) 服务器 TTS 音频响应 ←←
            ─────── 响应完成 ─────
T=132443ms  [166] AT+MIPSEND WS-TEXT listen/start auto  ← 新一轮监听
T=132500ms  [167-186] AT+MIPSEND WS-BIN ×20  ← 新一轮音频
```

---

## 代码文件索引

| 文件 | 关键函数 | 对应阶段 |
|------|---------|---------|
| `managed_components/78__esp-ml307/src/at_modem.cc` | `Detect()`, `WaitForNetworkReady()`, `GetModuleRevision()`, `GetCsq()`, `GetImei()`, `GetIccid()`, `GetCarrierName()`, `GetRegistrationState()` | 阶段一～四 |
| `managed_components/78__esp-ml307/src/ml307/ml307_at_modem.cc` | `Ml307AtModem()` 构造函数, `ResetConnections()`, `WaitForNetworkReady()` | 阶段一、三 |
| `main/ota.cc` | `CheckVersion()`, `SetupHttp()` | 阶段五 |
| `managed_components/78__esp-ml307/src/ml307/ml307_http.cc` | `Ml307Http::Open()`, `Close()` | 阶段五 |
| `managed_components/78__esp-ml307/src/ml307/ml307_ssl.cc` | `Ml307Ssl::ConfigureSsl()` | 阶段六 |
| `managed_components/78__esp-ml307/src/ml307/ml307_tcp.cc` | `Ml307Tcp::Connect()`, `Send()` | 阶段六～十三 |
| `managed_components/78__esp-ml307/src/web_socket.cc` | `WebSocket::Connect()`, `Send()` | 阶段七～十三 |
| `main/protocols/websocket_protocol.cc` | `OpenAudioChannel()`, `GetHelloMessage()`, `ParseServerHello()`, `SendAudio()` | 阶段八～十三 |
| `main/protocols/protocol.cc` | `SendWakeWordDetected()`, `SendStartListening()`, `SendAbortSpeaking()` | 阶段十～十三 |
