# SF32LB52x 8MB PSRAM 使用指南：从 RT-Thread SDK 到 Zephyr

## 目录

1. [硬件背景](#1-硬件背景)
2. [SF32 SDK（RT-Thread）下 PSRAM 的使用方式](#2-sf32-sdkrt-thread下-psram-的使用方式)
3. [iotpi/sifli_zephyr 已有的 PSRAM 支持](#3-iotpisifli_zephyr-已有的-psram-支持)
4. [Zephyr 上游 PSRAM 实现参考](#4-zephyr-上游-psram-实现参考)
5. [Zephyr 下启用 8MB PSRAM 的完整方案](#5-zephyr-下启用-8mb-psram-的完整方案)
6. [需要新增 / 修改的所有文件清单](#6-需要新增--修改的所有文件清单)
7. [关键注意事项](#7-关键注意事项)
8. [参考资源](#8-参考资源)

---

## 1. 硬件背景

### 芯片：SF32LB52x

- 双核 Cortex-M33（HCPU + LCPU）
- 内部 SRAM：512KB（RAM0/1/2 共享）
- MPI1 接口（QSPI1）：连接 **OPI PSRAM** 或 Flash
- MPI2 接口（QSPI2）：连接 NOR Flash

### xty-ai 板卡 PSRAM 规格

- 型号：APM XCELLA 64Mb = **8MB** OPI PSRAM（PID=3，即 `BOOT_PSRAM_APS_64P`）
- 接口：MPI1（QSPI1），工作在 **OPI（Octal SPI / SPI_MODE_OPSRAM）** 模式
- 供电：PERI LDO **1.8V**（不同于默认的 3.3V，必须显式配置）
- Pin 配置：`PAD_SA01~SA11`，使用 `MPI1_DIO0~7 + MPI1_CLK + MPI1_CS`（`board_pinmux_psram_func1_2_4(1)`）

### 地址映射（SF32LB52x）

| 地址空间 | 基址 | 用途 |
|---------|------|------|
| QSPI1_MEM_BASE（CBUS） | `0x10000000` | 代码总线，可 D-Cache，用于 XIP 代码执行 |
| PSRAM_BASE（SBUS/DBUS） | `0x60000000` | 数据总线，可 D-Cache，用于数据/堆访问 |

两个地址实际映射到同一片物理 PSRAM，偏移关系：`SBUS = CBUS + 0x50000000`。

PSRAM 大小：`PSRAM_SIZE = BSP_QSPI1_MEM_SIZE * 1024 * 1024 = 8MB = 0x800000`

---

## 2. SF32 SDK（RT-Thread）下 PSRAM 的使用方式

### 2.1 编译配置（Kconfig / board.conf）

在 `app/boards/sf32lb52-xty-ai/hcpu/board.conf` 中关键配置：

```conf
CONFIG_BSP_ENABLE_MPI1=y          # 启用 MPI1 控制器
CONFIG_BSP_MPI1_MODE_3=y          # MPI1 模式 3 = OPI PSRAM (SPI_MODE_OPSRAM)
# BSP_QSPI1_MEM_SIZE 在系统配置中为 8（8MB）
```

这些配置会生成以下宏（`rtconfig.h` / Kconfig 编译结果）：

```c
#define BSP_ENABLE_MPI1      1
#define BSP_MPI1_MODE_3      1
#define BSP_QSPI1_MODE       3        // SPI_MODE_OPSRAM
#define BSP_QSPI1_MEM_SIZE   8        // 8MB
#define BSP_USING_PSRAM1     1        // 启用 PSRAM1（MPI1）
#define BSP_USING_PSRAM      1        // 通用 PSRAM 标志
```

### 2.2 内存映射头文件（mem_map.h）

`sdk/drivers/cmsis/sf32lb52x/mem_map.h` 中：

```c
#ifdef BSP_USING_PSRAM1
    #define PSRAM_SIZE   (BSP_QSPI1_MEM_SIZE * 1024 * 1024)  // 8MB
    #define PSRAM_BASE   (0x60000000)
#else
    #define PSRAM_SIZE   (0)
    #define PSRAM_BASE   (0x60000000)
#endif
```

### 2.3 初始化流程

PSRAM 在 `HAL_PreInit()` 中初始化，该函数在 `app/boards/sf32lb52-xty-ai_base/bsp_init.c` 中重写（覆盖 HAL 库中的 weak 默认实现）。

**完整初始化序列**（`HAL_PreInit()` → `HAL_Init()` → 系统启动）：

```
HAL_Init()
└── HAL_PreInit()  <-- 在 bsp_init.c 中重写
    ├── 1. 切换 CPU 时钟到 HXT48（48MHz 晶振）
    ├── 2. 复位 LCPU
    ├── 3. 读取 EFUSE/OTP 系统配置（BSP_System_Config()）
    ├── 4. 启动 GTimer，配置 PMU DLL
    ├── 5. 切换 CPU 到 240MHz（HAL_RCC_HCPU_ConfigHCLK(240)）
    ├── 6. 启用 DLL2（288MHz）
    ├── 7. HAL_MspInit() → BSP_IO_Init() → BSP_PIN_Init()
    │       └── bsp_pinmux.c: 根据 PID 配置 PSRAM 引脚
    │           PID=3: board_pinmux_psram_func1_2_4(1)
    │           → PAD_SA01~SA11 设置为 MPI1_DIO0~7/CLK/CS
    ├── 8. 【关键】设置 PERI LDO 为 1.8V
    │       HAL_PMU_ConfigPeriLdo(PMU_PERI_LDO_1V8, true, true)
    ├── 9. 初始化 PSRAM（RT-Thread 路径）
    │       rt_psram_init()
    │       └── bsp_psramc_init()
    │           └── HAL_MPI_PSRAM_Init(&f_handle, &qspi_cfg, mpi1_div=2)
    │               qspi_cfg.SpiMode = SPI_MODE_OPSRAM（OPI 模式）
    └── 10. 初始化 Flash（rt_hw_flash_init()）
```

HAL_PreInit 中 PSRAM 初始化的核心代码（`bsp_init.c`）：

```c
#if defined(BSP_USING_PSRAM)
    // 必须先设置 1.8V，否则 PSRAM 无法工作！
    HAL_PMU_ConfigPeriLdo(PMU_PERI_LDO_1V8, true, true);

#ifdef BSP_USING_RTTHREAD
    rt_psram_init();   // RT-Thread 路径
#else
    board_init_psram();  // 裸机路径
#endif
#endif
```

`board_init_psram()` 内部：

```c
static void board_init_psram(void)
{
    qspi_configure_t qspi_cfg = {
        .Instance = hwp_qspi1,
        .SpiMode  = BSP_QSPI1_MODE,   // 3 = SPI_MODE_OPSRAM
        .msize    = BSP_QSPI1_MEM_SIZE, // 8
        .base     = QSPI1_MEM_BASE,    // 0x10000000
    };
    // 根据芯片 IDR 寄存器的 PID 字段自动检测 PSRAM 类型
    uint32_t pid = (hwp_hpsys_cfg->IDR & HPSYS_CFG_IDR_PID_Msk) >> HPSYS_CFG_IDR_PID_Pos;
    pid &= 7;
    switch (pid) {
    case 3:  // BOOT_PSRAM_APS_64P（8MB APM XCELLA）
    case 2:  // BOOT_PSRAM_APS_128P（16MB APM XCELLA）
        qspi_cfg.SpiMode = SPI_MODE_OPSRAM;
        break;
    // ...其他类型
    }
    HAL_MPI_PSRAM_Init(&f_handle, &qspi_cfg, mpi1_div); // mpi1_div=2
}
```

### 2.4 分区表（ptab.json）

`app/boards/sf32lb52-xty-ai/ptab.json` 中：

```json
{
    "mem": "psram1",
    "base": "0x60000000",
    "regions": [
        {
            "offset": "0x00000000",
            "max_size": "0x00800000",
            "tags": ["PSRAM_DATA"]
        }
    ]
}
```

### 2.5 电源管理（bsp_power.c）

睡眠/唤醒时管理 PSRAM 低功耗状态：

```c
#ifdef BSP_USING_PSRAM1
// 唤醒时退出低功耗
bsp_psram_exit_low_power("psram1");
// 休眠时进入低功耗
bsp_psram_enter_low_power("psram1");
#endif
```

### 2.6 PSRAM 堆使用

RT-Thread 下，PSRAM 注册为堆内存池，应用代码可直接通过 `rt_malloc()` 分配 PSRAM 内的内存。

---

## 3. iotpi/sifli_zephyr 已有的 PSRAM 支持

### 仓库地址

- **sifli_zephyr**（SiFli MCU Zephyr 移植）：https://github.com/iotpi/sifli_zephyr
- **hal_sifli**（SiFli HAL for Zephyr）：https://github.com/iotpi/hal_sifli（fork 自 https://github.com/zephyrproject-rtos/hal_sifli）

### 3.1 DTS：PSRAM 节点定义

`dts/arm/sifli/sf32lb52.dtsi` 中已包含 MPI1 PSRAM 控制器和 8MB PSRAM 节点：

```dts
mpi1: psram-controller@50041000 {
    compatible = "sifli,mpi-controller", "sifli,sf32lb52-flash-controller";
    reg = <0x50041000 0x1000>;
    clocks = <&rcc ENR1 MPI1>;

    #address-cells = <1>;
    #size-cells = <1>;

    psram1: mpi1@10000000 {
        compatible = "sifli,psram", "soc-psram";
        reg = <0x10000000 0x800000>;  /* 8MB，CBUS 地址 */
    };
};
```

> **注意**：DTS 中地址为 `0x10000000`（MPI1 CBUS 地址），对应 SBUS 地址为 `0x60000000`。数据访问推荐使用 `0x60000000`（D-Cache 管理的 SBUS 路径）。

### 3.2 静态配置（rtconfig.h）

`soc/sifli/sf32lb52x/rtconfig.h` 中已定义：

```c
#define BSP_USING_PSRAM1     1
#define BSP_QSPI1_MEM_SIZE   8    // 8MB
#define BSP_MPI1_MODE_3      1    // OPI PSRAM
#define BSP_QSPI1_MODE       3
#define BSP_USING_PSRAM      1
```

### 3.3 SoC 早期初始化（soc.c）

```c
void soc_early_init_hook(void)
{
    SCB_EnableICache();
    SCB_EnableDCache();  // ⚠️ 当前在 HAL_Init 前启用 D-Cache
    HAL_Init();          // 调用 HAL_PreInit()（weak，未被重写！）
}
```

### 3.4 ⚠️ 当前的缺口

`HAL_PreInit()` 在 iotpi/sifli_zephyr 中**未被重写**，所以：

1. PSRAM **未初始化**（引脚未配置、HAL_MPI_PSRAM_Init 未调用）
2. PERI LDO **未设置为 1.8V**
3. D-Cache 在 PSRAM 初始化之前就被启用（顺序有风险）
4. PSRAM 未作为 Zephyr 的内存区域注册（无法作为堆使用）

---

## 4. Zephyr 上游 PSRAM 实现参考

在修改 iotpi/sifli_zephyr 之前，先看 Zephyr 主仓库中其他厂商如何在 ARM Cortex-M 上实现 PSRAM，从中提取可复用的模式。

### 4.1 Bouffalo Lab BL61x（最接近的参考）

BL61x 是与 SF32 架构相近的 SoC（SIP 内嵌 PSRAM，通过专用控制器访问）。Zephyr 主仓库中的实现路径：

```
drivers/memc/memc_bflb_bl61x.c         # PSRAM 控制器驱动
dts/riscv/bflb/bl61x.dtsi              # SoC DTS（含 PSRAM 节点）
```

**SoC DTS 中的 PSRAM 节点模式**（`bl61x.dtsi`）：

```dts
/* PSRAM 控制器（memc）节点 - 负责初始化 PSRAM 硬件 */
memc: memc@20052000 {
    compatible = "bflb,bl61x-psram";
    reg = <0x20052000 DT_SIZE_K(4)>;   /* 控制器寄存器 */
    clock-divider = <1>;
    status = "okay";
};

/* PSRAM 内存区域节点 - 描述 CPU 可访问的内存窗口 */
psram: memory@a8000000 {
    compatible = "zephyr,memory-region";
    reg = <0xa8000000 DT_SIZE_M(128)>;
    zephyr,memory-region = "PSRAM";
    status = "disabled";               /* 默认禁用，板级覆盖启用 */
};
```

**驱动结构**（`memc_bflb_bl61x.c`）：

```c
/* 驱动注册，POST_KERNEL 阶段初始化，确保在应用层之前完成 */
DEVICE_DT_INST_DEFINE(0, memc_bflb_bl61x_init, NULL,
    &data, &config,
    POST_KERNEL, CONFIG_MEMC_INIT_PRIORITY, NULL);
```

初始化流程：
1. 从 EFUSE 读取 PSRAM 大小信息
2. 配置 PSRAM 时钟（`memc_bflb_bl61x_init_psram_clock`）
3. 配置 GPIO 引脚（`memc_bflb_bl61x_init_gpio`）
4. 初始化 PSRAM 控制器寄存器（`memc_bflb_bl61x_init_psram`）

**板级 DTS 激活 PSRAM**：

```dts
/* 板级 DTS 覆盖 */
&psram {
    status = "okay";
};

/ {
    chosen {
        zephyr,sram = &psram;   /* 将 PSRAM 作为主堆/SRAM */
    };
};
```

### 4.2 STM32 OCTO-SPI PSRAM（APMemory，与 SF32 PSRAM 同品牌）

STM32H5/H7/U5 系列使用 APMemory（与 SF32 的 APM XCELLA 相同品牌）的 OCTO-SPI PSRAM。

```
drivers/memc/memc_stm32_ospi_psram.c   # STM32 OSPI PSRAM 驱动
```

关键模式：
- 使用 `pinctrl_apply_state` Zephyr 标准引脚控制
- 使用 `clock_control_on` Zephyr 标准时钟控制
- 注册到 `shared_multi_heap`（`SMH_REG_ATTR_EXTERNAL`），让内核感知外部 PSRAM 内存池

```c
#ifdef CONFIG_SHARED_MULTI_HEAP
static struct shared_multi_heap_region smh_psram = {
    .addr = DT_REG_ADDR(DT_NODELABEL(psram)),
    .size = DT_REG_SIZE(DT_NODELABEL(psram)),
    .attr = SMH_REG_ATTR_EXTERNAL,
};
/* 在驱动 init 末尾注册 */
shared_multi_heap_pool_init();
shared_multi_heap_add(&smh_psram, NULL);
#endif
```

### 4.3 NXP MIMXRT1064（SDRAM，`zephyr,sram = &sdram0` 模式）

`boards/nxp/mimxrt1064_evk/mimxrt1064_evk.dts` 中：

```dts
sdram0: memory@80000000 {
    device_type = "memory";
    reg = <0x80000000 DT_SIZE_M(32)>;
};

/ {
    chosen {
        zephyr,sram = &sdram0;   /* 所有 malloc/stack 都用外部 SDRAM */
    };
};
```

SDRAM/PSRAM 控制器驱动在 `POST_KERNEL` 阶段完成初始化，确保在 `zephyr,sram` 被使用前内存已就绪。

### 4.4 关键规律总结

从上游所有 PSRAM/SDRAM 实现中提取的共同规律：

| 要素 | 做法 |
|------|------|
| 控制器节点 | SoC dtsi 定义，板级 DTS `status = "okay"` 激活 |
| 内存区域节点 | `compatible = "zephyr,memory-region"` + `zephyr,memory-region = "NAME"` |
| ARM MPU 属性 | `zephyr,memory-attr = <DT_MEM_ARM(ATTR_MPU_RAM)>` |
| 驱动初始化阶段 | `POST_KERNEL`，优先级 `CONFIG_MEMC_INIT_PRIORITY` |
| 堆注册 | `shared_multi_heap_add` 或 `chosen { zephyr,sram = &psram; }` |
| Kconfig | `CONFIG_MEMC=y`，驱动专属 `CONFIG_MEMC_xxx=y` |

---

## 5. Zephyr 下启用 8MB PSRAM 的完整方案

### 5.1 Step 1：新增 HAL_PreInit 实现

在板级目录下新增 `board_init.c`，重写 `HAL_PreInit()` 以完成 PSRAM 初始化：

```c
// boards/sifli/your_board/board_psram.c
#include "bf0_hal.h"
#include "bf0_hal_pmu.h"
#include "bf0_hal_pinmux.h"
#include "register.h"

/* OPI PSRAM（APM 64Mb/128Mb XCELLA）引脚配置 */
static void board_pinmux_opi_psram(void)
{
    HAL_PIN_Set(PAD_SA01, MPI1_DIO0, PIN_PULLDOWN, 1);
    HAL_PIN_Set(PAD_SA02, MPI1_DIO1, PIN_PULLDOWN, 1);
    HAL_PIN_Set(PAD_SA03, MPI1_DIO2, PIN_PULLDOWN, 1);
    HAL_PIN_Set(PAD_SA04, MPI1_DIO3, PIN_PULLDOWN, 1);
    HAL_PIN_Set(PAD_SA08, MPI1_DIO4, PIN_PULLDOWN, 1);
    HAL_PIN_Set(PAD_SA09, MPI1_DIO5, PIN_PULLDOWN, 1);
    HAL_PIN_Set(PAD_SA10, MPI1_DIO6, PIN_PULLDOWN, 1);
    HAL_PIN_Set(PAD_SA11, MPI1_DIO7, PIN_PULLDOWN, 1);
    HAL_PIN_Set(PAD_SA07, MPI1_CLK,  PIN_NOPULL,   1);
    HAL_PIN_Set(PAD_SA05, MPI1_CS,   PIN_NOPULL,   1);
    /* 其余 SA pad 设为模拟输入 */
    HAL_PIN_Set_Analog(PAD_SA00, 1);
    HAL_PIN_Set_Analog(PAD_SA06, 1);
    HAL_PIN_Set_Analog(PAD_SA12, 1);
}

/* 重写 HAL_PreInit，在 HAL_Init() 内部被调用 */
void HAL_PreInit(void)
{
    /* 1. 切换到 HXT48 时钟 */
    if (RCC_SYSCLK_HRC48 == HAL_RCC_HCPU_GetClockSrc(RCC_CLK_MOD_SYS)) {
        HAL_HPAON_EnableXT48();
        HAL_RCC_HCPU_ClockSelect(RCC_CLK_MOD_SYS, RCC_SYSCLK_HXT48);
    }
    HAL_RCC_HCPU_ClockSelect(RCC_CLK_MOD_HP_PERI, RCC_CLK_PERI_HXT48);

    /* 2. 读取 EFUSE 系统配置（可选，提升 PMU 精度） */
    BSP_System_Config();

    /* 3. CPU 升频到 240MHz */
    HAL_PMU_EnableDLL(1);
    HAL_RCC_HCPU_ConfigHCLK(240);
    HAL_RCC_HCPU_EnableDLL2(288000000);
    HAL_Delay_us(0);  /* 重置 HAL_Delay_us 内部时钟参考 */

    /* 4. 配置 PSRAM 引脚 */
    board_pinmux_opi_psram();

    /* 5. 【必须】设置 PERI LDO 为 1.8V，否则 PSRAM 无法工作 */
    HAL_PMU_ConfigPeriLdo(PMU_PERI_LDO_1V8, true, true);

    /* 6. 初始化 PSRAM */
    {
        static FLASH_HandleTypeDef f_handle = {0};
        qspi_configure_t qspi_cfg = {
            .Instance = hwp_qspi1,
            .SpiMode  = SPI_MODE_OPSRAM,  /* OPI PSRAM */
            .msize    = 8,                /* 8MB */
            .base     = QSPI1_MEM_BASE,   /* 0x10000000 */
        };
        f_handle.wakeup = 0;
        HAL_MPI_PSRAM_Init(&f_handle, &qspi_cfg, 2); /* div=2 for 64Mb XCELLA */
    }

    /* 7. 配置 MPI2（Flash）时钟 */
    HAL_RCC_HCPU_ClockSelect(RCC_CLK_MOD_FLASH2, RCC_CLK_FLASH_DLL2);
}
```

在板级 `CMakeLists.txt` 中加入该文件：

```cmake
zephyr_sources(board_psram.c)
```

### 5.2 Step 2：修正 D-Cache 初始化顺序

修改 `soc/sifli/sf32lb52x/soc.c`，确保 PSRAM 先初始化再启用 D-Cache：

```c
void soc_early_init_hook(void)
{
    /* 启用 I-Cache */
    SCB_EnableICache();

    /* 先调用 HAL_Init()，其中 HAL_PreInit() 会初始化 PSRAM */
    HAL_Init();

    /* PSRAM 初始化完成后，再启用 D-Cache */
    SCB_EnableDCache();

#if CONFIG_PM
    sf32lb_power_init();
#endif
}
```

> ⚠️ **必须**先初始化 PSRAM，再开启 D-Cache。否则 CPU 对 `0x60000000` 的缓存 speculatively pre-fetch 会读到未初始化的 PSRAM 数据，导致系统崩溃。

### 5.3 Step 3：DTS 配置

`psram_sbus: memory@60000000` 节点定义在 `sf32lb52.dtsi` 中（见 6.3 节），板级 DTS 只需覆盖 `status` 将其启用：

```dts
/* boards/sifli/your_board/your_board.dts */
/dts-v1/;
#include <sifli/sf32lb52.dtsi>   /* 其中已定义 psram_sbus: memory@60000000 */
#include "your_board-pinctrl.dtsi"

/ {
    model = "Your Board";
    compatible = "sifli,sf32lb52x";

    chosen {
        zephyr,console  = &usart1;
        zephyr,sram     = &sram0;    /* 内部 SRAM 做主 heap */
        zephyr,flash    = &flash2;
    };
};

/* 启用 MPI1 PSRAM 控制器 */
&mpi1 {
    status = "okay";
};

/* 启用 PSRAM CBUS 节点（0x10000000，XIP 代码执行） */
&psram1 {
    status = "okay";
};

/* 启用 PSRAM SBUS 内存区域（0x60000000，数据/堆访问） */
&psram_sbus {
    status = "okay";
};
```

### 5.4 Step 4：defconfig / prj.conf

```conf
# 必须：启用 nocache 内存支持（用于 PSRAM 区域）
CONFIG_NOCACHE_MEMORY=y

# 启用 MPU（用于 PSRAM 内存属性）
CONFIG_ARM_MPU=y

# 可选：以 PSRAM 作为主 heap（将 zephyr,sram 指向 psram 节点时使用）
# CONFIG_HEAP_MEM_POOL_SIZE=8388608  # 8MB

# 启用系统 heap（若要跨两个内存区分配）
CONFIG_HEAP_MEM_POOL_SIZE=1048576   # 额外 heap 示例
```

### 5.5 Step 5：应用层使用 PSRAM

**方式 A：使用 section 属性放置变量/缓冲区**

链接脚本会为 `PSRAM` 区域生成 `__PSRAM_start` 和 `__PSRAM_end` 符号。

```c
/* 将 buf 放入 PSRAM（通过 section 属性） */
static uint8_t large_buf[4 * 1024 * 1024] Z_GENERIC_SECTION(PSRAM);
```

**方式 B：手动初始化 PSRAM Heap**

```c
#include <zephyr/kernel.h>

/* PSRAM 起始/大小 由 DTS 生成 */
extern char __PSRAM_start[];
#define PSRAM_HEAP_SIZE  (8 * 1024 * 1024)

K_HEAP_DEFINE(psram_heap, 1024);  /* 占位，实际用下面方式初始化 */

static struct k_heap actual_psram_heap;

static int init_psram_heap(void)
{
    k_heap_init(&actual_psram_heap, (void *)0x60000000, PSRAM_HEAP_SIZE);
    return 0;
}
SYS_INIT(init_psram_heap, APPLICATION, 0);

/* 使用 */
void *ptr = k_heap_alloc(&actual_psram_heap, 1024 * 1024, K_NO_WAIT);
```

**方式 C：作为 Zephyr 主 SRAM（全部堆都在 PSRAM）**

将 `chosen { zephyr,sram = &psram; }` 指向 PSRAM 节点。适用于内部 SRAM 不够用的场景：

```dts
/ {
    chosen {
        zephyr,sram = &psram;  /* 主 heap 使用 PSRAM */
    };
};
```

---

## 6. 需要新增 / 修改的所有文件清单

本节列出在 iotpi/sifli_zephyr 框架下启用 SF32LB52x 8MB PSRAM 所需的**全部文件操作**。

### 6.1 文件总览

| 操作 | 文件路径（相对 sifli_zephyr 仓库根目录） | 说明 |
|------|----------------------------------------|------|
| ✏️ 修改 | `soc/sifli/sf32lb52x/soc.c` | 修正 D-Cache 初始化顺序 |
| ✏️ 修改 | `dts/arm/sifli/sf32lb52.dtsi` | 为 SoC dtsi 添加 PSRAM SBUS 内存区域节点 |
| ➕ 新增 | `boards/sifli/<board>/board_psram.c` | 重写 `HAL_PreInit()`，完成 PSRAM 硬件初始化 |
| ✏️ 修改 | `boards/sifli/<board>/<board>.dts` | 板级 DTS 启用 PSRAM 相关节点 |
| ✏️ 修改 | `boards/sifli/<board>/CMakeLists.txt` | 将 `board_psram.c` 纳入编译 |
| ✏️ 修改 | `boards/sifli/<board>/<board>_defconfig` | 添加必要的 Kconfig 选项 |
| ✏️ 修改 | `soc/sifli/sf32lb52x/Kconfig.soc`（如存在） | 确认 MPU/MEMC 相关选项默认值 |

> `<board>` 替换为实际板名，例如 `em-lb525` 或 `eh-lb563`。

---

### 6.2 【修改】`soc/sifli/sf32lb52x/soc.c`

**修改前**（当前 iotpi 实现）：
```c
void soc_early_init_hook(void)
{
    SCB_EnableICache();
    SCB_EnableDCache();   /* ⚠️ 错误：PSRAM 未初始化就开 D-Cache */
    HAL_Init();           /* HAL_PreInit() weak，无 PSRAM 初始化 */
}
```

**修改后**：
```c
void soc_early_init_hook(void)
{
    SCB_EnableICache();

    /* HAL_Init() 内部调用 HAL_PreInit()，后者（在板级重写）完成 PSRAM 初始化 */
    HAL_Init();

    /* PSRAM 初始化完成后才能启用 D-Cache，否则对 0x60000000 的推测预取会崩溃 */
    SCB_EnableDCache();

#if CONFIG_PM
    sf32lb_power_init();
#endif
}
```

---

### 6.3 【修改】`dts/arm/sifli/sf32lb52.dtsi`

在现有 `mpi1: psram-controller@50041000` 节点之后，在 `soc { }` 内新增 **PSRAM SBUS 内存区域节点**：

**新增内容**（在 `soc { ... }` 内部）：

```dts
/*
 * PSRAM SBUS 地址窗口（数据总线，0x60000000）
 * 与 psram1（CBUS 0x10000000）映射到同一片物理 PSRAM
 * 数据访问、堆、变量请使用此地址
 * 需在 PSRAM 硬件初始化完成后才可访问
 */
psram_sbus: memory@60000000 {
    compatible = "zephyr,memory-region", "mmio-sram";
    reg = <0x60000000 DT_SIZE_M(8)>;   /* 8MB，与 BSP_QSPI1_MEM_SIZE 对应 */
    zephyr,memory-region = "PSRAM";
    zephyr,memory-attr = <( DT_MEM_ARM(ATTR_MPU_RAM) )>;
    status = "disabled";               /* 板级 DTS 按需启用 */
};
```

> **为什么 dtsi 用 `status = "disabled"`**：
> 遵循 Zephyr 惯例（参考 BL61x `bl61x.dtsi` 中的 `psram` 节点），SoC 级默认禁用，由板级 DTS 决定是否启用，避免没有 PSRAM 的板子误用。

---

### 6.4 【新增】`boards/sifli/<board>/board_psram.c`

此文件重写 `HAL_PreInit()`，完成 PSRAM 硬件初始化。这是**最关键的文件**，对应 RT-Thread SDK 中的 `bsp_init.c` 功能。

```c
/*
 * SPDX-License-Identifier: Apache-2.0
 *
 * SF32LB52x PSRAM 板级初始化
 * 重写 HAL_PreInit()（weak 函数），在 HAL_Init() 早期被调用
 * 参考：app/boards/sf32lb52-xty-ai_base/bsp_init.c
 */

#include "bf0_hal.h"
#include "bf0_hal_pmu.h"
#include "bf0_hal_pinmux.h"
#include "bf0_hal_rcc.h"
#include "register.h"

/*
 * OPI PSRAM 引脚配置（APM XCELLA 64Mb PID=3 / 128Mb PID=2）
 * PAD_SA01~SA11 对应 MPI1_DIO0~7 / MPI1_CLK / MPI1_CS
 * 参考 bsp_pinmux.c: board_pinmux_psram_func1_2_4(1)
 */
static void board_pinmux_opi_psram(void)
{
    /* 8 根数据线（DIO0~7） */
    HAL_PIN_Set(PAD_SA01, MPI1_DIO0, PIN_PULLDOWN, 1);
    HAL_PIN_Set(PAD_SA02, MPI1_DIO1, PIN_PULLDOWN, 1);
    HAL_PIN_Set(PAD_SA03, MPI1_DIO2, PIN_PULLDOWN, 1);
    HAL_PIN_Set(PAD_SA04, MPI1_DIO3, PIN_PULLDOWN, 1);
    HAL_PIN_Set(PAD_SA08, MPI1_DIO4, PIN_PULLDOWN, 1);
    HAL_PIN_Set(PAD_SA09, MPI1_DIO5, PIN_PULLDOWN, 1);
    HAL_PIN_Set(PAD_SA10, MPI1_DIO6, PIN_PULLDOWN, 1);
    HAL_PIN_Set(PAD_SA11, MPI1_DIO7, PIN_PULLDOWN, 1);
    /* 时钟和片选 */
    HAL_PIN_Set(PAD_SA07, MPI1_CLK,  PIN_NOPULL,   1);
    HAL_PIN_Set(PAD_SA05, MPI1_CS,   PIN_NOPULL,   1);
    /* 未使用的 SA pad 设为模拟输入，避免漏电 */
    HAL_PIN_Set_Analog(PAD_SA00, 1);
    HAL_PIN_Set_Analog(PAD_SA06, 1);
    HAL_PIN_Set_Analog(PAD_SA12, 1);
}

/*
 * 重写 HAL_PreInit()（定义在 sdk/.../bf0_hal.c 中为 __weak）
 * 调用时机：HAL_Init() → HAL_PreInit()，早于任何 Zephyr 内核代码
 */
void HAL_PreInit(void)
{
    /* 1. 切换 CPU 时钟到 HXT48（48MHz 外部晶振），稳定时钟源 */
    if (HAL_RCC_HCPU_GetClockSrc(RCC_CLK_MOD_SYS) == RCC_SYSCLK_HRC48) {
        HAL_HPAON_EnableXT48();
        HAL_RCC_HCPU_ClockSelect(RCC_CLK_MOD_SYS, RCC_SYSCLK_HXT48);
    }
    HAL_RCC_HCPU_ClockSelect(RCC_CLK_MOD_HP_PERI, RCC_CLK_PERI_HXT48);

    /* 2. 读取 EFUSE/OTP 校准数据（可选，提升 PMU 精度） */
    BSP_System_Config();

    /* 3. CPU 升频到 240MHz（PSRAM 初始化需要稳定高频时钟） */
    HAL_PMU_EnableDLL(1);
    HAL_RCC_HCPU_ConfigHCLK(240);
    HAL_RCC_HCPU_EnableDLL2(288000000);   /* DLL2 for MPI2 Flash */
    HAL_Delay_us(0);                       /* 刷新 HAL tick 计数基准 */

    /* 4. 配置 MPI1 PSRAM 引脚 */
    board_pinmux_opi_psram();

    /* 5. 【关键】配置 PERI LDO 为 1.8V
     *    APM XCELLA PSRAM 工作电压要求 1.8V
     *    必须在 HAL_MPI_PSRAM_Init 之前完成，否则 PSRAM 无响应
     */
    HAL_PMU_ConfigPeriLdo(PMU_PERI_LDO_1V8, true, true);

    /* 6. 初始化 PSRAM（OPI 模式，8MB，CBUS 基址 0x10000000） */
    {
        static FLASH_HandleTypeDef f_handle;
        qspi_configure_t qspi_cfg = {
            .Instance = hwp_qspi1,
            .SpiMode  = SPI_MODE_OPSRAM,  /* OPI PSRAM（PID=2/3 APM XCELLA） */
            .msize    = 8,                /* 8MB */
            .base     = QSPI1_MEM_BASE,   /* 0x10000000，CBUS 地址 */
        };
        f_handle.wakeup = 0;
        /*
         * div 参数：OPI PSRAM 驱动内部忽略此值（固定使用 div=1）
         * 传 2 与 bsp_init.c 保持一致
         */
        HAL_MPI_PSRAM_Init(&f_handle, &qspi_cfg, 2);
    }

    /* 7. 配置 MPI2（NOR Flash）时钟源为 DLL2 */
    HAL_RCC_HCPU_ClockSelect(RCC_CLK_MOD_FLASH2, RCC_CLK_FLASH_DLL2);

    /*
     * 注意：D-Cache 不在此处启用
     * D-Cache 由 soc_early_init_hook() 在本函数返回后启用
     * 顺序：HAL_PreInit（PSRAM init）→ HAL_Init 返回 → SCB_EnableDCache()
     */
}
```

---

### 6.5 【修改】`boards/sifli/<board>/<board>.dts`

在板级 DTS 中启用 PSRAM 相关节点：

```dts
/dts-v1/;
#include <sifli/sf32lb52.dtsi>
/* ... 其他 include ... */

/ {
    model = "Your SF32LB52x Board";
    compatible = "sifli,sf32lb52x";

    chosen {
        zephyr,console    = &usart1;
        zephyr,flash      = &flash2;

        /*
         * 方案 A（推荐）：内部 SRAM 作主堆，PSRAM 作扩展区
         * 启用后通过 Z_GENERIC_SECTION(PSRAM) 或 k_heap_alloc 使用 PSRAM
         */
        zephyr,sram = &sram0;

        /*
         * 方案 B：将 PSRAM 作为主堆（内部 SRAM 仅用于栈/中断）
         * 仅当 8MB 内部 SRAM 不够用时启用，注释掉方案 A
         */
        /* zephyr,sram = &psram_sbus; */
    };
};

/* 激活 MPI1 PSRAM 控制器节点（SoC dtsi 中已定义）*/
&mpi1 {
    status = "okay";
};

/* 激活 PSRAM CBUS 节点（CBUS 0x10000000，用于 XIP 代码执行） */
&psram1 {
    status = "okay";
};

/* 激活 PSRAM SBUS 内存区域（SBUS 0x60000000，数据/堆访问） */
&psram_sbus {
    status = "okay";
};
```

---

### 6.6 【修改】`boards/sifli/<board>/CMakeLists.txt`

将 `board_psram.c` 加入编译：

```cmake
# SF32LB52x PSRAM board initialization
zephyr_sources(board_psram.c)
```

---

### 6.7 【修改】`boards/sifli/<board>/<board>_defconfig`

在现有 defconfig 基础上添加：

```conf
# ── PSRAM 支持 ──────────────────────────────────────────────
# MPU 支持（为 PSRAM 区域配置内存属性所必需）
CONFIG_ARM_MPU=y

# 额外内存区域支持（zephyr,memory-region 生效所必需）
CONFIG_SRAM_REGION_PERMISSIONS=y

# 系统堆（可选，应用层 k_malloc 使用；若 zephyr,sram 指向内部 SRAM 则设此值）
# 若 zephyr,sram 指向 psram_sbus，则此值自动为 PSRAM 大小，不需要单独设
CONFIG_HEAP_MEM_POOL_SIZE=65536

# 可选：启用 Shared Multi-Heap 以向应用层暴露 PSRAM 内存池
# （参考 STM32 OSPI PSRAM 驱动模式）
# CONFIG_SHARED_MULTI_HEAP=y
```

---

### 6.8 【可选进阶】标准 Zephyr memc 驱动（参考 BL61x 模式）

若希望 PSRAM 初始化遵循完整 Zephyr 驱动模型（`DEVICE_DT_INST_DEFINE`），需额外新增：

| 文件 | 说明 |
|------|------|
| `drivers/memc/memc_sifli_mpi_psram.c` | PSRAM memc 驱动，`POST_KERNEL` 阶段初始化 |
| `drivers/memc/Kconfig.sifli` | 驱动 Kconfig，`MEMC_SIFLI_MPI_PSRAM` 选项 |
| `dts/bindings/memc/sifli,mpi-psram.yaml` | DT binding 文件 |

这是更"Zephyr 原生"的方案，但存在根本性障碍：memc 驱动在 `POST_KERNEL` 阶段运行，而 D-Cache 在更早的 `soc_early_init_hook()` 中已被启用，导致 PSRAM 初始化始终晚于 D-Cache 使能，无法解决 7.2 节的时序问题。**`HAL_PreInit()` 方案是长期可行方案**，天然满足时序要求，不存在此障碍。

---

## 7. 关键注意事项

### 7.1 PERI LDO 1.8V 是必须的

在调用 `HAL_MPI_PSRAM_Init()` 前，**必须**先执行：

```c
HAL_PMU_ConfigPeriLdo(PMU_PERI_LDO_1V8, true, true);
```

若未配置，PSRAM 工作电压不对，初始化会失败，访问会崩溃。

### 7.2 D-Cache 初始化顺序

```
正确顺序：HAL_Init() → PSRAM 初始化 完成后 → SCB_EnableDCache()
错误顺序：SCB_EnableDCache() → HAL_Init() → PSRAM 初始化（当前 iotpi 实现）
```

### 7.3 PID 自动检测 vs 硬编码

SF32LB52x 通过 `HPSYS_CFG->IDR` 寄存器的 `PID` 字段区分 SIP 型号：

| PID | PSRAM 类型 | 大小 | SPI 模式 |
|-----|-----------|------|---------|
| 2 | APM XCELLA 128Mb | 16MB | SPI_MODE_OPSRAM |
| 3 | APM XCELLA 64Mb | 8MB | SPI_MODE_OPSRAM |
| 4 | APM LEGACY 32Mb | 4MB | SPI_MODE_LEGPSRAM |
| 5 | APM QSPI 16Mb | 2MB | SPI_MODE_PSRAM |
| 6 | Winbond HYPERBUS | 多种 | SPI_MODE_HBPSRAM |

xty-ai 板使用 PID=3（8MB）。Zephyr 实现中可根据板子固定使用 `SPI_MODE_OPSRAM`，也可读 PID 动态判断。

### 7.4 mpi1_div 参数

`HAL_MPI_PSRAM_Init()` 的第三个参数 `div` 含义：

- OPI PSRAM 驱动：`div` 固定使用 1（SDK 注释：`for OPI Psram driver always set 1`）
- QSPI PSRAM：依赖此参数控制时钟分频
- 实际 bsp_init.c 中 `mpi1_div = 2` 但对 OPI PSRAM 来说内部忽略

### 7.5 低功耗模式

Zephyr PM 中需对应实现 PSRAM 进入/退出低功耗，参考 `bsp_power.c`：

```c
/* 进入 Standby 前 */
HAL_PSRAM_EnterLowPower("psram1");

/* 唤醒后 */
HAL_PSRAM_ExitLowPower("psram1");
```

### 7.6 D-Cache 与 PSRAM 一致性

- 写入 PSRAM 的数据（通过 `0x60000000`）被 D-Cache 缓存
- DMA 操作 PSRAM 前需 `SCB_CleanInvalidateDCache_by_Addr()` 或使用 No-Cache 地址（`0x60000000 | 0x08000000` 等视 MPU 配置而定）
- Zephyr 中使用 `zephyr,memory-attr = <ATTR_MPU_RAM_NOCACHE>` 可标记该区域为 Non-Cacheable

---

## 8. 参考资源

### 本项目（RT-Thread SDK 参考实现）

| 资源 | 说明 |
|------|------|
| [app/boards/sf32lb52-xty-ai_base/bsp_init.c](../app/boards/sf32lb52-xty-ai_base/bsp_init.c) | PSRAM 完整初始化序列（HAL_PreInit 实现） |
| [app/boards/sf32lb52-xty-ai_base/bsp_pinmux.c](../app/boards/sf32lb52-xty-ai_base/bsp_pinmux.c) | PSRAM 引脚配置（根据 PID 选择） |
| [app/boards/sf32lb52-xty-ai_base/bsp_power.c](../app/boards/sf32lb52-xty-ai_base/bsp_power.c) | PSRAM 低功耗管理 |
| [sdk/drivers/cmsis/sf32lb52x/mem_map.h](../sdk/drivers/cmsis/sf32lb52x/mem_map.h) | PSRAM_BASE / PSRAM_SIZE 宏定义 |
| [app/boards/sf32lb52-xty-ai/hcpu/board.conf](../app/boards/sf32lb52-xty-ai/hcpu/board.conf) | 关键 Kconfig（BSP_MPI1_MODE_3 等） |

### iotpi/sifli_zephyr（Zephyr 移植目标仓库）

| 资源 | 地址 |
|------|------|
| iotpi/sifli_zephyr | https://github.com/iotpi/sifli_zephyr |
| iotpi/hal_sifli | https://github.com/iotpi/hal_sifli |
| zephyrproject-rtos/hal_sifli（上游） | https://github.com/zephyrproject-rtos/hal_sifli |
| sf32lb52.dtsi（PSRAM DTS） | `dts/arm/sifli/sf32lb52.dtsi` |
| soc.c（待修改） | `soc/sifli/sf32lb52x/soc.c` |

### Zephyr 上游 PSRAM 参考实现

| 资源 | 地址 | 说明 |
|------|------|------|
| BL61x SoC DTS | https://github.com/zephyrproject-rtos/zephyr/blob/main/dts/riscv/bflb/bl61x.dtsi | memc + memory-region 双节点模式 |
| BL61x PSRAM 驱动 | https://github.com/zephyrproject-rtos/zephyr/blob/main/drivers/memc/memc_bflb_bl61x.c | 完整 memc 驱动参考实现 |
| STM32 OSPI PSRAM | https://github.com/zephyrproject-rtos/zephyr/blob/main/drivers/memc/memc_stm32_ospi_psram.c | APMemory OPI PSRAM（与 SF32 同品牌） |
| ARM MPU 内存属性 | https://github.com/zephyrproject-rtos/zephyr/blob/main/include/zephyr/dt-bindings/memory-attr/memory-attr-arm.h | ATTR_MPU_RAM / ATTR_MPU_RAM_NOCACHE 宏 |
| NXP MIMXRT1064 | https://github.com/zephyrproject-rtos/zephyr/blob/main/boards/nxp/mimxrt1064_evk/mimxrt1064_evk.dts | `zephyr,sram = &sdram0` 作主堆模式 |
| SF32 SDK（OpenSiFli） | https://github.com/OpenSiFli/SiFli-SDK | SF32 完整 SDK 源码 |
