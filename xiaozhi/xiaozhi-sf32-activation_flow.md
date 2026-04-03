# 小智设备激活与连接流程

本文档梳理设备从启动到完成激活、建立 WebSocket 会话的完整流程。

---

## 总体流程概览

```
启动
 └─> 检查蓝牙 / PAN 网络
      └─> [OTA Check Version] POST /xiaozhi/ota/
           ├─ 服务器返回含 activation 字段（设备未绑定）
           │    └─> 屏幕显示绑定码，阻塞等待用户前往 xiaozhi.me 完成绑定
           │         └─> 用户长按KEY2 解除阻塞
           └─ 服务器返回不含 activation 字段（设备已绑定）
                └─> WebSocket 连接 /xiaozhi/v1/
                     └─> Hello 握手
                          └─> 进入对话待命状态
```

---

## 第一阶段：网络检查

**入口函数**：`xiaozhi2()` in `app/src/xiaozhi_websocket.c`

| 步骤 | 描述 |
|------|------|
| 1 | 检查蓝牙是否已连接，未连接则调用 `bt_interface_conn_ext()` 重连，等待最多 5 秒 |
| 2 | 蓝牙已连接但 PAN（手机网络共享）未连接，调用 `pan_reconnect()` 重连，等待最多 5 秒 |
| 3 | 仍无 PAN 连接，UI 提示"请在手机上开启网络共享后重新发起连接"并退出 |

---

## 第二阶段：OTA/Check Version 请求

若 `g_ota_verified == false`（首次或未通过验证），调用 `get_xiaozhi()` 发起 HTTP 请求。

### 客户端发出的请求

```
POST https://api.tenclass.net/xiaozhi/ota/
```

**请求头：**
```
Device-Id: xx:xx:xx:xx:xx:xx          # 蓝牙 MAC 地址
Client-Id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  # SHA256(MAC) 生成的 UUID
Content-Type: application/json
Content-length: <body 长度>
```

**`Client-Id` 生成方式**：对蓝牙 MAC 地址做 SHA256，取前 16 字节按 UUID 格式（8-4-4-4-12）拼装十六进制字符串。

**请求体（JSON）：**
```json
{
  "version": 2,
  "flash_size": 4194304,
  "psram_size": 0,
  "minimum_free_heap_size": 123456,
  "mac_address": "xx:xx:xx:xx:xx:xx",
  "uuid": "<Client-Id>",
  "chip_model_name": "sf32lb563",
  "chip_info": {
    "model": 1,
    "cores": 2,
    "revision": 0,
    "features": 0
  },
  "application": {
    "name": "my-app",
    "version": "1.0.0",
    "compile_time": "2021-01-01T00:00:00Z",
    "idf_version": "4.2-dev",
    "elf_sha256": ""
  },
  "partition_table": [
    {
      "label": "app",
      "type": 1,
      "subtype": 2,
      "address": 10000,
      "size": 100000
    }
  ],
  "ota": {
    "label": "ota_0"
  },
  "board": {
    "type": "hdk563",
    "mac": "xx:xx:xx:xx:xx:xx"
  }
}
```

---

## 第三阶段：解析服务器响应（`parse_ota_response()`）

服务器返回 JSON，由 `parse_ota_response()` 解析，主要提取两块内容：

### 情况一：设备已绑定（正常）

服务器响应中**不含** `activation` 字段：

```json
{
  "websocket": {
    "url": "wss://...",
    "token": "Bearer xxxxxxxx"
  }
}
```

- `is_activated = false`
- 提取 `websocket.url` 和 `websocket.token` 备用
- 设置 `g_ota_verified = true`，直接进入 WebSocket 连接阶段

### 情况二：设备未绑定（需要激活）

服务器响应中**包含** `activation` 字段：

```json
{
  "websocket": {
    "url": "wss://...",
    "token": "Bearer xxxxxxxx"
  },
  "activation": {
    "code": "ABC123"
  }
}
```

- `is_activated = true`
- `activation.code`（6位绑定码）被保存到 `g_activation_context.code`
- 进入激活等待流程（见第四阶段）

---

## 第四阶段：激活等待（设备未绑定时）

```c
// app/src/xiaozhi_websocket.c
she_bei_ma = 0;  // 关闭自动重连 timer 的触发
snprintf(str_temp, sizeof(str_temp),
    "设备未添加，请前往 xiaozhi.me 输入绑定码: \n %s \n ",
    g_activation_context.code);
xiaozhi_ui_chat_output(str_temp);           // 对话界面显示绑定码
xiaozhi_ui_standby_chat_output(str_temp);   // 待机界面同步显示
rt_sem_take(g_activation_context.sem, RT_WAITING_FOREVER);  // 永久阻塞
```

| 项目 | 说明 |
|------|------|
| 用户操作 | 用户查看屏幕上的 6 位绑定码，前往 **xiaozhi.me** 在网页上输入该码完成设备绑定 |
| 设备等待方式 | 信号量 `activation_sem` 永久阻塞，**设备本身不需要用户输入任何内容** |
| 解除阻塞方式 | 长按 KEY2 超过 3 秒触发 `xz_button2_event_handler`，调用 `rt_sem_release(activation_sem)` |
| 解除后状态 | `is_activated = false`，`she_bei_ma = 1`（重新允许自动重连），唤醒屏幕背光 |

> **注意**：目前代码逻辑是用户在 xiaozhi.me 网页完成绑定后，**需手动长按 KEY2** 让设备继续，设备端不会自动感知网页绑定是否完成。

---

## 第五阶段：WebSocket 连接（`xiaozhi_ws_connect()`）

### 连接参数

```
Host:   api.tenclass.net
Path:   /xiaozhi/v1/
Port:   443 (TLS)
Token:  Bearer 12345678
```

**升级请求额外头部：**
```
Protocol-Version: 1
Device-Id: xx:xx:xx:xx:xx:xx
Client-Id: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### 连接过程

1. `wsock_connect()` 发起 TLS WebSocket 握手
2. 等待信号量 `g_xz_ws.sem`，超时 50 秒
3. 服务器返回 HTTP `101 Switching Protocols` → 释放信号量，`is_connected = 1`
4. 连接成功后立即发送 **Hello 消息**

---

## 第六阶段：Hello 握手

### 客户端发送

```json
{
  "type": "hello",
  "version": 3,
  "transport": "websocket",
  "audio_params": {
    "format": "opus",
    "sample_rate": 16000,
    "channels": 1,
    "frame_duration": 60
  }
}
```

若启用了 MCP 功能（`CONFIG_IOT_PROTOCOL_MCP`），还会附加：
```json
"features": { "mcp": true }
```

### 服务器响应

```json
{
  "type": "hello",
  "session_id": "xxxxxxxx",
  "audio_params": {
    "sample_rate": "16000",
    "duration": "60"
  }
}
```

客户端解析后：
- 保存 `session_id`、`sample_rate`、`frame_duration`
- 状态变为 `kDeviceStateIdle`
- UI 更新：显示"小智已连接!"，emoji 切换为 neutral
- 若启用 AEC，发送 `listen start` 进入常驻监听模式

---

## 第七阶段：对话消息类型

WebSocket 建立后，双方通过如下消息类型交互：

### 客户端 → 服务器

| 消息 type | 触发时机 | 关键字段 |
|-----------|----------|----------|
| `listen` start | 按下 KEY1 / 语音唤醒 | `session_id`, `state: "start"`, `mode` |
| `listen` stop | 松开 KEY1 | `session_id`, `state: "stop"` |
| `abort` | 检测到唤醒词打断 | `session_id`, `reason` |
| Binary | 持续麦克风输入 | Opus 编码的 16kHz 单声道音频帧（60ms/帧）|
| `iot` update | 连接后主动上报 | `descriptors` / `states` |

### 服务器 → 客户端

| 消息 type | 含义 | 处理动作 |
|-----------|------|----------|
| `hello` | 会话握手确认 | 保存 session_id，进入 idle 状态 |
| `stt` | 语音识别文字 | UI 显示识别文本，启动扬声器 |
| `tts` start/stop | TTS 播报状态 | 控制扬声器开关，UI 状态切换 |
| `tts` sentence_start | TTS 分句文字 | UI 显示当前播报句子 |
| `llm` | 情绪标签 | 更新 emoji 表情 |
| `iot` | IoT 控制命令 | 调用 `iot_invoke()` 执行，上报新状态 |
| `mcp` | MCP 协议消息 | 转发给 `McpServer_ParseMessage()` |
| `goodbye` | 会话结束 | 状态回到 Unknown，UI 提示"睡眠中..." |
| Binary | TTS 音频数据 | Opus 解码后通过扬声器播放 |

---

## 关键变量说明

| 变量 | 位置 | 作用 |
|------|------|------|
| `g_activation_context.is_activated` | `xiaozhi_websocket.c` | 标记当前是否处于待激活状态 |
| `g_activation_context.code` | `xiaozhi_websocket.c` | 存储服务器下发的 6 位绑定码 |
| `g_activation_context.sem` | `xiaozhi_websocket.c` | 阻塞等待用户完成绑定的信号量 |
| `g_ota_verified` | `xiaozhi_websocket.c` | OTA check 是否已完成（避免重复请求）|
| `she_bei_ma` | `xiaozhi_client_public.c` | 自动重连 timer 开关，激活等待期间置 0 |
| `g_xz_ws.is_connected` | `xiaozhi_websocket.c` | WebSocket 是否已建立连接 |
