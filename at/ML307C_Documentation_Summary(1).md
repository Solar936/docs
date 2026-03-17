# ML307C技术文档总结

本文档总结了ML307C系列模组的关键技术特性，供驱动开发参考。

## 📚 文档列表

### 核心文档
1. **AT_Commands_Reference_Guide_4G_Series_V2.0.5.md** - AT命令参考手册（英文）
2. **ML307C_通信流程示例_V1.0.0.md** - 通信流程和配置示例
3. **TCP_IP用户手册_V5.1.5.md** - TCP/IP协议实现

### 协议文档
4. **SSL用户手册_V5.4.5.md** - SSL/TLS加密通信
5. **MQTT用户手册_V6.8.5.md** - MQTT协议
6. **HTTP_HTTPS用户手册_V6.1.4.md** - HTTP/HTTPS协议
7. **LwM2M用户手册_V5.2.2.md** - LwM2M物联网协议

### 功能文档
8. **LBS用户手册_4G系列_V1.6.4.md** - 基站定位服务
9. **ML307C_固件升级用户手册_V1.0.0.md** - OTA固件升级
10. **ML307C_拨号上网用户手册_V1.0.0.md** - RNDIS拨号上网
11. **ML307C_Android_RIL_驱动开发指导手册_V1.0.0.md** - Android集成
12. **扩展AT用户手册_4G系列_V1.7.4.md** - 扩展AT命令

## 🎯 核心技术特性

### 硬件接口
- **主通信**: UART（默认115200bps，支持自适应波特率）
- **控制引脚**: 
  - UART0_DTR: 睡眠唤醒控制
  - WAKEUP_OUT: 睡眠状态输出
  - Power/Reset GPIO

### 网络功能
- **制式**: 4G LTE Cat.1
- **频段**: 支持多个LTE频段
- **网络**: 支持中国移动/联通/电信
- **IP协议**: IPv4/IPv6双栈
- **PDP**: 支持多达5路PDP上下文（CID 1-7,9-15，CID8为IMS专用）

### Socket功能
- **支持协议**: TCP客户端/服务端、UDP
- **最大连接数**: 8个并发Socket
- **数据缓冲**: 支持大数据包传输
- **SSL/TLS**: 支持加密连接
- **DNS**: 内置DNS解析

### 电源管理
- **深睡眠**: 支持深度睡眠模式
- **唤醒方式**: 
  - DTR引脚低电平（永久唤醒）
  - 来电/短信（临时唤醒）
  - 网络数据（临时唤醒）  
  - AT命令（临时唤醒）
- **功耗配置**: 可配置延迟进入睡眠时间

## 📋 关键AT命令速查

### 基础命令
```
AT              # 测试通信
ATE0            # 关闭回显
AT+IPR=115200   # 设置波特率
AT+CFUN=1       # 全功能模式
AT+CPIN?        # 查询SIM卡状态
```

### 网络注册
```
AT+CEREG?                          # 查询网络注册状态
AT+CEREG=2                         # 启用网络注册URC
AT+CGDCONT=<cid>,"IP","<apn>"      # 配置PDP上下文
AT+MIPCALL=1,<cid>                 # 激活PDP并拨号
AT+MIPCALL?                        # 查询拨号状态
AT+CSQ                             # 查询信号质量
```

### Socket操作（重要）
```
# TCP客户端
AT+MIPOPEN=<id>,"TCP","<host>",<port>
AT+MIPSEND=<id>,<length>
> [发送数据]
AT+MIPRD=<id>[,<length>]
AT+MIPCLOSE=<id>

# UDP
AT+MIPOPEN=<id>,"UDP",,,<local_port>
AT+MIPSEND=<id>,<length>,"<remote_ip>",<remote_port>
> [发送数据]
AT+MIPRD=<id>[,<length>]

# TCP服务端
AT+MIPOPEN=<id>,"TCP",<local_port>

# DNS
AT+MIPDNS="<domain>"

# Socket配置
AT+MIPCFG=<id>,"tcp_timeout",<sec>
AT+MIPCFG=<id>,"tcp_alive",<keepalive>
AT+MIPCFG=<id>,"recv_mode",<mode>
```

### 重要URC（非请求结果码）
```
+MATREADY                                      # 模组就绪
+CEREG: <stat>[,<lac>,<ci>[,<AcT>]]           # 网络状态变化
+MIPCALL: <cid>,<status>[,<ip>[,<ip6>]]       # PDP状态变化
+MIPURC: "recv",<id>,<length>                  # 数据到达
+MIPURC: "close",<id>[,<err>]                  # 连接关闭
+MLPMENTER: <mode>                             # 进入睡眠
+MLPMEXIT: <mode>                              # 退出睡眠
```

### 电源管理
```
AT+MLPMCFG="sleepmode",2,0     # 启用深睡眠（2=深睡眠）
AT+MLPMCFG="delaysleep",<sec>  # 延迟进入睡眠（秒）
AT+MLPMCFG="sleepind",2        # 启用睡眠URC上报
```

## 🔧 驱动开发关键点（修正版）

### 0. 核心理念 ⭐⭐⭐
**Socket Offload = 简单的API到AT命令映射**
```
应用调用 socket() → 分配modem_socket结构
应用调用 connect() → 发送 AT+MIPOPEN
应用调用 send()    → 发送 AT+MIPSEND + 数据
应用调用 recv()    → 发送 AT+MIPRD
应用调用 close()   → 发送 AT+MIPCLOSE

就这么简单！不需要处理：
❌ TCP握手、挥手
❌ TCP分片、重组
❌ TCP重传、确认
❌ 流量控制
❌ 拥塞控制
→ 这些都在ML307C固件里！
```

### 1. 初始化序列（直接复制SIM7080！）
```c
// 基本流程（参考 sim7080/sim7080.c）
1. GPIO配置（power/reset/dtr）- 可选
2. UART初始化（115200bps）
3. 初始化modem框架
   - modem_iface_uart_init()
   - modem_cmd_handler_init()
4. 启动RX线程
5. 等待+MATREADY（约2-10秒）
6. 基础AT命令
   - ATE0 (关回显)（超简单！）
```c
// 参考 sim7080/sim7080_sock.c

创建: modem_socket_get() → 就一行！
连接: AT+MIPOPEN=<id>,"TCP","<host>",<port> → 等OK
发送: AT+MIPSEND=<id>,<len> → 等'>' → 发数据 → 等OK
接收: 收到+MIPURC:"recv"通知 → AT+MIPRD → 拷贝数据
关闭: AT+MIPCLOSE=<id> → modem_socket_put()

// 不需要：
❌ 处理TCP状态机
❌ 管理发送/接收窗口  
❌ 处理ACK/NACK
❌ 超时重传
→ 这些模组固件全搞定了！

### 2. Socket生（只需2个核心URC！）
```c
// 关键URC 1: 数据到达
+MIPURC: "recv",<id>,<length>
→ modem_socket_packet_size_update()  // 更新可读大小
→ modem_socket_data_ready()          // 唤醒recv()

// 关键URC 2: 连接关闭
+MIPURC: "close",<id>[,<error>]
→ sock->is_connected = false
→ modem_socket_data_ready()  // 唤醒等待的recv

// 其他URC（可选，后期添加）：
+MIPCALL: ...    // PDP状态
+CEREG: ...      // 网络状态
+MLPMENTER/EXIT  // 睡眠状态

// 代码直接复制SIM7080的URC处理！
```> 等待'>' -（简化版）

### 你只需要写的部分
```
┌─────────────────────────────────────┐
│ ml307c.c (约500行)                  │
│ • 初始化 (复制SIM7080)              │
│ • URC注册 (复制SIM7080)             │
│ • 网络激活 (复制SIM7080)            │
└─────────────────────────────────────┘
┌─────────────────────────────────────┐
│ ml307c_socket.c (约400行)           │
│ • socket/connect/send/              │
│   recv/close 函数                   │
│ • AT命令收发 (复制SIM7080)          │
└─────────────────────────────────────┘
```

### Zephyr提供的（直接用，不用写）
```
┌─────────────────────────────────────┐
│ modem_socket.c - Socket池管理       │
│ modem_cmd_handler.c - AT命令框架   │
│ modem_iface_uart.c - UART收发      │
└─────────────────────────────────────┘
```

### ML307C固件做的（不用管）
```
┌─────────────────────────────────────┐
│ TCP/IP协议栈                        │
│ • 三次握手/四次挥手                 │
│ • 分片/重组/重传                    │
│ • 流控/拥塞控制                     │
│ • DNS解析                           │
└───────────（简化版）

### 步骤1: 复制SIM7080作为起点（5分钟）
```bash
cd zephyr/drivers/modem
cp -r simcom/sim7080 ml307c
cd ml307c

# 批量替换
# SIM7080 → ML307C
# sim7080 → ml307c
# SIMCOM → CHINAMOBILE
# +CA → +MIP
```

### 步骤2: 修改初始化（1小时）
```c
// ml307c.c - 只需改几个地方
// 1. 模组特定的GPIO配置（如果有）
// 2. 上电时序（如需要）
// 3. AT命令字符串（+CA → +MIP）
// 4. 其他都一样！
```

### 步骤3: 修改Socket命令（2小时）
```c
// ml307c_socket.c - 只改AT命令字符串
"AT+CAOPEN"  → "AT+MIPOPEN"
"AT+CASEND"  → "AT+MIPSEND"
"AT+CARECV"  → "AT+MIPRD"
"AT+CACLOSE" → "AT+MIPCLOSE"
"+CAOPEN:"   → "+MIPOPEN:" (响应匹配)
```

### 步骤4: 修改URC匹配（30分钟）
```c
// ml307c.c - 修改URC字符串
"+CAOPEN:"  → "+MIPOPEN:"
"+CAURC:"   → "+MIPURC:"
// URC处理逻辑基本相同！
```

### 步骤5: 测试验证（变量）
```bash
# 配置项目
cd your_project
west build -b your_board samples/net/sockets/echo_client -- \
  -DCONFIG_MODEM_ML307C=y
（重要！）

### 新手最容易犯的错误
1. ❌ **想从零写** → ✅ 直接复制SIM7080！
2. ❌ **过度设计** → ✅ 先让最简单的TCP ping通
3. ❌ **纠结细节** → ✅ 模组固件搞定TCP/IP
4. ❌ **不看URC** → ✅ URC是数据到达的唯一通知！
5. ❌ **并发发AT命令** → ✅ 必须串行！

### 硬件注意事项
- DTR引脚默认拉高（1.8V），拉低睡眠
- 上电：power引脚高电平2秒
- 复位：reset引脚低电平200ms
- 波特率：115200（自适应模式需先发AT）

### 软件注意事项
- ✅ 等+MATREADY后至少延迟2秒再发AT+CFUN
- ✅ CID8是IMS专用，不能用于数据
- ✅ AT命令必须串行（modem_cmd_handler自动处理）
- ✅ URC及时处理，否则缓冲区溢出
- ✅ 接收数据注意解析格式列
    // 4. AT命令初始化
    // 5. 网络连接
}
```

### 步骤4: 实现Socket操作
```c
// ml307（FAQ）
1. **无响应**: 波特率、UART配置、上电时序
2. **无法驻网**: SIM卡、信号、APN配置
3. **连接失败**: PDP未激活、网络问题
4. **数据丢失**: 
   - ❌ 不是TCP分片问题！
   - ✅ 检查URC处理
   - ✅ 检查接收缓冲区
5. **功耗高**: 睡眠配置、DTR控制
### 必看资料
- **SIM7080驱动源码** ⭐⭐⭐⭐⭐: `zephyr/drivers/modem/simcom/sim7080/`
- **Zephyr文档**: https://docs.zephyrproject.org/latest/
- **Socket Offload API**: `include/zephyr/net/socket_offload.h`
- **Modem框架**: `drivers/modem/README.rst`

### 选看资料
- BG9X驱动: `drivers/modem/quectel-bg9x.c`
- Sara-R4驱动: `drivers/modem/ublox-sara-r4.c`

## 📝 附录：代码模板（从SIM7080稍作修改）

### Socket创建（一行代码！）
```c
static int ml307c_socket(int family, int type, int proto)
{
    return modem_socket_get(&mdata.socket_config, family, type, proto);
}
```

### TCP连接（简单映射）
```c
static int ml307c_connect(void *obj, const struct sockaddr *addr, 
                          socklen_t addrlen)
{
    struct modem_socket *sock = obj;
    char cmd[128], ip_str[INET6_ADDRSTRLEN];
    uint16_t port;
    
    modem_context_sprint_ip_addr(addr, ip_str, sizeof(ip_str));
    port = ntohs(net_sin(addr)->sin_port);
    
    snprintf(cmd, sizeof(cmd), "AT+MIPOPEN=%d,\"TCP\",\"%s\",%d",
             sock->id, ip_str, port);
    
    int ret = modem_cmd_send(&mctx.iface, &mctx.cmd_handler,
                             NULL, 0, cmd, &mdata.sem, K_SECONDS(30));
    if (ret == 0) sock->is_connected = true;
    return ret;
}
```

### URC注册（核心2个）
```c
// 数据到达URC
MODEM_CMD_DEFINE(on_recv_urc)
{
    int id = atoi(argv[0]), len = atoi(argv[1]);
    struct modem_socket *sock = modem_socket_from_id(&mdata.socket_config, id);
    if (sock) {
        modem_socket_packet_size_update(&mdata.socket_config, sock, len);
        modem_socket_data_ready(&mdata.socket_config, sock);
    }
    return 0;
}

static struct modem_cmd unsol[] = {
    MODEM_CMD("+MIPURC: \"recv\",", on_recv_urc, 2U, ","),
    // ... 其他URC
};
```

### Socket数据ready信号（框架提供）
```c
/* URC中调用 */
modem_socket_data_ready(&mdata.socket_config, sock);

/* recv中等待 */
modem_socket_wait_data(&mdata.socket_config, sock);
```

---

**记住核心理念**：
1. ✅ 70%代码直接复制SIM7080
2. ✅ Socket Offload = AT命令映射
3. ✅ TCP/IP协议栈在模组固件里
4. ✅ 不要过度设计，先跑起来！ 软件注意事项
- 等待`+MATREADY`后至少延迟2秒再执行`AT+CFUN`
- CID8为IMS专用，不要用于数据连接
- 断开连接后需要等待模组完全关闭（约1秒）
- AT命令需串行执行，不能并发
- 接收数据时注意处理分片

### 常见问题
1. **模组无响应**: 检查波特率、UART配置、上电时序
2. **无法驻网**: 检查SIM卡、信号、APN配置
3. **Socket连接失败**: 确认PDP已激活、网络正常
4. **数据丢失**: 检查接收缓冲区大小、URC处理
5. **功耗过高**: 检查睡眠配置、DTR控制

## 📖 相关链接

- **Zephyr文档**: https://docs.zephyrproject.org/latest/
- **Socket Offload API**: `include/zephyr/net/socket_offload.h`
- **Modem驱动**: `drivers/modem/README.rst`
- **参考驱动**: `drivers/modem/quectel-bg9x.c`

## 📝 附录：常用代码片段

### AT命令发送
```c
ret = modem_cmd_send(&mctx.iface, &mctx.cmd_handler,
                     NULL, 0, "AT+MIPCALL?", NULL,
                     K_SECONDS(10));
```

### URC注册
```c
MODEM_CMD_DEFINE(on_cmd_recv_urc)
{
    // 处理 +MIPURC: "recv",<id>,<length>
}

static struct modem_cmd response_cmds[] = {
    MODEM_CMD("+MIPURC: \"recv\",", on_cmd_recv_urc, 2U, ","),
};
```

### Socket数据ready信号
```c
/* 在URC处理中 */
modem_socket_data_ready(&mdata.socket_config, sock);

/* 在recv中等待 */
modem_socket_wait_data(&mdata.socket_config, sock);
```

---

**文档版本**: 1.0  
**最后更新**: 2026-02-07
