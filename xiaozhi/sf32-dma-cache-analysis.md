# SF32LB52 DMA Cache 一致性问题调研报告

> 本文档涵盖三次分析迭代：问题定位、根因解释、最终修复方案与实施。

---

## 一、硬件背景

SF32LB52 搭载 Cortex-M33，具有写回（write-back）D-Cache，Cache Line 大小为 **32 字节**。DMA 控制器直接挂在 AHB 总线上，完全绕过 CPU Cache，只能看到物理 RAM 上的内容。

### 32 字节 Cache Line 的依据

这个值来自两个层面：

**① ARM 架构规格**：Cortex-M33 的 D-Cache line size 是 **32 字节（8 words × 4B）**，这是该 IP 的硬件固定值，由 ARM 公司在 Cortex-M33 Technical Reference Manual 中定义。Cortex-A53 是 64B，服务器级 Neoverse 可达 128B，但 Cortex-M33 始终是 32B。

**② Zephyr Kconfig 确认**：Zephyr 在 `arch/arm/core/cortex_m/Kconfig` 中为所有 Cortex-M CPU 设置：
```
config DCACHE_LINE_SIZE
    default 32
```
构建产物在 `build/zephyr/include/generated/zephyr/autoconf.h` 中确认：
```c
#define CONFIG_DCACHE_LINE_SIZE 32
```
`.config-trace.json` 显示此值直接从 Cortex-M 的 Kconfig 默认值继承，SF32LB52 SoC 没有覆盖此默认值（`CONFIG_CPU_CORTEX_M33=y`）。

因此代码中**不应使用字面量 `32`**，而应使用 `CONFIG_DCACHE_LINE_SIZE`，与 Zephyr 本身的以太网、USB 等驱动保持一致，且在平台移植时自动适配。

```
CPU Core
   │
   ├── [D-Cache, 32字节 Cache Line, 写回模式]
   │         │
   │    (命中时不走总线)
   │         │
   └─────────┴──── AHB 总线 ────┬──── SRAM (物理 RAM)
                                │
                          DMA Controller ──── 外设 (UART/DAC/ADC)
```

**写回 Cache 行为**：CPU 写内存时数据先进 Cache，不立即写回 RAM（标记为 dirty）；CPU 读命中 Cache 时也不访问 RAM。这是所有问题的根源。

---

## 二、TX 方向（CPU→DMA→外设）异常详解

### 发生步骤

```
Step 1: CPU 执行 memcpy(tx_buf, "AT+CIMI\r\n")
        [Cache Line for tx_buf] = "AT+CIMI\r\n"    ← 数据在 Cache
        [RAM for tx_buf]        = "AT+CLCK\r\n"    ← RAM 还是旧数据

Step 2: CPU 调用 uart_tx() / codec_write()
        DMA 配置：源地址 = tx_buf 物理地址

Step 3: DMA 开始传输，读 RAM[tx_buf] → "AT+CLCK\r\n"（旧数据！）
        DMA 把旧数据送给 UART TX FIFO / DAC
        调制解调器收到错误命令 / 喇叭播出上一帧音频

Step 4: 某时刻 Cache Line 被驱逐，新数据才写回 RAM
        但 DMA 早已传完，为时已晚
```

**需要在 DMA 启动前做** `sys_cache_data_flush_range()`。

---

## 三、RX 方向（外设→DMA→CPU）异常详解

### 发生步骤

```
Step 1: 系统曾访问过 rx_dma_buf，Cache 中有该地址的快照（旧数据）

Step 2: UART/ADC 数据来了，DMA 写入 RAM[rx_dma_buf] = 新数据
        Cache 不知道，仍持有旧快照

Step 3: DMA 完成，触发回调
        CPU 读 rx_dma_buf → 命中 Cache → 读到旧数据
        AT 解析器/Opus 编码器拿到的是错误内容
```

**需要在回调通知应用前做** `sys_cache_data_invd_range()`。

---

## 四、Cache Line 对齐的重要性

若 buffer 起始地址未对齐到 **`CONFIG_DCACHE_LINE_SIZE`（Cortex-M33 上为 32 字节）**，`sys_cache_data_invd_range()` 会以 Cache Line 为粒度操作，导致相邻变量的 Cache 也被失效（false sharing）：

> **对齐值的来源**：`CONFIG_DCACHE_LINE_SIZE` 是 Zephyr 在 `arch/arm/core/cortex_m/Kconfig` 中为 Cortex-M（含 M33）设置的默认值 32。这是 ARM Cortex-M33 TRM 规定的硬件固定值（8 words × 4B = 32B），与 Cortex-A（64B）或服务器级（128B）不同。代码中使用宏而非字面量，以便平台移植时自动适配。

```
地址:  0   8   16  20  24  32
       [--- 其他变量 ---|--- rx_buf 前12B ---]
       <-------- 同一 Cache Line (32B) ------->

invd 整条 Cache Line → 其他变量的 dirty Cache 数据丢失 → 内存损坏
```

---

## 五、Codec commit 838118e5 分析（TX-only 修复）

该提交在 `drivers/audio/sf32lb_codec.c` 新增了 flush：

```c
// codec_write() — 应用写入 PCM 数据后
sys_cache_data_flush_range(dev_data->tx_write_ptr, dev_data->tx_half_dma_size);

// codec_configure() — 初始化清零 tx_buf 后
sys_cache_data_flush_range(data->tx_buf, data->tx_half_dma_size * 2);
```

**TX 方向修复正确**，但遗漏了以下问题：

| 问题 | 描述 |
|---|---|
| `dma_rx_callback` 无 invd | DMA 写完 ADC 数据通知应用前未失效 Cache，CPU 读到旧录音 |
| `tx_buf`/`rx_buf` 对齐仅 4B | `k_aligned_alloc(sizeof(uint32_t), ...)` = 4字节，Cache Line 需要 32 字节 |

---

## 六、UART 驱动现状（修复前）

`drivers/serial/uart_sf32lb.c` 中：
- `uart_async_sf32lb_tx()`：启动 DMA 前无 flush
- 3 处 `UART_RX_RDY` 触发点（async_rx_idle / dma_rx_done / rx_disable）：均无 invd
- 驱动层完全没有 Cache 维护代码，责任全甩给应用层

---

## 七、为什么 non-cacheable 内存（Shared RAM）没有问题

SF32LB52 DTS 定义了两段被 MPU 标记为 non-cacheable 的内存：

```dts
/* dts/arm/sifli/sf32lb52x-ram012.dtsi */
sram1_shared: memory@20400000 {
    reg = <0x20400000 DT_SIZE_K(64)>;       /* 64 KB */
    zephyr,memory-attr = <DT_MEM_ARM(ATTR_MPU_RAM_NOCACHE)>;
};
sram0_shared: memory@2007fc00 {
    reg = <0x2007fc00 DT_SIZE_K(1)>;        /* 1 KB */
    zephyr,memory-attr = <DT_MEM_ARM(ATTR_MPU_RAM_NOCACHE)>;
};
```

MPU 硬件禁止对这段地址做缓存：

```
普通 SRAM：  CPU → [Cache 可能滞留] → RAM ← DMA  （不一致）
sram1_shared：CPU → AHB → RAM ← DMA              （永远一致）
```

放到 non-cacheable 区域后，CPU 和 DMA 永远看到同一份 RAM 内容，无需任何软件 Cache 维护，是最干净的根治方案。当前两段区域均为空闲，完全够用。

---

## 八、修复方案（驱动层下沉，应用层无感）

### 8.1 修复原则

Cache 维护下沉到驱动层，应用层只需保证 DMA buffer **32 字节对齐**（防止 false sharing），不需要调用任何 `sys_cache_*` 函数。

### 8.2 UART 驱动修复（`drivers/serial/uart_sf32lb.c`）

**新增 `#include <zephyr/cache.h>`**（在 `CONFIG_UART_ASYNC_API` 块内）

**TX flush** — `uart_async_sf32lb_tx()` 中，DMA 启动前：

```c
data->async.tx.buf = buf;
data->async.tx.len = len;

sys_cache_data_flush_range((void *)data->async.tx.buf, data->async.tx.len); /* 新增 */
sf32lb_dma_reload_dt(...);
sf32lb_dma_start_dt(&config->tx_dma);
```

**RX invd** — 3 处 `UART_RX_RDY` 事件发送前，统一在 callback 调用前 invd：

- `async_rx_idle()`（IDLE 中断路径）：irq unlock 后、cb 前
- `uart_sf32lb_dma_rx_done()`（DMA 环绕路径）：cb 前
- `uart_async_sf32lb_rx_disable()`（主动关闭路径）：cb 前

### 8.3 Codec 驱动修复（`drivers/audio/sf32lb_codec.c`）

**对齐修正**（`codec_configure()`）：

```c
/* 修复前 */
k_aligned_alloc(sizeof(uint32_t), data->tx_half_dma_size * 2);  /* 4B 对齐 */

/* 修复后 — 使用 Kconfig 宏而非字面量 32 */
k_aligned_alloc(CONFIG_DCACHE_LINE_SIZE, data->tx_half_dma_size * 2);  /* = 32B on Cortex-M33 */
```

`CONFIG_DCACHE_LINE_SIZE` 由 `autoconf.h` 提供（Zephyr 构建系统自动包含），值来自 `arch/arm/core/cortex_m/Kconfig` 的默认值 32，SF32LB52（Cortex-M33）无覆盖。使用宏而非字面量，在工具链层面有明确出处，也便于未来移植到 Cache Line 不同的平台。

**RX invd**（`dma_rx_callback()`，callback 前调用）：

```c
if (status == DMA_STATUS_COMPLETE) {
    sys_cache_data_invd_range(data->rx_buf + data->rx_half_dma_size,  /* 新增 */
                              data->rx_half_dma_size);
    data->rx_done(dev, data->rx_buf + data->rx_half_dma_size, ...);
} else if (status == DMA_STATUS_HALF_COMPLETE) {
    sys_cache_data_invd_range(data->rx_buf, data->rx_half_dma_size);  /* 新增 */
    data->rx_done(dev, data->rx_buf, ...);
}
```

### 8.4 应用层清理（`samples/drivers/modem/uart_raw_test/src/main.c`）

- 移除 `#include <zephyr/cache.h>`
- 移除 `UART_RX_RDY` 回调中的手动 `sys_cache_data_invd_range()` 调用
- `rx_dma_slab` 保留 32 字节对齐（防止 invd false sharing，非 Cache 维护）

---

## 九、修复后各模块缺陷状态

| 组件 | 方向 | 修复内容 | 状态 |
|---|---|---|---|
| UART 驱动 | TX | `uart_async_sf32lb_tx()` DMA 前 flush | ✅ 已修复 |
| UART 驱动 | RX (IDLE路径) | `async_rx_idle()` callback 前 invd | ✅ 已修复 |
| UART 驱动 | RX (DMA环绕路径) | `dma_rx_done()` callback 前 invd | ✅ 已修复 |
| UART 驱动 | RX (关闭路径) | `rx_disable()` callback 前 invd | ✅ 已修复 |
| Codec 驱动 | TX | `codec_write()` flush（838118e5已有） | ✅ 已有 |
| Codec 驱动 | TX 对齐 | `tx_buf` 从 4B 改为 32B 对齐 | ✅ 已修复 |
| Codec 驱动 | RX | `dma_rx_callback()` callback 前 invd | ✅ 已修复 |
| Codec 驱动 | RX 对齐 | `rx_buf` 从 4B 改为 32B 对齐 | ✅ 已修复 |
| UART 应用 | TX | 无需修改（驱动已处理） | ✅ 透明 |
| UART 应用 | RX | 移除手动 invd | ✅ 已清理 |

---

## 十、备选方案：non-cacheable 内存（更彻底）

若后续想彻底消除 Cache 维护的复杂性：

1. 开启 `CONFIG_NOCACHE_MEMORY=y`
2. 阶段性将 DMA buffer 放到 `sram1_shared`（64KB, 0x20400000）
3. 驱动内部 `tx_buf`/`rx_buf` 用 `K_HEAP_DEFINE_NOCACHE` 分配
4. 应用 `rx_dma_slab` 用 `__nocache` 标记
5. 所有 `sys_cache_*` 调用均可删除

代价仅是 non-cacheable 区域 CPU 访问慢约 5-10x，对于 DMA buffer 影响可忽略不计。

---

## 十一、通俗解释：应用的 cacheable buffer 是如何安全传输的

### 核心问题

> 应用层有一块 64 字节要发送、一块 64 字节用来接收，这两块内存都是普通 cacheable 的。数据是怎么发出去、收进来的？驱动做了什么让它们不出问题？

答案的关键在于 **UART 驱动** 和 **Codec 驱动** 采用了完全不同的缓冲策略。

---

### UART 驱动：无内部缓冲，应用 buffer 直接给 DMA

UART 驱动不持有任何自己的数据缓冲区，应用的 buffer **就是** DMA 的源/目标，没有任何中间拷贝。

#### UART TX（发送 64 字节）

```
应用层                   UART 驱动（修复后）              硬件
─────────────────────────────────────────────────────────────────
uint8_t tx_buf[64];      ┌──────────────────────────┐
填好 tx_buf 内容          │ uart_async_sf32lb_tx()    │
                         │                          │
uart_tx(uart,            │ 1. 记录 buf 指针和长度    │
        tx_buf, 64, -1) ─►                          │
                         │ 2. flush(tx_buf, 64)     │  ← 把 Cache 里的
                         │    ↓ Cache 写回 RAM       │    数据强制刷入 RAM
                         │                          │
                         │ 3. 配置 DMA：             │
                         │    源 = tx_buf 物理地址   │
                         │                          │
                         │ 4. 启动 DMA              ─►  DMA 读 RAM[tx_buf]
                         └──────────────────────────┘   → UART TX FIFO
                                                         → 发给调制解调器
```

**关键点**：在 `uart_tx()` 内部，驱动已经替应用做了 flush，把 Cache 中还未写回的数据强制刷入 RAM，之后 DMA 从 RAM 读到的一定是应用刚写的新数据。应用不需要关心这件事。

#### UART RX（接收 64 字节）

```
应用层                   UART 驱动（修复后）              硬件
─────────────────────────────────────────────────────────────────
/* 提供 256 字节 DMA buffer */
uint8_t rx_dma_buf[256]; ┌──────────────────────────┐
RING_BUF_DECLARE(rx_rb,  │ uart_rx_enable()          │
        2048);           │                          │
                         │ 配置 DMA：               │
uart_rx_enable(uart,     │   目标 = rx_dma_buf      │
  rx_dma_buf, 256) ─────►│           物理地址        │
                         └──────────────────────────┘
                                    ↑ UART 收到数据
                                    │
                             DMA 写入 RAM[rx_dma_buf]
                             注意：Cache 里还是旧数据！
                                    │
                         ┌──────────▼───────────────┐
                         │ UART_RX_RDY 事件触发      │
                         │                          │
                         │ 驱动先 invd(rx_dma_buf,  │  ← 丢弃 Cache 中的
                         │          offset, len)    │    旧快照
                         │                          │
                         │ 再调用应用回调            ─►  应用回调里
                         └──────────────────────────┘   从 rx_dma_buf 读
                                                         读到最新数据

    /* 应用回调里把数据放到 ring buffer */
    ring_buf_put(&rx_rb, rx_dma_buf + offset, len);
    /* rx_rb 里的 64 字节完全是 CPU 操作，无 DMA，无需 cache 处理 */
```

**关键点**：驱动在通知应用之前已经做了 invd，丢弃了 Cache 中的旧快照，CPU 下次读 `rx_dma_buf` 时会直接从 RAM 拿 DMA 写入的新数据。应用从 ring buffer 取出的那 64 字节，是纯 CPU 操作，完全不涉及 DMA，cacheable 没有任何问题。

---

### Codec 驱动：有内部缓冲，应用 buffer 不接触 DMA

Codec 驱动**内部维护两个双缓冲区**（`tx_buf` 和 `rx_buf`），DMA 只操作这两块驱动内部的内存。应用的 buffer 只做 CPU 层面的数据搬运，**DMA 永远不知道应用 buffer 的存在**。

```
驱动内部内存（cacheable, 32字节对齐）：
  tx_buf[block_size * 2]   — DMA 持续从这里读 → DAC 播放
  rx_buf[block_size * 2]   — DMA 持续把 ADC 数据写到这里
```

#### Codec TX（应用写入 64 字节 PCM 数据，驱动播放）

```
应用层                   Codec 驱动（修复后）             DMA 层
─────────────────────────────────────────────────────────────────
uint8_t pcm[64];         驱动内部: tx_buf[2N]
(CPU 填好音频数据)         DMA 持续循环从 tx_buf 读 → DAC

codec_write(dev,          ┌──────────────────────────┐
            pcm, 64) ───►│ 1. memcpy(tx_write_ptr,  │  ← CPU 读 pcm（Cache
                         │          pcm, 64)         │    命中，读到最新数据）
                         │                          │    CPU 写 tx_buf（进
                         │                          │    Cache，RAM 暂时旧）
                         │ 2. flush(tx_write_ptr,   │  ← 把 tx_buf 的 Cache
                         │          block_size)      │    强制写回 RAM
                         └──────────────────────────┘
                                                         DMA 下次读 tx_buf
                                                         拿到的是新音频数据
                                                         → DAC → 喇叭
```

**为什么 memcpy 本身不存在一致性问题？**

memcpy 是纯 CPU 操作，CPU 读 `pcm` 和写 `tx_buf` 都经过同一个 D-Cache。Cache 一致性问题只发生在 **CPU ↔ DMA** 的边界，而不是 CPU 内部的读写之间：

- CPU 读 `pcm`：CPU 自己之前写的，数据就在 Cache 里，直接命中，读到最新值 ✅
- CPU 写 `tx_buf`：数据进 Cache（RAM 暂时旧）→ 之后 DMA 要读 RAM ⚠️ → 需要 flush ✅

**flush 的是驱动内部的 `tx_buf`，不是应用的 `pcm`。** 应用的 `pcm` 全程只被 CPU 读写，永远不会有一致性问题，cacheable 完全没问题。

#### Codec RX（DMA 录音，应用读取 64 字节 PCM 数据）

```
硬件/DMA 层              Codec 驱动（修复后）             应用层
─────────────────────────────────────────────────────────────────
ADC 采样数据             驱动内部: rx_buf[2N]
DMA 持续写入 rx_buf      (应用登记了 rx_done 回调)
                                    │
DMA 完成半块             ┌──────────▼───────────────┐
  → dma_rx_callback()   │ 1. invd(rx_buf 对应半块) │  ← 丢弃 Cache 旧快照
                         │                          │    CPU 下次读走 RAM
                         │ 2. rx_done(dev,          │    拿 DMA 写的新数据
                         │      ptr, size, ...) ────►│
                         └──────────────────────────┘
                                                         应用回调被触发
                                                         ptr 指向 rx_buf
                                                         的某半块（驱动内部）

    /* 应用把数据复制到自己的 buf */
    uint8_t my_buf[64];
    memcpy(my_buf, ptr, 64);
    /* CPU 读 rx_buf 时走 RAM（已 invd），读到 DMA 写的最新采样 */
    /* CPU 写 my_buf 进 Cache，之后 Opus 编码器读 my_buf 也命中 Cache */
    /* my_buf 全程只有 CPU 操作，无 DMA，天然无一致性问题 */
    opus_encode(..., my_buf, ...);
```

**invd 的是驱动内部的 `rx_buf`。** 应用 `my_buf` 全程只被 CPU 读写，DMA 从不碰它，cacheable 完全没问题。

---

### Cache 一致性问题的本质边界

> **只有 CPU 与 DMA 共享同一块内存时才存在一致性问题。纯 CPU 读写（包括 memcpy 两端）永远是一致的。**

| 操作类型 | 是否有一致性问题 | 原因 |
|---|---|---|
| CPU 写 → CPU 读（同一块内存） | ❌ 无 | 同一个 Cache，读写都命中，永远最新 |
| CPU 写 → DMA 读（TX 方向） | ⚠️ 有 | CPU 写在 Cache，DMA 读的是 RAM（可能旧） |
| DMA 写 → CPU 读（RX 方向） | ⚠️ 有 | DMA 写入 RAM，CPU 读 Cache（可能是旧快照） |
| DMA 写 → DMA 读（用途不同）| ❌ 无 | DMA 直接走 RAM，双方都不经过 Cache |

---

### 两种驱动的本质区别

| 对比项 | UART 驱动 | Codec 驱动 |
|---|---|---|
| 驱动有无内部缓冲区 | **无** | **有**（tx_buf / rx_buf，双缓冲） |
| DMA 操作的是谁的内存 | 应用直接提供的 buffer | 驱动内部的 tx_buf / rx_buf |
| 应用 buffer 接触 DMA 吗 | ✅ 是，直接作为 DMA 源/目标 | ❌ 否，只做 CPU memcpy 的源/目标 |
| 应用 buffer 有 cache 问题吗 | 有，交给驱动处理 | **没有**，DMA 不碰它 |
| 驱动如何保证一致性 | TX: flush 应用 buf；RX: invd 应用 buf | TX: flush 内部 tx_buf；RX: invd 内部 rx_buf |
| 应用层需要做什么 | 无感（驱动透明处理） | 无感（驱动透明处理） |

---

### 一句话总结

> **UART**：驱动没有自己的缓冲区，应用的 buffer 直接给 DMA 用，驱动在发送前 flush、在接收通知前 invd，应用无感。
> **Codec**：驱动有内部缓冲区（DMA 只操作这个），应用的数据通过 memcpy 进出驱动内部缓冲，应用自己的 buffer 完全不接触 DMA，天然无一致性问题。
