# WebSocket Opus 音频帧抓包分析报告

> 数据来源：`decoder--260418-214521.csv`（UART 逐字节抓包）  
> 分析范围：全文件，重点从 CSV 第 4945 行起

---

## 1. 抓包文件格式

每行对应 UART 上的一个字节（或控制字符）：

```
Id,Time[ns],1:UART: RX/TX
1,7604710080.00,[0D]
2,7604720880.00,[0A]
3,7604731760.00,O
...
```

| 字段 | 说明 |
|------|------|
| `Id` | 行号（字节序号） |
| `Time[ns]` | 纳秒时间戳 |
| 第 3 列 | 单个 ASCII 字符，或 `[XX]`（十六进制转义，如 `[0D]`=CR、`[0A]`=LF、`[20]`=空格） |

**重建方法：** 按 `[0D][0A]`（CRLF）分割，将逐字节数据重建为完整 UART 行。  
**注意：** 第 3 列可能包含 `,` 或 `"` 字符（作为数据本身），必须用 `split(',', 2)` 手动分割前两个逗号，**不可使用 Python csv 模块**，否则因引号引发错误解析。

---

## 2. ML307 WebSocket 数据 URC 格式

ML307 模块将接收到的 WebSocket 数据通过 `+MIPURC: "rtcp"` URC 上报（注意：这里的 `"rtcp"` 是 ML307 固件使用的 URC 事件名，与 RTP/RTCP 协议无关）：

```
+MIPURC: "rtcp",<linkid>,<total_ws_frame_bytes>,<hex_encoded_ws_frame>
```

| 字段 | 示例 | 说明 |
|------|------|------|
| `"rtcp"` | `"rtcp"` | ML307 WebSocket 接收事件名 |
| `<linkid>` | `0` | WebSocket 连接 ID |
| `<total_ws_frame_bytes>` | `119` | **hex 解码后的 WebSocket 帧总字节数** |
| `<hex_encoded_ws_frame>` | `8275000000716b83...` | WebSocket 完整帧的**十六进制编码** |

### 关键问题：是 hex 编码吗？

**是的。** URC 中的数据是 hex 字符串，**每 2 个 hex 字符 = 1 个实际字节**。  
例如：`238 个 hex 字符 → 119 字节的 WebSocket 帧`。

因此**不能直接把 hex 字符串当作二进制数据分析**，必须先 `bytes.fromhex(hex_str)` 解码，才能按 WebSocket 协议解析。

---

## 3. 如何从裸 hex 直接区分 Opus 帧与 JSON 帧

### 3.1 识别规则：只看 hex 字符串的头两个字符

ML307 URC 的 hex 字符串就是完整 WebSocket 帧的逐字节 hex 表示。  
**hex 字符串的前 2 个字符** = WebSocket 帧的第 1 个字节（Byte[0]），编码了帧类型：

| hex 字符串开头 | Byte[0] 值 | 二进制 | 含义 |
|---------------|-----------|--------|------|
| `82...` | `0x82` | `10000010` | **Binary/Opus 音频帧** |
| `81...` | `0x81` | `10000001` | **Text/JSON 控制帧** |

其中 bit 含义：
```
Byte[0]  =  1  0  0  0  0  0  X  X
            │  │  │  │  └──────┘ └── Opcode
            │  └──┴──┴── RSV1/2/3 = 0
            └── FIN=1（完整帧）

Opcode=0001 (0x1) → Text   → hex 前2字符 = 81
Opcode=0010 (0x2) → Binary → hex 前2字符 = 82
```

### 3.2 实战：用肉眼看 URC 原始行

```
+MIPURC: "rtcp",0,55,81357B22...
                      ↑↑
                      81 → Text/JSON 帧

+MIPURC: "rtcp",0,119,8275000000716B83...
                       ↑↑
                       82 → Binary/Opus 帧
```

无需任何工具，**直接看 hex 字符串头两个字符**：`81` 是 JSON，`82` 是 Opus。

### 3.3 以 tts_stop 为完整示例（Frame 50，CSV row=21237）

**URC 原始文本（UART 抓包重建后）：**
```
+MIPURC: "rtcp",0,55,81357B2274797065223A22747473222C227374617465223A2273746F70222C2273657373696F6E5F6964223A223366326633373861227D
```

**第一步：只看头两个 hex 字符**
```
hex_str = "81357B2274797065..."
           ^^
           81 → 立刻确认：这是 Text/JSON 帧，不是 Opus
```

**第二步：hex 解码后逐字节标注（共 55 字节）**
```
偏移  hex   二进制        含义
[0]   81    10000001b    Byte[0]
              ├─ bit[7]   FIN  = 1（完整帧）
              ├─ bit[6:4] RSV  = 000
              └─ bit[3:0] Opcode = 0001 → Text/JSON ← 帧类型在这里

[1]   35    00110101b    Byte[1]
              ├─ bit[7]   MASK = 0（服务器→客户端，无掩码）
              └─ bit[6:0] PayloadLen_raw = 53（≤125，直接是长度，无扩展字节）

[2..54]      Payload（53字节，UTF-8）：
             7B 22 74 79 70 65 22 3A 22 74 74 73 22 2C
             22 73 74 61 74 65 22 3A 22 73 74 6F 70 22
             2C 22 73 65 73 73 69 6F 6E 5F 69 64 22 3A
             22 33 66 32 66 33 37 38 61 22 7D
             ↓ UTF-8 解码
             {"type":"tts","state":"stop","session_id":"3f2f378a"}
```

Header = 2 字节，Payload = 53 字节，**无帧尾**，合计 55 字节。

**第三步：与最后一个 Opus 帧（Frame 48，Opus #40）对比**
```
偏移  hex   含义
[0]   82    10000010b → Opcode=0010 → Binary/Opus  ← 与 81 只差 1 bit
[1]   51    00110001b → MASK=0, PayloadLen_raw=81（≤125，直接长度，Header=2B）
[2..5] 00 00 00 4D   → xiaozhi 内部长度头：Opus 数据 77 字节
[6]   6B    Opus TOC（Hybrid SWB 24kHz，20ms/帧，单声道，Code=3）
[7]   03    Opus FCC（VBR=0，CBR 模式，3帧 × 20ms = 60ms，子帧等长 25B）
[8..84]     77字节 Opus 压缩数据（3 × 25B，CBR 等长子帧）
```

### 3.4 一句话总结

> URC hex 字符串开头是 `82` → Opus 音频；开头是 `81` → JSON 控制消息。  
> 这一判断**不需要解码，直接看字符串**即可。

---

## 4. WebSocket 帧结构（RFC 6455）

解码后的字节序列遵循标准 WebSocket 帧格式：

```
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-------+-+-------------+-------------------------------+
|F|R|R|R| opcode|M| Payload len |    Extended payload length    |
|I|S|S|S|  (4)  |A|     (7)     |             (16 or 64)        |
|N|V|V|V|       |S|             |  (if payload len==126 or 127) |
| |1|2|3|       |K|             |                               |
+-+-+-+-+-------+-+-------------+-------------------------------+
|     Masking-key (32 bits, if MASK=1)                          |
+---------------------------------------------------------------+
|                    Payload Data                               |
+---------------------------------------------------------------+
```

### Header、Payload、尾巴

| 部分 | 字节范围 | 内容 |
|------|----------|------|
| **Header（头）** | Byte 0-1（基础）+ 可选扩展 | FIN/RSV/Opcode + MASK + Payload Length |
| **Extended Length（扩展长度）** | Byte 2-3（payload_raw=126）或 Byte 2-9（payload_raw=127） | 实际 Payload 长度 |
| **Mask Key** | Header 末尾 4 字节（仅客户端→服务器方向，服务器→客户端 MASK=0） | 掩码键（服务器下发通常无） |
| **Payload（载荷）** | 剩余字节 | 实际数据（Text/Binary/Close 内容） |
| **尾** | 无 | WebSocket 无显式帧尾，通过 Length 字段定界 |

### Opcode 关键值

| Opcode | 十六进制 | 含义 |
|--------|----------|------|
| 0x1 | `0x81`（FIN+Text） | 文本帧（JSON 控制消息） |
| 0x2 | `0x82`（FIN+Binary） | 二进制帧（Opus 音频） |
| 0x8 | `0x88`（FIN+Close） | 关闭帧 |

---

## 4. 全局帧时序（50 帧总览）

抓包文件共发现 50 个 `+MIPURC: "rtcp"` 帧，按 opcode 分类：

| 类型 | 数量 |
|------|------|
| Text (0x1) | 8 帧 |
| Binary (0x2) | 40 帧（Opus 音频） |
| 其他（误识别为 Close） | 2 帧（实为 HTTP 响应，见第 6 节） |

---

## 5. Text 帧详细分析（服务器下发的 JSON 控制消息）

### 5.1 "前面4帧" 指的是哪4帧

在第 4945 行（首个 Opus 帧）之前，紧邻的 4 个 Text 帧如下：

| 帧号 | CSV Row | 时间 ms | Payload JSON |
|------|---------|---------|--------------|
| Frame 5 | 4208 | 10721.2 | `{"type":"tts","state":"start","sample_rate":24000,"session_id":"3f2f378a"}` |
| Frame 6 | 4385 | 10739.4 | `{"type":"stt","text":"你好。","session_id":"3f2f378a"}` |
| Frame 7 | 4528 | 11022.5 | `{"type":"llm","text":"😊","emotion":"happy","session_id":"3f2f378a"}` |
| Frame 8 | 4697 | 11122.8 | `{"type":"tts","state":"sentence_start","text":"你好呀，今天过得怎么样？","session_id":"3f2f378a"}` |

### 5.2 Text 帧 WS Header 解析示例（Frame 8）

```
原始 hex: 81 6D {...JSON...}
          ──  ──
          │   └─ Byte[1] = 0x6D: MASK=0, PayloadLen_raw=109 (直接读出)
          └─ Byte[0] = 0x81: FIN=1, RSV=000, Opcode=0x1 (Text)
Header 总长: 2 字节
Payload:    109 字节 (UTF-8 JSON)
Total:      111 字节
```

Frame 8 之后，227ms 后收到第一个 Opus Binary 帧（Frame 9，row 4945）。

---

## 6. Binary 帧 Opus 音频分析（Frame 9 起）

### 6.1 WebSocket Binary 帧 Header 解析

所有 Binary 帧 Byte[0] 固定为 `0x82`，Byte[1] 根据 Payload 大小取两种形式：

```
Byte[0] = 0x82 = 1000 0010
           │         └── Opcode = 0x2 (Binary)
           └──────────── FIN = 1

Byte[1] 取值决定 Header 长度和长度字段位置（WebSocket RFC 6455）
```

**两种 Payload Length 编码（WebSocket RFC 6455）：**

| Byte[1] 原始值 | 含义 | 真实 PayloadLen 来源 | Header 总长 |
|----------------|------|---------------------|-------------|
| ≤ 125 | **直接长度**，Byte[1] 本身即 Payload 字节数 | Byte[1] | **2 字节** |
| `0x7E` = 126 | **扩展长度标志**，126 本身无意义，真实长度在后续 2 字节 | Byte[2-3]（大端 uint16） | **4 字节** |
| `0x7F` = 127 | 64-bit 扩展长度，由后续 8 字节给出（本抓包未出现） | Byte[2-9]（大端 uint64） | 10 字节 |

**实际数据分布：**

| Opus 帧范围 | WS Payload 大小 | Byte[1] 值 | Header 形式 |
|------------|----------------|-----------|-------------|
| #1（首帧） | 117B | `0x75`（=117）| 直接长度，Header=2B |
| #2 ~ #34 | 131B ~ 239B | `0x7E`（=126，扩展标志）| 扩展长度，Header=4B |
| #35 ~ #40 | 81B ~ 118B | `0x5F`~`0x76`（直接值） | 直接长度，Header=2B |

> **重点澄清：`0x7E` 不是 Payload 长度是 126 字节，而是"使用 16-bit 扩展长度"的标志位。**  
> 真实 Payload 长度由紧跟其后的 Byte[2-3]（大端 uint16）给出，范围可达 0~65535。

**完整 Header 对比图：**

```
直接长度模式（Payload ≤ 125B）：           ← Opus #1, #35-#40 使用

 +--------+--------+
 | Byte 0 | Byte 1 |   Header = 2B
 +--------+--------+
   0x82     0x75         → PayloadLen = 0x75 = 117
   0x82     0x5F         → PayloadLen = 0x5F = 95
   0x82     0x51         → PayloadLen = 0x51 = 81


扩展长度模式（Payload > 125B，Byte[1]=0x7E）：← Opus #2-#34 使用

 +--------+--------+--------+--------+
 | Byte 0 | Byte 1 | Byte 2 | Byte 3 |   Header = 4B
 +--------+--------+--------+--------+
   0x82     0x7E    [hi byte] [lo byte]
                    └─────────────────┘
                    big-endian uint16 = 真实 PayloadLen

示例：
   0x82  0x7E  0x00  0x86  → PayloadLen = 0x0086 = 134
   0x82  0x7E  0x00  0xEF  → PayloadLen = 0x00EF = 239
   0x82  0x7E  0x00  0x83  → PayloadLen = 0x0083 = 131
```

**URC 字段关系验证（以 Opus #3 为例）：**
```
+MIPURC: "rtcp",0,243,827E00EF...
                  ^^^               ← URC 报告总字节数 = 243
                    WS Header(4B) + WS Payload(239B) = 243B ✓

hex 字节：82 7E 00 EF [239 bytes payload]
          ↑  ↑  └──┘
          │  │  Byte[2-3]=0x00EF=239 → 真实 Payload 长度
          │  0x7E=126 → 扩展长度标志（不是实际长度）
          0x82 → FIN=1, Binary
```

### 6.2 WebSocket Payload 内部格式（xiaozhi 协议）

WebSocket Binary Payload 由两部分组成：

```
WebSocket Binary Payload
├── [0..3]  4 字节大端长度字段: uint32_be = Opus 数据长度（= ws_payload_len - 4）
└── [4..]   Opus 编码数据 (opus_data_length 字节)
```

**验证（选取三种不同大小的帧）：**

| Opus # | URC total_B | WS Hdr(B) | WS Payload | 内部长度字段 (hex) | Opus 数据 | 验证 |
|--------|------------|-----------|-----------|-------------------|----------|------|
| #1  | 119 | 2（直接长度） | 117 | `00 00 00 71` = 113 | 113B | 4+113=117 ✓ |
| #2  | 138 | 4（扩展长度） | 134 | `00 00 00 82` = 130 | 130B | 4+130=134 ✓ |
| #3  | 243 | 4（扩展长度） | 239 | `00 00 00 EB` = 235 | 235B | 4+235=239 ✓ |
| #40 | 83  | 2（直接长度） | 81  | `00 00 00 4D` = 77  | 77B  | 4+77=81 ✓  |

### 6.3 Opus 编码格式分析

Opus 数据从 WS Payload 第 4 字节开始。**所有帧 TOC 字节固定为 `0x6B`，但 FCC（Frame Count/Config 字节）分两种**：

**TOC 字节：`0x6B` = `0110 1011`（全部 40 帧一致）**

| 字段 | 位 | 值 | 含义 |
|------|----|----|------|
| Config | [7:3] | `01101` = 13 | Hybrid SWB（24kHz），20ms/帧 |
| Stereo | [2] | 0 | 单声道（Mono） |
| Code | [1:0] | `11` = 3 | Code 3（多帧，VBR 或 CBR） |

> Config=13 对应 RFC 6716 Table 2：Hybrid SWB，采样率 24kHz，帧长 20ms。  
> Code=3 表示 Payload 内包含多个子帧，具体帧数由紧跟的 FCC 字节给出。

---

**FCC 字节（Frame Count Byte）：两种取值**

**类型 A：VBR 主体帧（FCC=`0x83`）— Opus #1 ~ #37**

```
FCC = 0x83 = 1000 0011
      ├─ bit[7]    VBR=1   → 可变比特率（各子帧长度不等）
      ├─ bit[6]    Pad=0   → 无填充
      └─ bit[5:0]  M=3     → 包含 3 个子帧
```

**类型 B：CBR 尾帧（FCC=`0x03`）— Opus #38 ~ #40（最后 3 帧）**

```
FCC = 0x03 = 0000 0011
      ├─ bit[7]    VBR=0   → 固定比特率（所有子帧等长）
      ├─ bit[6]    Pad=0   → 无填充
      └─ bit[5:0]  M=3     → 包含 3 个子帧
```

**结论：每个 WebSocket Binary 帧内含 3 个 Opus 子帧，每子帧 20ms，合计 60ms**，与 hello 消息的 `"frame_duration":60` 完全匹配。

---

**两种 Opus Packet 内部布局对比：**

```
─── VBR 帧（FCC=0x83，Opus #1~#37）───────────────────────────────────
WS Payload 偏移:
  [0..3]  4字节大端长度头（xiaozhi 协议层）
  [4]     TOC = 0x6B（Config=13, Mono, Code=3）
  [5]     FCC = 0x83（VBR=1, Pad=0, M=3）
  [6]     子帧1的字节数（1字节，值<252时直接读出）
  [7]     子帧2的字节数（1字节）
  [8..8+len1-1]   子帧1数据
  [8+len1..8+len1+len2-1]  子帧2数据
  [其余]  子帧3数据（长度=Opus总字节数 - 2 - len1 - len2，无显式字段）

示例（Opus #1，Opus=113B）：
  TOC=0x6B, FCC=0x83, Frame1_len=42, Frame2_len=32, [Frame1 42B][Frame2 32B][Frame3 35B]


─── CBR 尾帧（FCC=0x03，Opus #38~#40）────────────────────────────────
WS Payload 偏移:
  [0..3]  4字节大端长度头（xiaozhi 协议层）
  [4]     TOC = 0x6B（Config=13, Mono, Code=3）
  [5]     FCC = 0x03（VBR=0, Pad=0, M=3）
  [6..]   子帧数据（无显式长度字节，每帧等长 = (Opus总字节数 - 2) ÷ 3）
          其中 -2 是扣去 TOC + FCC 各1字节

示例（Opus #38-40，Opus=77B）：
  TOC=0x6B, FCC=0x03, [子帧1 25B][子帧2 25B][子帧3 25B]（三帧等长，CBR）
```

**为什么末尾变成 CBR？**

Opus #38-40 是 TTS 语音末尾的静默填充帧（出现在 `tts.sentence_end` 前约 120ms），静默音频编码简单、bit 需求低，Opus 自动切换为 CBR 模式填充固定大小，避免帧结构变化带来的解码开销。

---

**VBR 帧的子帧长度示例（完整数据）：**

| Opus # | Opus 字节 | 子帧1(B) | 子帧2(B) | 子帧3(B) |
|--------|----------|---------|---------|---------|
| #1     | 113      | 42      | 32      | 35      |
| #2     | 130      | 38      | 40      | 48      |
| #3     | 235      | 65      | 88      | 78      |
| #4     | 208      | 66      | 67      | 71      |
| #16    | 228      | 72      | 76      | 76      |
| #37    | 91       | 31      | 31      | 25      |

**CBR 帧（等长子帧）：**

| Opus # | Opus 字节 | 子帧1(B) | 子帧2(B) | 子帧3(B) |
|--------|----------|---------|---------|---------|
| #38    | 77       | 25      | 25      | 25      |
| #39    | 77       | 25      | 25      | 25      |
| #40    | 77       | 25      | 25      | 25      |

### 6.4 PCM 压缩比分析

按 hello 消息参数计算：

| 参数 | 值 |
|------|----|
| 采样率 | 24000 Hz |
| 帧时长 | 60 ms |
| PCM 样本数 | 24000 × 0.060 = **1440 样本** |
| 16-bit 单声道 PCM 字节数 | 1440 × 2 = **2880 字节** |

用户提到的 "1920字节" 对应 **16kHz 采样率** (16000 × 0.060 × 2 = 1920)；  
实际音频参数为 24kHz，原始 PCM 应为 **2880 字节**。

Opus 压缩后的实际大小（WS Payload - 4字节协议头）：

| Opus 帧范围 | WS Payload | Opus 数据 | 压缩比（基于 2880B PCM） |
|------------|-----------|----------|--------------------------|
| 主体 VBR 帧（#1~#37） | 81B~239B | 77B~235B | **12:1 ~ 37:1** |
| CBR 尾帧（#38~#40） | 81B | 77B | **37:1**（静默填充） |
| 最大帧（Opus #3/#5） | 239B | 235B | **12:1** |
| 最小主体帧（Opus #37） | 95B | 91B | **32:1** |

**结论：1440~2880 字节的 PCM 被压缩到 77~235 字节，Opus 对语音的压缩效果非常显著，完全正常。**

---

## 7. 前几帧间隔为何异常短？

```
Frame 9:  t=11350.1ms              ← 第一帧 Opus
Frame 10: t=11368.7ms  间隔=18.6ms ← 极短！
Frame 11: t=11391.2ms  间隔=22.5ms ← 极短！
Frame 12: t=11412.3ms  间隔=21.1ms ← 极短！
Frame 13: t=11462.8ms  间隔=50.6ms ← 恢复正常
Frame 14+: 间隔稳定在 50~67ms      ← 正常 60ms 节奏
```

**原因：TTS 服务器预缓冲（burst 发送）**

1. `tts_start` 在 t=10721ms 触发，服务器开始合成  
2. 服务器先合成了多个 Opus 帧缓冲起来，629ms 后才开始推送  
3. 第 9~12 帧是积压的缓冲帧，以 ~20ms 间隔（UART 传输速率限制）连续推送  
4. 从第 13 帧起，服务器进入实时推流模式，按 ~60ms/帧 的节奏发送  

**简单理解：** 就像水龙头开水前管道里已积存的水——开阀后先快速涌出，随后恢复匀速。

---

## 8. tts_stop 帧在哪里？长什么样？

> **关于之前报告中"row=21237, First byte=0x81"的说明**：  
> - **row=21237** 是 UART 抓包 CSV 文件中该 URC 起始行的**行号**，不是字节偏移量。  
> - **"hex 字符串头两字符是 `81`"** 的意思：把 URC 里的 hex 字符串 `bytes.fromhex()` 解码后，第一个字节值为 `0x81`，表示这是 WebSocket Text 帧（JSON）。这句话表达不清，已在第 3 节用完整示例替代。

### 位置

tts_stop 是全文件**最后一帧**，紧跟在 tts.sentence_end 后 14.7ms：

| 帧号 | CSV 起始行号 | 时间 ms | 与前帧间隔 | 内容 |
|------|------------|---------|------------|------|
| Frame 48 | row 20802 | 13556.1ms | — | 最后一个 Opus 音频帧 |
| Frame 49 | row 20993 | 13574.7ms | 18.6ms | `tts.sentence_end` |
| **Frame 50** | **row 21237** | **13589.4ms** | **14.7ms** | **`tts.stop`** |

### URC 原始内容

```
+MIPURC: "rtcp",0,55,81357B2274797065223A22747473222C227374617465223A2273746F70222C2273657373696F6E5F6964223A223366326633373861227D
                      ^^
                      81 → Text/JSON 帧（见第3节：开头 82=Opus，81=JSON）
```

hex 字符串共 110 字符 = 55 字节，解码后：Header=2B + Payload=53B。  
Payload UTF-8 内容：`{"type":"tts","state":"stop","session_id":"3f2f378a"}`

---

## 8.1 tts_stop 前面那帧（Frame 49）是什么？

Frame 49（row=20993，t=13574.7ms）同样是 `0x81` 开头的 Text/JSON 帧，是 **tts.sentence_end**：

**URC 原始文本：**
```
+MIPURC: "rtcp",0,109,816B7B2274797065223A22747473222C227374617465223A2273656E74656E63655F656E64222C2274657874223A22E4BDA0E5A5BDE59180EFBC8CE4BB8AE5A4A9E8BF87E5BE97E6808EE4B988E6A0B7EFBC9F222C2273657373696F6E5F6964223A223366326633373861227D
                       ^^
                       81 → Text/JSON 帧
```

**逐字节标注（共 109 字节）：**
```
偏移  hex   含义
[0]   81    10000001b → FIN=1, Opcode=0x1 (Text/JSON)
[1]   6B    01101011b → MASK=0, PayloadLen_raw=107（直接长度）
[2..108]    Payload（107字节 UTF-8）：
            E4 BD A0 E5 A5 BD ...（含 UTF-8 多字节中文）
            = {"type":"tts","state":"sentence_end","text":"你好呀，今天过得怎么样？","session_id":"3f2f378a"}
```

**与 tts_stop 对比：**

| | Frame 49 | Frame 50 |
|--|----------|----------|
| Byte[0] | `0x81` | `0x81` |
| Byte[1] | `0x6B`（107字节）| `0x35`（53字节）|
| JSON state | `"sentence_end"` | `"stop"` |
| 携带 text | 有（"你好呀，今天过得怎么样？"）| 无 |

两帧都是 `0x81` 开头（Text/JSON），只能通过解析 Payload JSON 中的 `"state"` 字段来区分：  
`"sentence_end"` = 本句话朗读完毕；`"stop"` = 整个 TTS 会话结束。

---

## 9. 如何区分 Opus 帧与 tts_stop 帧

hex 解码后，只需检查第一个字节（WebSocket Byte[0]）：

| 第一字节 | 值 | Opcode | 类型 |
|----------|-----|--------|------|
| `0x82` | FIN=1, Binary | 0x2 | **Opus 音频帧** |
| `0x81` | FIN=1, Text | 0x1 | **JSON 控制帧**（tts_stop、tts_start 等） |

**代码示例：**

```python
raw = bytes.fromhex(hex_data)
b0, b1 = raw[0], raw[1]
opcode   = b0 & 0x0F
pl_raw   = b1 & 0x7F

# 确定 Header 长度和 Payload 偏移
if pl_raw < 126:
    payload_len = pl_raw
    payload = raw[2:]           # Header=2B，直接长度
elif pl_raw == 126:
    payload_len = int.from_bytes(raw[2:4], 'big')
    payload = raw[4:]           # Header=4B，扩展长度（0x7E=126 是标志，非实际长度）
else:  # pl_raw == 127
    payload_len = int.from_bytes(raw[2:10], 'big')
    payload = raw[10:]

if opcode == 0x2:
    # Binary 帧 → Opus 音频
    opus_len  = int.from_bytes(payload[:4], 'big')  # xiaozhi 内部长度头
    opus_data = payload[4:4 + opus_len]
    toc = opus_data[0]  # 0x6B: Hybrid SWB, 20ms, Mono, Code=3
    fcc = opus_data[1]  # 0x83: VBR, M=3 | 0x03: CBR, M=3
elif opcode == 0x1:
    # Text 帧 → JSON，解析后判断 type/state
    import json
    msg = json.loads(payload[:payload_len].decode('utf-8'))
    if msg.get('type') == 'tts' and msg.get('state') == 'stop':
        pass  # → tts_stop
```

---

## 10. 数据流完整时序总览

```
8598ms  [HTTP 200 OK]           ← MIPURC"rtcp" 上报 HTTP 响应（非 WS 帧）
9586ms  [HTTP 101 Switching]    ← WebSocket 握手完成
9750ms  Frame 03  Text   hello  ← 服务器 hello（音频参数协商）
9769ms  Frame 04  Text   mcp    ← MCP 初始化
                │  951ms 间隔（用户说话、ASR 识别中）
10721ms Frame 05  Text   tts.start         ← TTS 开始
10739ms Frame 06  Text   stt.text "你好。"  ← 识别结果
11022ms Frame 07  Text   llm.text "😊"     ← LLM 情感
11123ms Frame 08  Text   tts.sentence_start "你好呀，今天过得怎么样？"
                │  227ms 合成耗时
11350ms Frame 09  Binary  Opus #1  ← 第一个 Opus 帧（row 4945）
11369ms Frame 10  Binary  Opus #2  18.6ms（缓冲burst）
11391ms Frame 11  Binary  Opus #3  22.5ms（缓冲burst）
11412ms Frame 12  Binary  Opus #4  21.1ms（缓冲burst）
11463ms Frame 13  Binary  Opus #5  50.6ms（趋于正常）
...     ...       Binary  Opus     ~54~67ms 稳定
13498ms Frame 47  Binary  Opus #39
13556ms Frame 48  Binary  Opus #40 ← 最后一个 Opus 帧（Payload 趋小）
13575ms Frame 49  Text    tts.sentence_end
13589ms Frame 50  Text    tts.stop          ← 全文件最后一帧（row 21237）
```

---

## 11. 总结答疑

| 问题 | 结论 |
|------|------|
| 这些裸数据是什么格式？ | URC 末尾是 **hex 字符串**，对应完整 WebSocket 帧（RFC 6455） |
| 哪些是头？ | hex 解码后 Byte[0]-Byte[1]（可能+2字节扩展长度）=WS Header，**无帧尾** |
| 哪些是 Payload？ | Header 之后全部为 Payload |
| 是 hex 编码吗？ | 是。必须先 `bytes.fromhex()` 才能按协议解析 |
| 前几帧间隔为何短？ | 服务器 TTS 缓冲 burst 发送，第 9~12 帧约 20ms，第 13 帧起恢复 ~60ms |
| "总长200字节" 是指 hex 字符数还是字节数？ | URC 报告的是**字节数**（hex 解码后），Binary 帧实际字节范围 83~243 B |
| hex 解码后是不是只剩一半？ | 是。238 hex 字符 = 119 字节；hex 字符数 ÷ 2 = 实际字节数 |
| 1920字节 PCM 能压缩到这么短吗？ | 24kHz/60ms PCM 实为 2880 字节；Opus 压到 77~235 字节，12:1~37:1 压缩比，完全正常 |
| tts_stop 在哪个位置？ | **Frame 50**，CSV 起始行号 row=21237，t=13589ms，ws_payload=53B |
| 如何与 Opus 帧区分？ | URC hex 字符串**头两字符**：`82...`=Opus，`81...`=JSON；无需解码即可判断（见第 3 节） |
| `0x7E` 是说 Payload 长 126 字节吗？ | **不是**。`0x7E`=126 是 WebSocket 的**扩展长度标志**，真实 Payload 长度由其后 Byte[2-3]（大端 uint16）给出，可以是任意大小（本抓包最大 239B） |
| 为什么有的帧 Header=2B，有的是 4B？ | WS Payload ≤ 125B → Header=2B（直接长度）；WS Payload > 125B → Header=4B（Byte[1]=0x7E + 2字节扩展长度） |
| Opus FCC 字节为何有时是 0x83 有时是 0x03？ | 0x83=VBR（主体帧，子帧大小各异），0x03=CBR（尾部静默帧，子帧等长 25B）。末尾 3 帧（#38~#40）是 TTS 静默填充，自动切 CBR |

---

## 12. CSV 数据的本质：每行是一个 ASCII 字符（字节）

### 12.1 CSV 里的 "8" 和 "1" 是什么？

CSV 抓包工具把 UART 总线上的每一个字节单独记为一行，显示为对应的 ASCII 字符。  
因此 CSV 里的 `8`、`2`、`1`、`A`、`B` 等**都是 ASCII 字符，不是字节值本身**。

以 Frame 9（第一个 Opus 帧，row=4945）为例，看 CSV 原始内容：

```
行号    时间戳(ns)         第3列（UART字节的ASCII表示）
4967,  11350331280.00,  8      ← ASCII字符 '8'，UART上传输的字节是 0x38
4968,  11350342160.00,  2      ← ASCII字符 '2'，UART上传输的字节是 0x32
4969,  11350353000.00,  7      ← ASCII字符 '7'，UART上传输的字节是 0x37
4970,  11350363840.00,  5      ← ASCII字符 '5'，UART上传输的字节是 0x35
```

把这 4 行的第 3 列拼起来，得到字符串 `"8275"`。  
这 4 个字符是 URC hex 数据的前 4 个字符，代表 2 个字节：`0x82 0x75`。

### 12.2 "字符串 82" 与 "字节 0x82" 的关系

```
CSV 字符  →  拼成 hex 字符串  →  bytes.fromhex()  →  真实字节值
  '8'  '2'   →      "82"       →                 →    0x82

0x82 = 10000010b = FIN=1, Opcode=2 → Binary/Opus 帧
```

**不能把 CSV 字符直接当字节值**。`'8'` 的 ASCII 值是 `0x38`，`'2'` 的 ASCII 值是 `0x32`，都不是 `0x82`。  
必须先把字符们**拼成 hex 字符串**，再用 `bytes.fromhex()` 转换，才能得到真正的 WebSocket 字节数据。

### 12.3 特殊字符的处理

CSV 中不可打印字符用 `[XX]` 转义表示：

| CSV 第 3 列 | 含义 | 实际 ASCII 值 |
|------------|------|--------------|
| `[0D]` | 回车 CR，行分隔符 | 0x0D |
| `[0A]` | 换行 LF，行分隔符 | 0x0A |
| `[20]` | 空格，出现在 `+MIPURC: ` 冒号后 | 0x20 |
| `A`~`F` | URC hex 数据中的十六进制字母 | 0x41~0x46 |
| `0`~`9` | URC hex 数据中的十六进制数字 | 0x30~0x39 |

### 12.4 解析流程总结

```
CSV 原始文件
    │ 每行第 3 列取字符，按 [0D][0A] 分割行
    ▼
完整 URC 文本行（字符串）
"+MIPURC: "rtcp",0,119,8275000000716B83..."
    │ 正则提取末尾 hex 部分
    ▼
hex 字符串（纯文本）
"8275000000716B83..."
    │ 头两字符判断帧类型：82=Opus, 81=JSON
    ▼
bytes.fromhex(hex_str)
    │
    ▼
真实字节序列 [0x82, 0x75, 0x00, 0x00, 0x00, 0x71, 0x6B, 0x83, ...]
    │ 按 WebSocket RFC 6455 协议解析
    ▼
Byte[0]=0x82 → Binary 帧
Byte[1]=0x75 → PayloadLen=117
[2..5]=0x00000071 → 内部 Opus 数据长度 = 113 字节
[6]=0x6B → Opus TOC
...
```
