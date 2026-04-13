# KWS 技术调研与多方案对比

> 整理自三次对话，涵盖 CM33 开源 KWS 仓库调研、SF32LB52 硬件分析、三方案横向对比。

---

## 一、CM33 开源 KWS 仓库调研（第一次对话）

### 检索结论

| 仓库 | Stars | 语言 | 许可证 | 描述 |
|------|-------|------|--------|------|
| [ARM-software/ML-KWS-for-MCU](https://github.com/ARM-software/ML-KWS-for-MCU) | ⭐1233 | C | Apache 2.0 | 基于 CMSIS-NN，支持 DS-CNN/GRU/LSTM，裸机设计 |
| [ARM-software/ml-embedded-evaluation-kit](https://github.com/ARM-software/ml-embedded-evaluation-kit) | ⭐400+ | C++ | Apache 2.0 | ARM 官方评估套件，含 KWS、ASR、目标检测等 Use Case |
| [alifsemi/alif_ml-embedded-evaluation-kit](https://github.com/alifsemi/alif_ml-embedded-evaluation-kit) | ⭐15 | C++ | Apache 2.0 | alif E7 双 CM55 移植，集成 NPU（Ethos-U55） |

### 推荐方案

**ML-KWS-for-MCU** 最适合直接移植到 CM33：
- 纯 C，无 C++ 依赖
- 直接调用 CMSIS-NN API，在 SF32LB52 上可经 NNACC 加速
- 支持 DS-CNN 模型（Deep Separable CNN），适合边缘推理
- 需要为 RT-Thread 封装任务包装层（原设计为裸机）

---

## 二、SF32LB52 硬件特性分析（第二次对话）

### 核心架构

| 组件 | 规格 | 说明 |
|------|------|------|
| HCPU | Cortex-M33 @ 0–240MHz | 用户可编程，全开放 |
| LCPU | Cortex-M33 @ 0–48MHz | **闭源**，仅运行蓝牙协议栈，不可修改用户代码 |
| SDK | RT-Thread | Apache 2.0，OpenSiFli/SiFli-SDK |

> **重要**：SF32LB52 的 LCPU 不开放代码修改，无法在其上运行 KWS 或任何用户任务。

### KWS 相关外设

#### NNACC（神经网络加速器）
- 支持 CMSIS-NN 接口，对上层透明
- 官方文档明确声称：**"可以实现低 MIPS 需求的关键词识别"**
- 相比 ARM DSP 软件实现：**速度提升 8 倍**
- 加速的算子：
  - `arm_convolve_1x1_HWC_q7_fast_nonsquare`
  - `arm_convolve_HWC_q7_basic_nonsquare`
  - `arm_depthwise_separable_conv_HWC_q7_nonsquare`
- 使能方式：`#define BSP_USING_CMSIS_NN` + `#define BSP_USING_NN_ACC`

#### PDM（数字麦克风接口）
- 支持单/双声道，8/12/16/24/32/48 KHz，24-bit 深度
- DMA 环形缓冲模式，支持增益控制
- 典型 KWS 配置：16KHz / 16-bit / 单声道 / DMA
- **PDM 属于 HPSYS 域**：HCPU 进入睡眠时 PDM 停止工作

#### FFT 硬件加速器
- 可用于 MFCC 特征提取，加速 KWS 前端处理

### 低功耗模式

| 模式 | HCPU 状态 | 唤醒时延 | 唤醒源 |
|------|-----------|----------|--------|
| Deep Sleep | 停止（RAM 保留） | ~250μs | RTC、IO、LPTIM、BT mailbox |
| Standby | 复位（384KB 保留） | ~1ms | RTC、PIN、LPTIM、BT |
| Hibernate | 完全断电 | >2ms | PIN、RTC |

### 低功耗常在线麦克风策略

SF32LB52 **没有专用的硬件 VAD 子系统**，PDM 不能在 HCPU 睡眠时工作。两种可行方案：

**方案 A（软件轮询，功耗较高）**
```
Deep Sleep → 定时唤醒 (~250μs) → HCPU @ 48MHz 中速模式
→ PDM 采集一帧 → NNACC 跑 KWS 推理 → 未检测到 → 回 Deep Sleep
→ 检测到唤醒词 → 切换全速 240MHz → 开始音频流采集
```

**方案 B（外部硬件 VAD，功耗最低，推荐）**
```
外部 VAD 芯片（如 ES7210 内置 VAD、MSM261S4030H0R 数字麦 VAD）
→ 检测到人声 → GPIO 中断唤醒 HCPU
→ HCPU 运行 KWS 精推理
→ 确认唤醒词 → 切换全速采集
```

### 唤醒后麦克风使用

**唤醒前后使用同一路 PDM 麦克风**。软件切换模式：
- 唤醒检测阶段：轻量级推理（低频/低分辨率）
- 唤醒后：全速 PCM DMA 采集 + OPUS 编码 + WebSocket 上传云端

---

## 三、三方案 KWS 技术对比（第三次对话）

### 方案概览

| 方案 | 平台 | KWS 引擎 | 代表项目 |
|------|------|----------|----------|
| A | SF32LB52 CM33 | ARM CMSIS-NN + NNACC | ML-KWS-for-MCU 移植 |
| B | ESP32-S3 Xtensa LX7 | ESP-SR WakeNet9 | xiaozhi-esp32 |
| C | SF32LB52 CM33 | ET-ASR（声智）`.a` 库 | xiaozhi-sf32 |

---

### xiaozhi-esp32 唤醒流程

**代码路径**：`main/application.cc`（ESP-SR + FreeRTOS EventGroup）

```
[Idle 状态]
  → audio_service_.EnableWakeWordDetection(true)
  → ESP-SR WakeNet9 持续 SIMD 推理

[MAIN_EVENT_WAKE_WORD_DETECTED 触发]
  → WakeWordInvoke(wake_word)
      ├── audio_service_.EncodeWakeWord()     // 预缓存编码最近 N 秒音频
      ├── SetDeviceState(kDeviceStateConnecting)
      └── Schedule → ContinueWakeWordInvoke()

  → ContinueWakeWordInvoke()
      ├── protocol_->OpenAudioChannel()       // 建立 WebSocket 音频信道
      ├── while(PopWakeWordPacket())
      │     └── protocol_->SendAudio()        // 发送预滚音频（唤醒词本身）
      ├── protocol_->SendWakeWordDetected(wake_word)
      └── SetListeningMode(GetDefaultListeningMode())

[Listening 状态]
  → audio_service_.EnableVoiceProcessing(true)  // 开启 AEC + VAD
  → protocol_->SendStartListening()
  → 同一麦克风持续采集 PCM → OPUS 编码 → 云端 ASR

[返回 Idle]
  → audio_service_.EnableWakeWordDetection(true)  // 重新开启唤醒检测
```

**注意**：默认配置下监听状态中唤醒检测关闭；可通过 `CONFIG_WAKE_WORD_DETECTION_IN_LISTENING` 启用同时监测。

---

### xiaozhi-sf32 唤醒流程

**KWS 引擎**：`libetasr_watch_sifli567.a`（ET-ASR，EtFace/宇极科技，闭源二进制）

**代码路径**：`app/src/kws/`

```
[系统启动]
  → kws_demo()
  → kws_start()
      ├── et_bsp_ctrl_main_init()          // 初始化 ET-ASR 引擎，加载权重/阈值
      ├── et_asr_wakeup_first_init(&cfg, et_func_kws_event)
      └── et_start_asr()                   // 启动推理循环

[kws_thread 线程，低优先级]
  → audio_open(AUDIO_TYPE_LOCAL_RECORD, 16KHz/16bit/单声道)
  → app_audio_record_callback():
      └── 每积累 640 样本(40ms) → KWS_EVT_DECODE

  → et_asr_wakeup_buf_write(pcm, 640, ...)  // 喂入 ET-ASR，NNACC 加速推理

[检测到唤醒词]
  → et_func_kws_event(event)
  → simulate_button_pressed()               // 模拟按键，触发主逻辑
  → 主应用切换到 Listening 状态

[切换到对话模式]
  → 同一 PDM 麦克风继续采集
  → 音频数据通过 BT PAN（蓝牙个人热点）→ 手机 → 云端 ASR
```

**关键词配置**（`et_keyword.c`，音素级编码）：

| ID | 关键词 | 阈值 |
|----|--------|------|
| 1 | 小智小智 | 130 |
| 2 | 小智小爱 | 100 |
| 3 | 小智小康 | 100 |
| 4 | 小智小酷 | 100 |
| 5 | 小智小灵 | 100 |
| 8 | 你好小康 | 100 |
| 9 | 你好小灵 | 100 |

**软 VAD**：ET-ASR 内置多级软 VAD（`ET_ASR_UI_VAD_TYPE`），支持能量/概率/人声三种判断模式，无需外部 VAD 芯片。

---

### 多角度横向对比

#### 1. 硬件平台

| 维度 | 方案 A (CMSIS-NN+SF32) | 方案 B (WakeNet9+ESP32-S3) | 方案 C (ET-ASR+SF32) |
|------|------------------------|----------------------------|----------------------|
| 主芯片 | SF32LB52 | ESP32-S3 | SF32LB52 |
| CPU 架构 | Cortex-M33 @ 240MHz | Xtensa LX7 @ 240MHz | Cortex-M33 @ 240MHz |
| AI 加速 | NNACC（CMSIS-NN 接口，8x） | 向量 SIMD（XRF 寄存器组） | NNACC（ET-ASR 内部调用） |
| RAM 容量 | PSRAM 可扩展 | 512KB SRAM + PSRAM 可选 | PSRAM 可扩展 |
| 网络连接 | BT/WiFi（视模块） | WiFi / WiFi+BT | 仅 BT PAN（通过手机热点） |
| 功耗特点 | 无片内硬件 VAD；需定时轮询或外部 VAD 芯片 | 无片内硬件 VAD（ESP32-P4 才有）；需持续运行主核或接外部 VAD | 无片内硬件 VAD；ET-ASR 软 VAD 辅助 |

#### 2. KWS 引擎

| 维度 | 方案 A | 方案 B | 方案 C |
|------|--------|--------|--------|
| 引擎名称 | ML-KWS-for-MCU / CMSIS-NN | ESP-SR WakeNet9/WakeNet9s | ET-ASR（libetasr） |
| 模型架构 | DS-CNN / GRU / LSTM（可选） | 专有 DNN（Espressif 训练） | 专有音素 HMM+DNN（EtFace） |
| 开源程度 | ✅ 完全开源（Apache 2.0） | ⚠️ 应用开源，模型+引擎闭源 | ⚠️ 应用开源，`.a` 库闭源 |
| 唤醒词定制 | ✅ 完全自由（重训练模型） | ✅ 通过乐鑫平台申请/训练 | ⚠️ 依赖 EtFace 提供定制服务 |
| 音频前端 | 需手动集成 MFCC（FFT 硬件加速） | ✅ AFE 一站式（AEC+VAD+BSS+NS+NSNET） | ✅ 内置软 VAD，自带前处理 |
| 关键词数量 | 模型决定（典型 10–35 词） | 1 个主唤醒词（可选命令词） | 10 个（`et_keyword.c` 可配置） |
| 推理帧长 | 典型 25ms 帧 + 10ms 步进 | 未公开 | 40ms/640 样本 |
| 误唤醒率 | 取决于模型训练质量 | <1/24h（乐鑫官方指标） | 阈值可调（130–100，可优化） |

#### 3. 音频前端（AFE）

| 维度 | 方案 A | 方案 B | 方案 C |
|------|--------|--------|--------|
| AEC（回声消除） | ❌ 需手动集成 | ✅ 内置（Amazon Alexa 2-mic 认证） | ❌ 需手动集成 |
| VAD | ❌ 需手动实现 | ✅ 内置 WebRTC VAD | ✅ 内置软 VAD（能量+概率） |
| 降噪 NS | ❌ 需手动集成 | ✅ NSNET（DNN 降噪） | ⚠️ 部分内置 |
| 波束成形 BSS | ❌ | ✅ 双麦支持 | ❌ |
| 特征提取 | MFCC（可用 FFT 硬件） | 内置（不透明） | 内置（不透明） |

#### 4. 系统集成

| 维度 | 方案 A | 方案 B | 方案 C |
|------|--------|--------|--------|
| 操作系统 | RT-Thread | FreeRTOS | RT-Thread |
| 唤醒事件机制 | 自定义回调 | FreeRTOS EventGroup + `MAIN_EVENT_WAKE_WORD_DETECTED` | `et_func_kws_event` → `simulate_button_pressed()` |
| 唤醒后麦克风 | 同一 PDM，切换采集模式 | 同一 I2S/PDM，切换语音处理模式 | 同一 PDM，保持 audio_open |
| 联网方式 | 视具体应用 | WiFi（WebSocket → 云端 ASR） | BT PAN → 手机 → WebSocket → 云端 |
| 云端协议 | 视具体应用 | WebSocket + OPUS | WebSocket + OPUS |
| 预滚缓存 | 需自行实现 | ✅ `EncodeWakeWord()` 自动缓存 N 秒 | 未见预滚处理 |
| CPU 频率策略 | NNACC 加速，频率可降 | 持续全速运行 | "kws不降频"，强制全速 240MHz |

#### 5. 开发成本与可维护性

| 维度 | 方案 A | 方案 B | 方案 C |
|------|--------|--------|--------|
| 集成难度 | 🔴 高（需裸机→RT-Thread 移植 + MFCC + VAD） | 🟢 低（ESP-IDF 组件，直接依赖） | 🟡 中（`.a` 库接口简单，但需 SiFli-SDK） |
| 唤醒词修改 | 🟢 自由（重训练 TF 模型 → 量化 → 部署） | 🟡 平台申请（乐鑫工具链） | 🔴 依赖厂商（需与 EtFace 合作） |
| 调试支持 | 🟢 完整源码，GDB 可打断点 | 🟡 应用层开源，引擎黑盒 | 🔴 引擎完全黑盒，仅日志 |
| 社区活跃度 | ARM 官方维护，但 MCU 方向更新慢 | 乐鑫官方 + 25.6k Stars 社区 | 171 Stars，主要靠 EtFace 支持 |
| 许可证 | Apache 2.0（商用友好） | ESP-SR：MIT（应用）/未知（引擎） | MIT（应用）/商业授权（`.a`） |

#### 6. 功耗对比

> **重要更正（2026-04）**：ESP32-S3 **没有**片内硬件 LP VAD。`ESP_SLEEP_WAKEUP_VAD` 枚举值存在于共享的 ESP-IDF 头文件中，但仅适用于 **ESP32-P4**（该芯片具有 LP I2S + LP VAD 硬件外设，可在主核深度睡眠时持续采集音频并做 VAD 检测）。ESP32-S3 的 ESP-SR AFE 中的 VAD 是纯软件（WebRTC VAD + NSNET），必须运行在主 Xtensa LX7 核上，无法在深度睡眠中工作。

| 方案 | KWS 待机策略 | 典型功耗说明 |
|------|-------------|-------------|
| A（CMSIS-NN + SF32LB52） | 可 Deep Sleep 定时轮询（~250μs 唤醒）；或外部 VAD 芯片 GPIO 中断唤醒 HCPU | 理论最低，轮询周期需精心设计；配合 ES7210 等外部 VAD 芯片可进一步降低待机功耗 |
| B（ESP-SR + ESP32-S3） | **无硬件 VAD**；主核（Xtensa LX7）需持续运行才能跑软件 VAD + WakeNet；Light Sleep 可降频但 I2S 仍需工作 | 功耗相对最高；如需低功耗须换用 **ESP32-P4**（有 LP VAD + LP I2S） |
| C（ET-ASR + SF32LB52） | SF32 可 Deep Sleep + 定时唤醒；ET-ASR 软 VAD 快速判断；commit "kws不降频"表明目前全速运行 | 中等；可优化为低频轮询模式，但需修改 ET-ASR 配置 |

**ESP32-P4 的低功耗 VAD 架构（对比参考）**：
```
LP I2S（数字麦克风，低功耗域，主核睡眠时仍工作）
    → LP VAD 硬件外设（持续检测语音活动）
    → 检测到人声 → 唤醒 LP Core（RISC-V）→ 运行 KWS 精推理
    → 确认唤醒词 → 唤醒 HP Core（主 CPU）→ 全速采集 + 云端 ASR
```
> ESP32-P4 可实现真正的毫瓦级常在线语音唤醒，ESP32-S3 不具备此能力。

**推荐外部 VAD 芯片**（适用方案 A/C，当 SF32LB52 需降低待机功耗时）：
- ES7210：4 通道 ADC，内置 VAD，I2C 配置，3.3V 供电
- MSM261S4030H0R：PDM 数字麦，内置 SNR 优化，支持 VAD 输出引脚

---

### 方案选型建议

```
需要完全开源 + 支持唤醒词自定义训练？
  → 方案 A（ML-KWS-for-MCU + SF32LB52 NNACC）
    注意：需自行集成 MFCC 前端、VAD，工作量较大

已有 ESP32-S3 平台 + 需要完整 AFE 生态？
  → 方案 B（xiaozhi-esp32 + ESP-SR WakeNet9）
    注意：网络依赖 WiFi；ESP32-S3 无硬件 LP VAD，主核需持续运行，待机功耗偏高

SF32LB52 平台 + 快速落地 + 接受闭源库？
  → 方案 C（xiaozhi-sf32 + ET-ASR）
    注意：唤醒词定制需联系 EtFace，.a 库无法调试

SF32LB52 平台 + 极低功耗要求？
  → 方案 C 基础上 + 外部硬件 VAD 芯片（ES7210）
    PDM 检测交由 VAD 芯片，HCPU 深度睡眠，GPIO 中断唤醒

需要芯片级硬件 LP VAD（真正低功耗常在线，无需外部 VAD 芯片）？
  → 换用 ESP32-P4 平台（LP I2S + LP VAD 硬件外设，主核可深度睡眠）
    注意：xiaozhi-esp32 已有 ESP32-P4 支持，但生态成熟度低于 ESP32-S3
```

---

## 四、VAD 与 KWS 的关系

VAD 和 KWS 是语音唤醒流水线中的**两个不同阶段**，通常串联工作：

```
麦克风 PCM 流
    │
    ▼
┌─────────────────────────────┐
│  Stage 1: VAD               │  ← 门控：有声音吗？
│  (Voice Activity Detection) │
│  轻量级，能量/频谱检测        │
└──────────────┬──────────────┘
               │ 有声帧（pass）
               ▼
┌─────────────────────────────┐
│  Stage 2: KWS               │  ← 识别：是指定唤醒词吗？
│  (Keyword Spotting)         │
│  重量级，DNN/CNN 推理         │
└──────────────┬──────────────┘
               │ 匹配（唤醒词确认）
               ▼
       唤醒主应用 / 云端 ASR
```

### VAD（Voice Activity Detection，语音活动检测）

| 属性 | 说明 |
|------|------|
| **目标** | 判断当前帧是否含有人声（二元 speech/non-speech） |
| **输入** | 原始 PCM 音频帧 |
| **输出** | 有声/无声 标志 |
| **计算量** | 极轻：能量阈值 / 过零率 / 简单频谱特征 |
| **作用** | 门控，避免 KWS 在静音/噪音帧上做昂贵推理，节省功耗 |
| **实现层级** | 可以是硬件（ESP32-P4 LP VAD 外设）、专用芯片（ES7210 内置 VAD）或软件（WebRTC VAD、ET-ASR 软 VAD） |

### KWS（Keyword Spotting，关键词检测）

| 属性 | 说明 |
|------|------|
| **目标** | 检测特定唤醒词（"小智小智"、"Hey Google" 等） |
| **输入** | 经 VAD 过滤的有声帧，提取 MFCC/Mel 特征 |
| **输出** | 关键词 ID + 置信度分数 |
| **计算量** | 重：需要 DNN/CNN 推理（DS-CNN、WakeNet9、ET-ASR 音素模型） |
| **作用** | 精确匹配，区分唤醒词与其他语音内容 |
| **实现层级** | 通常在主 CPU 或 NPU/加速器上运行（CMSIS-NN + NNACC、WakeNet9、ET-ASR） |

### 三方案 VAD 实现对比

| 方案 | VAD 实现 | VAD 运行位置 | 是否可低功耗 |
|------|----------|-------------|-------------|
| A（CMSIS-NN + SF32LB52） | 需手动实现（能量阈值或 WebRTC VAD 移植） | HCPU CM33 | ⚠️ 需定时唤醒轮询；可用外部 VAD 芯片彻底低功耗 |
| B（ESP-SR + ESP32-S3） | 软件 VAD（WebRTC VAD + NSNET，ESP-SR AFE 内置） | HP Core Xtensa LX7 | ❌ 纯软件，主核必须运行；**ESP32-S3 无硬件 LP VAD** |
| C（ET-ASR + SF32LB52） | 软件 VAD（ET-ASR 内置，能量+概率双判断） | HCPU CM33 | ⚠️ 软 VAD 可辅助静音跳帧；同方案 A 可加外部 VAD 芯片 |

> **注**：ESP32-**P4**（非 S3）才具有芯片级 **LP VAD + LP I2S** 硬件外设，可在主核深度睡眠时持续采集音频并做 VAD，再通过中断唤醒 LP Core 进行 KWS 推理，是目前乐鑫生态中实现真正低功耗常在线语音唤醒的方案。

---

## 附：关键参考链接

| 资源 | 链接 |
|------|------|
| ML-KWS-for-MCU | https://github.com/ARM-software/ML-KWS-for-MCU |
| ARM ml-embedded-evaluation-kit | https://github.com/ARM-software/ml-embedded-evaluation-kit |
| xiaozhi-esp32 | https://github.com/78/xiaozhi-esp32 |
| xiaozhi-sf32 | https://github.com/78/xiaozhi-sf32 |
| ESP-SR | https://github.com/espressif/esp-sr |
| SiFli-SDK | https://github.com/OpenSiFli/SiFli-SDK |
| SiFli NNACC 文档 | https://docs.sifli.com/projects/sdk/sf32lb52x/latest/hal/nnacc.html |
| SiFli Xiaozhi 架构 | https://docs.sifli.com/projects/xiaozhi/architecture/ |
| ESP32-P4 LP VAD 文档 | https://docs.espressif.com/projects/esp-idf/en/latest/esp32p4/api-reference/peripherals/vad.html |
| ESP32-P4 LP I2S 文档 | https://docs.espressif.com/projects/esp-idf/en/latest/esp32p4/api-reference/peripherals/lp_i2s.html |
