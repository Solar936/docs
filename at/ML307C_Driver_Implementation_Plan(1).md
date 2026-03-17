# ML307C Zephyr驱动实现计划（简化版）

## 文档概述

本文档基于ML307C系列模组的技术文档，制定了在Zephyr RTOS下基于Socket Offload框架实现ML307C驱动的详细计划。

**核心理念：Socket Offload就是将POSIX Socket API简单映射到AT命令，模组固件处理所有TCP/IP协议栈细节。**

## 1. ML307C模组核心特性分析

### 1.1 硬件接口
- **主通信接口**: UART0 (AT命令通道)
- **控制引脚**: 
  - UART0_DTR: DTR控制（休眠唤醒）
  - WAKEUP_OUT: 睡眠状态指示
  - Power/Reset GPIO

### 1.2 支持的功能（基于文档分析）

#### 网络功能
- ✅ 4G LTE Cat.1连接
- ✅ 驻网和PDP激活（支持多路拨号，最多5路CID）
- ✅ 自动拨号和手动拨号模式
- ✅ IPv4和IPv6双栈支持

#### 通信协议
- ✅ **TCP/IP**: `AT+MIPOPEN`, `AT+MIPSEND`, `AT+MIPRD`, `AT+MIPCLOSE`
- ✅ **UDP**: 同TCP命令集
- ✅ **SSL/TLS**: 支持多种加密套件和证书管理
- ✅ **MQTT**: 完整的MQTT客户端实现
- ✅ **HTTP/HTTPS**: GET/POST/PUT等方法
- ✅ **DNS**: 域名解析功能

#### 电源管理
- ✅ 深睡眠模式（Deep Sleep）
- ✅ 延迟睡眠配置
- ✅ DTR引脚唤醒
- ✅ URC睡眠状态上报

#### 其他功能
- ✅ LBS定位
- ✅ LwM2M协议
- ✅ 固件OTA升级
- ✅ SIM卡管理
- ✅ 信号质量查询

### 1.3 关键AT命令集

#### 基础命令
```
AT                      # 测试命令
ATE0/1                  # 回显控制
AT+IPR                  # 波特率设置
AT+CFUN                 # 功能模式设置
AT+CPIN?               # SIM卡状态查询
```

#### 网络注册
```
AT+CEREG?              # EPS网络注册状态
AT+CGDCONT             # 定义PDP上下文
AT+MIPCALL             # 应用层拨号连接
AT+CGACT               # PDP激活/去激活
```

#### Socket操作（重点）
```
AT+MIPCFG              # 配置套接字参数
AT+MIPOPEN             # 打开套接字连接
AT+MIPSEND             # 发送数据
AT+MIPRD               # 读取数据
AT+MIPCLOSE            # 关闭连接
AT+MIPDNS              # DNS查询
```

#### 信号和状态
```
AT+CSQ                 # 信号质量
AT+COPS?               # 运营商选择
AT+CGREG?              # GPRS网络注册
```

#### 电源管理
```
AT+MLPMCFG="sleepmode" # 睡眠模式配置
AT+MLPMCFG="delaysleep"# 延迟睡眠配置
AT+MLPMCFG="sleepind"  # 睡眠URC上报配置
```

## 2. Zephyr驱动架构设计

### 2.1 参考驱动选择

基于分析和代码复杂度评估，ML307C驱动应参考以下实现：

1. **首选参考**: `simcom/sim7080` ⭐⭐⭐⭐⭐
   - AT命令风格最接近（同样使用+MIP系列命令）
   - 代码结构清晰简单，易于理解
   - 实现相对现代，没有（简化理解）

```
┌─────────────────────────────────────────────────────────────┐
│  应用层 (标准POSIX Socket API)                               │
│  socket() connect() send() recv() close()                   │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│  Socket Offload层 (Zephyr框架提供)                          │
│  分发调用到驱动的offload函数                                 │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│  ML307C驱动 - 核心工作就在这里！                             │
│  ┌───────────────────────────────────────────────────────┐  │
│  │ Socket Offload实现 (ml307c_socket.c)                  │  │
│  │ • ml307c_socket()   → 分配socket结构                  │  │
│  │ • ml307c_connect()  → AT+MIPOPEN                     │  │
│  │ • ml307c_send()     → AT+MIPSEND + 数据              │  │
│  │ • ml307c_recv()     → AT+MIPRD                       │  │
│  │ • ml307c_close()    → AT+MIPCLOSE                    │  │
│  └───────────────────────────────────────────────────────┘  │
│  ┌───────────────────────────────────────────────────────┐  │
│  │ AT命令处理 (ml307c.c)                                 │  │
│  │ • 发送AT命令                                          │  │
│  │ • 解析响应 (OK/ERROR/+MIPURC)                        │  │
│  │ • URC处理 (数据到达/连接关闭通知)                     │  │
│  └───────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│  Zephyr Modem通用框架 (现成的，直接用！)                     │
│  • modem_socket - Socket池管理                              │
│  • modem_cmd_handler - AT命令收发框架                       │
│  • modem_iface_uart - UART收发                             │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│  硬件层                                                      │
│  • UART驱动 (Zephyr提供)                                    │
│  • GPIO控制 (Power/Reset/DTR)                              │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│  ML307C模组固件 (这里处理所有TCP/IP协议细节！)               │
│  ✅ TCP三次握手/四次挥手                                     │
│  ✅ TCP分片/重组/重传                                        │
│  ✅ IP路由/分片                                             │
│  ✅ DNS解析                                                 │
│  ✅ 网络协议栈                                              │
│  → 驱动完全不需要关心这些！                                  │
└─────────────────────────────────────────────────────────────┘
```

**关键理解**：
- ✅ **驱动只做映射**：Socket API → AT命令，就这么简单
- ✅ **框架已就绪**：Zephyr提供了所有基础组件，直接用
- ✅ **模组做重活**：TCP/IP协议栈在模组固件里，不是驱动的事
- ❌ **不需要处理**：TCP分片、重传、流控、拥塞控制等  - 命令队列管理                               │
├─────────────────────────────────────────────────┤
│         Modem Context                           │
│    (modem_context.h/c)                         │
│    - 模组状态管理                               │
│    - 初始化序列                                 │
│    - 错误恢复                                   │
├─────────────────────────────────────────────────┤
│         UART Interface                          │
│    (modem_iface_uart.h/c)                      │
│    - UART数据收发                               │
│    - 接收缓冲区管理                             │
├─────────────────────────────────────────────────┤
│         Hardware Layer                          │
│    - UART驱动 (Zephyr UART API)                │
│    - GPIO控制 (Power/Reset/DTR)                │
└─────────────────────────────────────────────────┘
```

### 2.3 核心数据结构

```c
/* ML307C驱动数据结构 */
struct ml307c_data {
    struct net_if *net_iface;
    uint8_t mac_addr[6];
    
    /* UART接口 */
    struct modem_iface_uart_data iface_data;
    uint8_t iface_rb_buf[ML307C_MAX_DATA_LENGTH];
    
    /* AT命令处理 */
    struct modem_cmd_handler_data cmd_handler_data;
    uint8_t cmd_match_buf[ML307C_RECV_BUF_SIZE + 1];
    
    /* Socket配置 */
    struct modem_socket_config socket_config;
    struct modem_socket sockets[ML307C_MAX_SOCKETS];
    
    /* 模组信息 */
    char mdm_manufacturer[16];
    char mdm_model[32];
    char mdm_revision[64];
    char mdm_imei[16];
    char mdm_imsi[16];
    char mdm_iccid[32];
    
    /* 网络状态 */
    int rssi;
    int ber;
    bool registered;
    bool pdp_active;
    
    /* 电源管理 */
    bool sleep_enabled;
    struct k_work_delayable sleep_work;
    
    /* RSSI查询 */
    struct k_work_delayable rssi_query_work;
    
    /* 信号量 */
    st简化实现步骤

### 核心文件（最小集合）

```
zephyr/drivers/modem/ml307c/
├── CMakeLists.txt              # 构建配置
├── Kconfig                     # 配置选项
├── ml307c.c                    # 主驱动：初始化、AT基础、URC
├── ml307c.h                    # 头文件
└── ml307c_socket.c             # Socket offload实现
```

**设备树绑定**（可选，后期添加）:
```
zephyr/dts/bindings/modem/chinamobile,ml307c.yaml
```

### 阶段1: 基础框架（2-3天）

#### 步骤1.1: 创建基础文件
**参考**: `drivers/modem/simcom/sim7080/`

**任务清单**:
- [ ] 创建目录 `drivers/modem/ml307c/`
- [ ] 复制SIM7080的CMakeLists.txt和Kconfig作为模板
- [ ] 创建ml307c.c和ml307c.h
- [ ] 修改配置名称（SIM7080 → ML307C）c/
├── CMakeLists.txt
├── Kconfig
├── ml307c.c           # 主驱动文件
├── ml307c.h           # 头文件
├── ml307c_socket.c    # Socket实现
└── dts/bindings/      # 设备树绑定
    └── chinamobile,ml307c.yaml
```
基础初始化（最简版）
**文件**: `ml307c.c`

**参考**: `sim7080/sim7080.c` 的 `modem_init()`

```c
static int ml307c_init(const struct device *dev)
{
    // 1. GPIO初始化（如果有）
    if (power_gpio.port) {
        gpio_pin_configure_dt(&power_gpio, GPIO_OUTPUT_ACTIVE);
    }
    
    // 2. 初始化modem接口（UART）
    ret = modem_iface_uart_init(&mctx.iface, &mdata.iface_data, 
                                MDM_UART_DEV);
    
    // 3. 初始化AT命令处理器
    ret = modem_cmd_handler_init(&mctx.cmd_handler, 
                                 &mdata.cmd_handler_data);
    
    // 4. 启动RX线程
    k_thread_create(&modem_rx_thread, ...);
    和URC处理（2-3天）

#### 步骤2.1: 注册URC处理器（核心！）
**文件**: `ml307c.c`

**参考**: `sim7080/sim7080_sock.c` 的URC处理

**实现内容**：只需要2个关键URC！

```c
/* URC 1: 数据到达通知 */
MODEM_CMD_DEFINE(on_cmd_recv_urc)
{
    // +MIPURC: "recv",<id>,<length>
    int sock_id = atoi(argv[0]);
    int length = atoi(argv[1]);
    
    struct modem_socket *sock = modem_socket_from_id(&mdata.socket_config, sock_id);
    if (sock) {
        // 更新可读数据大小
        modem_socket_packet_size_update(&mdata.socket_config, sock, length);
        // 唤醒等待的recv
        modem_socket_data_ready(&mdata.socket_config, sock);
    }
    return 0;
}

/* URC 2: 连接关闭通知 */
MODEM_CMD_DEFINE(on_cmd_close_urc)
{
    // +MIPURC: "close",<id>
    int sock_id = atoi(argv[0]);
    
    struct modem_socket *sock = modem_socket_from_id(&mdata.socket_config, sock_id);
    if (sock) {
        sock->is_connected = false;
        // 唤醒可能在等待的recv
        modem_socket_data_ready(&mdata.socket_config, sock);
    }
    return 0;
}
连接（简化版）
**文件**: `ml307c.c`

**参考**: `sim7080/sim7080_pdp.c`

```c
static int ml307c_activate_pdp(void)
{
    int ret;
    
    // 1. 检查网络注册状态
    ret = modem_cmd_send(&mctx.iface, &mctx.cmd_handler,
                         NULL, 0, "AT+CEREG?", 
                         &mdata.sem_response, K_SECONDS(10));
    if (ret < 0) return ret;
    
    // 2. 激活PDP（ML307C通常自动拨号，如未激活则手动）
    ret = modem_cmd_send(&mctx.iface, &mctx.cmd_handler,
                         NULL, 0, "AT+MIPCALL=1,1", 
                         &mdata.sem_response, K_SECONDS(30));
    
    return ret;
}
```

**注意**：
- ML307C支持自动拨号，初始化时可能已经激活
- 可以先查询 `AT+MIPCALL?` 检查状态
- 简化起见，初期可以假设网络已就绪DP状态变化
+MIPURC: "recv",<id>,<length>                             # 数据到达
+MIPURC: "close",<id>[,<error_no>]                        # 连接关闭
+CEREG: <n>,<stat>[...]                                   # 网络注册状态
+MLPMENTER: <mode>                                        # 进入睡眠
+MLPMEXIT: <mode>                                         # 退出睡眠
```

**关键函数**:
- `ml307c_cmd_handler_setup()` - 命令处理器初始化
- `ml307c_rx_handler()` - 接收数据处理
- `ml307c_urc_handler()` - URC处理分发

**参考**:
- `quectel-bg9x.c`: `on_cmd_*()` 系列函数
- `modem_cmd_handler.c`: 命令处理框架
3-4天，核心工作！）

#### 步骤3.1: Socket创建和连接（最简单！）
**文件**: `ml307c_socket.c`

**参考**: `sim7080/sim7080_sock.c` 几乎可以直接复制！

```c
/* 1. Socket创建 - 就一行代码！ */
static int ml307c_socket(int family, int type, int proto)
{
    return modem_socket_get(&mdata.socket_config, family, type, proto);
}

/* 2. TCP连接 - 简单映射AT命令 */
static int ml307c_connect(void *obj, const struct sockaddr *addr, 
                          socklen_t addrlen)
{
    struct modem_socket *sock = obj;
    char cmd[128];
    char ip_str[INET6_ADDRSTRLEN];
    uint16_t port;
    int ret;
    
    // 提取IP和端口
    ret = modem_context_sprint_ip_addr(addr, ip_str, sizeof(ip_str));
    port = ntohs(net_sin(addr)->sin_port);  // 假设IPv4
    
    // 构建AT命令
    snprintf(cmd, sizeof(cmd), "AT+MIPOPEN=%d,\"TCP\",\"%s\",%d",
             sock->id, ip_str, port);
    
    // 发送命令
    ret = modem_cmd_send(&mctx.iface, &mctx.cmd_handler,
                         NULL, 0, cmd, &mdata.sem_response, 
                         K_SECONDS(30));
    if (ret == 0) {
        sock->is_connected = true;
    }
    
    return ret;
}

/* 3. 关闭 - 也很简单 */
static int ml307c_close(void *obj)
{
    struct modem_socket *sock = obj;
    char cmd[32];
    
    snprintf(cmd（关键但不复杂）
**文件**: `ml307c_socket.c`

**参考**: `sim7080/sim7080_sock.c` 的 `offload_sendto()` 和 `offload_recvfrom()`

```c
/* 发送数据 - 三步骤 */
static ssize_t ml307c_sendto(void *obj, const void *buf, size_t len,
                             int flags, const struct sockaddr *dest_addr,
                             socklen_t addrlen)
{
    struct modem_socket *sock = obj;
    char cmd[32];
    int ret;
    
    // 步骤1: 发送 AT+MIPSEND=<id>,<len>
    snprintf(cmd, sizeof(cmd), "AT+MIPSEND=%d,%zu", sock->id, len);
    ret = modem_cmd_send_nolock(&mctx.iface, &mctx.cmd_handler,
                                NULL, 0, cmd, NULL, K_NO_WAIT);
    
    // 步骤2: 等待 '>' 提示符（命令处理器自动处理）
    k_sem_take(&mdata.sem_tx_ready, K_SECONDS(5));
    
    // 步骤3: 直接发送数据
    mctx.iface.write(&mctx.iface, buf, len);
    mctx.iface.write(&mctx.iface, "\x1A", 1);  // Ctrl+Z结束
    
    // 等待OK
    k_sem_take(&mdata.sem_response, K_SECONDS(5));
    
    return len;
}

/* 接收数据 - 简单查询 */
static ssize_t ml307c_recvfrom(void *obj, void *buf, size_t max_len,
                               int flags, struct sockaddr *from,
                               socklen_t *fromlen)
{
    struct modem_socket *sock = obj;
    char cmd[64];
    struct socket_read_data sock_data;
    int ret, packet_size;
    
    // 检查是否有数据（由URC设置）
    packet_size = modem_socket_next_packet_size(&mdata.socket_config, sock);
    if (!packet_size) {
        if (flags & ZSOCK_MSG_DONTWAIT) {
            errno = EAGAIN;
            return -1;
        }
        // 阻塞等待数据（URC会唤醒）
        modem_socket_wait_data(&mdata.socket_config, sock);
    }
    
    // 读取数据：AT+MIPRD=<id>,<len>
    snprintf(cmd, sizeof(cmd), "AT+MIPRD=%d,%zu", sock->id, max_len);
    
    sock_data.recv_buf = buf;
    sock_data.recv_buf_len = max_len;
    sock->data = &sock_data;
    
    // 发送命令并解析响应
    struct modem_cmd data_cmd[] = {
        MODEM_CMD("+MIPRD: ", on_cmd_miprd, 2U, ",")  // 解析数据
    };
    ret = modem_cmd_send(&mctx.iface, &mctx.cmd_handler,
                         data_cmd, 1, cmd, &mdata.sem_response, K_SECONDS(10));
    
    return (ret < 0) ? ret : sock_data.recv_read_len;
}

/* 数据解析回调 */
MODEM_CMD_DEFINE(on_cmd_miprd)
{
    // +MIPRD: <id>,<actual_len>\r\n[数据]
    int actual_len = atoi(argv[1]);
    struct socket_read_data *sock_data = sock->data;
    
    // 从rx_buf拷贝数据到应用缓冲区
    ret = net_buf_linearize(sock_data->recv_buf, sock_data->recv_buf_len,
                           data->rx_buf, 0, actual_len);
    sock_data->recv_read_len = ret;
    
    return ret;
}
```

**关键点**：
- ✅ 不需要处理TCP序列号、确认、重传 - 模组做
- ✅ 不需要处理TCP流控、拥塞控制 - 模组做
- ✅ 只需要：发AT命令 → 等响应 → 拷贝数据
```c
/* 发送函数 */
zsock_sendto_impl() {
    1. 检查socket状态
    2. 发送 AT+MIPSEND=<id>,<length>
    3. 等待 '>' 提示符
    4. 发送实际数据
    5. 等待发送完成响应
}

/* 接收函数 */
zsock_recvfrom_impl() {
    1. 检查是否有数据（通过URC或查询）
    2. 发送 AT+MIPRD=<id>,<length>
    3. 解析响应，提取数据
    4. 更新socket数据包计数
    5. 返回接收到的数据
}
```

**URC数据接收流程**:
```c
/* 当收到 +MIPURC: "recv",<id>,<length> */
1. 找到对应的socket
2. 更新socket的可读数据大小
3. 发送信号量唤醒阻塞的recv调用
4. 调用 modem_socket_data_ready()
```

**关键函数**:
- `ml307c_sendto()` - 数据发送实现
- `ml307c_recvfrom()` - 数据接收实现
- `ml307c_prepare_send()` - 发送准备
- `on_cmd_sockread()` - 接收URC处理

**参考**:
- `quectel-bg9x.c`: `offload_sendto()`, `offload_recvfrom()`, `on_cmd_sockread_common()`
- 文档: [TCP_IP用户手册_V5.1.5.md] 的数据收发命令

#### 步骤3.3: Socket选项和控制
**文件**: `ml307c_socket.c`

**实现内容**:
```c
/* Socket选项 */
zsock_setsockopt_impl() -> 设置超时、缓冲区等
zsock_getsockopt_impl() -> 获取socket选项
zsock_fcntl_impl()      -> 设置非阻塞等

/* Socket信息 */
zsock_getsockname_impl() -> 获取本地地址
zsock_getpeername_impl() -> 获取对端地址
```

**可使用的ML307C配置命令**:
```c
AT+MIPCFG=<id>,"tcp_timeout",<timeout>     // TCP超时
AT+MIPCFG=<id>,"tcp_alive",<keepalive>     // TCP keepalive
AT+MIPCFG=<id>,"recv_mode",<mode>          // 接收模式（手动/自动）
```

#### 步骤3.4: Poll支持
**文件**: `ml307c_socket.c`

**实现内容**:
```c
zsock_poll_impl() {
    1. 为每个pollfd创建poll事件
    2. 注册到socket的poll信号
    3. 等待事件或超时
    4. 返回就绪的socket数量
}
```

**参考**:
- `modem_socket.c`: `modem_socket_poll()`

### 阶段4: 高级功能实现（优先级：中）

#### 步骤4.1: DNS解析
**文件**: `ml307c.c` 或 `ml307c_dns.c`

**实现内容**:
```c
/* DNS查询 */
ml307c_getaddrinfo() {
    1. 发送 AT+MIPDNS="<domain>"
    2. 解析响应 +MIPDNS: "<ip>"
    3. 填充 addrinfo 结构
}
```

**AT命令**:
```
AT+MIPDNS="www.baidu.com"
+MIPDNS: "36.152.44.95"
OK
```

**参考**:
- `quectel-bg9x.c`: `offload_getaddrinfo()`

#### 步骤4.2: SSL/TLS支持
**文件**: `ml307c_ssl.c`

**实现内容**:
```c
/* SSL配置 */
1. 证书管理 (AT+MSSL="cert"...)
2. SSL配置 (AT+MSSL="seclevel"...)
3. SSL socket创建 (在MIPOPEN中启用SSL)
4. SSL上下文复用
```

**参考**:
- 文档: [SSL用户手册_V5.4.5.md](SSL用户手册_V5.4.5.md)

#### 步骤4.3: 电源管理
**文件**: `ml307c_pm.c`

**实现内容**:
```c
/* 低功耗模式 */
1. 深睡眠模式配置
2. DTR引脚控制
3. 睡眠状态监控
4. 自动睡眠管理

/* PM回调 */
pm_action_cb() {
    - PM_DEVICE_ACTION_SUSPEND: 进入睡眠
    - PM_DEVICE_ACTION_RESUME: 唤醒
}
```

**关键AT命令**:
```c
AT+MLPMCFG="sleepmode",2,0        // 启用深睡眠
AT+MLPMCFG="delaysleep",<sec>    // 配置延迟睡眠
AT+MLPMCFG="sleepind",2          // 启用睡眠URC
```

**参考**:
- 文档: [ML307C_通信流程示例_V1.0.0.md](ML307C_通信流程示例_V1.0.0.md) 第2章

### 阶段5: 测试和优化（优先级：中）

#### 步骤5.1: 单元测试
**创建测试**:
```
tests/drivers/modem/ml307c/
├── src/
│   ├── test_ml307c_basic.c      # 基础功能测试
│   ├── test_ml307c_socket.c     # Socket测试
│   └── test_ml307c_network.c    # 网络测试
└── testcase.yaml
```

**测试内容**:
- [ ] AT命令发送接收
- [ ] Socket创建/关闭
- [ ] TCP连接和数据传输
- [ ] UDP数据收发
- [ ] DNS解析
- [ ] 多socket并发
- [ ] 错误处理和恢复

#### 步骤5.2: 示例应用
**创建示例**:
```
samples/drivers/modem/ml307c/
├── tcp_echo/          # TCP回显测试
├── udp_echo/          # UDP回显测试
├── http_get/          # HTTP GET测试
└── mqtt_client/       # MQTT客户端测试
```

#### 步骤5.3: 性能优化
- [ ] 数据传输效率优化
- [ ] AT命令响应时间优化
- [ ] 内存使用优化
- [ ] 功耗优化

## 4. 设备树配置示例

### 4.1 设备树绑定文件
**文件**: `dts/bindings/modem/chinamobile,ml307c.yaml`

```yaml
description: China Mobile ML307C LTE Cat.1 modem

compatible: "chinamobile,ml307c"

include: [base.yaml, uart-device.yaml]

properties:
  mdm-power-gpios:
    type: phandle-array
    required: true
    description: GPIO for power control

  mdm-reset-gpios:
    type: phandle-array
    required: false
    description: GPIO for reset control

  mdm-dtr-gpios:
    type: phandle-array
    required: false
    description: GPIO for DTR (sleep/wakeup control)

  mdm-wakeup-out-gpios:
    type: phandle-array
    required: false
    description: GPIO for wakeup output status

  apn:
    type: string
    required: false
    description: APN for network connection

  uart-baudrate:
    type: int
    default: 115200
    description: UART baudrate

  mdm-power-on-pulse-ms:
    type: int
    default: 2000
    description: Power-on pulse duration in milliseconds

  mdm-reset-pulse-ms:
    type: int
    default: 200
    description: Reset pulse duration in milliseconds

  auto-start:
    type: boolean
    description: Automatically start network connection on boot
```

### 4.2 DTS应用示例
**文件**: `boards/arm/your_board/your_board.dts`

```dts
/ {
    ml307c_modem: ml307c {
        compatible = "chinamobile,ml307c";
        status = "okay";
        
        /* UART接口 */
        uart = <&uart2>;
        uart-baudrate = <115200>;
        
        /* GPIO控制 */
        mdm-power-gpios = <&gpio0 12 GPIO_ACTIVE_HIGH>;
        mdm-reset-gpios = <&gpio0 13 GPIO_ACTIVE_LOW>;
        mdm-dtr-gpios = <&gpio0 14 GPIO_ACTIVE_LOW>;
        mdm-wakeup-out-gpios = <&gpio0 15 GPIO_ACTIVE_HIGH>;
        
        /* 网络配置 */
        apn = "cmnet";
        auto-start;
        
        /* 时序配置 */
        mdm-power-on-pulse-ms = <2000>;
        mdm-reset-pulse-ms = <200>;
    };
};

/* UART配置 */
&uart2 {
    status = "okay";
    current-speed = <115200>;
    pinctrl-0 = <&uart2_default>;
    pinctrl-names = "default";
};
```

### 4.3 Overlay示例
**文件**: `samples/drivers/modem/ml307c/tcp_echo/boards/your_board.overlay`

```dts
&ml307c_modem {
    apn = "cmiot";
};
```

## 5. Kconfig配置

### 5.1 主配置文件
**文件**: `drivers/modem/ml307c/Kconfig`

```kconfig
# ML307C Modem Driver Configuration

menuconfig MODEM_ML307C
    bool "China Mobile ML307C LTE Cat.1 modem driver"
    depends on NET_OFFLOAD
    select MODEM_CONTEXT
    select MODEM_CMD_HANDLER
    select MODEM_IFACE_UART
    select MODEM_SOCKET
    select NET_SOCKETS_OFFLOAD
    select GPIO
    help
      Enable ML307C LTE Cat.1 modem driver.

if MODEM_ML307C

config MODEM_ML307C_RX_STACK_SIZE
    int "Size of the stack for the ML307C modem driver RX thread"
    default 1024
    help
      This stack is used by the ML307C RX thread.

config MODEM_ML307C_RX_WORKQ_STACK_SIZE
    int "Size of the stack for the ML307C work queue"
    default 2048
    help
      Stack size for the work queue thread.

config MODEM_ML307C_INIT_PRIORITY
    int "ML307C driver init priority"
    default 80
    help
      ML307C device driver initialization priority.
      Must be initialized after UART and GPIO drivers.

config MODEM_ML307C_APN
    string "Access Point Name (APN)"
    default ""
    help
      Specify APN for the network connection.
      Leave empty to use default.

config MODEM_ML307C_USERNAME
    string "APN username"
    default ""
    help
      Username for APN authentication (if required).

config MODEM_ML307C_PASSWORD
    string "APN password"
    default ""
    he简化实现清单（按优先级）

### 🎯 第一阶段：最小可用版本（MVP，7-10天）

#### Week 1: 基础功能
- [ ] 创建文件结构和配置（0.5天）
- [ ] UART和GPIO初始化（0.5天）
- [ ] AT命令基础设施（1天）
- [ ] URC处理框架（0.5天）
  - [ ] 数据到达URC (+MIPURC: "recv")
  - [ ] 连接关闭URC (+MIPURC: "close")
- [ ] 网络激活（简化版，0.5天）

#### Week 2: Socket核心
- [ ] Socket创建/关闭（0.5天）
- [ ] TCP连接 (ml307c_connect) （1天）
- [ ] 数据发送 (ml307c_sendto) （1.5天）
- [ ] 数据接收 (ml307c_recvfrom) （1.5天）
- [ ] 基础测试（echo_client）（0.5天）

**里程碑**: 可以运行标准的TCP echo客户端！

---

### 🚀 第二阶段：完善功能（可选，3-5天）

- [ ] UDP支持（1天）
- [ ] DNS解析 (AT+MIPDNS)（0.5天）
- [ ] Poll支持（1天）
- [ ] 错误处理增强（0.5天）
- [ ] 多Socket测试（1天）

**里程碑**: 通过Zephyr socket测试套件

---

### 📈 第三阶段：高级功能（按需，5-7天）

- [ ] SSL/TLS支持（2-3天）
- [ ] TCP服务端模式（1-2天）
- [ ] Socket（修正版）

### 保守估算（适合新手）
| 阶段 | 内容 | 预估时间 |
|------|------|----------|
| 第一阶段 | 基础框架和初始化 | 2-3天 |
| 第二阶段 | AT命令和URC | 2-3天 |
| 第三阶段 | TCP Socket核心功能 | 3-4天 |
| **MVP总计** | **最小可用版本** | **7-10天** |
| 第四阶段 | UDP和DNS | 2-3天 |
| 第五阶段 | 高级功能和优化 | 3-5天 |
| **完整版总计** | | **12-18天** |

### 乐观估算（有经验）（按重要性排序）

#### 🌟 主要参考（必读）
- `zephyr/drivers/modem/simcom/sim7080/sim7080.c` - **首选！** 初始化流程
- `zephyr/drivers/modem/simcom/sim7080/sim7080_sock.c` - **首选！** Socket实现
- `zephyr/drivers/modem/simcom/sim7080/sim7080_dns.c` - DNS实现
- `zephyr/drivers/modem/simcom/sim7080/sim7080_pdp.c` - PDP管理

#### 📚 框架组件（现成的，直接用）
- `zephyr/drivers/modem/modem_socket.c/.h` - Socket池管理
- `zephyr/drivers/modem/modem_cmd_handler.c/.h` - AT命令框架
- `zephyr/drivers/modem/modem_context.c/.h` - 上下文管理
- `zephyr/drivers/modem/modem_iface_uart.c/.h` - UART接口
- `zephyr/include/zephyr/net/socket_offload.h` - Socket Offload API

#### 🔍 次要参考（可选）
- `zephyr/drivers/modem/quectel-bg9x.c` - 另一种实现风格
- `zephyr/drivers/modem/ublox-sara-r4.c` - 传统实现方式

#### ❌ 不推荐
- `zephyr/drivers/modem/hl78xx/` - 太复杂，过度设计
- ❌ **调试时间**：硬件调试可能耗时
- ❌ **网络环境**：需要SIM卡和真实网络
- [ ] 性能优化（1-2天）
- [ ] 完整文档和示例（1-2天）

---

### ✅ 成功标准（MVP）

- ✅ 可以初始化模组
- ✅ 可以建立TCP连接
- ✅ 可以收发TCP数据
- ✅ 可以运行 `samples/net/sockets/echo_client`
- ✅ 稳定运行5分钟不崩溃
config MODEM_ML307C_RSSI_WORK
    bool "Enable periodic RSSI polling"
    default y
    help
      Enable periodic polling of RSSI and network status.

config MODEM_ML307C_RSSI_WORK_PERIOD
    int "RSSI polling period (seconds)"
    depends on MODEM_ML307C_RSSI_WORK
    default 30
    range 10 300
    help
      Period for polling RSSI and network status.

config MODEM_ML307C_POWER_MANAGEMENT
    bool "Enable power management support"
    depends on PM
    default n
    help
      常见问题和解决方案

### Q1: 我需要处理TCP分片/重传吗？
**答案**: ❌ **完全不需要！**
- ML307C固件内部处理所有TCP/IP协议栈
- 驱动只负责：AT命令 ←→ Socket API映射
- 不需要关心：序列号、确认、窗口、MTU等

### Q2: 数据收发需要自己实现缓冲区吗？
**答案**: ⚠️ **部分需要**
- ✅ 应用数据缓冲区：由应用/Zephyr提供
- ✅ UART接收缓冲区：`modem_iface_uart`提供
- ⚠️ 临时解析缓冲区：需要少量定义（几百字节）

### Q3: 多个Socket并发怎么处理？
**答案**: ✅ **框架已解决**
- `modem_socket`管理Socket池（8个ID）
- URC自动路由到对应Socket
- 每个Socket独立的信号量和状态

### Q4: AT命令需要并发发送吗？
**答案**: ❌ **必须串行！**
- UART只有一个，AT命令必须一个一个发
- `modem_cmd_handler`自动串行化
- 使用信号量保证互斥

### Q5: 最容易遇到的坑是什么？
**答案**: ⚠️ **这几个**
1. **忘记处理URC** → 数据到达但recv阻塞
2. **命令超时太短** → 网络慢时失败
3. **没有等+MATREADY** → 模组未就绪
4. **字符串格式错误** → AT命令解析失败
5. **硬件时序不对** → 上电复位失败

### Q6: 调试技巧？
**答案**: ✅ **这样做**
```c
// 1. 开启UART日志，看AT命令交互
CONFIG_MODEM_LOG_LEVEL_DBG=y

// 2. 使用串口监控工具同时监听
// 3. 先用AT工具手动测试命令
// 4. 单独测试每个函数
// 5. 从最简单的开始（connect、send）
```rs/modem/Kconfig`

添加:
```kconfig
rsource "ml307c/Kconfig"
```

## 6. 实现清单和优先级

### 高优先级（核心功能）
- [ ] **P0**: 基础驱动框架和设备初始化
- [ ] **P0**: AT命令处理和URC机制
- [ ] **P0**: 网络注册和PDP激活
- [ ] **P0**: TCP Socket基础功能（create/connect/send/recv/close）
- [ ] **P0**: UDP Socket基础功能
- [ ] **P0**: DNS解析
- [ ] **P0**: Socket Poll支持

### 中优先级（扩展功能）
- [ ] **P1**: SSL/TLS支持
- [ ] **P1**: 多Socket并发管理
- [ ] **P1**: RSSI和信号质量查询
- [ ] **P1**: 错误处理和自动恢复
- [ ] **P1**: TCP服务端模式（listen/accept）
- [ ] **P1**: Socket选项配置（timeout, keepalive等）

### 低优先级（高级功能）
- [ ] **P2**: 电源管理和睡眠模式
- [ ] **P2**: IPv6支持
- [ ] **P2**: 多PDP上下文支持
- [ ] **P2**: SIM卡热插拔支持
- [ ] **P2**: 性能优化和调优

### 测试和文档
- [ ] 单元测试套件
- [ ] 集成测试
- [ ] 示例应用
- [ ] 用户文档
- [ ] API文档

## 7. 开发时间估算

| 阶段 | 内容 | 预估时间 |
|------|------|----------|
| 阶段1 | 基础框架搭建 | 3-5天 |
| 阶段2 | AT命令处理 | 5-7天 |
| 阶段3 | Socket Offload (P0) | 7-10天 |
| 阶段4 | 高级功能 (P1) | 5-7天 |
| 阶段5 | 测试和优化 | 5-7天 |
| **总计** | | **25-36天** |

## 8. 参考资源

### 8.1 Zephyr源码参考
- `zephyr/drivers/modem/quectel-bg9x.c` - 主要参考
- `zephyr/drivers/modem/ublox-sara-r4.c` - 次要参考
- `zephyr/drivers/modem/modem_socket.c/.h` - Socket管理
- `zephyr/drivers/modem/modem_context.c/.h` - 上下文管理
- `zephyr/drivers/modem/modem_cmd_handler.c/.h` - AT命令处理
- `zephyr/include/zephyr/net/socket_offload.h` - Socket Offload API

### 8.2 ML307C文档
- AT_Commands_Reference_Guide_4G_Series_V2.0.5.md
- ML307C_通信流程示例_V1.0.0.md
- TCP_IP用户手册_V5.1.5.md
- SSL用户手册_V5.4.5.md
- MQTT用户手册_V6.8.5.md
- HTTP_HTTPS用户手册_V6.1.4.md

### 8.3 相关标准和协议
- RFC 793 - TCP
- RFC 768 - UDP
- RFC 1035 - DNS
- RFC 5246 - TLS 1.2
- Hayes AT Command Set

## 9. 潜在挑战和解决方案

### 挑战1: AT命令响应解析的复杂性
**问题**: ML307C的某些AT命令响应格式复杂，包含多行、可选字段等
**解决方案**: 
- 使用Zephyr的`modem_cmd_handler`提供的灵活匹配机制
- 为复杂响应编写专门的解析函数
- 充分利用URC机制异步处理状态变化

### 挑战2: 多Socket并发管理
**问题**: 需要处理多个Socket的并发数据收发
**解决方案**:
- 使用`modem_socket`框架提供的Socket池管理
- 通过URC及时处理每个Socket的数据到达事件
- 使用信号量和Poll机制协调阻塞和非阻塞操作

### 挑战3: 数据完整性保证
**问题**: UART数据传输中可能出现数据丢失或乱序
**解决方案**:
- 实现可靠的接收缓冲区管理
- 使用AT命令层面的确认机制
- 实现超时重传机制

### 挑战4: 错误恢复和稳定性
**问题**: 模组可能因各种原因进入异常状态
**解决方案**:
- 实现完善的错误检测机制
- 设计分级的错误恢复策略（软重启、硬重启）
- 记录详细的调试日志
- 实现看门狗和健康检查机制

## 10. 下一步行动

1. **立即开始**: 创建基础目录结构和配置文件
2. **第一周**: 完成基础框架和设备初始化
3. **第二周**: 实现AT命令处理和网络连接
4. **第三周**: 实现核心Socket功能
5. **第四周**: 添加高级功能和进行测试

## 11. 成功标准

驱动实现成功的标准：
- ✅ 可以成功初始化ML307C模组
- ✅ 可以连接到蜂窝网络并激活PDP
- ✅ 可以创建TCP连接并收发数据
- ✅ 可以创建UDP Socket并收发数据
- ✅ 可以进行DNS解析
- ✅ 可运行标准的Socket应用（如echo_server/echo_client）
- ✅ 可以同时管理多个Socket连接
- ✅ 通过Zephyr的Socket测试套件
- ✅ 稳定运行，无内存泄漏

---

**文档版本**: 1.0  
**创建日期**: 2026-02-07  
**最后更新**: 2026-02-07  
**作者**: GitHub Copilot (Claude Sonnet 4.5)
