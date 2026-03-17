4
MQTT用户手册 7
1
2
1
_
O
M
e
n
版本：V6.8O.5
发布日期：2025/3/26

中移物联网有限公司
服务与支持
如果您有任何关于模组产品及产品手册的评论、疑问、想法，或者任何无法从本手册中找到答案的疑问，
请通过以下方式联系我们。
OneMO官网： onemo10086.com
邮箱： SmartModule@cmiot.chinamobile.com
客户服务热线：400-110-0866
4
7
1
2
1
_
O
M
e
n
O

中移物联网有限公司
文档声明
注意
本手册描述的产品及其附件特性和功能，取决于当地网络设计或网络性能，同时也取决于用户预先安装的
各种软件。由于当地网络运营商、ISP，或当地网络设置等原因，可能也会造成本手册中描述的全部或部分产品
及其附件特性和功能未包含在您的购买或使用范围之内。
责任限制
除非合同另有约定，中移物联网有限公司对本文档内容不做任何明示或暗示的声明或保证，并且不对特定
目的适销性及适用性或者任何间接的、特殊的或连带的损失承担任何责任。
在适用法律允许的范围内，在任何情况下，中移物联网有限公司均不对用户因使用本手册内容和本手册中
描述的产品而引起的任何特殊的、间接的、附带的或后果性的损坏、利润损失、数据丢失、声誉和预期的节省
而负责。
因使用本手册中所述的产品而引起的中移物联网有限公司对用户的最大赔偿（除在涉及⼈身伤害的情况中根
据适用法律规定的损害赔偿外），不应超过用户为购买此产品而支付的金额。
由于产品版本升级或其他原因，本文档内容会不定期进行更新。除非另有约定，本文档仅作为使用指导，
本文档中的所有陈述、信息和建议不构成任何明示或暗示的担保。公司保留随时修改本手册中任何信息的权
4
利，无需进行提前通知且不承担任何责任。 7
1
2
商标声明
1
_
为中国移动注册商标。O
M
本手册和本手册描述的产品中出现的其他商标、产品名称、服务名称和公司名称，均为其各自所有者的财
e
产。
n
O
进出口法规
出口、转口或进口本手册中描述的产品（包括但不限于产品软件和技术数据），用户应遵守相关进出口法
律和法规。
隐私保护
关于我们如何保护用户的个人信息等隐私情况，请查看相关隐私政策。
操作系统更新声明
操作系统仅支持官方升级；如用户自己刷非官方系统，导致安全风险和损失由用户负责。

中移物联网有限公司
固件包完整性风险声明
固件仅支持官方升级；如用户自己刷非官方固件，导致安全风险和损失由用户负责。
版权所有©中移物联网有限公司。保留一切权利。
本手册中描述的产品，可能包含中移物联网有限公司及其存在的许可人享有版权的软件，除非获得相关权
利人的许可，否则，非经本公司书面同意，任何单位和个人不得擅自摘抄、复制本手册内容的部分或全部，并
以任何形式传播。
4
7
1
2
1
_
O
M
e
n
O

中移物联网有限公司
关于文档
修订记录
版本 描述
V1.0.0 初版。
V2.0.0 新增ML302S、ML307S相关内容。
V3.0.0 新增ML307A相关内容。
V4.0.0 新增ML302A相关内容。
V5.0.0 新增ML305U相关内容。
V6.0.0 新增MN318相关内容。
新增MN319相关内容；
V6.1.0
新增ML305A相关内容。
新增MN328相关内容;
V6.2.0
更新手册适用范围，新增ML307A-DL型号信息。
V6.3.0 更新手册适用范围，新增ML305A-DL型号信息。
4
新增ML307R相关内容； 7
1
新增MN316A相关内容；
2
移除MN328相关内容； 1
V6.4.0
_
更新“AT+MQTTCFG 配置连接参数”测试命令的响应，<cid>和<ssl_id>参
O
数描述；
M
更新“MQTT URC信息上报”，新增备注。
e
V6.5.0 新增ML305M相关内容。
n
O
V6.6.0 新增MN326相关内容。
更新手册适用范围，新增ML305M-DALM型号信息；
V6.7.0
新增ML307G相关内容。
新增ML307M相关内容；
新增ML307R-BL/ML307R-MC/ML307R-ML相关内容；
V6.8.0 更新“AT+MQTTPUB 发布消息”设置命令的成功响应；
更新“AT+MQTTPUB
发布消息”，新增“发布QoS=0消息（命令中不带数据输入）”示例。
V6.8.1 更新手册适用范围，新增ML307G-DC型号信息。
V6.8.2 更新手册适用范围，新增ML307M-DA型号信息。
V6.8.3 新增MR880A相关内容。

中移物联网有限公司
版本 描述
新增ML307H-DU/ML307H-GU相关内容；
V6.8.4
新增ML307C-DL-CN相关内容。
新增ML307N-DC/ML307N-DL相关内容；
新增ML307M-DH型号信息；
V6.8.5
新增ML307X-DB/ML307X-DC相关内容；
新增MN326A-DG相关内容。
4
7
1
2
1
_
O
M
e
n
O

中移物联网有限公司
目录
服务与支持.............................................................................................................................................................................................................................ii
文档声明................................................................................................................................................................................................................................iii
关于文档.................................................................................................................................................................................................................................v
1. 引言.....................................................................................................................................................................................................................................8
1.1. 适用型号...............................................................................................................................................................................................................8
2. AT命令概述....................................................................................................................................................................................................................10
2.1. AT命令语法.......................................................................................................................................................................................................10
2.2. AT命令响应.......................................................................................................................................................................................................12
3. MQTT协议AT命令.......................................................................................................................................................................................................13
3.1. AT+MQTTCFG 配置连接参数....................................................................................................................................................................13
3.2. AT+MQTTCONN 连接..................................................................................................................................................................................21
3.3. AT+MQTTSUB 订阅主题..............................................................................................................................................................................23
3.4. AT+MQTTUNSUB 取消订阅.......................................................................................................................................................................25
3.5. AT+MQTTPUB 发布消息..............................................................................................................................................................................26
3.6. AT+MQTTREAD 读取消息...........................................................................................................................................................................28
3.7. AT+MQTTSTATE 查询状态.........................................................................................................................................................................29
3.8. AT+MQTTDISC 主动断开连接...................................................................................................................................................................30
3.9. MQTT URC信息上报.....................................................................................................................................................................................31
4. MQTT使用示例............................................................................................................................................................................................................34
4
4.1. MQTT示例.........................................................................................................................7................................................................................34
1
4.1.1. 非加密接入...........................................................................................................................................................................................34
2
4.1.2. 缓存模式................................................................................................................................................................................................35
1
4.2. MQTTS示例.............................................................................._........................................................................................................................36
4.2.1. 加密接入......................................................O..........................................................................................................................................36
5. 错误码.................................................................M.............................................................................................................................................................37
e
n
O

中移物联网有限公司
1. 引言
本文档详细介绍了中移物联网基于MQTT通信协议定义的标准AT命令及其操作流程，适用于内部集成了
MQTT协议的模组产品。
文档中如有未尽细节，请咨询中移物联网技术支持。
1.1. 适用型号
Table 1. 适用模组
模组系列 模组子型号
MN316 MN316-DBRS/MN316-DLVS
MN316-S MN316-S-DLVS
MN316A MN316A-D/MN316A-DC
MN326A MN326A-DG
MN318 MN318-BX/MN318-LC/MN318-LX
MN319 MN319-DL
4
MN326 MN326-X
7
1
ML302A ML302A-DCLM/ML302A-DSLM/ML302A-GCLM/ML302A-GSLM
2
1
ML305A ML305A-DC/ML305A-DS/ML305A-DL
_
O
ML307A-DCLN/ML307A-DSLN/ML307A-GCLN/ML307A-GSLN/ML30
ML307A
M
7A-DL
e
ML302S ML302S-DNLM
n
O
ML307S ML307S-DNLM
ML305U ML305U-DBLN
ML307X ML307X-DB/ML307X-DC
ML307R ML307R-DC/ML307R-DL/ML307R-BL/ML307R-MC/ML307R-ML
ML307G ML307G-DC/ML307G-DL
ML305M ML305M-DSLM/ML305M-DALM
MR880A MR880A/MR880A Mini-PCIe/MR880A M.2
ML307M ML307M-DL/ML307M-DA/ML307M-DH
ML307H ML307H-DU/ML307H-GU
8

中移物联网有限公司
Table 1. 适用模组 (continued)
模组系列 模组子型号
ML307C ML307C-DL-CN
ML307N ML307N-DC/ML307N-DL
4
7
1
2
1
_
O
M
e
n
O
9

中移物联网有限公司
2. AT命令概述
本章主要介绍AT命令定义及其语法格式。
AT命令是从TE（Terminal Equipment，终端设备）或DTE（Data Terminal Equipment，数据终端设备）
向TA（Terminal Adaptor，终端适配器）或DCE（Data Circuit Terminal Equipment，数据电路终端设备）发送
的特定格式的字符串。TE通过TA发送AT命令来控制MS（Mobile Station，移动台）的功能，与网络业务进行交
互。用户可以通过AT命令进行呼叫、短消息、电话本、数据业务、补充业务、传真等方面的控制。
2.1. AT命令语法
AT命令必须以“AT”或“at”开头，以回车符<CR>结尾；命令后面跟随结构
为“<CR><LF>response<CR><LF>”的响应。为便于阅读，文档中将省略<CR><LF>，仅展示响应内容。
中移物联网模组实现的AT命令集包含3GPP TS 27.005、3GPP TS 27.007、ITU-TV.25ter标准命令集和中
移物联网自定义的扩展命令集。
AT命令根据语法结构可归为基础语法、S参数语法和扩展语法3类。
基础语法
该类AT命令格式为“AT<x><n>”或“AT&<x><n>”；其中“<x>”是命4令，“<n>”是命令参数。
7
比如命令“ATE<n>”，该命令根据“<n>”值确定DCE是否需要1将接收到的字符反馈给DTE。“<n>”是
2
可选项，如果不带该值则使用缺省值。
1
_
S参数语法 O
M
该类AT命令格式为“ATS<n>=<m>”，其中“<n>”是要设置S寄存器索引，“<m>”是设置值。
e
n
扩展语法 O
该类AT命令有多种操作模式。
Table 2. AT命令及响应类型
类型 命令 响应描述
测试命令 AT+<CMD>=? 返回参数列表及参数值范围
读取命令 AT+<CMD>? 返回参数当前值
设置命令 AT+<CMD>=<p1>[,<p2[,<p3>[…]]] 设置参数值
执行命令 AT+<CMD> 执行具体操作
其中：
10

中移物联网有限公司
▪ <...>尖括号中是参数，实际输入时不包含尖括号；
▪ [...]方括号中的参数是可选参数。
4
7
1
2
1
_
O
M
e
n
O
11

中移物联网有限公司
2.2. AT命令响应
Table 3. AT命令响应类型
响应 释义描述
ERROR AT命令格式错误或其他错误
+CME ERROR: <err>或者+CMS ERROR: <err> 或者 启用了扩展错误报告（+CMEE），其中<err>表示错
+CIS ERROR: <err> 误码或详细错误信息
OK AT命令执行成功
Note:
AT命令响应结果中，冒号“:”后均存在空格，用以分隔响应头与参数列表。
手册描述中错误响应用+ CME ERROR: <err>或者+CMS ERROR: <err>或者+CIS ERROR: <err>表
示，实际返回情况参考AT+CMEE命令。
4
7
1
2
1
_
O
M
e
n
O
12

中移物联网有限公司
3. MQTT协议AT命令
本章详细描述了MQTT协议相关的AT命令和命令格式。
3.1. AT+MQTTCFG 配置连接参数
该命令用于配置或查询MQTT参数，使用时应注意参数范围。模组掉电后配置参数不保存。
AT+MQTTCFG
语法 响应
成功
+MQTTCFG: "version",(list of supported <connect_id>s),(list of supported <version>s)
[+MQTTCFG: "cid",(list of supported <connect_id>s)[,(list of supported <cid>s)]]
[+MQTTCFG: "ssl",(list of supported <connect_id>s),(list of supported <ssl_enable>s),(list of
supported <ssl_id>s)]
+MQTTCFG: "keepalive",(list of supported <connect_id>s),(list of supported
<keepalive_time>s)
+MQTTCFG: "clean",(list of supported <connect_id>s),(list of supported <clean_session>s)
+MQTTCFG: "retrans",(list of supported <connect_id>s),(list of supported
<retrans_interval>s),(list of supported <retry_times>s)
+MQTTCFG: "willoption",(list of supported <connect_id>s),(list of supported
<will_flag>s),(list of supported <will_qos>s),(list of s4upported <will_retain>s)
测试命令
7
+MQTTCFG: "willpayload",(list of supported <connect_id>s),,
1
AT+MQTTCFG=? +MQTTCFG: "pingreq",(list of supported <connect_id>s),(list of supported
2
<ping_interval>s)
1
+MQTTCFG: "pingresp",(list of supported <connect_id>s),(list of supported <pingack>s)
_
+MQTTCFG: "encoding",(list of supported <connect_id>s),(list of supported
O
<input_format>s),(list of supported <output_format>s)
+MMQTTCFG: "cached",(list of supported <connect_id>s),(list of supported
<cached_mode>s)
e
+MQTTCFG: "reconn",(list of supported <connect_id>s),(list of supported
n
<reconn_times>s),(list of supported <reconn_interval>s),(list of supported <mode>s)
O
OK
错误
+CME ERROR: <err>
成功
若省略可选参数，则查询MQTT协议版本：
设置命令（配置 MQTT
协议版本） +MQTTCFG: "version",<version>
OK
AT
若指定可选参数且MQTT连接未创建，配置当前使用的MQTT协议版本：
+MQTTCFG="version",<conn
ect_id>[,<version>]
OK
错误
13

中移物联网有限公司
AT+MQTTCFG
+CME ERROR: <err>
成功
若省略可选参数，则查询当前MQTT客户端使用的PDP：
设置命令（配置MQTT客户端待
+MQTTCFG: "cid"[,<cid>]
使用的PDP） OK
AT 若指定可选参数且MQTT连接未创建，配置MQTT客户端待使用的PDP：
+MQTTCFG="cid",<connect_i
OK
d>[,<cid>]
错误
+CME ERROR: <err>
成功
若省略可选参数，则查询当前MQTT SSL模式以及SSL上下文索引
配置情况：
设置命令（配置MQTT
+MQTTCFG: "ssl",<ssl_enable>[,<ssl_id>]
SSL模式和SSL上下文索引） OK
AT 若指定可选参数且MQTT连接未创建，配置MQTT
+MQTTCFG="ssl",<connect_i SSL模式和SSL上下文索引：
d>[,<ssl_enable>[,<ssl_id>]]
OK
4
错误
7
1
+CME ERROR: <err>
2
1
成功
_
若省略可选参O数，则查询当前保活时间：
设置命令（配置保活时间） +MMQTTCFG: "keepalive",<keepalive_time>
OK
AT e
n
+MQTTCFG="keepalive",<co 若指定可选参数且MQTT连接未创建，配置保活时间：
O
nnect_id>[,<keepalive_tim
OK
e>]
错误
+CME ERROR: <err>
成功
若省略可选参数，则查询当前会话类型：
设置命令（配置会话类型）
+MQTTCFG: "clean",<clean_session>
AT OK
+MQTTCFG="clean",<connec
若指定可选参数且MQTT连接未创建，配置会话类型：
t_id>[,<clean_session>]
OK
错误
14

中移物联网有限公司
AT+MQTTCFG
+CME ERROR: <err>
成功
若省略可选参数，则查询当前设置的重传间隔以及重传次数配置：
1
设置命令（配置重传参数 ）
+MQTTCFG: "retrans",<retrans_interval>,<retry_times>
OK
AT
+MQTTCFG="retrans",<conn 若指定可选参数且MQTT连接未创建，配置消息重传参数：
ect_id>[,<retrans_interval>[,
OK
<retry_times>]]
错误
+CME ERROR: <err>
成功
若省略可选参数，则查询当前Will配置信息：
设置命令（配置Will
flag、qos及retain信息） +MQTTCFG: "willoption",<will_flag>,<will_qos>,<will_retain>
OK
AT
若指定可选参数且MQTT连接未创建，配置Will信息：
+MQTTCFG="willoption",<co
nnect_id>[,<will_flag>[,<will_ OK
qos>[,<will_retain>]]]
错误
+CME ERROR: <err>
4
7
成功 1
若省略可选参数，则查询当前2Will Topic和Will Message信息，
1
设置命令（配置 Will <will_msg>输入格式解析受"encoding"配置影响：
_
topic和message信息） O
+MQTTCFG: "willpayload",<will_topic>,<will_msg>
OKM
AT
+MQTTCFG="willpayload",<c e若指定可选参数且MQTT连接未创建，配置Will信息：
n
onnect_id>[,<will_topic>,<wil
O OK
l_msg>]
错误
+CME ERROR: <err>
成功
若省略可选参数，则查询当前MQTT的心跳间隔：
设置命令（配置MQTT心跳请求
间隔） +MQTTCFG: "pingreq",<ping_interval>
OK
AT
若指定可选参数且MQTT连接未创建，配置MQTT心跳间隔：
+MQTTCFG="pingreq",<conn
ect_id>[,<ping_interval>]
OK
错误
1. 重传间隔会随着重传次数增加在前一次间隔基础上自动加倍，重传时模组自动设置<dup>重传标志，重传失败将上报"timeout"消息。
15

中移物联网有限公司
AT+MQTTCFG
+CME ERROR: <err>
成功
若省略可选参数，则查询当前MQTT的心跳回显配置：
设置命令（配置MQTT心跳请求
+MQTTCFG: "pingresp",<pingack>
回显） OK
AT 若指定可选参数，配置MQTT心跳回显：
+MQTTCFG="pingresp",<con
OK
nect_id>[,<pingack>]
错误
+CME ERROR: <err>
成功
若省略可选参数，则查询当前编码配置：
设置命令（配置MQTT消息的输
入编码以及输出打印编码） +MQTTCFG: "encoding",<input_format>,<output_format>
OK
AT
若指定可选参数，配置MQTT消息的收发格式：
+MQTTCFG="encoding",<co
nnect_id>[,<input_format>[,< OK
output_format>]]
错误
+CME ERROR: <err>
4
7
成功 1
若省略可选参数，则查询当前2缓存配置：
1
设置命令（配置MQTT消息缓存
+MQTTCFG: "cached",_<cached_mode>
输出模式） OK O
M
AT 若指定可选参数，配置 MQTT消息的缓存模式：
+MQTTCFG="cached",<conn e
OK
n
ect_id>[,<cached_mode>]
O
错误
+CME ERROR: <err>
成功
若省略可选参数，则查询当前重连配置：
设置命令（配置MQTT重连次
数） +MQTTCFG: "reconn",<reconn_times>,<reconn_interval>,<mode>
OK
AT
若指定可选参数且 MQTT 连接未创建，配置MQTT重连参数：
+MQTTCFG="reconn",<conn
ect_id>[,<recon_times>[,<rec OK
onn_interval>[,<mode>]]]
错误
+CME ERROR: <err>
16

中移物联网有限公司
AT+MQTTCFG
成功
+MQTTCFG: "version",<version>
+MQTTCFG: "cid",<cid>
[+MQTTCFG: "ssl",<ssl_enable>[,<ssl_id>]]
+MQTTCFG: "keepalive",<keepalive_time>
+MQTTCFG: "clean",<clean_session>
设置命令（查询MQTT配置信
+MQTTCFG: "retrans",<retrans_interval>,<retry_times>
息）
+MQTTCFG: "willoption",<will_flag>,<will_qos>,<will_retain>
+MQTTCFG: "willpayload",[<will_topic>,<will_msg>]
AT
+MQTTCFG: "pingreq",<ping_interval>
+MQTTCFG="query",<conne +MQTTCFG: "pingresp",<pingack>
ct_id> +MQTTCFG: "encoding",<input_format>,<output_format>
+MQTTCFG: "cached",<cached_mode>
+MQTTCFG: "reconn",<reconn_times>,<reconn_interval>,<mode>
OK
错误
+CME ERROR: <err>
参数描述
2
<connect_id>整型，MQTT客户端标识ID。范围：0~5。
3
<version>整型，MQTT 协议版本。默认值4。
3
4
MQTT协议v3.1 7
1
4
2
MQTT协议v3.1.1 1
_
<cid>整型，MQTT 客户端待使用 PDP，范围与O平台相关，默认不指定。 4
M 5
<ssl_enable>整型，配置 MQTT SSL 模式。默认值0。
e
0
n
使用普通TCP连O接
1
使用SSL TCP连接
6
<ssl_id>整型，SSL 上下文索引，范围可参见文档《SSL用户手册》。
2. MN316A/MN326A/MN319范围：0~2。
3. 当前仅支持<version>=4（MQTT 协议v3.1.1）。
4. MN316/MN316A/MN326A/MN318/MN319/MN326/MR880A不支持该参数配置。
ML302A/ML305A/ML307A/ML302S/ML307S/ML307R/ML307C范围：1~15，默认值1。
ML307G/ML307H范围：1~5，默认值1。
ML305U范围：1~7，默认值1。
ML305M/ML307M/ML307N范围：1~8，默认不指定，指定时需保证支持cid已激活。
ML307X范围：1~15，默认不指定，指定时需保证支持cid已激活。
5. MN316A/MN326A/MN318/MN319/ML307H-GU不支持该参数配置。
6. MN316A/MN326A/MN318/MN319/ML307H-GU不支持该参数配置。
17

中移物联网有限公司
AT+MQTTCFG
<keepalive_time>整型，保活时间。范围：0或60~65535；默认值120；单位：s。该参数定义从客户端接收消
息的最大间隔时间，在 1.5 倍的设置时间内，若服务器未从客户端收到消息，则默认客户端发送了
DISCONNECT消息，因此服务器会断开客户端连接。
0
标识不断开连接
≥60
保活时间
<clean_session>整型，配置会话类型。默认值0。
0
服务端必须根据当前的会话状态恢复与客户端的通信
1
服务端必须清除之前的会话启动一个新的会话与客户端通信
<retrans_interval>整型，数据包重传初始间隔时间。范围：20~60；单位：s。默认值20。每次传输超时的总
<retry_times>+1
时间为<retrans_interval>*（2 -1）。
<retry_times>整型，数据包传输超时后重发次数。范围：0~3，默认值0，重传时模组自动填充<dup>重传标
志。
<will_flag>整型，配置 Will Flag。默认值0。
4
0
7
连接时无需携带Will信息 1
2
1 1
_
连接时需要携带Will信息
O
<will_qos>整型，发送Will消息时的 Q
M
oS 级别。默认值0。
0 e
n
最多发送一次
O
1
最少发送一次
2
只发送一次
<will_retain>整型，标记服务端在发布Will消息后是否需要保留。默认值0。
0
发布Will消息后，服务端不保留该消息
1
发布Will消息后，服务端保留该消息
ML302A/ML305A/ML307A/ML302S/ML307S/ML305U/ML307R/ML307C/ML305M/ML307G/MR880A/ML307H-DU/ML307M/ML30
7N/ML307X：范围：0~5。
18

中移物联网有限公司
AT+MQTTCFG
<will_topic>字符串，需要发布的Will Topic 主题。长度范围：0~256；单位：字节。
<will_msg>字符串，需要发布的Will Message消息。长度范围：0~256；单位：字节。
<ping_interval>整型，心跳间隔时间。范围：60~86400；单位：s。默认值120。
<pingack>整型，是否显示心跳结果。默认值0。
0
不显示
1
显示
<input_format>整型，MQTT消息发布格式，设置完成后立即生效。默认值0。对MQTTPUB的<message>参数
7
和MQTTCFG的<will_msg>参数生效。
0
ASCII码字符串
1
十六进制字符串
2
转义字符串
<output_format>整型，MQTT 消息接收格式，设置完成后立即生效。默认值0。
4
7
0
1
显示原始字符串 2
1
1
_
显示十六进制字符串 O
M
<cached_mode>整型，接收MQTT消息缓存模式，设置完成后立即生效。默认值0。缓存模式下，模组接收到
8
服务器下行Publish消息将上报"puebnmi" URC消息，之后应使用+MQTTREAD命令读取接收到的消息。
n
0 O
从服务器接收的MQTT消息以URC的形式上报消息内容
1
从服务器接收的MQTT消息缓存到本地（缓存模式）
<reconn_times>整型，MQTT重连次数。范围：0~3；单位：次。默认值3。
<reconn_interval>整型，重连间隔。范围：20~60；单位：s。默认值20。
<mode>整型，重连策略。默认值0。
0
以固定间隔重连
1
7. MR880A不支持2。
8. MN316/MN316A/MN326A/MN318/MN319/MN326不支持该参数配置。
19

中移物联网有限公司
AT+MQTTCFG
以n倍重连间隔重连。例如<reconn_interval>设置为20s，<reconn_times>设置为3次，则3次重连间隔为
20-40-60s。
示例
AT+MQTTCFG="pingresp",1,1
OK
AT+MQTTCFG="encoding",0
+MQTTCFG: "encoding",0,0
OK
4
7
1
2
1
_
O
M
e
n
O
20

中移物联网有限公司
3.2. AT+MQTTCONN 连接
该命令用于连接至MQTT或MQTTS服务器。
AT+MQTTCONN
语法 响应
成功
测试命令
+MQTTCONN: <connect_id>,<host>,<port>,<clientID>,<user>,<passwd>
AT+MQTTCONN=?
OK
成功
设置命令
OK
AT
+MQTTURC: "conn",<connect_id>,<conn_state>
+MQTTCONN=<connect_id>,
<host>[,<port>[,<clientID>,< 错误
user>,<passwd>]]
+CME ERROR: <err>
命令描述
9
执行命令后，将会创建MQTT的连接，并以URC方式上报连接结果。
参数描述
10
<connect_id>整型，MQTT客户端标识符。范围：0~5。
4
<host>字符串，MQTT服务器IP地址或域名，最大长度128。
7
1
<port>整型，MQTT服务器端口，范围：0~65535，省略时为默认值1883。
2
1
<clientID>字符串，终端ID，最大长度128。
_
O
<user>字符串，登录用户名，最大长度128。
M
<passwd>字符串，登录密码或鉴权信息，最大长度256。
e
<conn_state>整型，MQTT客n户端状态。参考URC信息上报。
O
0
连接成功
1
正在重连
2
客户端断开
3
服务器拒绝
4
服务器断开
9. MN319建立MQTT连接时，建议使用指令AT+MLPMCFG锁定睡眠，否则进入深睡眠后连接将断开。
10. MN316A/MN326A/MN319范围：0~2。
21

中移物联网有限公司
AT+MQTTCONN
5
ping超时
6
网络异常
255
未知错误
示例
AT
+MQTTCONN=0,"mqtt.heclouds.com",6002,"716335458","425089","SnMHaAOMCbQD90eADEGpMBPu8p
I="
OK
+MQTTURC: "conn",0,0
Important: 同一个<connect_id>重复连接将返回错误。
4
7
1
2
1
_
O
M
e
n
O
22

中移物联网有限公司
3.3. AT+MQTTSUB 订阅主题
该命令支持用于订阅主题。
AT+MQTTSUB
语法 响应
成功
+MQTTSUB: <connect_id>,<mid>
OK
设置命令
订阅成功，将收到URC：
AT
+MQTTURC: "suback",<connect_id>,<mid>,<code>[,<code1>,...]
+MQTTSUB=<connect_id>,<t
opic>,<qos>[,<topic1>,<qos 若超时，将收到URC：
1>..]
+MQTTURC: "timeout",<connect_id>,<mid>
错误
+CME ERROR: <err>
成功
[+MQTTSUB: <topic>,<code>[,<topic1>,<code1>..]]
设置命令
OK
AT+MQTTSUB=<connect_id>
错误 4
7
+CME ERROR: <err> 1
2
命令描述 1
_
11
该命令用于订阅主题。一次订阅多个主题时，OURC信息将会按照订阅顺序依次返回对应的QoS值。
M
参数描述
e 12
<connect_id>整型，MQTT客户端标识符。范围：0~5。
n
O13
<topic>字符串，订阅的主题。
<qos> 整型，消息交付质量等级。
0
最多发送一次
1
最少发送一次
2
只发送一次
11. ML307G同时订阅多个主题时，成功后分开响应，URC信息分开上报。
12. MN316A/MN326A/MN319范围：0~2。
13. ML302A/ML305A/ML307A/ML302S/ML307S/ML307R/ML307C/ML305M/ML307G/ML307H/MR880A/ML307X/ML307M/ML3
07N长度最大为256。
23

中移物联网有限公司
AT+MQTTSUB
<mid>整型，数据包标识。范围：0~65535。
<code>整型， 对应订阅时设置的QoS，订阅失败时值为128。
示例
AT+MQTTSUB=0,"world",1,"hello",2
+MQTTSUB: 0,35270
OK
+MQTTURC: "suback",0,35270,1,2
AT+MQTTSUB=0
+MQTTSUB: "world",1,"hello",2
OK
Note:
该命令支持同时订阅单个或多个主题，最多支持同时添加3个主题订阅；
MN319模组AT指令参数的最大长度为1560字节；
MN316A/MN326A建议订阅的主题长度不超过3000字节。
4
7
1
2
1
_
O
M
e
n
O
24

中移物联网有限公司
3.4. AT+MQTTUNSUB 取消订阅
该命令用于取消订阅。
AT+MQTTUNSUB
语法 响应
成功
+MQTTUNSUB: <connect_id>,<mid>
OK
设置命令 收到服务器取消订阅ACK后上报URC：
AT +MQTTURC: "unsuback",<connect_id>,<mid>
+MQTTUNSUB=<connect_id
若超时，会上报URC：
>,<topic>[,<topic1>…]
+MQTTURC: "timeout",<connect_id>,<mid>
错误
+CME ERROR: <err>
命令描述
14
取消订阅主题后，服务器将不会再下发对应主题的Publish消息。
参数描述
4
15
<connect_id>整型，MQTT客户端标识符。范围：0~5。 7
1
16
<topic>字符串，取消订阅的主题。 2
1
<mid>整型，数据包标识符。范围：0~65535。 _
O
示例
M
AT+MQTTUNSUB=0,"world" e
+MQTTUNSUB: 0,18795 n
OK O
+MQTTURC: "unsuback",0,18795
Note:
支持同时取消订阅3个主题；
MN319模组的AT指令参数的最大长度为1560字节。
14. ML307G同时取消订阅多个主题时，取消成功后分开响应，URC信息分开上报。
15. MN316A/MN326A/MN319范围：0~2。
16. ML302A/ML305A/ML307A/ML302S/ML307S/ML307R/ML307C/ML305M/ML307G/MR880A/ML307H/ML307M/ML307N长度最大
为256。
25

中移物联网有限公司
3.5. AT+MQTTPUB 发布消息
该命令用于向服务器发送指定Topic的消息，使用时根据消息性质选择QoS等级。
AT+MQTTPUB
语法 响应
成功
<msg_len>设置，<message>不设置，输入数据达到指定长度或达到超时时
间时发送数据：
>(输入数据)
+MQTTPUB: <connect_id>,<mid>,<length>
OK
异步信息上报：
设置命令
QoS=0，无消息上报。
AT QoS=1:
+MQTTPUB=<connect_id>,<t
+MQTTURC: "puback",<connect_id>,<mid>,<dup>
opic>,<qos>,<retain>,<dup>,
<msg_len>[,<message>] QoS=2:
+MQTTURC: "pubrec",<connect_id>,<mid>,<dup>
+MQTTURC: "pubcomp",<connect_id>,<mid>,<dup>
重传超时后会上报：
4
7
+MQTTURC: "timeout",<connect_id>,<mid>
1
错误 2
1
_
+CME ERROR: <err>
O
参数描述
M
17
<connect_id>整型，MQTT客户端e标识。范围：0~5。
n
18
<topic>字符串，发布O的主题。
<qos>整型，发布消息质量等级。范围：0~2。
<retain>整型，服务器是否存储该消息。
0
服务器不存储该消息
1
服务器储存该消息，并向新订阅者发送最新订阅消息。
<dup>整型， 重发标志，范围：0~1。数据发送失败后用户主动重发数据请置1，发送新消息一般设置为0。
17. MN316A/MN326A/MN319范围：0~2。
18. ML302A/ML305A/ML307A/ML302S/ML307S/ML307R/ML307C/ML305M/ML307G/ML307M/ML307N/ML307H长度最大为256。
26

中移物联网有限公司
AT+MQTTPUB
<msg_len>整型，待发布消息长度。当输入模式设置为Hex字符串时，消息长度为转换后的长度；设置为其他
19
模式即表示原始输入的数据长度；长度设置为0时，按照实际输入的数据发送。
20
<message>字符串，待发布消息，当此参数省略时进入数据模式。
<mid>整型，数据包标识。范围：0~65535。
<length>整型，已发送MQTT报文长度。
示例
发布QoS=0消息
AT+MQTTPUB=0,"world",0,0,0,4,"3242"
+MQTTPUB: 0,18797,13
OK
发布QoS=1消息
AT+MQTTPUB=0,"world",1,0,0,4,"3242"
+MQTTPUB: 0,18798,15
OK
+MQTTURC: "puback",0,18798,0
发布QoS=2消息
AT+MQTTPUB=0,"world",2,0,0,4,"3242"
+MQTTPUB: 0,18799,15
OK 4
7
+MQTTURC: "pubrec",0,18799,0
1
+MQTTURC: "pubcomp",0,18799,0
2
1
发布QoS=0消息（命令中不带数据输入）
_
O
AT+MQTTPUB=0,"world",0,0,0,70
>{"id":"speed1"\r,"tags":["mobile"],"unit":"m/s"M\r,"unit_symbol":"m/s"}
+MQTTPUB: 0,33244,89
OK e
n
O
Note: MN319模组的AT指令参数的最大长度为1560字节。
19. ML302A/ML305A/ML307A/ML302S/ML307S/ML307R/ML307C/ML305M/ML307G/ML307M/ML307N/ML307H命令中直接输入数
据和数据模式下的范围：0~1024；单位：字节。
MR880A命令中直接输入数据时的范围：0~2048；数据模式下的范围：0~8192；单位：字节。
ML307X命令中直接输入数据时的范围：0~2048；数据模式下的范围：0~4000；单位：字节。
20. MN316/MN316-S//MN316A/MN326A/MN318/MN319/MN326不支持进入数据模式。
ML307X在数据模式下，输入数据达到指定长度或超过10s时自动发送数据。
MR880A在数据模式下，输入数据达到指定长度或超过10s后自动发送数据，<msg_len>设置为0时，输入ctrl+z也将发送数据。数
据模式最大发送长度为8192，当配置的<msg_len>超过8192时按照8192截取计算。
27

中移物联网有限公司
3.6. AT+MQTTREAD 读取消息
该命令用于缓存模式下，读取缓存中的消息。
AT+MQTTREAD
语法 响应
成功
设置命令
+MQTTREAD: <connect_id>,<store_msgs>,<total_len>
OK
AT
+MQTTREAD=<connect_id> 错误
+CME ERROR: <err>
成功
设置命令
+MQTTREAD: <connect_id>,<mid>,<topic>,<payload_len>,<payload>
...
AT
OK
+MQTTREAD=<connect_id>,
错误
<count>
+CME ERROR: <err>
命令描述
NB-IoT系列模组缓存上限为4KB，4G/5G模组缓存上限为8KB。
4
7
参数描述
1
2
<connect_id>整型，MQTT客户端标识。范围：0~5。
1
_
<count>整型，读取消息条数。范围：1~当前缓存消息条数。
O
<store_msgs>整型， 表示缓存中有n条M数据。
0 e
n
无缓存消息
O
n>0
缓存区有n条数据
<total_len>整型，表示当前已缓存数据长度。
<mid>整型，数据包标识。范围：0~65535。
<topic>字符串， 接收到的主题名。
<payload_len>整型，本次读取到的payload长度。
<payload>字符串，本次读取到的payload内容。
Note: NB-IoT模组暂不支持该命令。
28

中移物联网有限公司
3.7. AT+MQTTSTATE 查询状态
该命令用于查询MQTT连接状态。
AT+MQTTSTATE
语法 响应
成功
查询指定<connect_id>的MQTT连接状态：
设置命令
+MQTTSTATE: <state>
AT OK
+MQTTSTATE=<connect_id>
错误
+CME ERROR: <err>
参数描述
21
<connect_id>整型，MQTT客户端标识。范围：0~5。
<state>整型，MQTT连接状态。
1
正在连接或重连
2
连接成功
4
3 7
1
连接断开
2
1
4~255
_
保留 O
示例 M
e
AT+MQTTSTATE=0
n
+MQTTSTATE: 2 O
OK
21. MN316A/MN326A/MN319范围：0~2。
29

中移物联网有限公司
3.8. AT+MQTTDISC 主动断开连接
该命令用于主动断开连接。
AT+MQTTDISC
语法 响应
成功
OK
连接断开后，返回：
执行命令
+MQTTURC: "conn",<connect_id>,2
AT
+MQTTDISC=<connect_id> 错误
连接未建立时使用该命令，返回：
+CME ERROR: <err>
命令描述
命令执行后将会强制断开MQTT连接并释放资源。
参数描述
22
<connect_id>整型，MQTT客户端标识。范围：0~5。
4
示例
7
1
AT+MQTTDISC=0
2
OK 1
+MQTTURC: "conn",0,2 _
O
M
e
n
O
22. MN316A/MN326A/MN319范围：0~2。
30

中移物联网有限公司
3.9. MQTT URC信息上报
MQTT URC信息上报集合。
+MQTTURC
分类 响应
连接状态URC +MQTTURC: "conn",<connect_id>,<conn_state>
接收提示URC +MQTTURC: "pubnmi",<connect_id>,<mid>,<data_len>
丢弃提示URC +MQTTURC: "drop",<connect_id>,<dropped_length>
接收消息URC +MQTTURC: "publish",<connect_id>,<mid>,<topic>,<total_len>,<payload_len>,<payload>
Ping结果URC +MQTTURC: "pingresp",<connect_id>,<ping_ret>
超时URC +MQTTURC: "timeout",<connect_id>,<mid>
订阅URC +MQTTURC: "suback",<connect_id>,<mid>,<code>[,<code1>,...]
取消订阅URC +MQTTURC: "unsuback",<connect_id>,<mid>
QoS=1时发送结果URC +MQTTURC: "puback",<connect_id>,<mid>,<dup>
4
QoS=2时消息到达URC +MQTTURC: "pubrec",<connect_id>,<mid>,<dup>
7
1
QoS=2时消息发送完成URC +MQTTURC: "pubcomp",<connec2t_id>,<mid>,<dup>
1
参数描述 _
O
命令标识（第一个参数）
M
"conn"
e
MQTT连接状态发生变化n事件上报。
O
"pubnmi"
新数据包上报（New Message Indication），提示从缓存区读取数据包。
"drop"
提示接收缓存区满，丢掉数据。
"publish"
接收到的MQTT Publish数据。
"pingresp"
配置心跳回显时，上报心跳包响应结果。
"timeout"
数据发送超时事件上报（仅上报订阅、取消订阅、发布最终超时结果，重传包超时不上报）。
"suback"
31

中移物联网有限公司
+MQTTURC
收到服务器订阅ACK信息上报。
"unsuback"
收到服务器取消订阅ACK信息上报。
"puback"
QoS1模式下的发布响应ACK信息上报。
"pubrec"
QoS2模式下的发布响应Receive上报。
"pubcomp"
QoS2模式下的发布响应Complete上报。
23
<connect_id>整型，MQTT客户端标识。范围：0~5。
<mid>整型，数据包标识。范围：0~65535。
<conn_state>整型，当前连接状态。
0
MQTT连接成功
1
正在重连
2
断开：用户主动断开
4
7
3
1
断开：拒绝连接（协议版本、标识符、用户名或密码错误） 2
1
4
_
断开：服务器断开 O
5 M
断开：Ping包超时断开（若服e务器未在1.5倍保活时间内接收到客户端的消息，则相当于客户端发送了
DISCONNECT消息，服n务器会断开与客户端的连接，此时会上报该URC。）
O
6
断开：网络异常断开
255
断开：未知错误
7~254
保留
<code>整型，服务器反馈码。多条消息订阅时，按照订阅顺序返回对应的结果。
0
订阅成功QoS=0
1
23. MN316A/MN326A/MN319范围：0~2。
32

中移物联网有限公司
+MQTTURC
订阅成功QoS=1
2
订阅成功QoS=2
128
订阅失败
<data_len>整型，接收数据长度。
<dropped_length>整型，丢弃的数据长度。
<topic>字符串，订阅的主题。
<total_len>整型，负载总长度。
<payload_len>整型，当前输出负载信息的长度。
<payload>字符串，当前输出的负载信息。
<ping_ret>整型，心跳上报结果。
0
心跳上报成功
1
心跳上报超时
<dup>整型，重发标志。 4
7
0 1
2
非重发数据
1
_
1
O
重发数据
M
e
Note: ：ML302A/ML305A/ML307A/ML307R/ML307C模组publish的URC中topic+msg总长度超过512字节
n
会进行分包。 O
33

中移物联网有限公司
4. MQTT使用示例
本章主要介绍MQTT命令在相关业务场景中的使用流程。
4.1. MQTT示例
本节主要介绍MQTT相关的操作流程，包含非加密接入、缓存模式等相关流程。
4.1.1. 非加密接入
该示例介绍了连接MQTT服务器，收发数据及断开连接的完整工作流程，仅供参考。
AT+MQTTCFG="pingresp",0,1
OK
AT
+MQTTCONN=0,"mqtt.heclouds.com",6002,"716335458","425089","SnMHaAOMCbQD90eADEGpMBPu8pI="
//连接至MQTT服务器
OK
+MQTTURC: "conn",0,0 //连接成功
AT+MQTTSUB=0,"world",1,"hello",2 //订阅主题
+MQTTSUB: 0,18807
OK
+MQTTURC: "suback",0,18807,1,2
4
+MQTTURC: "publish",0,2,"world",5,5,12345 //接收服务器下行消息 7
+MQTTURC: "publish",0,3,"hello",5,5,12345 1
AT+MQTTPUB=0,"world",2,0,0,4,"3242" //发布消息QoS=2 2
+MQTTPUB: 0,18808,15 1
_
+MQTTURC: "pubrec",0,18808,0
O
+MQTTURC: "pubcomp",0,18808,0
AT+MQTTPUB=0,"world",1,0,0,4,"324M2" //发布消息QoS=1
+MQTTPUB: 0,18809,15
OK e
n
+MQTTURC: "puback",0,18809,0
O
AT+MQTTPUB=0,"world",0,0,0,4,"3242" //发布消息QoS=0
+MQTTPUB: 0,18810,13
OK
AT+MQTTDISC=0 //断开连接
OK
+MQTTURC: "conn",0,2 //断开成功
34

中移物联网有限公司
4.1.2. 缓存模式
该示例介绍了缓存模式下的完整工作流程，仅供参考。
AT+MQTTCFG="cached",0,1 //配置为接收缓存模式
OK
AT+MQTTCONN=0,"120.27.12.119",1883,"716335459","425089","SnMHaAOMCbQD90eADEGpMBPu8pI="
//连接至MQTT服务器
OK
+MQTTURC: "conn",0,0 //连接成功
AT+MQTTSUB=0,"world",1,"hello",2 //订阅主题
+MQTTSUB: 0,1
OK
+MQTTURC: "suback",0,1,1,2
+MQTTUTC: "pubnmi",0,45,11 //接收服务器下行消息，缓存模式。
AT+MQTTREAD=0,1 //读取缓存数据
+MQTTREAD: 0,45,"hello",11,hello world
OK
AT+MQTTDISC=0 //断开连接
OK
+MQTTURC: "conn",0,2 //断开成功
Note: NB-IoT模组暂不支持MQTT缓存模式。
4
7
1
2
1
_
O
M
e
n
O
35

中移物联网有限公司
4.2. MQTTS示例
本节主要介绍MQTTS相关的操作流程。
4.2.1. 加密接入
该示例介绍了连接MQTTS服务器，收发数据及断开连接的完整工作流程，仅供参考。
AT+MQTTCFG="ssl",0,1
OK
AT+MQTTCONN=0,"120.27.12.119",8883,"716335459","425089","SnMHaAOMCbQD90eADEGpMBPu8pI="
//连接至MQTTS服务器
OK
+MQTTURC: "conn",0,0 //连接成功
AT+MQTTSUB=0,"world",1,"hello",2 //订阅主题
+MQTTSUB: 0,137
OK
+MQTTURC: "suback",0,137,1,2
+MQTTURC: "publish",0,2,"world",5,5,12345 //接收服务器下行消息
+MQTTURC: "publish",0,3,"hello",5,5,12345
AT+MQTTPUB=0,"world",2,0,0,4,"3242" //发布消息QoS=2
+MQTTPUB: 0,138,15
+MQTTURC: "pubrec",0,138,0
+MQTTURC: "pubcomp",0,138,0
AT+MQTTPUB=0,"world",1,0,0,4,"3242" //发布消息QoS=1
+MQTTPUB: 0,139,15
OK
4
+MQTTURC: "puback",0,139,0
7
AT+MQTTPUB=0,"world",0,0,0,4,"3242" //发布消息QoS=0
1
+MQTTPUB: 0,140,13 2
OK 1
AT+MQTTDISC=0 //断开连接 _
OK O
+MQTTURC: "conn",0,2 //断开成功
M
e
Note: MN316A/MN326A/MN318/MN319/ML307H-GU暂不支持MQTTS加密接入。
n
O
36

中移物联网有限公司
5. 错误码
本章为MQTT/MQTTS命令相关的错误码。
错误码 释义
600 未知错误
601 无效参数
602 未连接或连接失败
603 正在连接
604 已经连接
605 网络错误
606 存储错误
607 状态错误
608 DNS错误
609~649 保留
4
7
1
2
1
_
O
M
e
n
O
37