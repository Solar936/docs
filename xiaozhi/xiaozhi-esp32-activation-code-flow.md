# xiaozhi-esp32 激活码流程分析

## 结论

**设备本身不等待用户在设备上输入激活码。**  
激活码是由服务器生成后下发给设备，设备将其**显示在屏幕上并通过语音读出**，提示用户在外部平台（如 Web/App）完成绑定操作，同时设备在后台轮询服务器直到激活成功。

---

## 整体流程

```
启动
  └─ Application::MainLoop
       └─ Application::CheckNewVersion(ota)
            ├─ [循环重试] SetDeviceState(kDeviceStateActivating)
            │    └─ display->SetStatus("正在检查版本")
            ├─ ota.CheckVersion()  ──→  HTTP GET/POST {check_version_url}
            │    └─ 解析服务端 JSON
            │         ├─ activation.message  (提示文字/激活链接)
            │         ├─ activation.code     (激活码字符串)
            │         ├─ activation.challenge (HMAC 挑战字符串)
            │         └─ activation.timeout_ms
            │
            ├─ 无 activation 字段 → 设置 MAIN_EVENT_CHECK_NEW_VERSION_DONE，退出
            │
            └─ 有 activation 字段
                 ├─ display->SetStatus("激活")
                 ├─ ShowActivationCode(code, message)
                 │    ├─ Alert("激活", message, "link", OGG_ACTIVATION)
                 │    │    ├─ 屏幕显示激活提示文字（含激活链接/说明）
                 │    │    ├─ 表情区显示 🔗 (link 图标)
                 │    │    └─ 播放激活提示音
                 │    └─ 逐位播报激活码数字（OGG_0 ~ OGG_9）
                 │
                 └─ [最多10次] ota.Activate()
                      ├─ 构造 HMAC-SHA256 响应 payload
                      │    {algorithm, serial_number, challenge, hmac}
                      ├─ HTTP POST {check_version_url}/activate
                      ├─ 200 → 激活成功，设置 MAIN_EVENT_CHECK_NEW_VERSION_DONE，退出
                      ├─ 202 → 等待中（用户尚未在外部完成绑定），等 3s 重试
                      └─ 其他 → 失败，等 10s 重试
```

---

## 关键代码位置

| 步骤 | 文件 | 函数 |
|------|------|------|
| 入口：检查版本循环 | `main/application.cc:123` | `Application::CheckNewVersion` |
| 显示激活码 & 语音播报 | `main/application.cc:198` | `Application::ShowActivationCode` |
| 弹出提示框（显示+音效） | `main/application.cc:228` | `Application::Alert` |
| OTA：发送 CheckVersion 请求 | `main/ota.cc:74` | `Ota::CheckVersion` |
| OTA：解析 activation 字段 | `main/ota.cc:120` | `Ota::CheckVersion` 内部 |
| OTA：构造 HMAC 响应 | `main/ota.cc:406` | `Ota::GetActivationPayload` |
| OTA：轮询激活接口 | `main/ota.cc:443` | `Ota::Activate` |

---

## 服务端协议

### CheckVersion 响应（含激活信息）

```json
{
  "activation": {
    "message": "请访问 https://... 完成设备绑定，您的激活码为：1234",
    "code": "1234",
    "challenge": "<随机字符串>",
    "timeout_ms": 30000
  }
}
```

- `code`：设备语音播报的数字码，供用户在外部平台输入
- `message`：显示在设备屏幕上的提示文字（含激活链接）
- `challenge`：服务端随机挑战值，用于 HMAC-SHA256 设备身份验证
- `timeout_ms`：每次激活请求的超时时间（默认 30000ms）

### Activate 请求（POST /activate）

```json
{
  "algorithm": "hmac-sha256",
  "serial_number": "<设备序列号>",
  "challenge": "<服务端下发的 challenge>",
  "hmac": "<HMAC-SHA256(challenge, hardware_key0) 的十六进制>>"
}
```

- HMAC 使用芯片硬件 HMAC 模块（`HMAC_KEY0`），仅支持 `SOC_HMAC_SUPPORTED` 的芯片（如 ESP32-S3）
- 无序列号时返回空 payload `{}`，激活不会成功

### Activate 响应

| HTTP 状态码 | 含义 | 设备行为 |
|-------------|------|----------|
| 200 | 激活成功 | 退出激活循环，正常启动 |
| 202 | 等待用户在外部完成绑定 | 等待 3 秒后重试 |
| 其他 | 激活失败 | 等待 10 秒后重试 |

---

## 激活态的设备状态

- 设备状态：`kDeviceStateActivating`
- LED 会进入激活态闪烁（`single_led.cc`、`circular_strip.cc` 等均有对应分支）
- 用户按下按键（`ToggleChatState`）可将状态切换回 `kDeviceStateIdle` 以中断激活循环

---

## 总结

激活码流程是**设备侧被动验证**机制：设备不接受键盘输入，而是将激活码通过音频和屏幕展示给用户，用户需在配套 App 或 Web 页面输入该码完成绑定，设备则持续轮询服务端（最多 10 次）等待激活结果。
