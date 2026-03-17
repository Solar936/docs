4
TCP/IP用户手册 7
1
2
1
_
O
M
e
n
版本：V5.1O.5
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
因使用本手册中所述的产品而引起的中移物联网有限公司对用户的最大赔偿（除在涉及⼀身伤害的情况中根
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
V2.0.0 新增ML307A相关内容。
V3.0.0 新增ML302A相关内容。
新增ML305U相关内容；
更新“AT+MIPCFG TCP/IP参数设置”中<cid> 的参数描述；
V4.0.0
更新“AT+MDNSGIP 域名解析”中<cid> 的参数描述；
更新“AT+MPING PING服务器”中<cid> 的参数描述。
V4.1.0 新增ML305A相关内容。
V4.2.0 新增MN318相关内容。
新增MN319相关内容；
V4.3.0
更新“AT+MIPCFG TCP/IP参数设置”的命令描述。
新增MN328相关内容； 4
7
更新手册适用范围，新增ML307A-DL型号信息；
1
V4.4.0 更新“AT+MDNSCFG 设置域名解析服务器”中<address1>和<address2>的
2
参数描述; 1
_
更新“AT+MIPSEND 发送数据”中<seq> 的参数描述。
O
更新M手册适用范围，新增ML305A-DL型号信息;
更新“AT+MIPCFG TCP/IP参数设置”中<recv_format>参数描述；
e
n
更新“AT+MIPOPEN 建立TCP/IP连接”中<address>参数描述；
O更新“AT+MDNSCFG
V4.5.0 设置域名解析服务器”中命令描述，更新<address1>和<address2>参数描
述；
更新“AT+MDNSGIP 域名解析”中<domainname> 参数描述；
更新“AT+MPING PING服务器”中<host>参数描述；
更新“AT+MNTP 网络时间同步”中<server> 参数描述。
移除MN328相关内容；
新增ML305M相关内容；
更新“AT+MIPCFG TCP/IP参数设置”中<send_buffer>和<recv_buffer>参
V4.6.0 数描述；
更新“AT+MIPCLOSE 关闭TCP/IP连接”中<mode>参数描述；
更新“AT+MIPSEND 发送数据”中<send_length>参数描述；
更新“AT+MIPRD 读取缓存数据”中<pack_count>参数描述；

中移物联网有限公司
版本 描述
更新“AT+MIPMODE 切换数据模式”中<waittm>中参数描述；
更新“+MIPURC TCP/IP URC上报信息”
中<total_length>和<recv_count>参数描述。
更新ML305U相关内容；
更新MN319相关内容；
更新“AT+MIPTKA 设置TCP心跳”中命令描述；
更新“AT+MIPOPEN 建立TCP/IP连接”中<access_mode>脚注；
V4.7.0 更新“AT+MIPMODE 切换数据模式”中<packet_size>和<waittm>参数描
述；
更新“AT+MIPSEND 发送数据”中<seq>脚注；
更新“AT+MIPRD 读取缓存数据”中<read_len>和<pack_count>脚注；
更新“AT+MPING PING服务器”中<cid>脚注。
新增ML307R相关内容；
新增MN316A相关内容；
V4.8.0 更新“AT+MIPCLOSE 关闭TCP/IP连接”中<mode>脚注；
更新“AT+MIPRD 读取缓存数据”中<pack_count>脚注；
更新“AT+MIPMODE 切换数据模式”中<waittm> 脚注。
V4.9.0 新增MN326相关内容。
V5.0.0 新增ML307G相关内容。
新增ML307M相关内容； 4
V5.1.0 7
新增ML307R-BL/ML307R-MC/ML307R-ML相关内容。
1
2
更新手册适用范围，新增ML307G-DC相关内容;
1
V5.1.1 更新“AT+MDNSGI_P 域名解析”中参数<cid>适用范围，
ML307G暂不O支持该参数。
M
V5.1.2 更新手册适用范围，新增ML307M-DA相关内容。
e
n新增server模式，新增“ AT+MIPSRVCFG 设置服务器参数”/
O“AT+MIPLISTEN 进⼀服务器监听模式”/ “AT+MIPSRVSTATE
查询服务器状态”/ “AT+MIPSRVCLOSE 关闭服务器模式”/
V5.1.3 “AT+MIPRDU 读取缓存数据”/ “AT+MIPSENDTO
UDP指定地址发送数据”AT指令相关内容；以及第4章新增“server模
式”小节；
更新手册适用范围，新增ML307H-DU/ML307H-GU相关内容。
V5.1.4 更新手册适用范围，新增ML307C-DL-CN相关内容。
新增ML307N-DC/ML307N-DL相关内容；
新增MN326A-DG相关内容；
V5.1.5
新增ML307X-DB/ML307X-DC相关内容；
更新手册适用范围，新增ML307M-DH相关内容。

中移物联网有限公司
目录
服务与支持.............................................................................................................................................................................................................................ii
文档声明................................................................................................................................................................................................................................iii
关于文档.................................................................................................................................................................................................................................v
1. 引言.....................................................................................................................................................................................................................................8
1.1. 适用型号...............................................................................................................................................................................................................8
2. AT命令概述....................................................................................................................................................................................................................10
2.1. AT命令语法.......................................................................................................................................................................................................11
2.2. AT命令响应.......................................................................................................................................................................................................12
3. TCP/IP协议AT命令......................................................................................................................................................................................................13
3.1. AT+MIPCFG TCP/IP参数设置....................................................................................................................................................................14
3.2. AT+MIPTKA 设置TCP心跳..........................................................................................................................................................................20
3.3. AT+MIPOPEN 建立TCP/IP连接.................................................................................................................................................................22
3.4. AT+MIPCLOSE 关闭TCP/IP连接...............................................................................................................................................................25
3.5. AT+MIPSEND 发送数据................................................................................................................................................................................27
3.6. AT+MIPRD 读取缓存数据............................................................................................................................................................................30
3.7. AT+MIPMODE 切换数据模式.....................................................................................................................................................................32
3.8. AT+MIPSTATE 查询TCP/IP连接状态.......................................................................................................................................................34
3.9. AT+MIPSRVCFG 设置服务器参数............................................................................................................................................................36
3.10. AT+MIPLISTEN 进入服务器监听模式 ..................................................................................................................................................39
4
3.11. AT+MIPSRVSTATE 查询服务器状态 .....................................................................7...............................................................................41
1
3.12. AT+MIPSRVCLOSE 关闭服务器模式.....................................................................................................................................................43
2
3.13. AT+MIPRDU UDP读取缓存数据..............................................................................................................................................................45
1
3.14. AT+MIPSENDTO UDP指定地址发送数据..................._.........................................................................................................................47
3.15. AT+MIPSACK 已发送数据ACK查询..............O.........................................................................................................................................49
3.16. AT+MDNSCFG 设置域名解析服M务器....................................................................................................................................................51
3.17. AT+MDNSGIP 域名解析............................................................................................................................................................................54
e
3.18. AT+MPING PING服务器............................................................................................................................................................................56
n
3.19. +++ 退出透传O模式........................................................................................................................................................................................59
3.20. +MIPURC TCP/IP URC上报信息.............................................................................................................................................................60
3.21. AT+MNTP 网络时间同步...........................................................................................................................................................................63
4. 示例..................................................................................................................................................................................................................................65
4.1. TCP示例.............................................................................................................................................................................................................65
4.2. UDP示例.............................................................................................................................................................................................................66
4.3. 透传模式............................................................................................................................................................................................................66
4.4. 缓存模式............................................................................................................................................................................................................67
4.5. server模式.........................................................................................................................................................................................................69
4.6. PING示例...........................................................................................................................................................................................................71
4.7. DNS示例............................................................................................................................................................................................................71
5. 错误码..............................................................................................................................................................................................................................72

中移物联网有限公司
1. 引言
本文档详细介绍了中移物联网基于TCP、UDP、DNS、PING等定义的标准AT命令及其操作流程，适用于
内部集成了TCP、UDP、DNS和PING的模组产品。
文档中如有未尽细节，请咨询中移物联网技术支持。
1.1. 适用型号
Table 1. 适用模组
模组系列 模组子型号
MN316 MN316-DBRS/MN316-DLVS
MN316-S MN316-S-DLVS
MN316A MN316A-D/MN316A-DC
MN318 MN318-BX/MN318-LC/MN318-LX
MN319 MN319-DL
MN326 MN326-X
4
ML302A ML302A-DCLM/ML302A-DSLM/ML302A-GCLM/ML302A-GSLM
7
1
ML305A ML305A-DC/ML305A-DS/ML305A-DL
2
1
ML307A-DCLN/ML307A-DSLN/ML307A-GCLN/ML307A-GSLN/ML30
_
ML307A
7A-DL O
M
ML302S ML302S-DNLM
e
ML307S ML307S-DNLM
n
O
ML305U ML305U-DBLN
ML305M ML305M-DSLM
ML307X ML307X-DB/ML307X-DC
ML307R ML307R-DC/ML307R-DL/ML307R-BL/ML307R-MC/ML307R-ML
ML307G ML307G-DC/ML307G-DL
MR880A MR880A/MR880A Mini-PCIe/MR880A M.2
ML307M ML307M-DL/ML307M-DA/ML307M-DH
ML307H ML307H-DU/ML307H-GU
ML307C ML307C-DL-CN
8

中移物联网有限公司
Table 1. 适用模组 (continued)
模组系列 模组子型号
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
10

中移物联网有限公司
2.1. AT命令语法
AT命令必须以“AT”或“at”开头，以回车符<CR>结尾；命令后面跟随结构
为“<CR><LF>response<CR><LF>”的响应。为便于阅读，文档中将省略<CR><LF>，仅展示响应内容。
中移物联网模组实现的AT命令集包含3GPP TS 27.005、3GPP TS 27.007、ITU-TV.25ter标准命令集和中
移物联网自定义的扩展命令集。
AT命令根据语法结构可归为基础语法、S参数语法和扩展语法3类。
基础语法
该类AT命令格式为“AT<x><n>”或“AT&<x><n>”；其中“<x>”是命令，“<n>”是命令参数。
比如命令“ATE<n>”，该命令根据“<n>”值确定DCE是否需要将接收到的字符反馈给DTE。“<n>”是
可选项，如果不带该值则使用缺省值。
S参数语法
该类AT命令格式为“ATS<n>=<m>”，其中“<n>”是要设置S寄存器索引，“<m>”是设置值。
扩展语法
该类AT命令有多种操作模式。
4
Table 2. AT命令及响应类型 7
1
类型 命令 响应描述
2
1
测试命令 AT+<CMD>=? 返回参数列表及参数值范围
_
O
读取命令 AT+<CMD>? 返回参数当前值
M
配置命令 AT+<CMD>=<p1>[,<p2[,<p3>[…]]] 设置参数值
e
n
执行命令 AT+<CMD> 执行具体操作
O
其中：
▪ <...>尖括号中是参数，实际输入时不包含尖括号；
▪ [...]方括号中的参数是可选参数。
11

中移物联网有限公司
2.2. AT命令响应
Table 3. AT命令响应类型
响应 释义描述
ERROR AT命令格式错误或其他错误
+CME ERROR: <err>或者+CMS ERROR: 启用了扩展错误报告（+CMEE），其中<err>表示错
<err>或者+CIS ERROR:<err> 误码或详细错误信息
OK AT命令执行成功
Note: AT命令响应结果中，冒号“:”后均存在空格，用以分隔响应头与参数列表。
Note: 手册描述中错误响应用+CME ERROR: <err>或者+CMS ERROR:<err>或者+CIS ERROR:<err>表
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
3. TCP/IP协议AT命令
本章详细描述了TCP、UDP、DNS、PING相关的AT命令和命令格式。
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
13

中移物联网有限公司
3.1. AT+MIPCFG TCP/IP参数设置
该命令用于设置客户端实例相关的通用配置参数。
AT+MIPCFG
语法 响应
成功
+MIPCFG: (list of supported<cmd>s),(list of supported<connect_id>s)
测试命令
OK
AT+MIPCFG=?
失败
+CME ERROR: <err>
成功
仅配置"cid"，查询所有<connect_id>配置：
[+MIPCFG: "cid",<connect_id>[,<cid>]
[…]]
OK
设置命令（PDP上下文索引
号） 配置<connect_id>，查询指定<connect_id>配置：
AT +MIPCFG: "cid",<connect_id>[,<cid>]
+MIPCFG="cid"[,<connect_id OK
>[,<cid>]] 配置完整参数：
4
7
OK
1
2
失败
1
_
+CME ERROR: <err>
O
成功M
仅配置"encoding"，查询所有<connect_id>配置：
e
n
[+MIPCFG: "encoding",<connect_id>,<send_format>,<recv_format>
O
[…]]
OK
设置命令（数据格式）
配置<connect_id>，查询指定<connect_id>配置：
AT
+MIPCFG="encoding"[,<conn +MIPCFG: "encoding",<connect_id>,<send_format>,<recv_format>
ect_id>[,<send_format>,<rec OK
v_format>]]
配置完整参数：
OK
失败
+CME ERROR: <err>
成功
设置命令（发送超时时间）
仅配置"timeout"，查询所有<connect_id>配置：
14

中移物联网有限公司
AT+MIPCFG
[+MIPCFG: "timeout",<connect_id>,<send_timeout>
[…]]
OK
配置<connect_id>，查询指定<connect_id>配置：
AT +MIPCFG: "timeout",<connect_id>,<send_timeout>
+MIPCFG="timeout"[,<conne OK
ct_id>[,<send_timeout>]] 配置完整参数：
OK
失败
+CME ERROR: <err>
成功
仅配置"autofree"，查询所有<connect_id>配置：
[+MIPCFG: "autofree",<connect_id>,<free_mode>
[…]]
OK
设置命令（连接释放模式标
志） 配置<connect_id>，查询指定<connect_id>配置：
AT +MIPCFG: "autofree",<connect_id>,<free_mode>
+MIPCFG="autofree"[,<conn OK
ect_id>[,<free_mode>]] 配置完整参数： 4
7
1
OK
2
失败 1
_
+CME ERRORO: <err>
M
成功
e仅配置"sndbuf"，查询所有<connect_id>配置：
n
O [+MIPCFG: "sndbuf",<connect_id>,<send_buffer>
[…]]
OK
设置命令（发送缓存）
配置<connect_id>，查询指定<connect_id>配置：
AT
+MIPCFG: "sndbuf",<connect_id>,<send_buffer>
+MIPCFG="sndbuf"[,<connec
OK
t_id>[,<send_buffer>]]
配置完整参数：
OK
失败
+CME ERROR: <err>
设置命令（接收缓存） 成功
15

中移物联网有限公司
AT+MIPCFG
仅配置"rcvbuf"，查询所有<connect_id>配置：
[+MIPCFG: "rcvbuf",<connect_id>,<recv_buffer>
[…]]
OK
配置<connect_id>，查询指定<connect_id>配置：
AT
+MIPCFG: "rcvbuf",<connect_id>,<recv_buffer>
+MIPCFG="rcvbuf"[,<connec
OK
t_id>[,<recv_buffer>]]
配置完整参数:
OK
失败
+CME ERROR: <err>
成功
仅配置"ackmode"，查询所有<connect_id>配置：
[+MIPCFG: "ackmode",<connect_id>,<ack_mode>
[…]]
OK
设置命令（发送响应模式）
配置<connect_id>，查询指定<connect_id>配置：
AT
+MIPCFG: "ackmode",<connect_id>,<ack_mode>
+MIPCFG="ackmode"[,<conn
OK 4
ect_id>[,<ack_mode>]] 7
配置完整参数： 1
2
OK 1
_
失败 O
+CMME ERROR: <err>
e
成功
n
O仅配置"ssl"，查询所有<connect_id>配置：
[+MIPCFG: "ssl",<connect_id>,<ssl_enable>[,<ssl_id>]
[…]]
OK
设置命令（SSL连接参数）
配置<connect_id>，查询指定<connect_id>配置：
AT
+MIPCFG: "ssl",<connect_id>,<ssl_enable>[,<ssl_id>]
+MIPCFG="ssl"[,<connect_id
OK
>[,<ssl_enable>,<ssl_id>]]
配置完整参数：
OK
失败
+CME ERROR: <err>
16

中移物联网有限公司
AT+MIPCFG
命令描述
该命令可用于设置或读取客户端实例的通用配置参数，该设置在模组本次启动周期内一直有效。设置命令
中，如果只设置<cmd>(命令标识符)，将返回所有已创建实例的当前配置参数，如果只设置<cmd>
和<connect_id>，将返回指定实例的当前配置参数，如果设置后续参数，将进行参数设置。
参数描述
1
<cmd>字符串型，命令标识符。
cid
设置连接实例所属的PDP上下文
encoding
连接实例的输入和输出格式
timeout
发送超时时间，用于AT命令输入数据，并采用">"模式输入时。
autofree
断开连接后资源释放模式
sndbuf
发送缓存大小
rcvbuf
接收缓存大小
4
7
ackmode
1
TCP响应模式，UDP无效。 2
1
ssl
_
SSL模式设置项，包括使能开关和SSL上下文OID。
M 2
<connect_id>整型，客户端连接实例id。范围：0~5。
e
<cid>整型，PDP上下文id，指定当前实例使用的PDP上下文，范围与AT+CGDCONT命令支持的范围相同，默
n
3
认不指定，指定时需O保证指定cid已激活。
4
<send_format>整型，数据发送格式。默认值0。
0
1. MN316/MN316-S/MN326：<cmd>暂不支持"cid"、"timeout"、"sndbuf"、"ackmode"、"ssl"选项设置。
MN318：<cmd>暂不支持 "timeout"、"sndbuf"、"rcvbuf"、"ssl" 选项设置。
MN316A/MN326A/MN319：<cmd>不支持"cid"、"timeout"、"sndbuf"、"ssl"选项设置。
MR880A：<cmd>不支持"cid"选项设置。
ML307-GU：<cmd>不支持"ssl"选项设置。
2. MN316/MN316-S/MN326模组范围0~4。
MN316A/MN326A/MN319范围0~3。
MR880A范围0~9。
3. ML307H：范围1~5，默认使用第1路，指定时需要保证指定cid已激活。
4. 该参数影响AT+MIPSEND命令的数据输入。
MR880A不支持转义字符输入。
17

中移物联网有限公司
AT+MIPCFG
ASCII字符串（原始数据）
1
HEX字符串
2
带转义的字符串
5
<recv_format>整型，数据接收格式。默认值0。
0
ASCII字符串（原始数据）
1
HEX字符串
6
<send_timeout>整型，发送超时时间。范围：1~180；单位：s。默认值10。
<free_mode>整型，连接异常断开后资源释放模式。默认值0。
0
连接异常断开后自动释放资源，无需使用AT+MIPCLOSE命令。
1
连接异常断开后，需使用AT+MIPCLOSE命令手动释放资源。
<send_buffer>整型，发送缓存大小，范围：1~8192；单位：字节。默认值1460，适用于数据模式下发送缓存
的设置，即AT+MIPSEND命令中，不设置<send_length>时，最大数据缓存长度4。 7
7
<recv_buffer>整型，接收缓存大小，范围：1~65535；单位：字节。1NB模组默认值4096，Cat1模组默认值
2
65535，使用缓存模式时有效。TCP连接时，受TCP协议滑动窗口机制限制，可能出现URC上报信息中缓存无
1
法达到上限的情况，属正常现象，TCP协议配置无效。_需在连接建立之前配置，连接建立后，缓存大小禁止
8 O
配置。
M
<ack_mode>整型，TCP发送数据时，接收ACK包URC上报模式，默认值0，UDP无效。
e
0 n
O
TCP接收ACK包时不上报URC
5. 该参数影响普通模式下+MIPURC: "rtcp"/+MIPURC: "rudp"数据输出打印，缓存模式下AT+MIPRD数据输出打印。
6. ML302S/ML307S/ML302A/ML305A/ML307A/ML307G/ML305M/ML307M/ML307N/ML307R/ML307H/ML307C范围1~120。
7. MN316/MN316-S/MN316A/MN326A/MN326/ML302A/ML305A/ML307A/ML307G/ML302S/ML307S/ML305U/ML305M/ML307M/M
L307R/ML307C模组不支持设置。
8. MN316/MN316-S/MN326：UDP协议接收缓存大小设置范围为4096~8192，TCP协议设置该参数将不做改变，受TCP协议滑动窗口机
制影响，由于TCP滑动窗口大小为4096不可变，故TCP在缓存模式下最大缓存大小为4096字节。
ML302A/ML305A/ML307A/ML302S/ML307S/ML305M/ML307M/ML307N/ML307R/ML307C/ML307G：TCP协议接收缓存大小为滑
动窗口大小，默认64240字节，配置无效；UDP协议默认64K，范围1460~65535，配置有效。
ML305U：TCP协议接收缓存大小为滑动窗口大小，默认63920字节，配置无效；UDP配置有效。
ML307X：TCP协议接收缓存大小为滑动窗口大小，默认65534字节，配置无效；UDP配置有效。
MN316A/MN326A/MN319：TCP协议配置无效，TCP滑动窗口默认2048。UDP协议支持范围1~4096。
MN318：不支持该参数设置，TCP滑动窗口默认2048，UDP缓存默认4096，内存有限，多路缓存时请及时读取，否则内存耗尽可能存
在报错情况。
ML307H：TCP协议接收缓存为滑动窗口大小，默认值为65535，配置无效；UDP配置有效。
MR880A：默认65535字节，TCP、UDP均支持设置。
18

中移物联网有限公司
AT+MIPCFG
1
TCP接收ACK包时上报URC
<ssl_enable>整型，SSL连接使能开关。默认值0。
0
关闭SSL连接
1
开启SSL连接
<ssl_id>整型，SSL连接上下文编号，范围参照《SSL用户手册》。
示例
autofree设置
AT+MIPCFG="autofree",0,1
OK
autofree查询
AT+MIPCFG="autofree",0
+MIPCFG: "autofree",0,1
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
19

中移物联网有限公司
3.2. AT+MIPTKA 设置TCP心跳
该命令用于设置TCP连接的心跳配置。
AT+MIPTKA
语法 响应
成功
+MIPTKA: (list of supported<connect_id>s),(list of supported<keepalive>s),(list of
测试命令 supported<keepidle>s),(list of supported<keepinterval>s),(list of supported<keepcount>s)
OK
AT+MIPTKA=?
失败
+CME ERROR: <err>
成功
+MIPTKA: <connect_id>,<keepalive>[,<keepidle>,<keepinterval>,<keepcount>]
查询命令 …
OK
AT+MIPTKA?
失败
+CME ERROR: <err>
成功
仅配置<connect_id>，查询指定<connect_id4>配置：
7
设置命令
+MIPTKA: <connect_id>,<keepalive>[,<1keepidle>,<keepinterval>,<keepcount>]
OK 2
AT
1
+MIPTKA=<connect_id>[,<ke 配置后续参数，进行_模式设置：
epalive>[,<keepidle>[,<keepi O
OK
nterval>[,<keepcount>]]]] M
失败
e
n+CME ERROR: <err>
O
命令描述
该命令可用于设置或读取客户端实例的心跳参数，该设置在模组本次启动周期内一直有效。查询命令查询所
有连接的心跳参数，查询命令中，如果<keepalive>为0则，不显示后续参数。设置命令中，如果只设
置<connect_id>，将返回当前连接的心跳参数。
参数描述
9
<connect_id>整型，客户端连接实例id，范围：0~5。
<keepalive>整型，心跳使能开关。默认值0。
0
9. MN316/MN316-S/MN326模组范围0~4。
MN316A/MN326A/MN319范围0~3。
MR880A范围0~9。
20

中移物联网有限公司
AT+MIPTKA
关闭心跳
1
打开心跳
10
<keepidle>整型，心跳包发送间隔，范围：30~7200；单位：s。默认值90。
11
<keepinterval>整型，心跳包重传间隔，范围：30~600；单位：s。默认值75。
<keepcount>整型，心跳包重传次数，范围：1~9。默认值3。
示例
测试命令
AT+MIPTKA=?
+MIPTKA: (0-4),(0-1),(30-7200),(30-600),(1-9)
OK
读取命令
AT+MIPTKA?
+MIPTKA: 0,1,90,75,3
+MIPTKA: 1,0
+MIPTKA: 2,0
+MIPTKA: 3,0
+MIPTKA: 4,0
OK
4
查询当前连接配置
7
1
AT+MIPTKA=0
2
+MIPTKA: 0,1,90,75,3 1
OK _
O
设置keepalive
M
AT+MIPTKA=0,1,120,60,1
e
OK
n
O
Note: 创建socket为UDP类型时，不可设置心跳参数。
10. MN316/MN316-S/MN326默认值30。
11. MN316/MN316-S/MN326默认值90。
21

中移物联网有限公司
3.3. AT+MIPOPEN 建立TCP/IP连接
该命令用于建立TCP或UDP连接。
AT+MIPOPEN
语法 响应
成功
+MIPOPEN: (list of supported<connect_id>s),<proto_type>,,(list of
supported<remote_port>s),(list of supported<timeout>s),(list of
测试命令
supported<access_mode>s),(list of supported<local_port>s)
AT+MIPOPEN=? OK
失败
+CME ERROR: <err>
设置命令
成功
AT
OK
+MIPOPEN=<connect_id>,<p
roto_type>,<address>,<remo 失败
te_port>[,<timeout>[,<acces
+CME ERROR: <err>
s_mode>[,<local_port>]]]]
URC（连接失败或非透传模式
+MIPOPEN: <connect_id>,<result> 4
下连接成功）
7
1
URC（透传模式下连接成功） CONNECT 2
1
命令描述 _
O
该命令可用于建立TCP、UDP连接，命令中的设置参数仅对本次连接有效。连接结果以异步URC上报，连接
M
失败或非透传模式下连接成功，上报信息为+MIPOPEN: <connect_id>,<result>，透传模式下连接成功，
上报信息为CONNECT。 12 e
n
参数描述 O
13
<connect_id>整型，客户端连接实例id，范围：0~5。
<proto_type>字符串，连接类型。
TCP
TCP连接
UDP
UDP连接
<address>字符串，服务器地址。
12. MN319建立TCP连接时，建议使用指令AT+MLPMCFG锁定睡眠，否则进入深睡眠后连接将断开。
13. MN316/MN316-S/MN326模组范围0~4。
MN316A/MN326A/MN319范围0~3。
MR880A范围0~9。
22

中移物联网有限公司
AT+MIPOPEN
<remote_port>整型，服务器端口号，范围：0~65535。
14
<timeout>整型，连接超时时间，范围：1~180；单位：s。默认值60s。
15
<access_mode>整型，数据收发模式，默认值0。
0
普通模式（数据接收：接收到的数据加URC前缀后串口直接输出，数据发送：通过AT+MIPSEND命令。）
1
透传模式（数据接收：接收到的数据串口直接输出，数据发送：输入串口的数据直接发送。）
2
流缓存模式（TCP连接支持，数据接收：只上报数据接收提示，数据发送：同普通模式。）
3
包缓存模式（UDP连接支持，数据接收：只上报数据接收提示，数据发送：同普通模式。）
<local_port>整型，客户端端口号，范围：0~65535。默认值0。
0
系统自动分配本地端口号
1-65535
指定的本地端口号，建议配置5 位以上的端口且不使用特殊协议默认的端口。
<result>整型，TCP、UDP连接结果。
4
0 7
1
连接成功 2
1
其他
_
连接失败 16 O
M
示例
e
测试命令
n
O
AT+MIPOPEN=?
+MIPOPEN: (0-4),,,(0-65535),(1-180),(0-3),(0-65535)
OK
建立连接
AT+MIPOPEN=0,"TCP","120.27.12.119",2040
OK
+MIPOPEN: 0,0
AT+MIPOPEN=1,"UDP","120.27.12.119",2040
14. MN316/MN316-S/MN326该参数不生效。
15. MN316/MN316-S/MN316A/MN326A/MN318/MN319/MN326不支持<access_mode>=1透传模式。
ML302A/ML305A/ML307A/ML307X/ML302S/ML307S/ML305U/ML305M/ML307M/ML307N/ML307R/ML307G/MR880A/ML30
7H/ML307C的SSL协议数据需要解密，故流缓存模式下需要将当前数据读取完后才能收到后续数据。
16. NB系列返回1；其他系列模组返回值<err>参考附录错误码表。
23

中移物联网有限公司
AT+MIPOPEN
OK
+MIPOPEN: 1,0
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
3.4. AT+MIPCLOSE 关闭TCP/IP连接
该命令用于关闭TCP或UDP连接。
AT+MIPCLOSE
语法 响应
成功
+MIPCLOSE: (list of supported<connect_id>s),(list of supported<mode>s)
测试命令
OK
AT+MIPCLOSE=?
失败
+CME ERROR: <err>
成功
设置命令
OK
AT
+MIPCLOSE=<connect_id>[, 失败
<mode>]
+CME ERROR: <err>
URC +MIPCLOSE: <connect_id>[,<ret_code>]
命令描述
该命令可用于关闭TCP、UDP连接，最终关闭结果将以+MIPCLOSE: <connect_id>4[,<ret_code>]异步URC上报，
关闭模式<mode>=1时（立即关闭），<ret_code>将无效不打印。 7
1
参数描述 2
1
<connect_id>整型，客户端连接实例id，范围：0~5。_17
O
18
<mode>整型，仅对TCP生效，socket关闭模式，默认值0。
M
0
e
等待发送缓存区数据发送n完毕后（不等待2MSL），关闭TCP连接。
O
1
立即关闭不等待缓存区数据发送完毕（不等待2MSL）。
2
17. MN316/MN316-S/MN326模组范围0~4。
MN316A/MN326A/MN319范围0~3。
MR880A范围0~9。
18. MN316/MN316-S/MN319/MN326不支持<mode>参数配置，关闭方式为模式2。
MN316A/MN326A不支持<mode>参数配置，关闭方式为模式4。
MN318不支持<mode>参数配置，关闭方式为向服务器发送RST消息重置连接后关闭。
ML302A/ML305A/ML307A/ML302S/ML307S/ML305U/ML305M/ML307M/ML307R/ML307C/ML307X不支持<mode>参数配置，
关闭方式为等待缓存区数据发送完毕。
ML307G<mode>参数范围：0~3。
ML307H不支持<mode>参数配置，关闭方式为立即关闭不等待缓存区数据发送完毕 。
MR880A不支持<mode>参数配置，关闭方式为模式4，其中MSL=30s。
25

中移物联网有限公司
AT+MIPCLOSE
不等待发送缓存区数据发送完毕，等待 2MSL（Maximum Segment Lifetime，最大分段报文生存周期）
后关闭。如果建立连接时未指定本地端口，关闭连接时等待的时间将不影响下一次创建，可忽略该等待时
间；如果建立连接时指定了本地端口，关闭连接时该端口将在2MSL后才可使用。该等待的起始时间为最后
19
一次数据交互的时间，所以资源释放等待时间为小于等于2MSL。
3
向服务器发送RST消息重置连接后关闭。
4
等待发送缓存区数据发送完毕后，再等待 2MSL（Maximum Segment Lifetime，最大分段报文生存周期）
后关闭。 等待 2MSL策略与模式2相同。
<ret_code>整型，返回码，<mode>有效时触发。
0
正常关闭
1
服务器端未响应，超时关闭。
2
其他原因关闭（收到RST、传输超时等。）
示例
测试命令
AT+MIPCLOSE=? 4
7
+MIPCLOSE: (0-4),(0-3)
1
OK
2
关闭连接 1
_
AT+MIPCLOSE=0 O
OK
M
+MIPCLOSE: 0
e
n
O
19. MN316/MN316-S/MN316A/MN326A/MN319/MN326中MSL=5.5s。
26

中移物联网有限公司
3.5. AT+MIPSEND 发送数据
该命令用于TCP或UDP连接发送数据。
AT+MIPSEND
语法 响应
成功
+MIPSEND: (list of supported<connect_id>s),(list of supported<send_length>s),,(list of
测试命令 supported<rai>s),(list of supported<seq>s),(list of supported<pri_flag>s)
OK
AT+MIPSEND=?
失败
+CME ERROR: <err>
成功
<send_length>不设置，<data>不设置，以<CTRL+Z>发送数据，以<ESC>取
消发送：
>(输入数据)
OK
<send_length>设置不为0，<data>不设置，达到指定长度或达到超时时间时
发送数据：
设置命令
>(输入数据)
AT 4
OK
7
+MIPSEND=<connect_id>[,<s
<send_length>设置为0，<data>不1设置，UDP时有效，发送空包： 20
end_length>[,<data>[,<rai>[, 2
<seq>[,<pri_flag>]]]]] +MIPSEND: <connect_id>,<s 1 end_length>
_
OK
O
<send_length>和<data>都设置，命令中直接输入数据并发送：
M
+MIPSEND: <connect_id>,<send_length>
e
nOK
O
失败
+CME ERROR: <err>
命令描述
该命令可用于TCP、UDP连接发送数据。当数据成功发送到协议栈的缓存中时，将上报+MIPSEND:
<connect_id>,<send_length>，要判断对端服务器是否成功接收，请通过ackmode配置。
设置命令：命令中带<data>设置时，数据直接发送，数据输入格式受AT+MIPCFG="encoding"命令
中<send_format>参数影响；命令中不带<data>设置时，将以">"模式输入数据，当收到">"打印后输入相应数
20. MN316/MN316-S/MN316A/MN326A/MN326/MN318 模组暂不支持 UDP 发送空包。
ML302A/ML305A/ML307A/ML307G/ML302S/ML307S/ML305U/ML305M/ML307M/ML307R/ML307H/ML307C/ML307X此时为不
指定长度的数据模式。
27

中移物联网有限公司
AT+MIPSEND
据，输入数据为原始数据，不受其他命令控制，数据输入超时时间受AT+MIPCFG="timeout"命令
21
中<send_timeout>参数影响。
参数描述
22
<connect_id>整型，客户端连接实例id，范围：0~5。
<send_length>整型，发送的数据长度，命令中直接输入数据时的范围：0~1460。数据模式下的范围：
23
1~8192。单位：字节。
以">"模式输入数据时，<send_length>不能设置为0。命令中直接输入数据时：当<send_length> 等于0
或缺省，不对数据长度进行校验；当<send_length>大于0，将对输入数据的长度进行校验（ASCII字符串和带
转义的字符串输入模式下：校验实际命令中输入字符串长度是否与指定长度相等。HEX字符串输入模式下：
校验实际命令中输入字符串长度是否是指定长度的两倍）。
以">"模式输入数据时，<data>之后的参数无法设置。数据发送单包长度受MTU限制，超过长度时IP层将自动
分包，对于UDP协议，数据分包会增加组包后整包丢包的概率，因此建议使用时单包不超过最大长度。IPv4
TCP单包最大长度为MTU-40，IPv4 UDP 单包最大长度为MTU-28，IPv6 TCP单包最大长度为MTU-60，
IPv6 UDP单包最大长度为MTU-48。
<data>字符串，发送的数据内容。
24
<rai>整型，列举帮助信息。默认值0。
0
无信息
1 4
7
终端发送一个上行包
1
2 2
1
终端发送一个上行包，并期望接收到一个下行包。
_
O 25
<seq>整型，UDP空口回传序列号，仅UDP连接支持，范围：1~255。
M
26
<pri_flag>整型，优先级标志。默认值0。
e
0 n
O
IPTOS 可靠性
1
21. MN316/MN316-S/MN316A/MN326A/MN318/MN319/MN326不支持“>”下的数据输入模式，只能在 AT 命令中直接输入数据。
ML305U以“>”数据模式输入数据时，数据长度最大支持4096字节。
22. MN316/MN316-S/MN326模组范围0~4。
MN316A/MN326A/MN319范围0~3。
MR880A范围0~9。
23. ML307H建立连接后发送的首个数据包，最大只能发送16K字节,后续单个数据包能支持发送的最大长度，根据拥塞窗口变化逐渐增
加。
ML307N：设置命令带数据内容参数为hex输入时，数据长度受平台单条命令长度限制，数据内容过长时建议使用数据模式。
24. MN316/MN316-S/MN316A/MN326A/MN318/MN326TCP不 支持 RAI。
4G/MN319/MR880A不支持RAI。
25. 4G/MR880A系列不支持。
MN316A/MN326A/MN319：当配置参数时，IPv6数据包长度最大仅支持MTU-48，MTU根据网络决定，范围为1280~1500，建议
IPv6数据发送长度不超过1232，否则超过MTU-48，AT+MIPSEND将报错。
26. 暂不支持该参数设置。
28

中移物联网有限公司
AT+MIPSEND
IPTOS 低延时
2
IPTOS 吞吐量
3
IPTOS 低消耗
示例
测试命令
AT+MIPSEND=?
+MIPSEND: (0-4),(0-1460),,(0-2),(1-255)
OK
发送测试
AT+MIPSEND=0,10,"0123456789"
+MIPSEND: 0,10
OK
Note: 使用RAI和SEQ时不支持灌包。
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
29

中移物联网有限公司
3.6. AT+MIPRD 读取缓存数据
该命令用于读取TCP、UDP连接的缓存数据。
AT+MIPRD
语法 响应
成功
+MIPRD: (list of supported<connect_id>s),(list of supported<cache>s)
测试命令
OK
AT+MIPRD=?
失败
+CME ERROR: <err>
成功
仅设置<connect_id>，查询指定连接缓存信息：
+MIPRD: <connect_id>,<unread_len>
设置命令（TCP连接）
OK
AT 设置<read_len>，读取缓存：
+MIPRD=<connect_id>[,<rea
[+MIPRD: <connect_id>,<unread_len>,<data_len>,<data>]
d_len>]
OK
失败
4
+CME ERROR: <err>
7
1
成功
2
仅设置<connect_id>，查1询指定连接缓存信息：
_
+MIPRD: <coOnnect_id>,<unread_packcount>
设置命令（UDP连接）
OK
M
AT 设置<pack_count>，读取缓存：
e
+MIPRD=<connect_id>[,<pac
n
[+MIPRD: <connect_id>,<unread_packcount>,<data_len>,<data>]
k_count>] O
OK
失败
+CME ERROR: <err>
命令描述
该命令可用于读取TCP、UDP连接的缓存数据。TCP连接为流缓存模式，读取时按数据长度读取；UDP连接
为包缓存模式，读取时按数据包个数读取。若缓存区无可读的缓存数据，则执行设置命令时，仅返回OK。
参数描述
27
<connect_id>整型，客户端连接实例id，范围：0~5。
27. MN316/MN316-S/MN326模组范围0~4。MN316A/MN326A/MN319范围0~3。MR880A范围0~9。
30

中移物联网有限公司
AT+MIPRD
<read_len>整型，读取缓存数据长度，范围：0~4096；单位：字节。为0或超过已有缓存长度时读取全部缓存
28
数据。
<unread_len>整型，流缓存模式（TCP连接）下，剩余缓存数据大小。
<data_len>整型，读取的缓存数据长度。
<data>字符串型，读取到的缓存数据。数据格式受AT+MIPCFG="encoding"命令中<recv_format>参数影响。
<pack_count>整型，包个数。受协议栈限制，NB模组最大缓存包个数为12个，Cat1模组最大缓存包个数为
256个，UDP协议缓存模式达到缓存上限后，继续接收数据将出现丢包并打印+MIPURC:
29
"drop",<connect_id>,<drop_length>提示。
<unread_packcount>整型，包缓存模式（UDP连接）下，剩余缓存包个数。
示例
测试命令
AT+MIPRD=?
+MIPRD: (0-4),(0-4096)
OK
读取数据
AT+MIPRD=0,20
+MIPRD: 0,10,20,01234567890123456789
OK 4
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
28. MN316A/MN326A/MN319内存有限，不支持大包读取，支持范围：1~1460。
29. ML302A/ML305A/ML307A/ML307G/ML302S/ML307S/ML307R/ML307C：缓存包数为256。
MN316A/MN326A/MN319：内存有限，不支持多包读取，只可单包读取。
ML305M/ML307M/ML307N：缓存包数为128。
ML307H：缓存包数为45，每包最大1500个字节。
MR880A缓存包数为256。
31

中移物联网有限公司
3.7. AT+MIPMODE 切换数据模式
该命令用于切换已有连接的数据模式。
AT+MIPMODE
语法 响应
成功
+MIPMODE: (list of supported<connect_id>s),(list of supported<access_mode>s),(list of
测试命令 supported<packet_size>s),(list of supported<waittm>s)
OK
AT+MIPMODE=?
失败
+CME ERROR: <err>
成功
仅设置<connect_id>，查询指定连接的数据模式：
设置命令
+MIPMODE: <connect_id>,<access_mode>
OK
AT
+MIPMODE=<connect_id>[,< 设置<connect_id>之后参数，切换数据模式：
access_mode>[,<packet_size
OK
>,<waittm>]]
失败
4
+CME ERROR: <err>
7
1
命令描述
2
1
该命令可用于切换TCP、UDP连接的数据模式，仅可在连接建立成功后使用，数据模式的初始状态可在AT
_
+MIPOPEN命令中进行设置。<packet_size>和<Owaittm>在透传模式下有效。当连接为缓存模式，且接收缓存区
中有数据未读取，执行该命令切换到普通模式时，模组将自动读取缓存区中所有的缓存数据，数据上报格式
M
参考AT+MIPRD命令。
e
n
参数描述
O
30
<connect_id>整型，客户端连接实例id，范围：0~5。
31
<access_mode>整型，数据收发模式，默认值由AT+MIPOPEN命令中的参数<access_mode>配置。
0
普通模式（数据接收：接收到的数据加URC前缀后串口直接输出，数据发送：通过AT+MIPSEND命令。）
1
透传模式（数据接收：接收到的数据串口直接输出，数据发送：输入串口的数据直接发送。）
2
流缓存模式（TCP连接支持，数据接收：只上报数据接收提示，数据发送：同普通模式。）
30. MN316/MN316-S/MN326模组范围0~4。
MN316A/MN326A/MN319范围0~3。
MR880A范围0~9。
31. MN316/MN316-S/MN316A/MN326A/MN318/MN319/MN326：暂不支持透传模式。
32

中移物联网有限公司
AT+MIPMODE
3
包缓存模式（UDP连接支持，数据接收：只上报数据接收提示，数据发送：同普通模式。）
<packet_size>整型，透传模式下，指定单次发送的最大数据长度，范围：512~1460；单位：字节。默认值
32
1024。
<waittm>整型，透传模式下，传入数据未达到<packet_size>指定长度时，超过<waittm>时间后直接发送，范
33
围：0~2000；单位：ms。默认值200。
示例
测试命令
AT+MIPMODE=?
+MIPMODE: (0-4),(0-3),(512-1460),(100-2000)
OK
查询连接模式
AT+MIPMODE=0
+MIPMODE: 0,0
OK
切换为流缓存模式
AT+MIPMODE=0,2
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
32. ML302S/ML307S /ML302A/ML305A/ML307A/ML307G/ML305U/ML305M/ML307M/ML307N/ML307R/ML307C/ML307X不支持配
置。
33. ML302A/ML305A/ML307A/ML302S/ML307S/ML307G/ML305U/ML305M/ML307M/ML307N/ML307R/ML307C/ML307X不支持配
置。
33

中移物联网有限公司
3.8. AT+MIPSTATE 查询TCP/IP连接状态
该命令用于查询TCP、UDP连接状态信息。
AT+MIPSTATE
语法 响应
成功
+MIPSTATE: <connect_id>,<service_type>,<address>,<remote_port>,<state>
查询命令 …
OK
AT+MIPSTATE?
失败
+CME ERROR: <err>
成功
+MIPSTATE: <connect_id>,<service_type>,<address>,<remote_port>,<state>
设置命令
OK
AT+MIPSTATE=<connect_id>
失败
+CME ERROR: <err>
命令描述
该命令可用于查询TCP、UDP连接的状态信息。查询命令查询所有连接的状态（包括未建立的连接），设置
4
命令查询指定连接的状态。 7
1
参数描述 2
1
34
<connect_id>整型，客户端连接实例id，范围：0~5。_
O
<service_type>字符串，连接类型。
M
TCP
e
TCP连接 n
O
UDP
UDP连接
<address>字符串，服务器地址。
<remote_port>整型，服务器端口号，范围：0~65535。
<state>字符串，连接状态。
INITIAL
未建立连接
CONNECTING
34. MN316/MN316-S/MN326模组范围0~4。
MN316A/MN326A/MN319范围0~3。
MR880A范围0~9。
34

中移物联网有限公司
AT+MIPSTATE
正在建立连接
CONNECTED
已建立连接
CLOSING
正在关闭连接
CLOSED
连接已关闭
示例
查询所有连接状态
AT+MIPSTATE?
+MIPSTATE: 0,"TCP","120.27.12.119",2040,"CONNECTED"
+MIPSTATE: 1,"TCP","120.27.12.119",2040,"CONNECTING"
+MIPSTATE: 2,,,,"INITIAL"
+MIPSTATE: 3,"UDP","120.27.12.119",2040,"CONNECTED"
+MIPSTATE: 4,,,,"INITIAL"
OK
查询指定连接状态
AT+MIPSTATE=0
+MIPSTATE: 0,"TCP","120.27.12.119",2040,"CONNECTED"
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
35

中移物联网有限公司
3.9. AT+MIPSRVCFG 设置服务器参数
该命令用于配置服务器相关指定参数。
AT+MIPSRVCFG
语法 响应
成功
测试命令
+MIPSRVCFG: "cid",(list of supported<sid>s),(list of supported<cid>s)
+MIPSRVCFG: "encoding",(list of supported<sid>s),(list of supported <recv_format>s)
AT+MIPSRVCFG=?
+MIPSRVCFG: "rcvbuf",(list of supported<sid>s),(list of supported <recv_buffer>s)
OK
成功
仅配置"cid"，查询所有<sid>配置：
[+MIPSRVCFG: "cid",<sid>[,<cid>][…]]
OK
设置命令（PDP上下文索引
配置<sid>，查询指定<sid>配置：
号）
+MIPSRVCFG: "cid",<sid>[,<cid>]
AT
OK
+MIPSRVCFG="cid",<sid>[,<c
id>]
配置完整参数：
4
OK
7
1
失败
2
1
+CME ERROR: <err>
_
O
成功
M
仅配置"encoding"，查询所有<sid>配置：
e
[+MIPSRVCFG: "encoding",<sid>,<recv_format>[…]]
n
OK
O
设置命令（新连接接收数据格
配置<sid>，查询指定<sid>配置：
式）
+MIPSRVCFG: "encoding",<sid>,<recv_format>
AT
OK
+MIPSRVCFG="encoding"[,<
配置完整参数：
sid>[,<recv_format>]]
OK
失败
+CME ERROR: <err>
设置命令（新连接接收缓存， 成功
为单路连接缓存大小） 仅配置"rcvbuf"，查询<sid>配置：
36

中移物联网有限公司
AT+MIPSRVCFG
[+MIPSRVCFG: "rcvbuf",<sid>,<recv_buffer>[…]]
OK
配置<sid>，查询指定<sid>配置：
+MIPSRVCFG: "rcvbuf",<sid>,<recv_buffer>
AT
OK
+MIPSRVCFG="rcvbuf"[,<sid
配置完整参数：
>[,<recv_buffer>]]
OK
失败
+CME ERROR: <err>
命令描述
该命令用于配置服务器相关参数，对应参数值均将映射至监听建立的新连接中，作为新连接的默认配置。如
下参数均需在监听建立前设置，监听建立后无法设置。
参数描述
35
<cmd>字符串，命令标识符。
cid
设置服务器实例所属的PDP上下文
encoding
4
设置服务器新连接的数据接收格式 7
1
rcvbuf 2
1
设置服务器新连接的接收缓存大小
_
O
<sid>整型，服务器实例id，用于标识操作的服务器实例。范围：0~3。
M
<cid>整型，PDP上下文id，指定当前实例使用的PDP上下文，范围与AT+CGDCONT命令支持的范围相同，默
认不指定，指定时需保证指定<cied>已激活。
n
<rev_format>整型，O新连接数据接收格式。默认值0。
0
ASCII字符串（原始数据）
1
HEX字符串
<recv_buffer>整型，新连接接收缓存大小，范围：1~65535，单位：字节。使用缓存模式时有效。TCP连接
时，受TCP协议滑动窗⼀机制限制，可能出现URC上报信息中缓存⼀法达到上限的情况，属正常现象，TCP协
36
议配置无效。
示例
35. MR880A不支持"cid"设置。
36. MR880A TCP协议配置有效。
37

中移物联网有限公司
AT+MIPSRVCFG
AT+MIPSRVCFG=?
+MIPSRVCFG: "encoding",(0-3),(0-1)
+MIPSRVCFG: "rcvbuf",(0-3),(1-65535)
OK
Note: 该命令目前仅适用于MR880A。
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
38

中移物联网有限公司
3.10. AT+MIPLISTEN 进入服务器监听模式
该命令用于打开服务器监听模式。
AT+MIPLISTEN
语法 响应
成功
测试命令
+MIPLISTEN: (list of supported<sid>s),(list of supported<protocol>s),(list of
AT+MIPLISTEN=? supported<port>s),(list of supported<type>s),(list of supported<access_mode>s)
OK
成功
查询命令
+MIPLISTEN: <sid>,<protocol>,<port>,<type>,<access_mode>
AT+MIPLISTEN? [+MIPLISTEN: <sid>,<protocol>,<port>,<type>,<access_mode>[…]]
OK
设置命令 成功
AT OK
+MIPLISTEN=<sid>,<protoco
失败
l>,<port>[,<type>[,<access_
mode>]] ERROR
URC（监听模式打开异步结
+MIPLISTEN: <sid>,<result> 4
果）
7
1
命令描述
2
1
该命令用于打开监听模式，进行新连接监听。TCP服务器模式下，监听到新连接请求时将自动接收新连接，
_
并建立一路客户端。UDP服务器模式下，默认O不占用TCP/IP客户端路数，当初次接收到数据时，建立一路客
户端，一个UDP服务器最多只占用一路客户端。可接受的最大连接数受限于TCP/IP命令支持的最大客户端路
M
数。
e
n
参数描述
O
<sid>整型，服务器实例id，用于标识操作的服务器实例。范围：0~3。
<protocol>字符串，协议类型。
TCP
TCP协议
UDP
UDP协议
<port>整型，监听端口。范围：1~65535。
<type>字符串，IP类型。设置的类型需与当前UE获取到的IP类型匹配，否则报错。例如，无IPv6地址设置
IPv6时将报错。
IP
39

中移物联网有限公司
AT+MIPLISTEN
IPv4类型
IPV6
IPv6类型
<access_mode>整型，客户端连接后的默认数据收发模式。
0
普通模式（数据接收：接收到的数据加URC前缀后串⼀直接输出。数据发送：通过AT+MIPSEND命令。）
1
透传模式（服务器模式，不支持。）
2
流缓存模式（TCP连接支持。数据接收：只上报数据接收提示。数据发送：同普通模式。）
3
包缓存模式（UDP连接支持。数据接收：只上报数据接收提示。数据发送：同普通模式。）
示例
测试命令
AT+MIPLISTEN=?
+MIPLISTEN: (0-3),("tcp","udp"),(1-65535),("ip","ipv6"),(0,2,3)
OK
进入监听模式
4
7
AT+MIPLISTEN=0,"tcp",1,"ip"
1
OK
2
+MIPLISTEN: 0,0
1
_
O
Note: 该命令目前仅适用于MR880A。
M
e
n
O
40

中移物联网有限公司
3.11. AT+MIPSRVSTATE 查询服务器状态
该命令用于查询服务器状态。
AT+MIPSRVSTATE
语法 响应
成功
测试命令
+MIPSRVSTATE: (list of supported<sid>s)
AT+MIPSRVSTATE=?
OK
成功
+MIPSRVSTATE: <sid>,<srvstate>,<client_count>,<total_sent>,<total_recv>
读取命令 [+MIPSRVSTATE: <sid>,<srvstate>,<client_count>,<total_sent>,<total_recv>[…]]
OK
AT+MIPSRVSTATE?
失败
ERROR
成功
设置命令
+MIPSRVSTATE: <sid>,<connect_id>,<addr>,<port>
[+MIPSRVSTATE: <sid>,<connect_id>,<addr>,<p
AT+MIPSRVSTATE=<sid>
ort>…]
OK
4
命令描述 7
1
读取命令可用于查询服务器已成功建立连接的客户端数量，以及2服务器收发数据的总量。设置命令可用于查
1
询服务器已成功建立连接的对端地址和<connent_id>。
_
O
参数描述
M
<sid>整型，服务器实例id，用于标识操作的服务器实例。范围：0~3。
e
<srvstate>字符串，服务器开n启状态。
O
INITIAL
初始化/关闭状态
OPENNING
启动任务
LISTENING
监听状态
CLOSING
关闭过程中
<client_count>整型，监听模式下已建立的客户端总数。UDP服务器模式下，该值返回记录的对端地址数量。
<total_sent>整型，监听模式下服务器向所有客户端累积发送数据总数。
41

中移物联网有限公司
AT+MIPSRVSTATE
<total_recv>整型，监听模式下客户端向服务器累积发送的数据总数。
<connect_id>整型，客户端连接实例id。
<addr>字符串，客户端IP地址。
<port>整型，客户端端口。
示例
查询所有服务器实例状态
AT+MIPSRVSTATE?
+MIPSRVSTATE: 0,"LISTENING",1,0,0
+MIPSRVSTATE: 1,"INITIAL",0,0,0
+MIPSRVSTATE: 2,"INITIAL",0,0,0
+MIPSRVSTATE: 3,"INITIAL",0,0,0
OK
查询服务器实例0相关信息
AT+MIPSRVSTATE=0
+MIPSRVSTATE: 0,0,"182.14.25.45",46854
OK
Note: 该命令目前仅适用于MR880A。
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
42

中移物联网有限公司
3.12. AT+MIPSRVCLOSE 关闭服务器模式
该命令用于关闭服务器。
AT+MIPSRVCLOSE
语法 响应
成功
测试命令
+MIPSRVCLOSE: (list of supported<sid>s)
AT+MIPSRVCLOSE=?
OK
成功
设置命令
OK
AT
+MIPSRVCLOSE=<sid>[,<mo 失败
de>]
ERROR
URC（服务器关闭异步结果） +MIPSRVCLOSE: <sid>,<ret>
命令描述
该命令用于关闭服务器，监听连接将关闭，服务器建立的客户端连接也将全部关闭。
参数描述
<sid>整型，服务器实例id，用于标识操作的服务器实例。范围：0~3。 4
7
37
<mode>整型，仅对TCP有效，socket关闭模式，默认值0。 1
2
0 1
_
等待发送缓存区数据发送完毕后（不等待2MSL），关闭TCP连接。
O
1
M
⼀即关闭不等待缓存区数据发送完毕（不等待2MSL）。
e
2 n
O
不等待发送缓存区数据发送完毕，等待 2MSL（Maximum Segment Lifetime，最大分段报文⼀存周期）
后关闭。如果建立连接时未指定本地端口，关闭连接时等待的时间将不影响下⼀次创建，可忽略该等待时
间；如果建立连接时指定了本地端口，关闭连接时该端口将在2MSL后才可使用。该等待的起始时间为最后
一次数据交互的时间，所以资源释放等待时间为小于等于2MSL。
3
向服务器发送RST消息重置连接后关闭。
4
等待发送缓存区数据发送完毕后，再等待 2MSL（Maximum Segment Lifetime，最大分段报文生存周期）
后关闭。等待2MSL策略与模式2相同。
示例
关闭服务器0
37. MR880A不支持<mode>参数配置，关闭方式为模式4，其中MSL=30s。
43

中移物联网有限公司
AT+MIPSRVCLOSE
AT+MIPCLOSE=0
OK
Note: 该命令目前仅适用于MR880A。
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
44

中移物联网有限公司
3.13. AT+MIPRDU UDP读取缓存数据
该命令用于读取缓存数据，仅UDP连接可用。
AT+MIPRDU
语法 响应
成功
+MIPRDU: (list of supported<connect_id>s),(list of supported<pack_count>s)
测试命令
OK
AT+MIPRDU=?
失败
+CME ERROR: <err>
成功
仅设置<connect_id>，查询指定连接缓存信息：
+MIPRDU: <connect_id>,<unread_packcount>
OK
设置命令（UDP连接）
设置<pack_count>，读取缓存：
AT
+MIPRDU: <connect_id>,<client_addr>,<client_port>,<unread_packcount>,<data_len>,
+MIPRDU=<connect_id>[,<p <data>
ack_count>] [+MIPRDU:
<connect_id>,<client_addr>,<client_port>,<unread_packcount>,<data_len>,<data>[…]]
OK 4
7
失败
1
2
+CME ERROR: <err>
1
_
命令描述 O
该命令仅UDP连接可用，用于按包读M取缓存数据，并打印对端地址和端口数据。
e
参数描述
n
O 38
<connet_id>整型，客户端连接实例id，范围：0~5。
<pack_count>整型，读取数据包个数。范围：0~256。设置0时读取全部缓存数据。
<unread_packcount>整型，UDP包缓存模式下，剩余缓存包个数。
<client_addr>字符串，客户端地址。
<client_port>整型，客户端端口。
<data_len>整型，读取的缓存数据长度。
<data>字符串，数据内容。
示例
读取缓存
38. MR880A范 围 0~9。
45

中移物联网有限公司
AT+MIPRDU
AT+MIPRDU=0,1
+MIPRDU:
0,"8.137.154.246",64775,3,100,K$cUaJFs;3>2j#GWoq=N^2+7kBqds<|Nu._S!Z5R>FuN7$LTIa;FR0wSC7nNHXHk`,/zg['s=z)Hc,=Y|S,
vcOh]718+uXQ2s{P9
OK
AT+MIPRDU=0,0
+MIPRDU:
0,"8.137.154.246",64775,2,100,{X-!g8$>]KAZxWy$]"V16}s9b{rLTU(-</*s>4J.RHPo#xy]^~#fU"@0*NH8m}jaK{2<G4^M&/Cp8`lSF#-*
u=-T5C&+b>hBih)H
+MIPRDU:
0,"8.137.154.246",64775,1,100,I{}WcJx^~#m.RCXAH:PBT2:)~t?:tq3NT8nV{]^5}(0&?<7@6"f;agjzZ`rr.l<B}O2lT{Xc_A0iZ8I*lg2z[f.&1+
#q?i}D`<nN
+MIPRDU:
0,"8.137.154.246",64775,0,100,ww0r2m[QL*(K=Kb6@zMbfhKS$<F"^Gj5BDj60WB@0,I=}<rvmOaOt"#;I.];]<Cf<G#\p<TKR(;{lht8RX|E
Tz5{p,-'n7(^>*+C
OK
Note:
此命令操作TCP连接id报错，不指定读取数量时，查询缓存数据包个数，否则读取FIFO列表中count
个数据包；
该命令目前仅适用于MR880A。
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
46

中移物联网有限公司
3.14. AT+MIPSENDTO UDP指定地址发送数据
该命令用于UDP连接向指定IP地址发送数据。
AT+MIPSENDTO
语法 响应
成功
测试命令
+MIPSENDTO: (list of supported<connect_id>s),,(list of supported<port>s),(list of
supported<send_length>s),,(list of supported<rai>s),(list of supported<seq>s),(list of
AT+MIPSENDTO=?
supported<pri_flag>s)
OK
成功
<send_length>不设置，<data>不设置，以<CTRL+Z>发送数据，以<ESC>取
消发送：
>(输入数据)
+MIPSENDTO: <connect_id>,<send_length>
OK
<send_length>设置不为0，<data>不设置，达到指定长度或达到超时时间时
设置命令 发送数据：
AT >(输入数据)
+MIPSENDTO: <connect_id>,<send_length>
+MIPSENDTO=<connect_id>,
OK 4
<addr>,<port>[,<send_lengt
7
h>[,<data>[,<rai>[,<seq>[,<p <send_length>设置为0，<data>不 1 设置，发送空包：
ri_flag>]]]]] 2
+MIPSENDTO: <connect_id>,<send_length>
1
OK _
O
<send_length>和<data>都设置，命令中直接输入数据并发送：
M
+MIPSENDTO: <connect_id>,<send_length>
eOK
n
O失败
+CME ERROR: <err>
命令描述
该命令用于UDP连接向指定IP地址发送数据。当数据成功发送到协议栈的缓存中时，将上报+MIPSENDTO:
<connect_id>,<send_length>。
设置命令：命令中带<data>设置时，数据直接发送，数据输入格式受AT+MIPCFG="encoding"命令
中<send_format>参数影响；命令中不带<data>设置时，将以">"模式输入数据，当收到">"打印后输入相应数
据，输入数据为原始数据，不受其他命令控制，数据输入超时时间受AT+MIPCFG="timeout"命令
中<send_timeout>参数影响。
参数描述
47

中移物联网有限公司
AT+MIPSENDTO
39
<connet_id>整型，客户端连接实例id，范围：0~5。
<addr>字符串，对端IP地址。
<port>整型，对端端口，范围：1~65535。
<send_length>整型，发送的数据长度，命令中直接输入数据时的范围：0~1460，数据模式下的范围：
1~8192，单位：字节。
<data>字符串，发送的数据内容。
40
<rai>整型，列举帮助信息。默认值0。
0
无信息
1
终端发送一个上行包
2
终端发送一个上行包，并期望接收到一个下行包。
41
<seq>整型，UDP空口回传序列号，仅UDP连接支持，范围：1~255。
42
<pri_flag>整型，优先级标志。默认值0。
0
IPTOS可靠性 4
7
1 1
2
IPTOS低延时
1
2 _
O
IPTOS吞吐量
M
3
e
IPTOS低消耗
n
O
示例
发送数据
AT+MIPSENDTO=0,8.137.154.246,2045,,1
+MIPSENDTO: 0,1
OK
Note: 该命令目前仅适用于MR880A。
39. MR880A范 围 0~9。
40. MR880A不支持设置。
41. MR880A不支持设置。
42. MR880A暂不支持该参数设置。
48

中移物联网有限公司
3.15. AT+MIPSACK 已发送数据ACK查询
该命令用于查询已发送数据的ACK信息。
AT+MIPSACK
语法 响应
成功
+MIPSACK: (list of supported<connect_id>s)
测试命令
OK
AT+MIPSACK=?
失败
+CME ERROR: <err>
成功
+MIPSACK: <sent>,<acked>,<nack>,<received>
设置命令
OK
AT+MIPSACK=<connect_id>
失败
+CME ERROR: <err>
命令描述
该命令可用于查询指定连接的已发送数据的ACK信息，仅对已建立连接有效。对于UDP连接，不存在服务器
确认接收这一环节，所以查询UDP连接时，所有发送的数据都计算在<nack>中，<acked>固定为0。
4
7
参数描述
1
2
43
<connect_id>整型，客户端连接实例id，范围：0~5。 1
_
<sent>整型，已发送的数据量，单位：字节。
O
<acked>整型，对端已确认接收到的数M据量，单位：字节。
<nack>整型，对端还未确认接收的e数据量，单位：字节。
n
<received>整型，本O地已接收到的数据量，单位：字节。
示例
测试命令
AT+MIPSACK=?
+MIPSACK: (0-5)
OK
查询ACK信息
AT+MIPSACK=0
+MIPSACK: 10,10,0,30
OK
43. MN316/MN316-S/MN326范围：0~4。
MN316A/MN326A/MN319范围：0~3。
49

中移物联网有限公司
Note: MN316/MN316-S/MN326模组暂不支持该命令。
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
50

中移物联网有限公司
3.16. AT+MDNSCFG 设置域名解析服务器
该命令用于配置模组域名解析服务器地址，配置成功将对涉及模组DNS服务请求的全部应用生效，影响范
围包括：TCP/IP、HTTP、MQTT、LWM2M等。
AT+MDNSCFG
语法 响应
成功
+MDNSCFG: "ip",,
+MDNSCFG: "ipv6",,
+MDNSCFG: "priority",(list of supported<priority>s)
测试命令 +MDNSCFG: "cached",(list of supported<cached_mode>s),(list of
supported<cached_period>s)
AT+MDNSCFG=?
+MDNSCFG: "timeout",(list of supported<time>s),(list of supported<retries>s)
OK
失败
+CME ERROR: <err>
成功
仅设置"ip"，查询当前服务器地址：
设置命令（设置ipv4解析服务器
+MIPCFGDNSCFG: "ip",<server_address1>,<server_address2>
地址） OK
4
AT 设置<address1>、<address2>，设置服务器地址：
7
+MDNSCFG="ip"[,<address1 1
OK
>[,<address2>]] 2
失败 1
_
+CME ERRORO: <err>
M
成功
e仅设置"ipv6"，查询当前服务器地址：
n
设置命令（设置ipv6解析服务器
O +MDNSCFG: "ipv6",<server_address1>,<server_address2>
地址） OK
AT 设置<address1>、<address2>，设置服务器地址：
+MDNSCFG="ipv6"[,<addres
OK
s1>[,<address2>]]
失败
+CME ERROR: <err>
成功
设置命令（优先级设置）
仅设置"priority"，查询当前优先级：
AT
+MDNSCFG: "priority",<priority>
+MDNSCFG="priority"[,<prior
OK
ity>]
设置<priority>，设置优先级：
51

中移物联网有限公司
AT+MDNSCFG
OK
失败
+CME ERROR: <err>
成功
仅设置"cached"，查询当前缓存模式：
设置命令（缓存设置）
+MDNSCFG: "cached",<cached_mode>,<cached_period>
OK
AT
+MDNSCFG="cached"[,<cac 设置<cached_mode>、<cached_period>，设置缓存模式：
hed_mode>[,<cached_perio
OK
d>]]
失败
+CME ERROR: <err>
成功
仅设置"timeout"，查询当前超时配置：
设置命令(超时设置) +MDNSCFG: "timeout",<time>,<retries>
OK
AT
设置<time>、<retries>，设置超时参数：
+MDNSCFG="timeout"[,<tim
e>[,<retries>]] OK
4
7
失败
1
2
+CME ERROR: <err>
1
_
命令描述
O
该命令可用于设置和查询DNS相关配M置。模组根据配置会优先选择IPV4或IPV6协议并依次使用<address1>、
<address2>进行解析，直到解析成功或者两种协议均解析失败为止。该命令的参数每次设置后会被保存到NV
e
中，每次上电或从PSM状态n唤醒时，将取NV中的值作为实际值。此外注册网络时还会获取基站下发的DNS服
务器地址，模组将按O照优先级:用户配置(保存到NV中)>网络下发>默认值来选择DNS服务器地址。
参数描述
<address1>字符串，解析服务器首选IP地址。如果配置为空字符串""，将清空NV，并按照如上优先级恢复默
44
认配置，ipv4默认值为"119.29.29.29"，ipv6默认值为"2400:3200::1"。
<address2>字符串，解析服务器备用IP地址。如果配置为空字符串""，将清空NV，并按照如上优先级恢复默
认配置，ipv4默认值为"114.114.114.114"，ipv6默认值为"2001:4860:4860::8888"。
<priority>整型，解析协议优先级。默认值1。
0
IPV4优先
1
44. NB系列：如SIM卡PLMN是46003、46005、460011，ipv4默认值是218.4.4.4。
52

中移物联网有限公司
AT+MDNSCFG
IPV6优先
<cached_mode>整型，DNS缓存模式；使用缓存时，每次解析结果保存到缓存，下次解析时直接使用缓存结
45
果，不再请求解析服务器。默认值0。
0
使用缓存
1
不使用缓存
<cached_period>整型，DNS缓存周期，每一组解析结果单独计算，到期后需要重新请求解析服务器。范围：
1~65535；单位：s。默认3600s。
<time>整型，DNS一次请求等待服务器响应的超时时间。范围：1~60；单位：s。NB模组默认30s，
4G、5G模组默认10s。
<retries>整型，DNS最大请求次数，解析失败或超时后将进行重试请求，范围：1~9。默认值3。
示例
测试命令
AT+MDNSCFG=?
+MDNSCFG: "ip",,
+MDNSCFG: "ipv6",,
+MDNSCFG: "priority",(0,1)
+MDNSCFG: "cached",(0,1),(1-65535) 4
+MDNSCFG: "timeout",(1-60),(1-9) 7
1
OK
2
设置IPV4服务器解析地址 1
_
AT+MDNSCFG="ip","114.114.114.114","8.8.8O.8"
OK
M
查询IPV4服务器解析地址
e
n
AT+MDNSCFG="ip"
O
+MDNSCFG: "ip","114.114.114.114","8.8.8.8"
OK
Note:
MN316/MN316-S/MN316A/MN326A/MN318/MN319/MN326/ML307X模组暂不支持"cached"缓存设
置；
ML307G/ML307H模组暂不支持"cached_period"缓存周期；
ML302A/ML305A/ML307A/ML307G/ML302S/ML307S/ML305U/ML305M/ML307M/ML307N/
ML307R/ML307H/ML307C模组仅支持"priority"优先级设置，暂不保存NV；
MR880A不支持域名服务器地址设置，不支持重试请求，设置无效，超时后直接退出。
45. ML307G默认值为1。
53

中移物联网有限公司
3.17. AT+MDNSGIP 域名解析
该命令用于解析指定域名。
AT+MDNSGIP
语法 响应
成功
+MDNSGIP: ,(list of supported<cid>s)
测试命令
OK
AT+MDNSGIP=?
失败
+CME ERROR: <err>
成功
设置命令
OK
AT
+MDNSGIP=<domainname>[, 失败
<cid>]
+CME ERROR: <err>
URC（DNS响应结果） +MDNSGIP: <domainname>[,<ip>[,<…>]]
命令描述
该命令可用于解析域名，解析结果会以列表的形式一次列出，根据平台不同，列表成员数不同。当DNS请求
4
异常，如超时或DNS解析错误时，将返回原域名，例如: "+MDNSGIP: w7ww.baidu.com"。
1
参数描述 2
1
<domainname>字符串，需要解析的域名，最大长度2_55字节。
O
<cid>整型，PDP上下文索引号，范围与AT+CGDCONT命令支持的范围相同，默认不指定，指定时需保证指定
M
46
cid已激活。
e
<ip>字符串，DNS解析结果n，及域名对应的ip地址。最大支持4个ip返回。
O
示例
测试命令
AT+MDNSGIP=?
+MDNSGIP: ,(0-4)
OK
解析地址（NB系列模组）
AT+MDNSGIP="iot.10086.cn" //AT+MDNSCFG="priority"配置为 IPv4 优先则将获取 IPv4 地址列表
OK
+MDNSGIP: "iot.10086.cn","183.230.40.127"
AT+MDNSGIP="iot.10086.cn" //AT+MDNSCFG="priority"配置为 IPv6 优先则将获取 IPv6 地址列表
46. MN316/MN316-S/MN316A/MN326A/MN318/MN319/MN326/ML307G/MR880A/ML307H：暂不支持<cid>参数，默认不指定。
4G系列默认为1。
54

中移物联网有限公司
AT+MDNSGIP
OK
+MDNSGIP: "iot.10086.cn","2409:8060:8EA:601:0:3:3E:5464"
解析地址（4G系列模组）
AT+MDNSGIP="iot.10086.cn"
OK
+MDNSGIP: "iot.10086.cn","183.230.40.127","2409:8060:8EA:601:0:3:3E:5464"
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
55

中移物联网有限公司
3.18. AT+MPING PING服务器
该命令用于发送ICMP包到服务器。
AT+MPING
语法 响应
成功
+MPING: ,(list of supported<timeout>s),(list of supported<ping_num>s),(list of
测试命令 supported<packet_len>s),(list of supported<cid>s)
OK
AT+MPING=?
失败
+CME ERROR: <err>
设置命令 成功
AT OK
+MPING=<host>[,<timeout>[,
失败
<ping_num>[,<packet_len>[,
<cid>]]]] +CME ERROR: <err>
+MPING: <result>[,<ip>,<packet_len>,<time>,<ttl>]
URC（ping包发送结果）
[+MPING: <result>[,<ip>,<packet_len>,<time>,<ttl>][...]]
URC（<ping_num>大于1时， 4
+MPING: "statistics",<sent>,<lost>,<rtt_min>,<7rtt_max>,<rtt_avg>
响应统计数据）
1
2
命令描述
1
_
该命令用于发送ICMP包到服务器。<ping_num
O
> = 1时，响应结果只有单包响应结果+MPING:
<result>[,<ip>,<packet_len>,<time>,<ttl>]。<ping_num>>1时，响应结果最后还将上报多包的统计数据+MPING:
M
"statistics",<sent>,<lost>,<rtt_min>,<rtt_max>,<rtt_avg>。
e
参数描述 n
O
<host>字符串，服务器地址，字符串形式的域名或IP，最大长度255字节。
<timeout>整型，ping超时时间，范围：1~60；单位：s。默认值10s。
<ping_num>整型，ping次数，范围：1~65535。默认值4。
<packet_len>整型，ping包大小，范围：1~1400；单位：字节。默认值16。
<cid>整型，PDP上下文索引号，范围与AT+CGDCONT命令支持的范围相同，默认不指定，指定时需保证指定
47
cid已激活。
<result>整型，每次ping的结果。
0
47. MN316/MN316-S/MN316A/MN326A/MN319/MN326/ML307H/MR880A暂不支持<cid>参数的配置，默认不指定。
4G系列默认为1。
56

中移物联网有限公司
AT+MPING
成功
1
DNS 解析失败
2
DNS 解析超时
3
响应错误
4
响应超时
5
其他错误
<ip>字符串，服务器IP地址。
<time>整型，ping包响应时长，单位：ms。
<ttl>整型，ping包路由跳转次数。
<sent>整型，发送ping包次数。
<lost>整型，丢包次数。
<rtt_min>整型，最小响应时间，单位：ms。
4
<rtt_max>整型，最大响应时间，单位：ms。 7
1
<rtt_avg>整型，平均响应时间，单位：ms。 2
1
示例 _
O
测试命令
M
AT+MPING=?
e
+MPING: ,(1-60),(1-65535),(1-14n00)
OK O
PING服务器
AT+MPING="ipv6.sjtu.edu.cn" //AT+MDNSCFG="priority"配置为 IPv4 优先则 PING IPv4 地址
OK
+MPING: 0,"202.120.35.204",16,607,235
+MPING: 0,"202.120.35.204",16,334,235
+MPING: 0,"202.120.35.204",16,347,235
+MPING: 0,"202.120.35.204",16,320,235
+MPING: "statistics",4,0,320,607,402
AT+MPING="ipv6.sjtu.edu.cn" //AT+MDNSCFG="priority"配置为 IPv6 优先则 PING IPv6 地址
OK
+MPING: 0,"2001:DA8:8000:1::80",16,548,51
+MPING: 0,"2001:DA8:8000:1::80",16,187,51
+MPING: 0,"2001:DA8:8000:1::80",16,178,51
57

中移物联网有限公司
AT+MPING
+MPING: 0,"2001:DA8:8000:1::80",16,156,51
+MPING: "statistics",4,0,156,548,267
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
58

中移物联网有限公司
3.19. +++ 退出透传模式
该命令用于退出透传模式。
+++
语法 响应
成功
+++
OK
命令描述
该命令仅在透传模式下使用，执行成功后退出透传并返回OK。执行+++请与数据发送保持一定的间隔，且独
立输入该命令，若+++命令执行失败，则可能作为数据被发送出去。
Note: MN316/MN316-S/MN316A/MN326A/MN318/MN319/MN326暂不支持该命令。ML307X执行+++请
与数据发送保持1秒的间隔。
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
59

中移物联网有限公司
3.20. +MIPURC TCP/IP URC上报信息
该命令为TCP、UDP异步上报信息。
+MIPURC
语法 响应
连接异常提示 +MIPURC: "disconn",<connect_id>,<connect_state>
普通模式
+MIPURC: "rtcp",<connect_id>,<recv_length>,<data>
接收TCP数据提示
流缓存模式
+MIPURC: "rtcp",<connect_id>,<recv_length>,<total_length>
48
普通模式
+MIPURC: "rudp",<connect_id>,<recv_length>,<data>
接收UDP数据提示
包缓存模式
+MIPURC: "rudp",<connect_id>,<recv_count>
UDP空口回传序列号提示 +MIPURC: "seq",<connect_id>,<seq>,<result>
TCP接收ACK包提示 +MIPURC: "ack",<connect_id>,<length>
4
7
接收数据溢出提示 +MIPURC: "drop",<connect_id>,<drop_length>
1
2
TCP服务器接受客户端新连接 +MIPURC: "itcp",<sid>,<conn1ect_id>,<client_a
49 _
提示 ddr>,<client_port>
O
51
普通模式
M
e+MIPURC: "iudp",<sid>,<connect_id>,<client_
naddr>,<client_port>,<recv_length>,<data>
UDP服务器接收客户端数据提
示 50 O 包缓存模式 52
+MIPURC: "iudp",<sid>,<connect_id>,<client_
addr>,<client_port>,<recv_count>
命令描述
该命令可为TCP、UDP连接的异步上报信息，无控制开关。
48. MR880A：URC打印长度限制为8192字节，普通模式下UDP下行时，URC打印长度超过8192时将丢包，建议下行包长度小于
8165，超过时建议使用包缓存模式进行接收。
49. 目前仅适用于MR880A。
50. 目前仅适用于MR880A。
51. MR880A：URC打印长度限制为8192字节，普通模式下UDP下行时，URC打印长度超过8192时将丢包，建议客户端发送长度小于
8114，超过时建议使用包缓存模式进行接收。
52. 可用AT+MIPRDU命令读取。
60

中移物联网有限公司
+MIPURC
参数描述
53
<connect_id>整型，客户端连接实例id，范围：0~5。
<connect_state>整型，连接异常状态。
若AT+MIPCFG="autofree"[,<connect_id>[,<free_mode>]]命令中<free_mode>参数为1，模组上报该URC，需要
执行AT+MIPCLOSE=<connect_id>[,<mode>]关闭连接和释放资源；若<free_mode>参数为0，模组上报该
URC，将自动断开连接并释放资源，无需再执行AT+MIPCLOSE=<connect_id>[,<mode>]。
1
服务器关闭连接。
2
连接异常
3
PDP去激活
<recv_length>整型，接收到的数据长度。
<data>字符串，接收到的数据。
<total_length>整型，当前已有缓存总大小，单位：字节。NB模组最大缓存12包，Cat1最大缓存256包，当缓
存包数达到缓存上限值后，无论缓存是否填满，都无法继续接受新的数据包，直到缓存数据被读取后，方可
54
继续接收。
4
55
<recv_count>整型，当前已有缓存包总数量。NB模组最大缓存12包，Cat1最大缓存256包。
7
1
<seq>整型，UDP空口回传序列号，范围：1~255。
2
1
56
<length>整型，服务器确认收到的数据长度，范围：1_~1460；单位：字节。
O
<drop_length>整型，溢出数据长度，溢出的数据会被抛弃。
M
<result>整型，UDP空口回传结果，1表示失败，0表示成功。
e
n 57
<sid>服务器实例id，用于标识操作的服务器实例。
O
58
<client_addr>字符串，客户端地址。
59
<client_port>整型，客户端端口。
示例
53. MN316/MN316-S/MN326模组范围0~4。
MN316A/MN326A/MN319范围0~3。
MR880A范围0~9。
54. ML305M/ML307M缓存包数为128。
MR880A缓存包数为256。
55. ML305M/ML307M缓存包数为128。
56. MN316/MN316-S/MN316A/MN326A/MN326模组暂不支持。
57. 目前仅适用于MR880A。
58. 目前仅适用于MR880A。
59. 目前仅适用于MR880A。
61

中移物联网有限公司
+MIPURC
TCP接收数据上报
+MIPURC: "rtcp",0,10,0123456789
OK
TCP接收缓存数据上报
+MIPURC: "rtcp",0,10,10
OK
接收数据溢出
+MIPURC: "drop",0,10
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
62

中移物联网有限公司
3.21. AT+MNTP 网络时间同步
该命令用于同步网络时间。
AT+MNTP
语法 响应
成功
+MNTP: ,(list of supported<port>s),(list of supported<sync>s),(list of supported<timeout>s)
测试命令
OK
AT+MNTP=?
失败
+CME ERROR: <err>
成功
设置命令
OK
AT
+MNTP[=<server>[,<port>,[< 失败
sync>,[<timeout>]]]]
+CME ERROR: <err>
URC +MNTP: <result>[,<time>]
命令描述
该命令可用于获取网络时间，根据指定的NTP服务器查询NTP时间，获取到的时区为1/4制式时区。
4
7
参数描述
1
2
<server>字符串，NTP服务器IP地址或域名，默认服务器"n
1
tp1.aliyun.com"，最大长度255字节。
_
<port>整型，NTP服务器端口，范围：0~6553
O
5。默认值123。
<sync>整型，是否更新本地RTC计时M器的时间。默认值1。
0 e
n
不更新 O
1
更新
60
<timeout>整型，请求超时，范围：1~300；单位：s。默认值20s。
<time>字符串，NTP时间获取结果。格式为"yy/MM/dd,hh:mm:ss±zz”，格式与AT+CCLK?返回结果相同，其
中zz代表时区的4倍。
<result>整型，返回结果码。
0
成功
3
60. ML302A/ML305A/ML307A/ML307G/ML302S/ML307S/ML307R/ML307H/ML307C默认值30s。
63

中移物联网有限公司
AT+MNTP
时间同步失败
示例
设置命令（NB系列模组）
AT+MNTP="cn.ntp.org.cn",,0
OK
+MNTP: 0,"19/02/21,07:29:20+32"
AT+MNTP="cn.ntp.org.cn",123,1,30 //同步网络时间至本地
OK
+MNTP: 0,"19/02/21,07:35:02+32"
AT+CCLK?
+CCLK: 19/02/21,07:35:03+32 //本地时间同步成功
OK
设置命令（4G、5G系列模组）
AT+MNTP="cn.ntp.org.cn",,0
OK
+MNTP: 0,"19/02/21,07:29:20+32"
AT+MNTP="cn.ntp.org.cn",123,1,30 //同步网络时间至本地
OK
+MNTP: 0,"19/02/21,07:35:02+32"
AT+CCLK?
+CCLK: "19/02/21,07:35:03+32" //本地时间同步成功
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
64

中移物联网有限公司
4. 示例
本章主要介绍TCP、UDP、DNS、PING命令在相关业务场景中的使用流程。
4.1. TCP示例
TCP连接，收发数据示例。
操作步骤
AT+MIPOPEN=0,"TCP","120.27.12.119",2040 //建立TCP连接
OK
+MIPOPEN: 0,0 //连接成功
AT+MIPSTATE=0 //查询指定连接的连接状态
+MIPSTATE: 0,"TCP","120.27.12.119",2040,"CONNECTED"
OK
AT+MIPSEND=0,11,"12345678900" //发送数据
+MIPSEND: 0,11 //发送成功
OK
+MIPURC: "rtcp",0,10,ABCDE12345 //收到10个字节的数据
AT+MIPCLOSE=0 //断开连接
OK
+MIPCLOSE: 0 //连接断开成功
4
7
Note: MN316/MN316-S/MN326模组暂不支持AT+MIPSACK命令1。
2
1
_
O
M
e
n
O
65

中移物联网有限公司
4.2. UDP示例
UDP建立创建、数据收发、关闭流程。
操作步骤
AT+MIPOPEN=0,"UDP","120.27.12.119",2040 //创建UDP
OK
+MIPOPEN: 0,0
AT+MIPSTATE=0 //查询指定连接的连接状态
+MIPSTATE: 0,"UDP","120.27.12.119",2040,"CONNECTED"
OK
AT+MIPSEND=0,11,"12345678900",0,1 //发送数据，序列号为1。
+MIPSEND: 0,11
OK
+MIPURC: "seq",0,1,0 //空口回传，数据已发送至基站。
+MIPURC: "rudp",0,10,ABCDE12345 //收到10个字节的数据
AT+MIPCLOSE=0 //关闭UDP
OK
+MIPCLOSE: 0 //关闭成功
Note:
MN316/MN316-S/MN326模组暂不支持AT+MIPSACK命令；
ML302A/ML305A/ML307A/ML307G/MR880A/ML302S/ML307S/ML305U/ML305M/ML307M/
ML307N/ML307R/ML307H/ML307C/ML307X模组不支持空口回传功能。
4
7
1
2
4.3. 透传模式
1
_
TCP连接的透传模式，UDP的透传模式流程O与TCP相同。
M
操作步骤
e
AT+MIPOPEN=0,"TCP","120n.27.12.119",2040 //建立TCP连接
O
OK
+MIPOPEN: 0,0 //连接成功
AT+MIPMODE=0,1 //切换透传模式
OK
CONNECT
12345678900 //等待200ms自动发送数据
ABCDE12345 //收到10个字节的数据
+++ //退出透传
AT+MIPSACK=0 //查询指定连接的数据收发情况
+MIPSACK: 22,22,0,10
AT+MIPCLOSE=0 //断开连接
OK
+MIPCLOSE: 0 //连接断开成功
Note: MN316/MN316-S/MN316A/MN326A/MN318/MN319/MN326模组暂不支持透传功能。
66

中移物联网有限公司
4.4. 缓存模式
TCP、UDP缓存模式下的数据收发流程。
TCP缓存模式
建立连接，切换为缓存模式。
AT+MIPOPEN=0,"TCP","120.27.12.119",2040 //建立TCP连接
OK
+MIPOPEN: 0,0 //连接成功
AT+MIPMODE=0,2 //切换为缓存模式(TCP为流缓存)
OK
建立缓存模式的连接
AT+MIPOPEN=0,"TCP","120.27.12.119",2040,10,2 //建立TCP连接
OK
+MIPOPEN: 0,0 //连接成功
数据收发
AT+MIPSEND=0,11,"12345678900"
+MIPSEND: 0,11 //发送数据
OK
+MIPURC: "rtcp",0,10,10 //收到10个字节的数据，总共缓存10字节。
+MIPURC: "rtcp",0,10,20 //收到10个字节的数据，总共缓存20字节。
4
AT+MIPRD=0 //查询缓存信息
7
+MIPRD: 0,20
1
OK
2
AT+MIPRD=0,10 //读取10字节的缓存数据 1
+MIPRD: 0,10,10,ABCDE12345 _
OK O
AT+MIPRD=0,0 //读取剩下的所有缓存数据
M
+MIPRD: 0,0,10,ABCDE12345
OK e
AT+MIPCLOSE=0 //断开连接n
OK O
+MIPCLOSE: 0 //连接断开成功
UDP缓存模式
建立连接，切换为缓存模式。
AT+MIPOPEN=0,"UDP","120.27.12.119",2040 //建立UDP连接
OK
+MIPOPEN: 0,0
AT+MIPMODE=0,3 //切换为缓存模式(UDP为包缓存)
OK
建立缓存模式的连接
67

中移物联网有限公司
AT+MIPOPEN=0,"UDP","120.27.12.119",2040,10,3 //建立UDP连接
OK
数据收发
AT+MIPSEND=0,11,"12345678900"
+MIPSEND: 0,11 //发送数据
OK
+MIPURC: "rudp",0,1 //收到1数据包，总共缓存1个包。
+MIPURC: "rudp",0,2 //收到1数据包，总共缓存2个包。
AT+MIPRD=0 //查询缓存信息
+MIPRD: 0,2
OK
AT+MIPRD=0,1 //读取1个缓存数据包
+MIPRD: 0,1,10,ABCDE12345
OK
AT+MIPRD=0,0 //读取剩下的所有缓存数据
+MIPRD: 0,0,10,ABCDE12345
OK
AT+MIPCLOSE=0 //断开连接
OK
+MIPCLOSE: 0 //连接断开成功
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
68

中移物联网有限公司
4.5. server模式
打开服务器监听模式以及数据收发流程。
TCP服务器
打开TCP监听，设置为IPV6协议。
AT+MIPLISTEN=0,TCP,123,IPV6 //打开TCP监听，IP类型和默认数据接收模式可根据实际需求设置
OK
+MIPLISTEN: 0,0
接受新连接请求，建立连接。
+MIPURC: "itcp",0,0,"::1",38293 //新连接<connect_id>为0，可使用第0路连接进行数据收发和相关模式设置
数据收发。
+MIPURC: "rtcp",0,13,test123456789 //接收到数据
AT+MIPSEND=0,4,"test" //发送数据
+MIPSEND: 0,4
OK
UDP服务器
打开UDP监听，设置为IPV6协议。
4
AT+MIPLISTEN=1,UDP,125,IPV6 //打开UDP监听，IP类型和默认数据接收模式可根7据实际需求设置
1
OK
2
+MIPLISTEN: 1,0
1
_
数据收发。 O
M
+MIPURC: "iudp",1,0,"::1",14,4,TEST //初次接收数据，上报<connect_id>为0，则当前UDP服务器连接均使用第0路连接进行数据收发
和相关模式设置
e
+MIPURC: "iudp",1,0,"::1",15,5,TEnST1 //接收到另外一个客户端发来的数据，依然是第0路上报
O
AT+MIPSENDTO=0,"::1",14,,"123456789" //向对端发送数据
+MIPSENDTO: 0,9
OK
AT+MIPMODE=0,3 //切换缓存模式
OK
+MIPURC: "iudp",1,0,"::1",3840,1
+MIPURC: "iudp",1,0,"::1",3840,2 //接收到数据
AT+MIPRDU=0 //查询缓存包个数
+MIPRDU: 0,2
OK
AT+MIPRDU=0,1 //读取缓存
+MIPRDU: 0,"::1",3840,1,9,123456789
OK
AT+MIPRDU=0,1
69

中移物联网有限公司
+MIPRDU: 0,"::1",3840,0,9,123456789
OK
Note: 目前仅适用于MR880A。
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
70

中移物联网有限公司
4.6. PING示例
ping命令流程示例。
操作步骤
AT+MPING="ipv6.sjtu.edu.cn" //AT+MDNSCFG="priority"配置为IPv6优先则PING IPv6地址
OK
+MPING: 0,"2001:DA8:8000:1::80",16,548,51
+MPING: 0,"2001:DA8:8000:1::80",16,187,51
+MPING: 0,"2001:DA8:8000:1::80",16,178,51
+MPING: 0,"2001:DA8:8000:1::80",16,156,51
+MPING: "statistics",4,0,156,548,267
AT+MPING="ipv6.sjtu.edu.cn",30,1 //只PING一次
OK
+MPING: 0,"202.120.35.204",16,496,233
AT+MPING="ipv6.sjtu.edu.cn"
OK
+MPING: 3 //PING超时，退出PING流程。
4.7. DNS示例
域名解析命令使用流程。
操作步骤
AT+MDNSCFG="ip","114.114.114.114","8.8.8.8" //设置IPV4服务器
4
OK 7
AT+MDNSCFG="ip" //查询 1
+MIPCFG: "ip","114.114.114.114","8.8.8.8" 2
AT+MDNSGIP="www.baidu.com" //DNS解析 1
_
OK
O
+MDNSGIP: "www.baidu.com","183.232.231.172"
M
e
n
O
71

中移物联网有限公司
5. 错误码
本章为TCP/UDP命令相关的错误码。
错误码 说明
550 TCP/IP 未知错误
551 TCP/IP 未被使用
552 TCP/IP 已被使用
553 TCP/IP 未连接
554 SOCKET 创建失败
555 SOCKET 绑定失败
556 SOCKET 监听失败
557 SOCKET 连接被拒绝
558 SOCKET 连接超时
559 SOCKET 连接失败（其他异常）
560 SOCKET 写入异常 4
7
561 SOCKET 读取异常 1
2
562 SOCKET 接受异常 1
_
570 PDP 未激活O
M
571 PDP 激活失败
e
572 PDP 去激活失败
n
O
575 APN 未配置
576 端口忙碌
577 不支持的IPV4/IPV6
580 DNS解析失败或错误的IP格式
581 DNS忙碌
582 PING忙碌
72