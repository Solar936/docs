# ARM CM33 KWS + VAD 库调研与 Zephyr 移植指南

> 调研背景：参考 xiaozhi-sf32 的 KWS 实现（ET-ASR + libetasr_watch_sifli567.a），寻找可移植到 Zephyr 的同类开源替代方案。

---

## 一、xiaozhi-sf32 KWS 现状

**引擎**：`libetasr_watch_sifli567.a`（ET-ASR，宇极科技，**闭源**，无法移植）

**关键接口**（`app/src/kws/`）：
```c
et_bsp_ctrl_main_init()                    // 初始化引擎
et_asr_wakeup_first_init(&cfg, cb)         // 注册唤醒词 + 回调
et_asr_wakeup_buf_write(pcm, 640, ...)     // 每帧 640 samples (40ms @ 16kHz)
et_func_kws_event(event)                   // 检测到唤醒词后回调
et_asr_set_ignore(1/0)                     // 播放 TTS 期间暂停/恢复 KWS
```

**内置 VAD**：多级软 VAD（能量/人声/声学特征），`ET_ASR_UI_VAD_TYPE` 配置。  
**移植障碍**：芯片绑定（sifli567），无法用于 Zephyr / 其他平台。

---

## 二、KWS 库推荐（按推荐度排序）

所有库默认英文，支持中文均需重训练模型（见第七节）。

### 🥇 推荐 1：ccli8/NuMaker-Zephyr-TFLM-KWS

| 维度 | 详情 |
|------|------|
| 链接 | https://github.com/ccli8/NuMaker-Zephyr-TFLM-KWS |
| 协议 | 开源 |
| 推荐理由 | **唯一在 Zephyr 上跑通完整 KWS 链路**（DMIC → TFLM 推理 → 事件），移植等于复制修改 |
| Flash | DS-CNN Medium INT8 ~200KB + TFLM ~150KB = **~400KB** |
| RAM | Tensor Arena ~70KB + 栈/堆 ~20KB = **~90KB** |
| CM33 推理速度 | 纯 CMSIS-NN（无 Ethos-U NPU）约 **50~150ms @ 240MHz** |
| Zephyr 集成 | ✅ `west config manifest.project-filter -- +tflite-micro` |
| 移植难度 | ⭐⭐ 去掉 Ethos-U Vela 步骤，换纯 CMSIS-NN 模型即可 |

### 🥈 推荐 2：ARM-software/ML-KWS-for-MCU

| 维度 | 详情 |
|------|------|
| 链接 | https://github.com/ARM-software/ML-KWS-for-MCU |
| 协议 | Apache 2.0，⭐1.2k |
| Flash | DS-CNN Small **~80KB**；Large ~330KB |
| RAM | DS-CNN Small **~50KB** |
| CM33 推理速度 | DS-CNN Small **20~40ms**；Large 80~120ms |
| Zephyr 集成 | ❌ 裸机设计，需手动封装：`Deployment/` → CMakeLists，`microphone_input()` → Zephyr PDM，推理循环 → `k_thread` |
| 移植难度 | ⭐⭐⭐ 与 xiaozhi-sf32 接口最接近（40ms 帧），CMSIS-NN 可用 NNACC 加速 |

### 🥉 推荐 3：tensorflow/tflite-micro — micro_speech

| 维度 | 详情 |
|------|------|
| 链接 | https://github.com/tensorflow/tflite-micro/tree/main/tensorflow/lite/micro/examples/micro_speech |
| 协议 | Apache 2.0，⭐2.9k（仓库整体）|
| Flash | 模型 <20KB + TFLM ~150KB = **~170KB** |
| RAM | Tensor Arena **~30KB** |
| CM33 推理速度 | tiny_conv **<10ms**（极快）|
| Zephyr 集成 | ✅ 已内置 `CONFIG_TENSORFLOW_LITE_MICRO=y` |
| 移植难度 | ⭐⭐ 但内置模型只能分 4 类（yes/no/unknown/silence）|

> **为何排第三**：tiny_conv 模型是 demo 级别，用于实际唤醒词必须换模型（换后 Flash/RAM 与 NuMaker 接近），NuMaker 胜在已在 Zephyr 跑通完整链路。**例外**：Flash < 200KB 或 RAM 极紧张时，可将 micro_speech 升为首选。

### 第 4 名：bamert/stm32_speech_commands

| 维度 | 详情 |
|------|------|
| 链接 | https://github.com/bamert/stm32_speech_commands |
| 协议 | **无许可证**（谨慎使用）|
| Flash / RAM | ~100KB / ~60KB |
| CM33 推理速度 | 估算 <50ms @ 240MHz |
| Zephyr 集成 | ❌ 裸机 + STM32 HAL，需大量改写 |
| 移植难度 | ⭐⭐⭐⭐ 不支持中文，无许可证，不推荐 |

---

## 三、VAD 库推荐

### 🥇 唯一推荐：dpirch/libfvad

| 维度 | 详情 |
|------|------|
| 链接 | https://github.com/dpirch/libfvad |
| 协议 | BSD-3-Clause（商用友好）|
| 来源 | WebRTC 原始 VAD 引擎独立提取，去除平台相关汇编 |
| Flash / RAM | <30KB / <5KB（每实例约 4KB）|
| CM33 速度 | 每帧（30ms）<0.5ms（纯整数运算）|
| Zephyr 集成 | ✅ 手动列出源文件即可，无 autoconf 依赖 |
| 移植难度 | ⭐ 纯 C，零依赖 |
| 语言支持 | ✅ 语言无关，基于能量/频谱检测人声 |

**CMakeLists 集成**：
```cmake
target_sources(app PRIVATE
    modules/libfvad/src/vad/vad_core.c
    modules/libfvad/src/vad/vad_filterbank.c
    modules/libfvad/src/vad/vad_gmm.c
    modules/libfvad/src/vad/vad_sp.c
    modules/libfvad/src/signal_processing/division_operations.c
    modules/libfvad/src/signal_processing/energy.c
    modules/libfvad/src/signal_processing/get_scaling_square.c
    modules/libfvad/src/signal_processing/resample_48khz.c
    modules/libfvad/src/signal_processing/resample_by_2.c
    modules/libfvad/src/signal_processing/resample_by_2_internal.c
    modules/libfvad/src/signal_processing/resample_fractional.c
    modules/libfvad/src/signal_processing/spl_sqrt.c
    modules/libfvad/src/signal_processing/spl_sqrt_floor.c
)
target_include_directories(app PRIVATE modules/libfvad/include)
```

---

## 四、关键参数对比汇总

| 库 | 推荐度 | 协议 | 中文唤醒词 | Flash | RAM | CM33 推理速度 | Zephyr 集成 | 移植难度 |
|----|--------|------|-----------|-------|-----|--------------|-------------|---------|
| NuMaker-Zephyr-TFLM-KWS | 🥇 | 开源 | ⚠️需重训 | ~400KB | ~90KB | 50~150ms | ✅ 最完整 | ⭐⭐ |
| ML-KWS-for-MCU | 🥈 | Apache 2.0 | ⚠️需重训 | 80~330KB | ~50KB | 20~120ms | ❌需适配 | ⭐⭐⭐ |
| TFLM micro_speech | 🥉 | Apache 2.0 | ⚠️需换模型 | ~170KB | ~30KB | <10ms | ✅ 已内置 | ⭐⭐ |
| stm32_speech_commands | 4 | 无许可证 | ❌ | ~100KB | ~60KB | <50ms | ❌ | ⭐⭐⭐⭐ |
| **libfvad (VAD)** | 🥇VAD | BSD-3 | ✅语言无关 | <30KB | <5KB | <0.5ms | ✅手动加入 | ⭐ |

---

## 五、xiaozhi-sf32 的 VAD + KWS 完整流程（源码分析）

### 5.1 初始化流程

```
kws_start()
  → et_bsp_ctrl_main_init()
      ├── 加载关键词表（et_keyword.c → et_kws_cfg.m_kws_param_buf）
      ├── 加载权重（et_weights.c → et_kws_cfg.weightFile）
      ├── 设置 VAD 参数（set_et_ui_soft_vad()）
      │     et_hm_db_max_thd = 41×2    // 触发 VAD 的能量阈值（约 41dB）
      │     et_vad_prob_thd = 0
      │     et_hm_no_cnt_max = 24      // 24 帧（960ms）无声则复位状态
      ├── et_asr_wakeup_first_init(&cfg, et_func_kws_event)  // 注册回调
      └── et_start_asr()                                      // 启动推理状态机
```

### 5.2 运行时数据流

```
PDM 麦克风
  → audio_server DMA 回调（每次 320 samples = 20ms @ 16kHz）
  → app_audio_record_callback()
      ├── 累积到 640 samples（两次回调 = 40ms）
      └── rt_event_send(KWS_EVT_DECODE)

kws_thread 线程
  → 收到 KWS_EVT_DECODE
  → et_asr_wakeup_buf_write(pcm, 640, ...)
      │
      │  ── ET-ASR 内部处理 ────────────────────────────────────────
      │  Step 1: 软 VAD（ET_ASR_UI_VAD_TYPE=HUMAN_VOL）
      │    ├── 计算帧能量（dB）→ 与 et_hm_db_max_thd(41dB) 比较
      │    ├── 静音 → 跳过推理（节省算力）
      │    └── 有声 → 继续 Step 2
      │  Step 2: KWS 推理（DS-CNN/LSTM，NNACC 加速）
      │    ├── MFCC 特征提取（硬件 FFT）
      │    ├── 神经网络推理 → 各关键词得分
      │    └── score > threshold（et_keyword.c 中每词单独配置）
      │  Step 3: 后处理
      │    ├── 连续帧投票（et_wake_times_thd=4，需 4 帧≈160ms 确认）
      │    └── 触发 et_func_kws_event(event_id)
      │  ─────────────────────────────────────────────────────────
      │
      └── 若检测到唤醒词
          → simulate_button_pressed()   // 模拟按键
          → 主应用切换到 Listening 状态
```

### 5.3 ET-ASR VAD 类型对比

| VAD 类型 | 判断方式 | 适用场景 |
|---------|---------|---------|
| `ET_VAD_TYPE_PURE_DB` | 纯能量阈值 | 安静环境 |
| `ET_VAD_TYPE_HUMAN_VOL`（xiaozhi-sf32 使用）| 能量 + 人声音量特征 | 一般噪声环境 |
| `ET_VAD_TYPE_HUMAN_FEATURE` | 能量 + 完整声学特征 | 嘈杂环境（算力较高）|

---

## 六、Zephyr 移植架构（libfvad + TFLM）

### 6.1 整体架构

```
┌──────────────────────────────────────────┐
│  PDM/I2S Driver → DMA → ring_buf         │
│  640 samples/帧（40ms @ 16kHz）持续运行   │
└─────────────────┬────────────────────────┘
                  │ DMA 中断 → 唤醒 kws_thread
                  ▼
┌──────────────────────────────────────────┐
│  libfvad VAD Gate                        │
│  fvad_process(vad, pcm, 480)             │
│  = 0(静音) → 跳过（CPU 回 Sleep）         │
│  = 1(人声) → 继续推理                    │
└─────────────────┬────────────────────────┘
                  │ 仅在有声时推理
                  ▼
┌──────────────────────────────────────────┐
│  TFLM KWS 推理                           │
│  tflite::MicroInterpreter::Invoke()      │
│  输入：MFCC spectrogram（int8）           │
│  输出：各关键词得分 → score > threshold  │
│  → k_event / k_work 通知主线程           │
└─────────────────┬────────────────────────┘
                  ▼
         主应用（xiaozhi 协议层）
         WebSocket → 音频流上传
```

### 6.2 libfvad vs ET-ASR 内置 VAD

| 特性 | ET-ASR 内置 VAD | libfvad |
|------|----------------|---------|
| 算法 | 能量 + 人声音量特征（专用模型）| WebRTC GMM（频谱 + 能量）|
| 精度 | 高（中文语音调优）| 中（通用）|
| RAM | 含在 ET-ASR 中 | <5KB 独立 |
| 参数调节 | 结构体配置 | `fvad_set_mode(0~3)` |
| 移植 | 不可移植（闭源绑定）| ✅ 极易（纯 C，零依赖）|

---

## 七、中文唤醒词训练方案

### 哪些组件需要训练？

| 组件 | 是否需要训练 | 原因 |
|------|-----------|------|
| **KWS**（关键词识别）| ✅ 需要 | 神经网络必须见过你的唤醒词才能识别 |
| **VAD**（人声检测）| ❌ 不需要 | 通用算法，检测"有没有人声"，语言无关 |
| **AEC**（回声消除）| ❌ 不需要 | 信号处理算法，不是学习模型 |

### 方案对比

| 方案 | 难度 | 需要 GPU | 时间 | 推荐度 |
|------|------|---------|------|--------|
| Edge Impulse（网页平台）| ⭐ | 不需要 | 1~2 天 | 🥇 |
| ML-KWS-for-MCU 本地训练 | ⭐⭐⭐ | 需要 | 1~2 周 | 🥈 |
| 第三方闭源库（讯飞/ET-ASR）| ⭐ | 不需要 | 1~3 天 | 仅商用 |

### 方案一：Edge Impulse（首选）

**无需本地 GPU / Python 环境**

**第一步：采集语料**

自己录制即可，不需要下载预置数据集。

| 录制情况 | 效果 |
|---------|------|
| 只有 1 人 × 100 条 | 训练集准确率高，**换别人说则完全不识别**（严重过拟合）|
| 5~10 人，每人 20 条 | 泛化能力大幅提升 |
| 20+ 人，每人 10 条 | 接近可用水平，达 85~90% |

**实际操作**：找 5~10 个人（男女各半），在安静 + 轻微噪声两种环境各录 10~15 条，加上背景噪声标签，Edge Impulse 自动做数据增强（加噪/变速/音调偏移）扩充到 100~200 条。

录制要求：**16kHz / 16-bit / 单声道 / 每条约 1 秒**

**第二步：训练**

```
1. 注册 https://edgeimpulse.com → 创建 Keyword Spotting 项目
2. 网页端录制 / 上传 WAV → 标注关键词（如"小智小智"）和"noise"
3. 设计 Impulse：输入 16kHz 1s / 特征 MFCC / 模型 DS-CNN
4. 云端训练（约 5~15 分钟）
```

**第三步：部署（生成 C 数组）**

训练完成后，点击 Deployment → 选择 **Zephyr Module**（推荐）：

```
Zephyr Module   ← 已配好 CMakeLists.txt + Kconfig，west.yml 引用即可使用
C++ Library     ← 通用 C++ SDK + 模型，需手动写 CMakeLists，适合非 west 工程
```

下载 ZIP 解压后包含：
```
edge-impulse-sdk/        // 推理框架
model-parameters/
    model_metadata.h
tflite-model/
    tflite_learn_6_compiled.cpp  // 模型以 C 数组形式内嵌：
                                 // const unsigned char model[] = { 0x1c, ... };
CMakeLists.txt           // Zephyr Module 专用，直接引用
Kconfig
```

在 `west.yml` 中加入：
```yaml
- name: ei-model
  url: https://github.com/你的账号/ei-kws-model   # 把解压的 ZIP 目录 push 到这里
  revision: main
  path: modules/ei-model
```

然后在 `app/CMakeLists.txt` 中：
```cmake
find_package(Zephyr REQUIRED HINTS $ENV{ZEPHYR_BASE})
add_subdirectory(${ZEPHYR_BASE}/../modules/ei-model ei)
target_link_libraries(app PRIVATE ei)
```

**第四步：应用层调用**

Edge Impulse SDK 对外暴露一个统一接口，不需要手写模型加载：
```c
#include "edge-impulse-sdk/classifier/ei_run_classifier.h"

// 推理：传入 16kHz int16 音频帧
signal_t signal;
numpy::signal_from_buffer(pcm_buf, 16000, &signal);

ei_impulse_result_t result;
run_classifier(&signal, &result, false);

// 读取各关键词得分
for (int i = 0; i < EI_CLASSIFIER_LABEL_COUNT; i++) {
    if (result.classification[i].value > 0.8f) {
        // 检测到关键词 ei_classifier_inferencing_categories[i]
    }
}
```

**部署选项对比（重要）**：

| 部署选项 | 模型格式 | 能否用于 NuMaker（TFLM）| 推理 API |
|---------|---------|----------------------|---------|
| **C++ Library**（推荐）| 标准 `.tflite` flatbuffer C 数组 | ✅ 可直接使用 | `MicroInterpreter::Invoke()` |
| **Zephyr Module** | EI 编译格式 C 数组（非标准 TFLite）| ❌ TFLM 无法解析 | `run_classifier()` |

> **最干净的组合**：Edge Impulse 训练 → 部署选 **C++ Library** → 拿到 `.tflite` → 转 C 数组 → 放入 NuMaker 工程替换原来的英文模型，整个工程都是 TFLM 体系。
>
> **注意**：选 **Zephyr Module** 时，模型是 EI 专有编译格式（非标准 TFLite），**不能直接给 NuMaker 的 TFLM 使用**。只有 C++ Library 导出的 `.tflite` 才是标准格式，TFLM 可以解析。

**完整流程（Edge Impulse → NuMaker）**：
```
1. Edge Impulse 录音 + 训练（网页端，5~15 分钟）
2. 部署 → 选 C++ Library → 下载 ZIP
3. 解压找到 model.tflite

4. 转 C 数组（Linux/macOS）：
   xxd -i model.tflite > model_data.cc
   
   Windows 替代（Python）：
   python -c "
   data=open('model.tflite','rb').read()
   open('model_data.cc','w').write(
     'const unsigned char model[]={'+','.join(f\"0x{b:02x}\" for b in data)+
     f\"}};\nconst int model_len={len(data)};\n\")"

5. 将 model_data.cc 放入 NuMaker 工程，替换原英文模型文件
6. west build → 固件中包含中文唤醒词模型
7. TFLM 调用 MicroInterpreter::Invoke() 推理
```

`xxd` 是 Linux/macOS 自带工具，作用是把二进制文件转成 C 十六进制数组。Windows 下用 WSL 或上面的 Python 脚本替代。

---

### 方案二：ML-KWS-for-MCU 本地训练

**需要 TensorFlow 1.x + GPU（推荐 Docker）**

语料准备同上（建议 500~1000 条/词，多人录制）。

```bash
# docker pull tensorflow/tensorflow:1.15.5-py3
python train.py \
  --model_architecture ds_cnn \
  --wanted_words "小智小智,你好小康,noise,silence" \
  --data_dir /path/to/dataset

# 量化为 INT8 TFLite
python quant_models.py --model_architecture ds_cnn \
  --checkpoint /path/to/checkpoints

# 生成 C 数组
xxd -i model.tflite > model_data.cc
# 产物：
# unsigned char model_tflite[] = { 0x1c, 0x00, ... };
# unsigned int model_tflite_len = 12345;
```

在代码中引用：
```c
#include "model_data.cc"  // 或加入 CMakeLists
// 使用：tflite::MicroInterpreter(model_tflite, ...)
```

难点：TF 1.x 环境繁琐；中文语料需词边界标注；量化精度损失需调参。

---

### 方案三：第三方闭源库

| 厂商 | 产品 | 说明 |
|------|------|------|
| 科大讯飞 | 离线唤醒 SDK | 商用收费 |
| 宇极科技（ET-ASR）| 同 xiaozhi-sf32 使用 | SF32 专版，需联系 |
| 思必驰 | DUI 离线 SDK | 商用收费 |

### 语料数量建议

| 每词数量 | 预期准确率 |
|---------|-----------|
| 50~100 条（1 人）| 70~80%，严重过拟合，仅供测试 |
| 100~200 条（5~10 人）| 85~90%（Edge Impulse 可用下限）|
| 500+ 条（20+ 人）| 90~95%（生产级）|
| 1000+ 条 | 95%+（接近 ET-ASR 水平）|

---

## 八、AEC（回声消除）

### 问题背景

设备播放 TTS 时，扬声器声音被麦克风重新拾取（回声）→ VAD 误判有人声 → KWS 误触发。

### xiaozhi-sf32 的做法

```c
et_asr_set_ignore(1);   // 播放 TTS 前，完全暂停 KWS
// ... 播放 TTS ...
et_asr_set_ignore(0);   // 播放结束后，恢复 KWS
```

**代价**：不支持打断唤醒（边播边听）。设备说话时无法响应唤醒词。

### 三种策略

| 策略 | 打断唤醒 | 额外 RAM | 移植难度 | 说明 |
|------|---------|---------|---------|------|
| **静音推理**（同 xiaozhi-sf32）| ❌ | ~0 | ⭐ | 播放时 `k_event` 挂起 KWS 线程 |
| **SpeexDSP AEC** | ✅ | 50~100KB | ⭐⭐⭐ | 需要扬声器参考 PCM，手动移植 |
| **硬件 AEC**（芯片集成）| ✅ | ~0 | ⭐ | 依赖芯片支持 |

### SpeexDSP AEC 详情

| 维度 | 详情 |
|------|------|
| 仓库 | https://github.com/xiph/speexdsp |
| 协议 | BSD-style（商用友好）|
| 语言 | 纯 C（94%），⭐685 |
| 算法 | NLMS/MDF 频域回声消除 |
| RAM | 50~100KB（随 `filter_length` 变化）|
| Flash | ~80KB |
| CM33 速度 | 每帧（20ms）约 2~10ms @ 240MHz |
| Zephyr 官方支持 | ❌ 无内置模块，手动加入 CMakeLists |

```c
// 初始化
SpeexEchoState *echo = speex_echo_state_init(160, 2048);  // 10ms帧, filter_length=2048

// 每帧调用（ref_pcm = 当前播放的 PCM，需与 mic 帧对齐）
speex_echo_cancellation(echo, mic_pcm, ref_pcm, output_pcm);
```

**移植难点**：I2S TX（播放）与 PDM RX（采集）的参考 PCM 帧对齐，需要在 Zephyr 音频框架中额外处理。

### 建议路径

1. 先实现**静音推理**，验证基本功能
2. 若需打断唤醒，引入 SpeexDSP AEC（`filter_length=1024`，RAM ~50KB）
3. 若芯片支持硬件 AEC，优先使用

---

## 九、功耗与 CPU 占用分析

### PDM Mic 是否需要持续工作？

**是**。PDM/I2S 麦克风必须持续 DMA 采集，否则会漏掉唤醒词。DMA 搬运数据，CPU 无参与，麦克风功耗极低。

### CPU 是否需要全速运行？

**否**。CPU 大部分时间在 Sleep，只在 DMA 中断时被唤醒执行推理：

```
DMA 持续传输（CPU 无参与）
    ↓ 每 20ms DMA 中断 → 唤醒 kws_thread
kws_thread 执行：AEC（可选）→ VAD → [有声?] → KWS 推理
    ↓ 执行完毕（约 5~50ms，取决于推理时长）
CPU 进入 WFI Sleep → 等待下一次 DMA 中断
```

**VAD 门控是关键**：安静环境中大多数帧返回"静音"，跳过 KWS 推理，CPU 占用率极低（ET-ASR 的 `et_hm_no_cnt_max=24` 参数即为此设计）。

### Zephyr 低功耗选项

| 机制 | 说明 | 是否需修改 KWS 代码 |
|------|------|-----------------|
| `CONFIG_PM=y` + WFI | CPU 空闲自动 Sleep，DMA 中断唤醒 | ❌ 无需修改 |
| `CONFIG_PM_DEVICE=y` | 非必要外设（UART 等）静默期挂起 | ❌ |
| 降频运行 | 推理期间降至 ~100MHz | ⚠️ 需验证时序 |
| PDM 持续 DMA | 麦克风全程 DMA，CPU 不参与 | ❌ 默认行为 |

**平均功耗**主要由推理时长占帧周期（20ms）的比例决定。VAD 门控后，有声帧占比通常 <20%，KWS 推理对平均功耗的影响大幅降低。
