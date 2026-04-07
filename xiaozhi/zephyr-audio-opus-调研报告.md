# Zephyr Audio 子系统与 Opus 编解码器调研报告

> 调研日期：2026-04-06  
> 调研人：GitHub Copilot  
> 调研范围：本地 Zephyr 仓库（v4.3.0-dev）+ 官方在线文档 + niu 项目模块

---

## 一、Zephyr 是否有统一的 Audio 子系统

### 结论：**没有**类似 Linux ALSA 那样的统一 Audio 子系统，但提供了分层的 Audio 驱动抽象

Zephyr 没有一个叫做 `subsys/audio/` 的子系统目录（`zephyr/subsys/` 里没有 audio 条目）。  
音频功能由以下几个**独立的驱动层 API** 组成，每一层都有自己的抽象接口：

| 层级 | API 位置 | Kconfig | 说明 |
|------|----------|---------|------|
| Audio Codec | `include/zephyr/audio/codec.h` | `CONFIG_AUDIO_CODEC` | 屏蔽底层传输（I2S/PCM）的最高层抽象 |
| DMIC | `include/zephyr/audio/dmic.h` | `CONFIG_AUDIO_DMIC` | PDM 数字麦克风抽象接口 |
| I2S 驱动 | `include/zephyr/drivers/i2s.h` | `CONFIG_I2S` | 原始 I2S 总线读写 |
| DAI 驱动 | `drivers/dai/` | `CONFIG_DAI` | Intel/NXP 芯片的数字音频接口 |
| MIDI | `include/zephyr/audio/midi.h` | — | MIDI 2.0 UMP 报文定义（v4.1 新增） |

---

## 二、Audio Codec API——ADC/DAC/I2S 差异屏蔽层

### 2.1 设计目标

`zephyr/audio/codec.h`（自 Zephyr 1.13 引入，v0.2.0）是目前**最接近硬件无关**的音频抽象层。  
它将底层传输协议（I2S、PCM-A、PCM-B 等）封装在 `audio_dai_type_t` 枚举里，应用只需面向 `struct device` 编程，**切换底层硬件时只需修改设备树（DTS）和 Kconfig，应用代码无需改动**。

### 2.2 支持的 DAI 类型（`audio_dai_type_t`）

```c
typedef enum {
    AUDIO_DAI_TYPE_I2S,              // 标准 I2S
    AUDIO_DAI_TYPE_LEFT_JUSTIFIED,   // 左对齐 I2S
    AUDIO_DAI_TYPE_RIGHT_JUSTIFIED,  // 右对齐 I2S
    AUDIO_DAI_TYPE_PCMA,             // PCM 变体 A
    AUDIO_DAI_TYPE_PCMB,             // PCM 变体 B
    AUDIO_DAI_TYPE_PCM,              // PCM
    AUDIO_DAI_TYPE_INVALID,
} audio_dai_type_t;
```

### 2.3 核心 API

```c
// 配置 codec（传入 dai_type，驱动内部处理 I2S/PCM 差异）
int audio_codec_configure(const struct device *dev, struct audio_codec_cfg *cfg);

// 启动/停止播放、录音
void audio_codec_start_output(const struct device *dev);
void audio_codec_stop_output(const struct device *dev);
int audio_codec_start(const struct device *dev, audio_dai_dir_t dir);  // v0.2.0 新增
int audio_codec_stop(const struct device *dev, audio_dai_dir_t dir);

// 设置属性（音量、静音等）
int audio_codec_set_property(const struct device *dev,
                             audio_property_t property,
                             audio_channel_t channel,
                             audio_property_value_t val);

// 非阻塞写入 PCM 数据（DMA 驱动，TX Done 通过回调通知）
int audio_codec_write(const struct device *dev, uint8_t *data, size_t data_size);

// 注册 TX/RX 完成回调（ISR 上下文，需保持简短）
int audio_codec_register_done_callback(const struct device *dev,
    audio_codec_tx_done_callback_t tx_cb, void *tx_cb_user_data,
    audio_codec_rx_done_callback_t rx_cb, void *rx_cb_user_data);
```

### 2.4 应用代码示例（来自本仓库 `zephyr/samples/drivers/audio/codec/`）

```c
#include <zephyr/audio/codec.h>

struct audio_codec_cfg cfg = {
    .dai_type = AUDIO_DAI_TYPE_PCM,   // 切换为 I2S 只需改这一行
    .dai_cfg.pcm.dir        = AUDIO_DAI_DIR_TX,
    .dai_cfg.pcm.pcm_width  = AUDIO_PCM_WIDTH_16_BITS,
    .dai_cfg.pcm.channels   = 1,
    .dai_cfg.pcm.block_size = 320,
    .dai_cfg.pcm.samplerate = AUDIO_PCM_RATE_16K,
};

const struct device *dev = DEVICE_DT_GET(DT_ALIAS(codec0));
audio_codec_configure(dev, &cfg);
audio_codec_start_output(dev);
audio_codec_write(dev, pcm_buf, 320);
```

> **重要结论**：只要底层 codec 驱动实现了 `audio_codec_api`，**应用层切换 I2S/PCM/ADC/DAC 底层，只需更改 DTS 中的绑定型号和 `dai_type` 字段，应用逻辑代码（configure/write/callback）完全不变**。

### 2.5 与原始 I2S API 的对比

| 维度 | 原始 I2S API（`drivers/i2s.h`） | Audio Codec API（`audio/codec.h`） |
|------|-------------------------------|-----------------------------------|
| 抽象层级 | 总线级（环形缓冲区 + trigger） | 设备级（write + callback） |
| 切换底层硬件 | 需修改应用代码 | 仅改 DTS/Kconfig |
| 数字麦克风 | 不支持 | 通过 DMIC 子接口支持 |
| 音量/静音控制 | 不支持 | 内置 `set_property` |
| 支持的 DAI 类型 | 仅 I2S | I2S + PCM-A/B + PCM |
| DMA | 手动管理 | 驱动透明管理 |

### 2.6 已支持的 Codec 驱动（`drivers/audio/Kconfig`）

本地仓库中已有驱动：
- `TLV320DAC310x`（Texas Instruments）
- `TLV320AIC3110`
- `CS43L22`（ST）
- `DA7212`（Dialog Semiconductor）
- `MAX98091`
- `PCM1681`
- `WM8904 / WM8962`（Wolfson/Cirrus Logic）
- `AW88298`（Awinic）
- `TAS6422DAC`
- **`SF32LB Codec`**（本仓库使用的 Sifli SF32LB 片上 codec，`Kconfig.sf32lb`）

---

## 三、Opus 编解码器支持

### 3.1 结论：**已支持 libopus 1.4**，通过独立 west 模块集成

Zephyr 官方主仓库**本身不包含 Opus 源代码**，但通过 west 模块机制允许外部集成。  
本项目（niu）已完整集成 libopus 1.4，路径如下：

```
modules/opus-1.4/          ← libopus 1.4 源码（west 拉取自 Solar936/opus-1.4）
niu/modules/opus/
  ├── CMakeLists.txt        ← Zephyr 集成构建脚本
  ├── Kconfig               ← 提供 CONFIG_OPUS 选项
  └── config.h              ← 固定点 ARM 构建配置
```

### 3.2 构建配置（固定点 ARM 版本）

| 编译宏 | 值 | 说明 |
|--------|-----|------|
| `FIXED_POINT` | 1 | 使用定点运算（无需 FPU） |
| `DISABLE_FLOAT_API` | 1 | 禁用浮点 API |
| `OPUS_BUILD` | 1 | 内部构建标志 |
| `VAR_ARRAYS` | 1 | 使用可变长数组（代替 alloca） |

### 3.3 启用方式

在 `prj.conf` 中添加：

```kconfig
CONFIG_OPUS=y
```

### 3.4 使用示例

```c
#include <opus.h>

int err;
OpusEncoder *enc = opus_encoder_create(16000, 1, OPUS_APPLICATION_VOIP, &err);
OpusDecoder *dec = opus_decoder_create(16000, 1, &err);

// 编码（输入 PCM，输出压缩包）
opus_encode(enc, pcm_in, frame_size, opus_out, max_out_bytes);

// 解码
opus_decode(dec, opus_in, opus_len, pcm_out, frame_size, 0);
```

### 3.5 west.yml 引用

```yaml
- name: opus-1.4
  remote: solar936-github
  revision: main
  path: modules/opus-1.4
```

模块通过 `niu/modules/opus/` 中的 `CMakeLists.txt` + `Kconfig` 注入 Zephyr 构建系统，`module.yml` 声明了 `cmake-ext: true` 和 `kconfig-ext: true`，由 `niu/modules/modules.cmake` 自动扫描注册。

---

## 四、整体架构图（针对小智语音应用）

```
应用层（小智语音逻辑）
        │
        │  audio_codec_configure / audio_codec_write / opus_encode/decode
        │
┌───────┴──────────────────────────────────┐
│       Audio Codec API                     │  ← zephyr/audio/codec.h（硬件无关）
└───────┬──────────────────────────────────┘
        │
        ├──── SF32LB 片上 codec 驱动（sf32lb_codec.c）   ← 当前使用
        ├──── I2S Codec 驱动（如 WM8904、TLV320...）     ← 切换时只改 DTS
        └──── DMIC 驱动（dmic_nrfx_pdm.c 等）            ← PDM 麦克风

底层总线（对应用透明）
  I2S / PCM-A / PCM-B / SAI / ...
```

---

## 五、关键结论与建议

1. **Zephyr 无统一 Audio 子系统**（不像 Linux ALSA），但 `Audio Codec API` 是目前最好的硬件无关抽象层，能屏蔽 I2S/PCM/ADC/DAC 差异。

2. **切换底层硬件的正确方法**：
   - 在设备树中更换 codec 绑定节点（`compatible = "vendor,codecXXX"`），并添加 `codec0` alias
   - 修改 `dai_type` 为对应的接口类型（`AUDIO_DAI_TYPE_I2S` / `AUDIO_DAI_TYPE_PCM` 等）
   - **应用代码无需修改**

3. **Opus 已完整集成**：固定点 ARM 版本，无 FPU 依赖，适合 Cortex-M 系列。启用只需 `CONFIG_OPUS=y`。

4. **注意**：Audio Codec API 的设计目标是**外置 codec 芯片**（通过 I2S/PCM 与 MCU 连接），对于纯粹的片上 ADC/DAC，Zephyr 提供的是独立的 `adc.h` / `dac.h` API，这两者**不在** Audio Codec 框架内，切换时仍需修改代码。若需完全屏蔽 ADC/DAC 与 I2S 的差异，建议在应用层自己封装一个统一采集/播放接口。

---

## 六、参考链接

- [Zephyr Audio Codec API 文档](https://docs.zephyrproject.org/latest/hardware/peripherals/audio/codec.html)
- [Zephyr I2S API 文档](https://docs.zephyrproject.org/latest/hardware/peripherals/audio/i2s.html)
- [Zephyr DMIC API 文档](https://docs.zephyrproject.org/latest/hardware/peripherals/audio/dmic.html)
- [Zephyr DAI 文档](https://docs.zephyrproject.org/latest/hardware/peripherals/audio/dai.html)
- [libopus 官网](https://opus-codec.org/)
- 本仓库 Audio Codec 示例：`zephyr/samples/drivers/audio/codec/`
- 本仓库 Opus 集成：`niu/modules/opus/CMakeLists.txt`
