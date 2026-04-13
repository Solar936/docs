# SF32LB52 DMA Cache 一致性问题调研报告

## 背景

SF32LB52 搭载 Cortex-M33，具有写回（write-back）D-Cache，Cache Line 大小为 **32 字节**。DMA 控制器绕过 CPU Cache 直接访问物理 RAM，因此任何 CPU 与 DMA 共享的内存区域都存在 Cache 一致性（coherency）问题。

本报告分析以下三个层面：
1. Codec 驱动 commit `838118e5` 仅修复 TX 方向是否合理
2. SF32 是否可将 DMA buffer 放到 non-cacheable 内存区域
3. Cache 维护代码能否下沉到驱动层，对应用层透明

---

## 一、Codec 驱动 commit 838118e5 分析

### 修改内容

该提交在 `drivers/audio/sf32lb_codec.c` 中新增了两处 `sys_cache_data_flush_range()` 调用：

```c
// codec_write() — 应用写入音频数据时
sys_cache_data_flush_range(dev_data->tx_write_ptr, dev_data->tx_half_dma_size);

// codec_configure() — 初始化 tx_buf 清零后
sys_cache_data_flush_range(data->tx_buf, data->tx_half_dma_size * 2);
```

两处均为 **TX 方向（DAC 播放）**：CPU 写数据 → Cache → flush → RAM → DMA → DAC 输出。

### TX-only 修复是否合理？

**合理，但不够完整。** 逻辑如下：

- **TX 方向**：CPU 写入 `tx_buf`，数据先进 Cache，DMA 从 RAM 读取数据发给 DAC。若不 flush，DMA 读到的是旧的 RAM 内容。此次修复正确。
- **RX 方向**：DMA 从 ADC 读数据写入 `rx_buf`（RAM），CPU 通过 `rx_done` 回调读取。若不 invalidate，CPU 命中 Cache 中的旧数据，读不到 DMA 写入的新内容。

**结论：RX 方向（ADC 录音）存在同样的 Cache 一致性 bug，当前提交未修复。**

### Codec RX 方向存在的问题

#### 问题一：对齐不足

`rx_buf` 和 `tx_buf` 都用以下方式分配：

```c
k_aligned_alloc(sizeof(uint32_t), data->rx_half_dma_size * 2);
// sizeof(uint32_t) = 4 字节 —— 但 Cache Line 是 32 字节
```

`sys_cache_data_invd_range()` 操作以 Cache Line 为粒度，若 buffer 起始地址不对齐到 32 字节，会影响相邻共用同一 Cache Line 的无关变量，导致数据损坏。

**正确做法：`k_aligned_alloc(32, ...)`**

#### 问题二：RX 回调前缺少 Cache 失效

`dma_rx_callback` 中直接调用用户回调，未执行 `sys_cache_data_invd_range()`：

```c
// 当前代码（有 bug）
void dma_rx_callback(...) {
    if (status == DMA_STATUS_COMPLETE) {
        if (data->rx_done) {
            // 直接给应用，此时 CPU 可能读到 Cache 中的旧数据
            data->rx_done(dev, data->rx_buf + data->rx_half_dma_size, ...);
        }
    } else if (status == DMA_STATUS_HALF_COMPLETE) {
        if (data->rx_done) {
            data->rx_done(dev, data->rx_buf, ...);
        }
    }
}
```

**正确做法：**

```c
void dma_rx_callback(...) {
    uint8_t *ptr;
    if (status == DMA_STATUS_COMPLETE) {
        ptr = data->rx_buf + data->rx_half_dma_size;
    } else if (status == DMA_STATUS_HALF_COMPLETE) {
        ptr = data->rx_buf;
    } else { ... }
    // 先失效 Cache，再通知应用
    sys_cache_data_invd_range(ptr, data->rx_half_dma_size);
    data->rx_done(dev, ptr, data->rx_half_dma_size, data->rx_cb_user_data);
}
```

---

## 二、UART 驱动 DMA Cache 问题

对照 commit `6e1ab80`（uart_raw_test 中的修复）：

### RX 方向（已在应用层修复）

应用在 `UART_RX_RDY` 回调中手动 invalidate：

```c
sys_cache_data_invd_range((void *)(evt->data.rx.buf + evt->data.rx.offset),
                           evt->data.rx.len);
```

`rx_dma_slab` 对齐已修正为 32 字节。

### TX 方向（未修复）

| Buffer | 位置 | 实际对齐 | Cache Flush |
|---|---|---|---|
| `tx_buf[192]` | `at_cmd_run()` 内 static | 无保证 | ❌ 无 |
| `mipsend_tx_buf[1500]` | 全局 static `char` | 1 字节 | ❌ 无 |

`raw_tx()` 中直接调用 `uart_tx()`，未做任何 flush：

```c
static void raw_tx(const uint8_t *buf, size_t len)
{
    log_hex("TX", buf, (int)len);
    uart_tx(modem_uart, buf, len, SYS_FOREVER_US);  // DMA 直接读 RAM
    k_sem_take(&tx_done_sem, K_FOREVER);
}
```

### UART 驱动层现状

`drivers/serial/uart_sf32lb.c` 中的 `uart_async_sf32lb_tx()` 在 DMA 启动前无任何 cache 操作；所有三个 `UART_RX_RDY` 触发点（line 473、583、705）前均无 `sys_cache_data_invd_range()`。**驱动层完全没有 Cache 维护代码。**

---

## 三、SF32 non-cacheable 内存区域

### 硬件支持情况

SF32LB52 的 DTS（`dts/arm/sifli/sf32lb52x-ram012.dtsi`）定义了两个被 MPU 标记为 non-cacheable 的专用内存区域：

```dts
sram0_shared: memory@2007fc00 {
    compatible = "zephyr,memory-region", "mmio-sram";
    reg = <0x2007fc00 DT_SIZE_K(1)>;        /* 1 KB */
    zephyr,memory-region = "sram0_shared";
    zephyr,memory-attr = <DT_MEM_ARM(ATTR_MPU_RAM_NOCACHE)>;
};

sram1_shared: memory@20400000 {
    compatible = "zephyr,memory-region", "mmio-sram";
    reg = <0x20400000 DT_SIZE_K(64)>;       /* 64 KB */
    zephyr,memory-region = "sram1_shared";
    zephyr,memory-attr = <DT_MEM_ARM(ATTR_MPU_RAM_NOCACHE)>;
};
```

MPU 驱动（`arch/arm/core/mpu/arm_mpu.c`）读取 DTS 属性后将这两段内存配置为 `REGION_RAM_NOCACHE_ATTR`，CPU 访问这些地址时硬件强制绕过 Cache，无需任何软件 Cache 维护。

当前 build 的 map 文件确认这两段 section 已正确链接：

```
sram0_shared   0x2007fc00   0x400   rw   (1KB, 当前为空)
sram1_shared   0x20400000  0x10000  rw   (64KB, 当前为空)
```

### Zephyr 中使用 non-cacheable 内存的方式

**方式一：`__nocache` 属性（静态变量）**

需开启 `CONFIG_NOCACHE_MEMORY=y`：

```c
// 静态 DMA buffer 自动放入 non-cacheable section
static uint8_t __nocache rx_dma_buf[256];
```

注意：`CONFIG_NOCACHE_MEMORY` 当前在 uart_raw_test 的 build 中**未启用**（`.config` 中确认为 `not set`）。

**方式二：直接使用已知地址的 non-cacheable 区域（`sram1_shared`）**

```c
// 在 .overlay 或代码中将 buffer 声明到 sram1_shared section
static uint8_t __attribute__((section(".sram1_shared.noinit"))) rx_dma_buf[256];
```

**方式三：从 non-cacheable heap 动态分配**

```c
// 定义 nocache heap（需 CONFIG_NOCACHE_MEMORY）
K_HEAP_DEFINE_NOCACHE(dma_heap, 4096);
uint8_t *buf = k_heap_alloc(&dma_heap, 256, K_NO_WAIT);
```

### 使用 non-cacheable 内存的代价

| 项目 | 说明 |
|---|---|
| 访问速度 | 每次读写都经总线直达 RAM，无 Cache 加速，比 cacheable 内存慢约 5-10x |
| 资源限制 | `sram1_shared` 仅 64KB，需与蓝牙核心共享；`sram0_shared` 仅 1KB |
| 可行性 | 对于 UART/Codec DMA buffer（通常 < 4KB），**完全可行** |
| 编程简化 | 一旦使用 non-cacheable 区域，无需任何 `sys_cache_*` 调用 |

---

## 四、Cache 维护代码能否下沉到驱动层

### 结论：**可以且应该下沉，但目前两个驱动均未实现**

### UART 驱动层方案

在 `drivers/serial/uart_sf32lb.c` 中：

**TX flush**（在 `uart_async_sf32lb_tx()` 启动 DMA 前）：

```c
static int uart_async_sf32lb_tx(const struct device *dev, const uint8_t *buf,
                                 size_t len, int32_t timeout)
{
    // ... 参数校验 ...
    sys_cache_data_flush_range((void *)buf, len);   // 新增
    sf32lb_dma_reload_dt(...);
    sf32lb_dma_start_dt(&config->tx_dma);
    // ...
}
```

**RX invalidate**（在所有 `UART_RX_RDY` 事件发出前）：

三处 `evt.type = UART_RX_RDY` 赋值后、`data->async.cb(...)` 调用前，统一加：

```c
sys_cache_data_invd_range(evt.data.rx.buf + evt.data.rx.offset, evt.data.rx.len);
```

这样应用层的 `modem_uart_cb` 中就不再需要手动 invalidate，`rx_dma_slab` 的对齐可保留在 32 字节（以满足部分操作的对齐约束），但也可以改回 4 字节。

### Codec 驱动层方案

**TX flush**：已在 commit `838118e5` 中部分修复，但 `tx_buf` 的 `k_aligned_alloc` 对齐应改为 32：

```c
data->tx_buf = k_aligned_alloc(32, data->tx_half_dma_size * 2);
```

**RX invalidate**：在 `dma_rx_callback` 中，调用 `rx_done` 前加：

```c
sys_cache_data_invd_range(ptr, data->rx_half_dma_size);
```

`rx_buf` 的 `k_aligned_alloc` 同样应改为 32。

### 驱动层方案的限制

| 限制 | 说明 |
|---|---|
| 对齐约束照旧 | 即便驱动做了 `sys_cache_*`，应用传入的 buffer 起始地址仍需 32 字节对齐，否则会影响相邻变量。这个约束无法完全对应用透明。|
| Zephyr API 规范 | Zephyr 的 `uart_tx()` / `uart_rx_enable()` API 文档要求调用者保证 buffer 是 DMA-safe（对齐且 coherent），驱动层做 cache 维护超出规范，但在自有驱动中是可接受的工程做法。|
| non-cacheable 方案下无需驱动介入 | 若改用 non-cacheable 内存，驱动和应用均无需任何 `sys_cache_*` 调用，是最干净的方案。|

---

## 五、综合方案推荐

### 方案 A：non-cacheable 内存（推荐，最干净）

将所有 DMA buffer 放到 `sram1_shared`（0x20400000, 64KB, non-cacheable）：

- 启用 `CONFIG_NOCACHE_MEMORY=y`
- 驱动内部的 `tx_buf` / `rx_buf` 用 `K_HEAP_DEFINE_NOCACHE` 分配
- UART 应用的 `rx_dma_slab`、`tx_buf`、`mipsend_tx_buf` 用 `__nocache` 标记
- 无需任何 `sys_cache_*` 调用

**代价**：non-cacheable 内存访问速度较慢，但音频/UART buffer 访问频率低，影响微乎其微。

### 方案 B：驱动层做 Cache 维护（次优）

在 UART 驱动和 Codec 驱动的关键路径加 `sys_cache_data_flush_range` / `sys_cache_data_invd_range`，同时修正 `k_aligned_alloc` 的对齐参数为 32。应用层只需保证传入 buffer 32 字节对齐。

### 方案 C：维持现状（不推荐）

当前状态：
- Codec TX：已修复（838118e5）
- Codec RX：**未修复**，存在 ADC 录音数据损坏风险
- UART TX：**未修复**，存在发送数据错误风险（小数据量下概率较低但不可靠）
- UART RX：应用层已部分修复（6e1ab80）

---

## 附：缺陷速查表

| 组件 | 方向 | 问题 | 风险 | 是否修复 |
|---|---|---|---|---|
| Codec 驱动 | TX | `tx_buf` 对齐仅 4B | flush 可能污染相邻数据 | ❌ |
| Codec 驱动 | TX | `codec_write` 有 flush | 正确 | ✅ 838118e5 |
| Codec 驱动 | RX | `rx_buf` 对齐仅 4B | invd 污染相邻数据 | ❌ |
| Codec 驱动 | RX | `dma_rx_callback` 无 invd | CPU 读到 ADC 旧数据 | ❌ |
| UART 驱动 | TX | 驱动无 flush | DMA 读到 TX 旧数据 | ❌ |
| UART 应用 | TX | `tx_buf`/`mipsend_tx_buf` 无 flush | DMA 读到 TX 旧数据 | ❌ |
| UART 应用 | RX | `rx_dma_slab` 对齐改为 32B | 对齐正确 | ✅ 6e1ab80 |
| UART 应用 | RX | `UART_RX_RDY` 回调中有 invd | 正确 | ✅ 6e1ab80 |
