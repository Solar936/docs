# SF32LB52x UART DMA 收发机制分析

> 源码路径：  
> - 示例：`sdk/example/uart/src/main.c`  
> - HAL 驱动：`sdk/drivers/hal/bf0_hal_uart.c`  
> - 头文件：`sdk/drivers/Include/bf0_hal_uart.h`  
> - 寄存器定义：`sdk/drivers/cmsis/Include/usart.h`  
> - DMA 驱动：`sdk/drivers/Include/bf0_hal_dma.h`

---

## 1. 寄存器结构（`USART_TypeDef`）

```c
typedef struct {
    __IO uint32_t CR1;    // 控制寄存器 1
    __IO uint32_t CR2;    // 控制寄存器 2
    __IO uint32_t CR3;    // 控制寄存器 3
    __IO uint32_t BRR;    // 波特率寄存器
    __IO uint32_t GTPR;
    __IO uint32_t RTOR;
    __IO uint32_t RQR;
    __IO uint32_t ISR;    // 状态寄存器
    __IO uint32_t ICR;    // 清除寄存器
    __IO uint32_t RDR;    // 接收数据寄存器（9 位有效宽度）
    __IO uint32_t TDR;    // 发送数据寄存器（9 位有效宽度）
    __IO uint32_t MISCR;  // 杂项控制寄存器
    // SF32LB56X / SF32LB52X 系列还有：
    __IO uint32_t DRDR;   // DMA 批量接收寄存器（32 位）
    __IO uint32_t DTDR;   // DMA 批量发送寄存器（32 位）
    __IO uint32_t EXR;    // 扩展状态寄存器
} USART_TypeDef;
```

### RDR / TDR 宽度问题

- **RDR / TDR 均为 `uint32_t` 类型寄存器**，但有效数据宽度只有 **9 位**（`USART_RDR_RDR_Msk = 0x1FF`，bits[8:0]）。  
  > 通常 8 位数据帧时只有 bits[7:0] 有效。
- 在轮询模式下，HAL 驱动对读出值施加掩码（`UART_MASK_COMPUTATION`）：
  - 8 位无校验 → mask = `0x00FF`
  - 8 位奇偶校验 → mask = `0x007F`（校验位占据 bit7）
  - 9 位无校验 → mask = `0x01FF`
- **DMA 模式下不施加掩码**（注释原文：*This masking operation is not carried out in the case of DMA transfers*）。DMA 按字节对齐传输（`DMA_PDATAALIGN_BYTE` / `DMA_MDATAALIGN_BYTE`），每次搬运 1 字节，直接读写 RDR/TDR 的低 8 位，多余高位不影响实际数据。
- SF32LB52x / 56x 系列额外提供 **DRDR / DTDR**（32 位宽），可供 DMA 一次传输 4 字节，但 HAL 层 `HAL_UART_DmaTransmit` 仍以字节对齐操作 RDR/TDR，DRDR/DTDR 由底层 DMA 驱动选择使用。

---

## 2. UART DMA 发送（TX）工作流程

### 2.1 调用方式

示例中 TX 使用 **轮询阻塞**（`HAL_UART_Transmit`）或 **DMA 方式**（`HAL_UART_Transmit_DMA`）。  
`sdk/example/uart` 中 TX 通过辅助函数 `HAL_UART_DmaTransmit` 统一封装，传入方向参数 `DMA_MEMORY_TO_PERIPH`。

### 2.2 TX DMA 初始化参数

```c
hdma_tx.Init.Direction           = DMA_MEMORY_TO_PERIPH;
hdma_tx.Init.PeriphInc           = DMA_PINC_DISABLE;   // 外设地址固定（TDR）
hdma_tx.Init.MemInc              = DMA_MINC_ENABLE;    // 内存地址自增
hdma_tx.Init.PeriphDataAlignment = DMA_PDATAALIGN_BYTE;
hdma_tx.Init.MemDataAlignment    = DMA_MDATAALIGN_BYTE;
hdma_tx.Init.Mode                = DMA_NORMAL;         // 普通模式（单次）
hdma_tx.Init.Priority            = DMA_PRIORITY_LOW;
```

### 2.3 TX DMA 完整工作流程

```
用户调用 HAL_UART_Transmit_DMA(huart, pData, Size)
  │
  ├─ 设置回调：XferCpltCallback = UART_DMATransmitCplt
  │           XferHalfCpltCallback = UART_DMATxHalfCplt
  │
  ├─ HAL_DMA_Start_IT(hdmatx, pData地址, &TDR地址, Size)
  │   └─ DMA 开始搬运：内存 → TDR，每次 1 字节，地址自增
  │
  ├─ 清除 TC 标志（UART_CLEAR_TCF）
  │
  └─ 置 CR3.DMAT 位，UART 触发 DMA 请求

  [每发送 1 字节：TXE 标志置位 → DMA 自动搬运下一字节]

  DMA 搬完全部数据 → 触发 DMA 传输完成中断
  └─ UART_DMATransmitCplt 回调（普通模式）：
      ├─ 清 DMAT 位（停止 DMA 请求）
      └─ 使能 CR1.TCIE（等待最后一帧移位完成）

  UART TX 移位寄存器发完最后 1 字节 → TC 标志置位 → UART 中断
  └─ UART_EndTransmit_IT：
      ├─ 清 TCIE
      ├─ 恢复 gState = READY
      └─ 调用 HAL_UART_TxCpltCallback（可由用户重载）
```

> **关键点**：DMA TX 为普通（NORMAL）模式，单次传完后自动停止。TC 中断是"最后字节移位完毕"的信号。

---

## 3. UART DMA 接收（RX）工作流程

### 3.1 调用方式

示例使用 `HAL_UART_DmaTransmit` 传入方向参数 `DMA_PERIPH_TO_MEMORY`，内部调用 `HAL_UART_Receive_DMA`。

### 3.2 RX DMA 初始化参数

```c
hdma_rx.Init.Direction           = DMA_PERIPH_TO_MEMORY;
hdma_rx.Init.PeriphInc           = DMA_PINC_DISABLE;   // 外设地址固定（RDR）
hdma_rx.Init.MemInc              = DMA_MINC_ENABLE;    // 内存地址自增
hdma_rx.Init.PeriphDataAlignment = DMA_PDATAALIGN_BYTE;
hdma_rx.Init.MemDataAlignment    = DMA_MDATAALIGN_BYTE;
hdma_rx.Init.Mode                = DMA_CIRCULAR;       // 环形模式（重点！）
hdma_rx.Init.Priority            = DMA_PRIORITY_MEDIUM;
```

> **RX 使用 DMA 环形（Circular）模式**，DMA 计数器（CNDTR）自动重装，永不停止，缓冲区被反复覆盖写入。

### 3.3 RX DMA 完整工作流程

```
用户调用 HAL_UART_Receive_DMA(huart, buffer, BUFF_LEN)
  │
  ├─ 设置回调：XferCpltCallback = UART_DMAReceiveCplt
  │           XferHalfCpltCallback = UART_DMARxHalfCplt
  │
  ├─ HAL_DMA_Start_IT(hdmarx, &RDR地址, buffer地址, BUFF_LEN)
  │   └─ DMA 开始监听：RDR → buffer[0..BUFF_LEN-1] 循环写入
  │
  ├─ 使能 CR1.PEIE（奇偶校验错误中断）
  ├─ 使能 CR3.EIE（帧错/噪声/溢出错误中断）
  └─ 置 CR3.DMAR 位，UART 触发 DMA 请求

  [每收到 1 字节：RXNE 标志置位 → DMA 自动搬运到 buffer]

  DMA 写满 BUFF_LEN 个字节（绕一圈） → 触发半完成 / 全完成中断
  ├─ UART_DMARxHalfCplt → 调用 HAL_UART_RxHalfCpltCallback
  └─ UART_DMAReceiveCplt（环形模式下不停 DMA） → 调用 HAL_UART_RxCpltCallback

  同时：UART IDLE 中断（IDLEIE）
  └─ 用户 UART_IRQ_HANDLER：
      ├─ 检测 UART_FLAG_IDLE
      ├─ 读取 CNDTR（剩余计数），计算已接收字节数
      │   recv_total_index = BUFF_LEN - __HAL_DMA_GET_COUNTER(&dma_rx_handle)
      │   recv_len = recv_total_index - last_index
      ├─ 处理 buffer[(last_index .. last_index+recv_len) % BUFF_LEN]
      └─ 清除 IDLE 标志（__HAL_UART_CLEAR_IDLEFLAG）
```

---

## 4. IDLE 中断（空闲中断）

### 4.1 是否支持

**支持**。寄存器定义和 HAL 头文件均包含：

```c
// ISR 状态标志
#define UART_FLAG_IDLE    USART_ISR_IDLE    // ISR 寄存器 bit[4]

// 中断使能
#define UART_IT_IDLE      0x0424U           // CR1.IDLEIE bit[4]

// 清除宏
#define UART_CLEAR_IDLEF  USART_ICR_IDLECF // 写 ICR 清零
#define __HAL_UART_CLEAR_IDLEFLAG(__HANDLE__)  __HAL_UART_CLEAR_FLAG((__HANDLE__), UART_CLEAR_IDLEF)
```

### 4.2 IDLE 中断的用途与触发时机

- IDLE（空闲帧）检测：RX 引脚在一帧数据后，持续 **一个完整字符时间**没有新数据到来时触发。
- **在 DMA 环形接收模式下，IDLE 中断是唯一可靠的"不定长数据帧结束"通知机制**，因为 DMA 满计数中断只能在接收到固定 N 字节后触发。

### 4.3 示例中的 IDLE 处理代码

```c
// 使能：
__HAL_UART_ENABLE_IT(&UartHandle, UART_IT_IDLE);

// 中断处理：
void UART_IRQ_HANDLER(void)
{
    if (__HAL_UART_GET_FLAG(&UartHandle, UART_FLAG_IDLE) &&
        __HAL_UART_GET_IT_SOURCE(&UartHandle, UART_IT_IDLE))
    {
        static int last_index = 0;
        // CNDTR 为 DMA 剩余未传字节数
        int recv_total_index = BUFF_LEN - __HAL_DMA_GET_COUNTER(&dma_rx_handle);
        int recv_len = recv_total_index - last_index;
        if (recv_len < 0) recv_len += BUFF_LEN;  // 处理环形绕回
        // ... 处理 buffer[last_index .. last_index+recv_len)
        last_index = recv_total_index;
        __HAL_UART_CLEAR_IDLEFLAG(&UartHandle);  // 必须手动清除
    }
}
```

> **注意**：HAL 的 `HAL_UART_IRQHandler()` **不处理 IDLE 标志**，用户必须在自己的 ISR 中读取并清除它。

---

## 5. 中断向量说明

示例中需要注册两个中断服务函数：

| 中断 | 处理函数 | 作用 |
|------|---------|------|
| `UART_RX_DMA_IRQ` | `UART_RX_DMA_IRQ_HANDLER` → `HAL_DMA_IRQHandler` | DMA 半完成 / 全完成 / 错误回调 |
| `UART_INTERRUPT` | `UART_IRQ_HANDLER`（用户实现） | IDLE 帧检测（手动清 IDLE 标志）+ 可选 RXNE/错误处理 |

---

## 6. TX 与 RX DMA 模式对比

| 维度 | TX DMA | RX DMA |
|------|--------|--------|
| DMA 方向 | `DMA_MEMORY_TO_PERIPH` | `DMA_PERIPH_TO_MEMORY` |
| DMA 模式 | `DMA_NORMAL`（单次） | `DMA_CIRCULAR`（环形） |
| 触发源 | TXE（发送寄存器空，CR3.DMAT） | RXNE（接收寄存器非空，CR3.DMAR） |
| 地址配置 | 源：内存自增；目标：TDR 固定 | 源：RDR 固定；目标：内存自增 |
| 完成通知 | DMA 完成 → 使能 TCIE → TC 中断 | DMA 半/全 完成 + **IDLE 中断** |
| 需要的中断 | DMA_IRQ（+ UART_IRQ 用于 TC） | DMA_IRQ + **UART_IRQ（IDLE）** |

---

## 7. 状态机与多任务安全

- `UART_HandleTypeDef` 包含 `gState`（TX）和 `RxState`（RX）两个独立字段。
- TX 和 RX 可以**同时进行**（全双工），分别由 gState / RxState 保护。
- HAL 内部用 `__HAL_LOCK` / `__HAL_UNLOCK`（简单 flag 锁）防止重入，不适合 OS 多线程直接调用。在 RTOS 环境中需要在上层加互斥锁。

---

## 8. 错误处理

| 错误类型 | HAL 错误码 | 处理策略 |
|---------|-----------|---------|
| 奇偶校验错误（PE） | `HAL_UART_ERROR_PE` | 非阻塞，继续传输 |
| 帧格式错误（FE） | `HAL_UART_ERROR_FE` | 非阻塞，继续传输 |
| 噪声错误（NE） | `HAL_UART_ERROR_NE` | 非阻塞，继续传输 |
| 溢出错误（ORE） | `HAL_UART_ERROR_ORE` | **阻塞**，中止 DMA RX，调用 `HAL_UART_ErrorCallback` |
| DMA 传输错误 | `HAL_UART_ERROR_DMA` | **阻塞**，中止 DMA，调用 `HAL_UART_ErrorCallback` |

> ORE（溢出）在 DMA 模式下是严重错误：意味着新数据到来时 RDR 中上一字节尚未被 DMA 搬走，通常是 DMA 带宽不足或 CPU 太忙导致。可通过 `UART_ADVFEATURE_OVERRUN_DISABLE` 关闭 ORE 检测（代价是丢数据不报错）。

---

## 9. 关键 API 速查

| API | 说明 |
|-----|------|
| `HAL_UART_Init` | 初始化 UART 外设 |
| `HAL_UART_Transmit` | 轮询阻塞发送 |
| `HAL_UART_Receive` | 轮询阻塞接收 |
| `HAL_UART_Transmit_IT` | 中断发送（逐字节 TXE 触发） |
| `HAL_UART_Receive_IT` | 中断接收（逐字节 RXNE 触发） |
| `HAL_UART_Transmit_DMA` | DMA 发送（NORMAL 模式，单次） |
| `HAL_UART_Receive_DMA` | DMA 接收（CIRCULAR 模式，环形） |
| `HAL_UART_DmaTransmit` | 封装函数，统一设置 DMA Init 后调用上面两个 |
| `HAL_UART_DMAPause` | 暂停 DMA 传输 |
| `HAL_UART_DMAResume` | 恢复 DMA 传输 |
| `HAL_UART_DMAStop` | 停止 DMA 传输 |
| `HAL_DMA_IRQHandler` | DMA 中断处理（在 DMA ISR 中调用） |
| `__HAL_DMA_GET_COUNTER` | 读取 CNDTR，获取剩余未传字节数 |
| `__HAL_UART_ENABLE_IT` | 使能指定 UART 中断（如 UART_IT_IDLE） |
| `__HAL_UART_CLEAR_IDLEFLAG` | 清除 IDLE 标志（必须手动） |

---

## 10. 典型用法总结（不定长 DMA 接收）

```
1. HAL_UART_Init()                          // 初始化 UART
2. __HAL_LINKDMA(&uart, hdmarx, dma_rx)     // 绑定 DMA handle
3. HAL_UART_DmaTransmit(..., DMA_PERIPH_TO_MEMORY)
   └─ 内部依次：DMA DeInit → Init(CIRCULAR) → HAL_UART_Receive_DMA
               → 使能 PEIE + EIE + DMAR
4. HAL_NVIC_EnableIRQ(DMA_IRQ)              // 使能 DMA 中断
5. __HAL_UART_ENABLE_IT(&uart, UART_IT_IDLE)
6. HAL_NVIC_EnableIRQ(UART_IRQ)             // 使能 UART 中断（IDLE）

运行时：
  UART 字节接收 → RXNE → DMA 自动搬运 → buffer 循环写入
  帧尾空闲 → IDLE 中断 → 用户 ISR 读 CNDTR 计算接收长度 → 处理数据
```
