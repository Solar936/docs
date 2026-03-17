4
LwM2M用户手册7
1
2
1
_
O
M
e
n
版本：V5.2O.2
发布日期：2025/3/25

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
移除MN316无效信息；
新增MN318相关内容；
AT+MIPLNOTIFY 新增可选参数<contentType>和参数注意事项；
+MIPLWRITE的注意事项新增下发浮点数的大小限制；
AT+MIPLCFG 新增华为云物联网平台配置；
V2.0.0
AT+MIPLCREATEEX 新增可选参数<PSKID> 和<psk_encode> ；
DTLS相关描述新增支持CTWing、华为云物联网平台；
新增基于DTLS加密的CTWing平台接入示例；
新增华为云物联网平台接入示例；
新增基于DTLS加密的华为云物联网平台接入示例。
新增MN319相关内容；
V3.0.0 更正手册适用范围;
更新“AT+MIPLCREATEEX 创建LwM2M设备”备注内容。
4
7
新增ML302A/ML305A/ML307A相关内容；
1
更新“引言”； 2
V4.0.0
更新“AT+MIPLOPEN 登录请1求”备注内容；
_
更新“华为云物联网平台接入示例”中数据收发示例。
O
更新MN3M19相关内容；
V4.1.0
更新“AT+MIPLNMI 消息上报控制”备注内容。
e
n
新增ML307R相关内容；
O
新增MN316A相关内容；
V4.2.0
更新“AT+MIPLNOTIFY 上报数据”中<contentType>参数描述，新增脚注；
更新“AT+MIPLNMI 消息上报控制”备注内容。
新增MN326相关内容；
更新MN319适用范围，MN319支持“+MIPLNSMI 数据发送上报”；
V4.3.0
更新“+MIPLNSMI 数据发送上报”响应；
新增“+MIPLNMI 数据接收上报”。
更新“AT+MIPLNOTIFY 上报数据”中<type> 参数说明；
V4.4.0 更新“AT+MIPLREADRSP 读操作请求响应”中<type> 参数说明；
更新“+MIPLWRITE 写操作请求消息”注意事项。
更新“+MIPLEVENT 状态事件”，新增FOTA事件描述；
V5.0.0
扩展AT命令章节新增“AT+MIPLFOTACFG 配置FOTA可选参数”；

中移物联网有限公司
版本 描述
示例章节新增“LwM2M平台FOTA升级示例”。
新增ML307M相关内容；
V5.1.0
新增ML307R-BL/ML307R-MC/ML307R-ML相关内容。
V5.1.1 更新手册适用范围，新增ML307M-DA相关内容。
新增MR880A相关内容；
更新AT+MIPLADDOBJ 添加OMA Object (on page 17)，添加示例；
更新AT+MIPLNOTIFY 上报数据 (on page 24)中<ackid>参数说明；
更新AT+MIPLREADRSP 读操作请求响应 (on page 28)中<flag>参数说明；
更新+MIPLWRITE 写操作请求消息 (on page 31)中<ref> 参数说明；
V5.2.0
更新AT+MIPLPARAMETERRSP 设置策略参数操作请求响应 (on page 36)中
示例；
更新AT+MIPLOBSERVERSP 观测请求操作响应 (on page 38)中<result> 参数
说明及示例；
更新OneNET平台接入示例 (on page 59)中数据收发的示例代码。
新增MN326A相关内容；
V5.2.1
新增ML307C-DL-CN相关内容。
V5.2.2 新增ML307N-DC/ML307N-DL相关内容。
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
1. 引言.....................................................................................................................................................................................................................................9
1.1. 适用型号...............................................................................................................................................................................................................9
2. AT命令概述....................................................................................................................................................................................................................10
2.1. AT命令语法.......................................................................................................................................................................................................11
2.2. AT命令响应.......................................................................................................................................................................................................12
3. LwM2M通用AT命令...................................................................................................................................................................................................13
3.1. AT+MIPLCREATE 创建LwM2M设备........................................................................................................................................................14
3.2. AT+MIPLDELETE 删除LwM2M设备........................................................................................................................................................16
3.3. AT+MIPLADDOBJ 添加OMA Object.......................................................................................................................................................17
3.4. AT+MIPLDELOBJ 删除OMA Object........................................................................................................................................................18
3.5. AT+MIPLDISCOVERRSP 资源列表设置..................................................................................................................................................19
3.6. AT+MIPLOPEN 登录请求.............................................................................................................................................................................20
3.7. AT+MIPLUPDATE 更新请求........................................................................................................................................................................22
3.8. AT+MIPLCLOSE 退出请求...........................................................................................................................................................................23
3.9. AT+MIPLNOTIFY 上报数据.........................................................................................................................................................................24
3.10. +MIPLREAD 读操作请求消息..................................................................................................................................................................27
4
3.11. AT+MIPLREADRSP 读操作请求响应.....................................................................7................................................................................28
1
3.12. +MIPLWRITE 写操作请求消息................................................................................................................................................................31
2
3.13. AT+MIPLWRITERSP 写操作请求响应...................................................................................................................................................32
1
3.14. +MIPLEXECUTE 命令操作请求消息............................._........................................................................................................................33
3.15. AT+MIPLEXECUTERSP 命令操作请求响应O........................................................................................................................................34
3.16. +MIPLPARAMETER 设置策略参M数请求消息......................................................................................................................................35
3.17. AT+MIPLPARAMETERRSP 设置策略参数操作请求响应................................................................................................................36
e
3.18. +MIPLOBSERVE 观测请求消息...............................................................................................................................................................37
n
3.19. AT+MIPLOBSOERVERSP 观测请求操作响应........................................................................................................................................38
3.20. +MIPLEVENT 状态事件.............................................................................................................................................................................39
4. LwM2M扩展AT命令...................................................................................................................................................................................................41
4.1. AT+MIPLCFG 配置可选参数.......................................................................................................................................................................42
4.2. AT+MIPLNMI 消息上报控制.......................................................................................................................................................................44
4.3. AT+MIPLMGR 获取缓存请求......................................................................................................................................................................46
4.4. AT+MIPLQMGR 查询缓存数据统计值.....................................................................................................................................................48
4.5. AT+MIPLUPDATESET 自动更新设置.......................................................................................................................................................49
4.6. AT+MIPLCREATEEX 创建LwM2M设备..................................................................................................................................................50
4.7. AT+MIPLDEVINFO 查询设备实例参数....................................................................................................................................................52
4.8. AT+MIPLDTLSNAT DTLS自动重协商时间间隔设置..........................................................................................................................53
4.9. AT+MIPLLTRDP 查询剩余登录时长.........................................................................................................................................................54
4.10. AT+MIPLFOTACFG 配置FOTA可选参数..............................................................................................................................................55
4.11. +MIPLNSMI 数据发送上报.......................................................................................................................................................................56
4.12. +MIPLNMI 数据接收上报..........................................................................................................................................................................57

中移物联网有限公司
5. 示例..................................................................................................................................................................................................................................58
5.1. OneNET平台接入示例..................................................................................................................................................................................59
5.2. 基于DTLS加密的OneNET平台接入示例................................................................................................................................................61
5.3. CTWing平台接入示例...................................................................................................................................................................................63
5.4. 基于DTLS加密的CTWing平台接入示例.................................................................................................................................................65
5.5. 雁飞平台接入示例..........................................................................................................................................................................................67
5.6. 华为云物联网平台接入示例........................................................................................................................................................................69
5.7. 基于DTLS加密的华为云物联网平台接入示例......................................................................................................................................71
5.8. LwM2M平台FOTA升级示例........................................................................................................................................................................72
6. 错误码..............................................................................................................................................................................................................................74
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
1. 引言
本文档为中移物联网定义的LwM2M相关功能的AT命令标准文档。 文档对LwM2M相关命令和操作流程进行
了详细说明，以供客户参考，如有未尽细节，请咨询中移物联网技术支持。
1.1. 适用型号
Table 1. 适用模组
模组系列 模组子型号
MN316 MN316-DBRS/MN316-DLVS
MN316-S MN316-S-DLVS
MN316A MN316A-D/MN316A-DC
MN318 MN318-BX/MN318-LC/MN318-LX
MN319 MN319-DL
MN326 MN326-X
MN326A MN326A-DG
4
ML302A ML302A-DCLM/ML302A-DSLM/ML302A-GCLM/ML302A-GSLM
7
1
ML305A ML305A-DC/ML305A-DS/ML305A-DL
2
1
ML307A-DCLN/ML307A-DSLN/ML307A-GCLN/ML307A-GSLN/ML30
ML307A _
7A-DL O
ML307R ML3M07R-DC/ML307R-DL/ML307R-BL/ML307R-MC/ML307R-ML
e
MR880A MR880A/MR880A Mini-PCIe/MR880A M.2
n
O
ML307M ML307M-DL/ML307M-DA
ML307N ML307N-DC/ML307N-DL
ML307C ML307C-DL-CN
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
设置命令 AT+<CMD>=<p1>[,<p2[,<p3>[…]]] 设置参数值
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
3. LwM2M通用AT命令
本章详细描述了基于LwM2M协议连接LwM2M平台相关的AT命令和命令格式。
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
3.1. AT+MIPLCREATE 创建LwM2M设备
该命令用于创建一个LwM2M设备实例。
AT+MIPLCREATE
1
语法 响应
成功
设置命令
+MIPLCREATE: <ref>
AT
OK
+MIPLCREATE=<totalsize>,<
config>,<index>,<currentsize 错误
>,<flag>
+CIS ERROR: <err>
成功
+MIPLCREATE: <ref>
执行命令
OK
AT+MIPLCREATE
错误
+CIS ERROR: <err>
命令描述
该设置命令可由工具直接生成；执行命令配置默认以bootstrap方式连接至OneNET重庆主平台。
4
参数描述
7
1
<totalsize>整型，指示<config>部分总数据长度，按照ASCII计数。
2
1
<config>十六进制字符串，具体的设备配置数据，满足配置结构体规范。
_
O
<index>整型，配置数据分片参数。
M
<currentsize>整型，当前分片部分数据长度。
e
<flag>整型，配置数据流结束n符。
O
<ref>整型，LwM2M设备实例ID。
示例
2
执行命令
1. MR880A返回结果为异步上报，具体如下。
成功：
OK
+MIPLCREATE: <ref>
DNS失败：
+MIPLCREATE: FAIL
2. MR880A返回为结果为异步上报。
14

中移物联网有限公司
AT+MIPLCREATE
AT+MIPLCREATE
+MIPLCREATE: 0
OK
Important: MR880A执行AT+MIPLCREATE命令，返回结果为异步上报，即先返回OK，再返回+MIPLCREATE:
0。
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
15

中移物联网有限公司
3.2. AT+MIPLDELETE 删除LwM2M设备
该命令用于删除一个LwM2M设备实例。
AT+MIPLDELETE
语法 响应
成功
设置命令 OK
AT+MIPLDELETE=<ref> 错误
+CIS ERROR: <err>
参数描述
<ref>整型，LwM2M设备实例ID。
示例
设置命令
AT+MIPLDELETE=0
OK
Note: 若设备登录平台成功，使用该命令将直接关闭所使用的socket，并删除所有object、instance、
resource资源。 4
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
16

中移物联网有限公司
3.3. AT+MIPLADDOBJ 添加OMA Object
该命令用于为指定设备示例添加一个object及其所属的instance。
AT+MIPLADDOBJ
语法 响应
设置命令（通用报头设置） 成功
AT OK
+MIPLADDOBJ=<ref>,<objid
错误
>,<inscount>,<bitmap>,<atts
>,<acts> +CIS ERROR: <err>
参数描述
<ref>整型，LwM2M设备实例ID。
<objid>uint16类型，Object ID。
<inscount>整型，实例个数。
<bitmap>字符串，实例位图，字符串格式，每一个字符表示为一个实例，其中1表示可用，0表示不可用。例
如当前添加的object有5个实例，其中0，3可用，则实例位图为10010。
<atts>整型，属性个数，默认0。
<acts>整型，操作个数，默认0。 4
7
示例 1
2
OneNET平台 1
_
AT+MIPLADDOBJ=0,3200,1,"1",0,1 //订阅Object组 O
OK
M
CTWing平台
e
n
AT+MIPLADDOBJ=0,19,2,"11",0,0 //添加object 19
O
OK
雁飞平台
AT+MIPLADDOBJ=0,3300,1,"1",0,1 //订阅Object组
OK
Note: FOTA功能会默认使用3/0，4/0，5/0资源的读、写、执行操作，用户应避免使用此部分对象资源的相
关功能。
17

中移物联网有限公司
3.4. AT+MIPLDELOBJ 删除OMA Object
该命令用于删除指定object及其所属的instance和resource。
AT+MIPLDELOBJ
语法 响应
成功
设置命令（通用报头设置）
OK
AT
+MIPLDELOBJ=<ref>,<obj 错误
id>
+CIS ERROR: <err>
参数描述
<ref>整型，LwM2M设备实例ID。
<objid>uint16类型，Object ID。
示例
设置命令
AT+MIPLDELOBJ=0,3200
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
18

中移物联网有限公司
3.5. AT+MIPLDISCOVERRSP 资源列表设置
该命令用于设置指定object的所需资源列表。
AT+MIPLDISCOVERRSP
语法 响应
成功
设置命令
+MIPLCREATE: <ref>
AT
OK
+MIPLDISCOVERRSP=<ref>,<
objid>,<result>,<length>,<da 错误
ta>
+CIS ERROR: <err>
参数描述
<ref>整型，LwM2M设备实例ID。
<objid>uint16类型，Object ID。
<result>整型，保留，应设置为1。
<length>整型，data长度。
<data>字符串，object的资源列表多个属性之间使用分号“;”隔开。
示例
4
7
设置命令
1
2
AT+MIPLDISCOVERRSP=0,3200,1,14,”5500;5501;5750”
1
OK
_
O
M
Note:
e
使用该命令后，当n平台下发+MIPLDISCOVER消息向模组获取资源列表时，模组自动反馈相关资源列
O
表，无需MCU处理；
该命令在登录前使用，在MIPLCLOSE退出后需使用该命令重新指定资源列表；
此命令功能可由多条AT+MIPLNOTIFY命令替代。
19

中移物联网有限公司
3.6. AT+MIPLOPEN 登录请求
该命令用于向平台发起登录请求或查询是否登录平台。
AT+MIPLOPEN
语法 响应
成功
设置命令
OK
AT +MIPLEVENT: <ref>,<event>
+MIPLOPEN=<ref>,<lifetime>
错误
[,<timeout>]
+CIS ERROR: <err>
成功
OK
读取命令
+MIPLEVENT: <ref>,<event>
AT+MIPLOPEN?
错误
+CIS ERROR: <err>
参数描述
<ref>整型，LwM2M设备实例ID。
4
<lifetime>整型，本次登录平台的生命周期，单位：s。
7
1
<timeout>整型，可选参数，登录的超时时长，单位：s，默认30。
2
1
<event>整型，事件ID，参考+MIPLEVENT。
_
O
示例
M
登录成功
e
AT+MIPLOPEN=0,300,30 n
OK O
+MIPLEVENT: 0,6
登录失败
AT+MIPLOPEN=0,300,30
OK
+MIPLEVENT: 0,7
登录状态查询
AT+MIPLOPEN?
OK
+MIPLEVENT: 0,6
20

中移物联网有限公司
Note:
返回的OK仅代表命令执行成功；
登录结果为异步上报消息+MIPLEVENT: <ref>,<event>；
在平台拒绝登录或登录超时的情景下会上报+MIPLEVENT: 0,7；
若在bootstrap流程中登录失败，会上报+MIPLEVENT: 0,3；
使用读取命令查询是否登录上平台时亦需等待异步上报结果+MIPLEVENT: 0,6；
若查询时未登录上平台，直接返回+CIS ERROR: 602；
查询命令仅OneNET平台支持。
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
21

中移物联网有限公司
3.7. AT+MIPLUPDATE 更新请求
该命令用于向平台发起更新请求。
AT+MIPLUPDATE
语法 响应
成功
设置命令
OK
AT +MIPLEVENT: <ref>,<event>
+MIPLUPDATE=<ref>,<lifetim
错误
e>,<withObjectFlag>
+CIS ERROR: <err>
参数描述
<ref>整型，LwM2M设备实例ID。
<lifetime>整型，本次更新的生命周期，单位：s。
<withObjectFlag>整型，是否需要同时更新登录的Object对象，一般置0即可。
示例
设置命令
AT+MIPLUPDATE=0,0,0
4
OK 7
+MIPLEVENT: 0,11 1
2
1
_
Note:
O
返回的OK仅代表命令发起M请求成功；
更新成功结果为异步上
e
报消息+MIPLEVENT: 0,11；
<lifetime>值小于1n0时，<lifetime>将默认被设置为86400s，即一天。
O
22

中移物联网有限公司
3.8. AT+MIPLCLOSE 退出请求
该命令用于向平台发起退出登录请求。
AT+MIPLCLOSE
语法 响应
成功
OK
设置命令
+MIPLEVENT: <ref>,<event>
AT+MIPLCLOSE=<ref>
错误
+CIS ERROR: <err>
参数描述
<ref>整型，LwM2M设备实例ID。
<event>整型，参考+MIPLEVENT内容。
示例
设置命令
AT+MIPLCLOSE=0
OK
+MIPLEVENT: 0,15
4
7
1
Note: 2
1
_
返回的OK仅代表发起退出请求流程成功；
O
退出成功结果为异步上报消息+MIPLEVENT: 0,15；
M
退出平台事件仅上报成功；如未上报成功则代表未能退出成功；如有需要可直接调用MIPLDELETE
命令删除整个设备实例； e
n
MIPLCLOSE执行成功后SDK会删除本次登录相关的resource；此时如未使用AT+MIPLDELETE删除
O
设备且有再次登录的需求，应先使用MIPLDISCOVERRSP重新申请资源。
23

中移物联网有限公司
3.9. AT+MIPLNOTIFY 上报数据
该命令用于向平台上报指定资源的数据。
AT+MIPLNOTIFY
语法 响应
设置命令
成功
AT
OK
+MIPLNOTIFY=<ref>,<mid>,<
[+MIPLEVENT: <ref>,<event>,<ackid>]
objid>,<insid>,<resid>,<type
>,<len>,<value>,<index>,<fla 错误
g>[,<ackid>[,<contentType
+CIS ERROR: <err>
>]]
参数描述
<ref>整型，LwM2M设备实例ID。
<mid>uint16类型，该resource所属的instance observe操作时下发的mid。上报时会自动搜索当前可用资源，
故置0即可。
<objid>uint16类型，Object ID。
<insid>uint16类型，Instance ID。
4
<resid>uint16类型，Resource ID。
7
1
<type>整型，指定上报资源的数据类型。 2
1
1 _
O
String
M
2
Opaque 3 e
n
3
O
Integer
4
Float
5
Bool
6
4
Hex str
<len>整型，value的值的长度。模组会依据当前的值的范围上报报文做优化算法，无需指定<len>。
<value>字符串，上报的数据值。<value>参数为string类型时必须两端加双引号，其他类型可选。
3. Opaque类型2为透传类型，通过AT命令输入16进制字符串将被模组转换为ASCII码后上传至平台。
4. Hex_str类型6为16进制字符串，输入的16进制将被模组转换为ASCII码后以string类型1上传至平台。
24

中移物联网有限公司
AT+MIPLNOTIFY
<index>整型，命令序号。若某个notify操作需要N条报文组合为一完整命令，则index从N-1至0降序编号，当
index编号为0时表示本次notify命令结束。直接置0亦可。
<flag>整型，消息标识。<flag>参数不为0时，当前报文暂不发送；当<flag>为0时，会将同一object下同一
instance下的未发送resource一起组包上报。<flag>为0x200或0x400时为release indicator模式上报。此时
报文为将<flag>置0送并附带释放连接的功能。DTLS模式下<flag> 不支持0x200、0x400，命令设置该值将
5
默认<flag> 为0进行发送。
1
第一条报文
2
中间报文
0
最后一条报文
0x200
发送后模组进入IDLE态
0x400
接收到应答报文后模组进入IDLE态
<ackid>uint16类型，可选参数。若被设置，平台在接收到该上报报文后将下发ACK报文。模组收到ACK报文
后向MCU上报以下消息+MIPLEVENT: <ref>,<event_id>,<ackid>。<ackid>参数被设置的情况下，平台侧收
到数据会执行ACK回复操作； 如果缺省，平台侧不会有ACK回复消息。<ack4id>参数仅在该报文为发送报文
时(即<flag>为0)有效。 7
1
<contentType>整型，可选参数。设置上报报文的编码格式，未2设置时采用平台默认的格式。<contentType>
1
6
参数设置后，该资源后续应保持相同的contentType类型。
_
O
0
M
TEXT格式
1 e
n
LINK格式 O
2
OPAQUE格式
3
TLV格式
4
JSON格式
示例
5. MN319/MR880A不支持<flag>设置0x200、0x400，对以上值<flag>当作0处理。
ML305A/ML307A/ML307R/ML307C/ML307M不支持<flag>设置0x200、0x400。
6. 仅TLV格式支持<flag>设置为1、2进行多条报文组合发送；
MN319/MN316A/MN326A/MR880A设置<contentType>不改变<value>的值。
25

中移物联网有限公司
AT+MIPLNOTIFY
设置命令
AT+MIPLNOTIFY=0,0,3200,0,5500,5,1,"0",2,1
OK
AT+MIPLNOTIFY=0,0,3200,0,5501,3,3,"-12",1,2
OK
AT+MIPLNOTIFY=0,0,3200,0,5750,1,4,"test",0,0
OK
AT+MIPLNOTIFY=0,0,3200,0,5505,2,10,"E309C82FE6",0,0x400,278
OK
+MIPLEVENT: 0,26,278
Note: 由于buffer资源限制，本次发送上报数据总共建议不超过1000Bytes。
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
26

中移物联网有限公司
3.10. +MIPLREAD 读操作请求消息
该上报是一个读操作请求消息，模组自动上报。
+MIPLREAD
语法 响应
+MIPLREAD: <ref>,<mid>,<objid>,<insid>,<resid>
参数描述
<ref>整型，LwM2M设备实例ID。
<mid>uint16类型，消息ID。
<objid>uint16类型，Object ID。
<insid>整型，Instance ID，如果为-1，需要读取该object下的所有资源。
<resid>整型，Resource ID，如果为-1，需要读取该instance下的所有资源。
Note: <insid>参数值为-1时，<resid>参数值也必为-1。
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
27

中移物联网有限公司
3.11. AT+MIPLREADRSP 读操作请求响应
该命令用于在接收到+MIPLREAD消息后，返回所读取到的资源值。
AT+MIPLREADRSP
语法 响应
设置命令
成功
AT
OK
+MIPLREADRSP=<ref>,<mid
>,<result>[,<objid>,<insid>,< 错误
resid>,<type>,<len>,<value>,
+CIS ERROR: <err>
<index>,<flag>]
参数描述
<ref>整型，LwM2M设备实例ID。
<mid>uint16类型，该resource所属的instance observe操作时下发的<mid>。
<result>整型，操作结果。
1
Content OK
11
4
Bad Request 7
1
12
2
Unauthorized 1
_
13
O
Not Found
M
14
e
Method Not Allowed n
O
15
Not Acceptable
<objid>uint16类型，Object ID。
<insid>uint16类型，Instance ID。
<resid>uint16类型，Resource ID。
<type>整型，指定响应资源的数据类型。
1
String
2
7
Opaque
7. Opaque类型2为透传类型，通过AT命令输入16进制字符串将被模组转换为ASCII码后上传至平台。
28

中移物联网有限公司
AT+MIPLREADRSP
3
Integer
4
Float
5
Bool
6
8
Hex str
<len>整型，value的值的长度。模组会依据当前的值的范围上报报文做优化算法，无需指定<len>。
<value>字符串，上报的数据值。
<index>整型，命令序号。若某个READRSP操作需要N条报文组合为一完整命令，则index从N-1至0降序编
号，当index编号为0时表示本次READRSP命令结束。直接置0亦可。
<flag>整型，消息标识。<flag>参数为1和2时，当前报文暂不发送；当<flag>为0时，会将同一object下同一
instance下的未发送resource一起组包上报。
1
第一条报文
2
中间报文
4
7
0
1
最后一条报文 2
1
示例 _
O
设置命令
M
+MIPLREAD:0,18172,3200,0,5750
e
AT+MIPLREADRSP=0,18172,11
n
OK
O
+MIPLREAD:0,18172,3200,0,-1
AT+MIPLREADRSP=0,18172,1,3200,0,5500,5,1,"0",2,1
OK
AT+MIPLREADRSP=0,18172,1,3200,0,5501,3,3,"-12",1,2
OK
AT+MIPLREADRSP=0,18172,1,3200,0,5750,1,4,"test",0,0
OK
8. Hex_str类型6为16进制字符串，将被模组转换为ASCII码后以string类型1上传至平台。
29

中移物联网有限公司
Note:
执行READ操作回复命令中的<mid>必须对应平台下发READ操作中<mid>；
由于buffer资源限制，本次发送读取数据建议总共不超过1000Bytes；
模组收到READ命令并转发到MCU后，MCU需要在响应时间窗内做出READ响应操作（平台侧现在
推荐3s内执行操作回复）。
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
30

中移物联网有限公司
3.12. +MIPLWRITE 写操作请求消息
该上报是一个写操作请求消息，由模组自动上报。
+MIPLWRITE
语法 响应
+MIPLWRITE: <ref>,<mid>,<objid>,<insid>,<resid>,<type>,<len>,<value>,<flag>
参数描述
<ref>整型，LwM2M设备实例ID。
<mid>uint16类型，消息ID。
<objid>uint16类型，Object ID。
<insid>整型，Instance ID，如果为-1，需要写入该object下的所有资源。
<resid>整型，Resource ID，如果为-1，需要写入该instance下的所有资源。
<type>整型，待写入的数据类型。
<len>整型，本次报文写入的数据值长度。
<value>字符串，写入的数据值。
<flag>整型，多条命令上报时消息标识。
4
1 7
1
第一条消息
2
2 1
_
中间消息
O
0
M
最后一条消息
e
n
O
Note:
所写的值只有在该资源类型被申明（notify或read）过后才能正确识别；
未申明过类型的值将默认以16进制字符串类型打印；
下发写入未申明过类型的值是一种错误用法，不建议使用；
下发写入浮点数时，整数位不超过10位，小数位不超过9位，否则超出范围的浮点数将无法正常打
印。
31

中移物联网有限公司
3.13. AT+MIPLWRITERSP 写操作请求响应
该命令用于接收到+MIPLWRITE消息后，回复模组WRITE操作的结果。
AT+MIPLWRITERSP
语法 响应
成功
设置命令
OK
AT
+MIPLWRITERSP=<ref>,<mid 错误
>,<result>
+CIS ERROR: <err>
参数描述
<ref>整型，LwM2M设备实例ID。
<mid>uint16类型，该resource所属的instance observe操作时下发的<mid>。
9
<result>整型，操作结果。
2
Changed
11
Bad Request
4
12
7
1
Unauthorized
2
13 1
_
Not Found
O
14
M
Method Not Allowed
e
示例 n
O
设置命令
+MIPLWRITE: 0,18173,3200,0,5750,1,4,test,0
AT+MIPLWRITERSP=0,18173,2
OK
Note:
执行WRITE操作回复命令中的<mid>必须与对应平台下发的WRITE操作中的<mid>相同。
9. 操作结果正确<result>返回2；其他返回值都表示操作错误，一般返回11。
32

中移物联网有限公司
3.14. +MIPLEXECUTE 命令操作请求消息
该上报为命令操作请求消息，由模组自动上报。
+MIPLEXECUTE
语法 响应
+MIPLEXECUTE: <ref>,<mid>,<objid>,<insid>,<resid>[,<len>,<cmd>]
参数描述
<ref>整型，LwM2M设备实例ID。
<mid>uint16类型，消息ID。
<objid>uint16类型，Object ID。
<insid>整型，Instance ID。如果为-1，需要执行该object下的所有资源。
<resid>整型，Resource ID。如果为-1，需要执行该instance下的所有资源。
<len>整型，本次报文写入的数据值长度。
<cmd>字符串，下发的命令。
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
33

中移物联网有限公司
3.15. AT+MIPLEXECUTERSP 命令操作请求响应
该命令用于接收到+MIPLEXECUTE消息后，回复模组EXECUTE操作的结果。
AT+MIPLEXECUTERSP
语法 响应
成功
设置命令
OK
AT
+MIPLEXECUTERSP=<ref>,< 错误
mid>,<result>
+CIS ERROR: <err>
参数描述
<ref>整型，LwM2M设备实例ID。
<mid>uint16类型，该resource所属的instance observe操作时下发的<mid>。
10
<result>整型，操作结果。
2
Changed
11
Bad Request
4
12
7
1
Unauthorized
2
13 1
_
Not Found
O
14
M
Method Not Allowed
e
示例 n
O
设置命令
+MIPLEXECUTE: 0,18174,3200,0,5750,4,”test”
AT+MIPLEXECUTERSP=0,18174,2
OK
Note: 执行EXECUTE操作回复命令中的<mid>必须对应平台下发的EXECUTE操作中的<mid>相同。
10. 操作结果正确<result>返回2；其他返回值都表示操作错误，一般返回11。
34

中移物联网有限公司
3.16. +MIPLPARAMETER 设置策略参数请求消息
该上报为设置策略参数请求消息，由模组自动上报。
+MIPLPARAMETER
语法 响应
+MIPLPARAMETER: <ref>,<mid>,<objid>,<insid>,<resid>,<len>,<para>
参数描述
<ref>整型，LwM2M设备实例ID。
<mid>uint16类型，消息ID。
<objid>uint16类型，Object ID。
<insid>整型，Instance ID。如果为-1，需要设置该object下的所有资源。
<resid>整型，Resource ID。如果为-1，需要设置该instance下的所有资源。
<len>整型，本次报文写入的数据值长度。
11
<para> 字符串，策略参数。包括如下策略：pmin=xxx;pmax=xxx;gt=xxx;lt=xxx;st=xxx。
pmin
上传数据的最小时间间隔int类型
pmax 4
7
上传数据的最大时间间隔int类型
1
2
gt
1
当数据大于该值上传double类型 _
O
lt
M
当数据小于该值上传double类型
st e
n
当两个数据点相差大于等于该值上传double类型
O
Note:
模组仅上报该策略消息至MCU，该策略由MCU侧具体实现；
该策略的下发伴随着OBSERVE消息；
目前无需MCU执行AT+MIPLPARAMETERRSP命令回复平台，模组将会自动回复平台。
11. 下发的pmin和pmax都存在时，pmax>pmin，大于0；
下发的pmin默 认为 0；
下发的lt < gt并且lt +2*st < gt；
下发的策略参数如果有gt、lt、st，则<resid>不为 -1。
35

中移物联网有限公司
3.17. AT+MIPLPARAMETERRSP 设置策略参数操作请求响应
该命令用于接收到MIPLPARAMETER消息后，回复模组PARAMETER操作的结果。
AT+MIPLPARAMETERRSP
语法 响应
成功
设置命令
OK
AT
+MIPLPARAMETERRSP=<ref 错误
>,<mid>,result>
+CIS ERROR: <err>
参数描述
<ref>整型，LwM2M设备实例ID。
<mid>uint16类型，该resource所属的instance observe操作时下发的<mid>。
12
<result>整型，操作结果。
2
Changed
11
Bad Request
4
12
7
1
Unauthorized
2
13 1
_
Not Found
O
14
M
Method Not Allowed
e
示例 n
O
设置命令
+MIPLPARAMETER: 0,18174,3200,0,5758,38,"pmin=18;pmax=90;lt=0.0;gt=60.0;stp=2.0"
AT+MIPLPARAMETERRSP=0,18174,2
OK
Note:
执行PARAMETER操作回复命令中的<mid>必须与对应平台下发的PARAMETE操作中的<mid>相
同；
目前无需MCU执行AT+MIPLPARAMETERRSP命令回复平台，模组将会自动回复平台。
12. 操作结果正确<result>返回2；其他返回值都表示操作错误，一般返回11。
36

中移物联网有限公司
3.18. +MIPLOBSERVE 观测请求消息
该上报为观测请求消息，由模组自动上报。
+MIPLOBSERVE
语法 响应
+MIPLOBSERVE: <ref>,<mid>,<flag>,<objid>,<insid>,<resid>
参数描述
<ref>整型，LwM2M设备实例ID。
<mid>uint16类型，消息ID。
<flag>整型，操作标志。
1
为添加观测
0
为取消观测
<objid>uint16类型，Object ID。
<insid>整型，Instance ID。如果为-1，需要观测该object下的所有资源。
<resid>整型，Resource ID。如果为-1，需要观测该instance下的所有资源。
4
7
1
Note: 该消息会在以下两种情景时上报： 2
1
_
▪ 模组登录上平台之后上报所有建立的object_instance实例，此时MCU侧无需处理该消息；
O
▪ 通过API向平台下发OBSERVE消息至模组，此时平台会先下发相关+MIPLPARAMETER消息，模组
M
自动回复该消息至平台后下发该OBSERVE消息，此时模组自动处理该OBSERVE消息。目前无需MCU执
行AT+MIPLPARAMETERRSPe与AT+MIPLOBSERVERSP命令回复平台，模组将会自动回复平台。
n
O
37

中移物联网有限公司
3.19. AT+MIPLOBSERVERSP 观测请求操作响应
该命令用于接收到+MIPLRVERSP消息后，回复模组OBSERVE操作的结果。
AT+MIPLOBSERVERSP
语法 响应
成功
设置命令
OK
AT
+MIPLOBSERVERSP=<ref>,< 错误
msgid>,<result>
+CIS ERROR: <err>
参数描述
<ref>整型，LwM2M设备实例ID。
<msgid>uint16类型，该resource所属的instance observe操作时下发的<mid>。
13
<result>整型，操作结果。
1
Content OK
11
Bad Request
4
12
7
1
Unauthorized
2
13 1
_
Not Found
O
14
M
Method Not Allowed
e
示例 n
O
设置命令
+MIPLOBSERVE: 0,18175,1,3200,0,5750
AT+MIPLWRITEOBSERVERSP=0,18175,1
OK
Note:
执行OBSERVE操作回复命令中的<mid>必须对应平台下发的OBSERVE操作中的<mid>相同；
目前无需MCU执行AT+MIPLOBSERVERSP命令回复平台，模组将会自动回复平台。
13. 操作结果正确<result>返回1；其他返回值都表示操作错误，一般返回11。
38

中移物联网有限公司
3.20. +MIPLEVENT 状态事件
该命令用于对MCU上报一个状态事件。
+MIPLEVENT
语法 响应
+MIPLEVENT: <ref>,<evtid>[,<ackid>]
参数描述
<ref>整型，LwM2M设备实例ID。
14
<evtid>整型，事件ID。
2
Bootstrap流程成功
3
Bootstrap流程失败
6
登录LwM2M平台成功
7
登录LwM2M平台失败
11
4
更新LwM2M平台成功 7
1
15 2
1
退出LwM2M平台成功
_
26 O
Notify上报响应 M
31
e
n
设备被平台删除
O
32
DTLS IP老化
33
接收包数据总长度超过1500，丢弃。
40
设备进入下载升级包状态
41
设备下载升级包失败
42
设备下载升级包完成
43
14. 其中事件ID 40-47和51为FOTA事件。
39

中移物联网有限公司
+MIPLEVENT
设备进入升级状态
44
设备升级成功
45
设备升级失败
46
设备升级完成
47
设备下载中断
51
设备下载进度
<ackid>uint16类型，可选参数。
Note:
FOTA升级事件仅适用于MN316/MN316A/MN326A/MN318模组；
仅CTWing平台FOTA上报下载进度。
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
40

中移物联网有限公司
4. LwM2M扩展AT命令
本章详细描述了LwM2M相关的附加AT命令和命令格式。
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
41

中移物联网有限公司
4.1. AT+MIPLCFG 配置可选参数
该命令用于配置可选参数，以支持各个LwM2M协议平台登录请求。
AT+MIPLCFG
语法 响应
成功
设置命令（平台选项设置）
OK
AT
+MIPLCFG="platform",<ref>, 错误
<platform>[,<pattern>]
+CIS ERROR: <err>
成功
设置命令（平台选项设置）
OK
AT
+MIPLCFG="platform",<ref>, 错误
<platform>[,<epname>]
+CIS ERROR: <err>
成功
设置命令（绑定设置）
OK
AT
+MIPLCFG="binding",<ref>,< 错误
binding_mode>
+CIS ERROR: <err> 4
7
参数描述 1
2
<ref>整型，LwM2M设备实例ID。 1
_
<platform>整型，使用的目标IoT平台。 O
M
0
中国移动OneNET平台 e
n
1 O
中国电信CTWing平台
2
15
中国联通雁飞格物DMP平台
3
16
华为云物联网平台
10
其他平台
<pattern>整型，登录平台所使用的的endpoint name模式，为0~4时各个参数由模组自动获取。
0
15. ML305A/ML307A/ML307R/ML307C/ML307M暂不支持。
16. ML307M暂不支持。
42

中移物联网有限公司
AT+MIPLCFG
不指定，各个平台采用默认值。
1
IMEI，CTWing平台、华为云物联网平台默认方式。
2
IMEI;IMSI，OneNET平台默认方式。
3
urn:imei:⼈⼈⼈⼈⼈⼈⼈⼈⼈⼈⼈⼈; CTWing平台可选方式。
4
urn:imei-imsi:⼈⼈⼈⼈⼈⼈⼈⼈⼈⼈⼈⼈⼈⼈⼈-⼈⼈⼈⼈⼈⼈⼈⼈⼈⼈⼈⼈⼈⼈⼈; CTWing平台可选方式。
<epname>字符串，登录平台的epname由客户指定，platform=10时有效。
<binding_mode>整型，客户端绑定模式，登录/更新过程上传的设备绑定参数。
0
客户端不设置，采用服务器默认模式。
1
UDP模式
2
UDP & Queue模式
示例
4
7
设置命令
1
2
AT+MIPLCFG="platform",0,1
1
OK _
O
M
e
n
O
43

中移物联网有限公司
4.2. AT+MIPLNMI 消息上报控制
该命令用于控制平台下发请求消息上报，模组数据发送结果消息上报。目前仅OneNET支持相关功能。
AT+MIPLNMI
语法 响应
成功
设置命令
OK
AT
+MIPLNMI=<ref>,<nnmi>,<ns 错误
mi>
+CIS ERROR: <err>
参数描述
<ref>整型，LwM2M设备实例ID。
<nnmi>整型，接收消息上报模式。
nnmi被置为2时，平台下发消息时模组不会有任何信息传递至MCU；
nnmi被置为1,2时，模组队列最多保存40个下发操作，队列满后，将丢弃最先进入队列的下发操作；
nnmi被置为1,2时，模组缓存最多保存的write、execute操作信息所带数据不超过2K，否则将会丢弃当前接收
的下发操作。
0
直接上报操作至MCU，默认模式。
4
1 7
1
仅通知MCU平台有操作下发，通知格式为+MIPLNMI。
2
2 1
_
模组不做任何通知与上报处理。 O
<nsmi>整型，是否开启发送消息上报M。
0 e
n
关闭
O
1
开启
示例
设置命令
AT+MIPLNMI=0,1,1
OK
44

中移物联网有限公司
Note:
重启后，<nnmi>与<nsmi>将重置为0；
<nnmi>设置仅对READ、WRITE、EXECUTE操作有效，其他操作皆为直接上报模式；
WRITE、EXECUTE操作整体报文超过1024字节时将触发block分包机制，此时该设置无效，将直接
上报操作至MCU；
<nsmi>设置为1后，将会在AT+MIPLNOTIFY、AT+MIPLREADRSP、AT+MIPLWRITERSP、AT
+MIPLEXECUTERSP命令执行时将这几条命令的<mid>参数与127做&运算，以运算的结果计为<num>，其
上报结果以+MIPLNSMI的形式上报MCU。并且mid由用户维护自加，每发一条消息相应的mid加1；
ML305A/ML307A/ML307R/ML307C/ML307M/ML307N不支持发送消息上报功能。
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
45

中移物联网有限公司
4.3. AT+MIPLMGR 获取缓存请求
该该命令用于获取未被读取的缓存下发操作，仅用于URC非默认模式下。
AT+MIPLMGR
语法 响应
成功
设置命令 OK
AT+MIPLMGR=<ref> 错误
+CIS ERROR: <err>
参数描述
<ref>整型，LwM2M设备实例ID。
示例
设置命令
AT+MIPLMGR=0
+MIPLREAD: 0,8172,3200,0,-1
OK
AT+MIPLMGR=0 //需执行对应的RSP命令后，才可读取下一条操作。
+MIPLREAD: 0,8172,3200,0,-1
OK 4
AT+MIPLREADRSP=0,8172,1,3200,0,5500,5,1,"0",2,1 7
OK 1
2
AT+MIPLREADRSP=0,8172,1,3200,0,5501,3,3,"-12",1,2
1
OK
_
AT+MIPLREADRSP=0,8172,1,3200,0,5750,1,4,"test",0,0
O
OK
AT+MIPLMGR=0 //如果一次write操作中M有多条资源被write，将一并上报。
+MIPLWRITE: 0,8173,3200,0,5501,3,4,1860,1,0
e
+MIPLWRITE: 0,8173,3200,0,5750,1,4,test,0,0
n
OK
O
AT+MIPLWRITERSP=0,8173,2
OK
AT+MIPLMGR=0
OK
AT+MIPLMGR=0
+CIS ERROR: 602
46

中移物联网有限公司
Note:
该命令只在模组登录后方可执行，否则返回错误；
如当前无未被读取的下发消息，直接返回OK；
如当前URC为默认模式，直接返回OK；
需在执行对应的RSP命令后，才可读取下一条操作；
如果一次WRITE操作中有多条资源被WRITE，将一并上报。
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
47

中移物联网有限公司
4.4. AT+MIPLQMGR 查询缓存数据统计值
该命令用于查询URC缓存相关数据统计值。
AT+MIPLQMGR
语法 响应
成功
+MIPLQMGR: <ref>,QUEUES=<queues>,DROPPED=<dropped>,BUFFERS=<buffers>
设置命令
OK
AT+MIPLQMGR=<ref>
错误
+CIS ERROR: <err>
参数描述
<ref>整型，LwM2M设备实例ID。
<queues>整型，统计当前未返回结果的READ、WRITE、EXECUTE操作数。
<dropped>整型，当前被丢弃的未返回结果的操作数。
<buffers>整型，统计当前未返回结果的WRITE、EXECUTE操作所占用的缓存空间。。
示例
设置命令
4
7
AT+MIPLQMGR=0
1
+MIPLQMGR: 0,QUEUES=3,DROPPED=0,BUFFERS=12
2
OK
1
_
O
Note:
M
该命令所统计数据为本e次登录中的数据，若退出平台，将被清空。
n
O
48

中移物联网有限公司
4.5. AT+MIPLUPDATESET 自动更新设置
该命令用于设置自动update功能。
AT+MIPLUPDATESET
语法 响应
成功
设置命令
OK
AT
+MIPLUPDATESET=<ref>,<e 错误
nable>
+CIS ERROR: <err>
参数描述
<ref>整型，LwM2M设备实例ID。
<enable>整型，操作结果。
0
关闭
1
开启
示例
4
设置命令 7
1
AT+MIPLUPDATESET=0,1 2
OK 1
_
O
Note: M
e
该命令在创建设备后（AT+MIPLCREATE）、登录设备（AT+MIPLOPEN）前使用；
n
自动updaOte功能开启后，将在lifetime计时器执行之前30s自动向平台发起update请求；
若模组在发送时网络异常导致update请求不成功，将在lifetime计时器到来之时退出平台；
lifetime大于等于60s时，自动update功能方可生效；
若执行AT+MIPLUPDATE命令，自动update时间将更新;
仅MN318/ML305A/ML307A/ML307R/ML307C支持该命令。
49

中移物联网有限公司
4.6. AT+MIPLCREATEEX 创建LwM2M设备
该命令用于创建一个LwM2M设备实例。
AT+MIPLCREATEEX
17
语法 响应
成功
设置命令
+MIPLCREATEEX: <ref>
AT
OK
+MIPLCREATEEX=<host>,<fl
ag>[,<authcode>,<PSK>[,<P 错误
SKID>[,<psk_encode>]]]
+CIS ERROR: <err>
参数描述
<ref>整型，LwM2M设备实例ID。
<host>字符串，服务器地址，格式如ip:port。port不设置时默认5683。当port设置为5684时，模组使用
DTLS接入OneNET平台。
<flag>整型，标志位。
bit0
1：bootstrap服务器
4
0：lwm2m ip直连服务器
7
1
bit1
2
1：禁止moniter功能 1
_
0：使能moniter功能 O
<authcode>字符串，登录服务器认证M参数，不设值时平台无认证参数。该参数需在平台端设置后方可使用。
e
<PSK>字符串，配置与平台建立DTLS连接的PSK秘钥。该参数采用API接口向服务器设置，由数字、大小写
n
字母组成的字符串，长度最大16字节。
O
<PSKID>字符串，配置与平台建立DTLS连接的PSKID。参数值为空时，采用平台默认的PSKID。
<psk_encode>整型，psk字符串的编码方式，默认0。<psk_encode>根据平台指定，目前OneNET采用字符串
编码，CTWing采用字符串或十六进制编码，华为云物联网平台采用十六进制编码。
0
字符串编码
17. MR880A返回结果为异步上报，具体如下。
成功：
OK
+MIPLCREATEEX: <ref>
DNS失败：
+MIPLCREATEEX: FAIL
50

中移物联网有限公司
AT+MIPLCREATEEX
1
十六进制编码
示例
18
设置命令
AT+MIPLCREATEEX=”nbiotbt.heclouds.com”,1
+MIPLCREATEEX: 0
OK
Note:
该设置命令与AT+MIPLCREATE命令功能类似，只有主要配置项，其它为默认；
模组仅支持一路设备接入，故其设备实例ID恒为0；
Bootstrap服务器和LwM2M服务器使用不同的PSK ；
目前OneNET、CTWing、华为云物联网平台支持DTLS相关功能；
开启moniter功能后，平台读取object为10290，instance为0的资源时，平台会返回驻网小区及邻近
小区信息；
moniter功能为OneNET独有；
MN316/MN316-S/MN316A/MN326A/MN318/MN326/MR880A的DTLS支持加密套件
TLS_PSK_WITH_AES_128_CBC_SHA256；
MN319的DTLS支持加密套件
TLS_PSK_WITH_AES_128_CBC_SHA256、TLS_PSK_WITH_AES_1428_CCM_8；
7
ML307M/ML307N不支持DTLS配置；
1
MR880A执行AT+MIPLCREATEEX命令，返回结果为异2步上报，即先返回OK，再返回+MIPLCREATEEX:
1
0。
_
O
M
e
n
O
18. MR880A返回结果为异步上报。
51

中移物联网有限公司
4.7. AT+MIPLDEVINFO 查询设备实例参数
该命令用于查询设备实例参数。
AT+MIPLDEVINFO
语法 响应
成功
+MIPLDEVINFO: <host>,<flag>[,<authcode>[,<psk>]]
读取命令
OK
AT+MIPLDEVINFO?
错误
+CIS ERROR: <err>
参数描述
<host>字符串，当前服务器URI，格式如<ip>:<port>。
<flag>整型，标志位。
bit0
1：bootstrap服务器
0：lwm2m ip直连服务器。
bit1
4
1：禁止moniter功能
7
1
0：使能moniter功能。
2
1
<authcode>字符串，登录服务器认证参数。
_
O
<PSK>字符串，预共享密钥，DTLS服务使用。
M
示例
e
读取命令 n
O
AT+MIPLDEVINFO?
+MIPLDEVINFO: "183.230.40.149:5684",1,,"abcd1234"
OK
Note:
该命令获取的是当前状态的相关设备信息;
如用AT+MIPLCREATEEX设置的信息为bootstrap服务器信息，但设备已完成bootstrap流程则当前为
LwM2M服务器，AT+MIPLDEVINFO获取的为LwM2M服务器信息。
52

中移物联网有限公司
4.8. AT+MIPLDTLSNAT DTLS自动重协商时间间隔设置
AT+MIPLDTLSNAT
语法 响应
成功
设置命令
OK
AT
+MIPLDTLSNAT=<ref>,<time 错误
out>
+CIS ERROR: <err>
命令描述
该命令设置DTLS自动重协商时间间隔。设置间隔时间不能低于60s。
参数描述
<ref>整型，LwM2M设备实例ID。
<timeout>整型，自动重协商时间间隔，单位：s。
0
关闭DTLS自动重协商功能
大于等于60时
有效自动重协商时间间隔
4
7
示例
1
2
设置命令
1
_
AT+MIPLDTLSNAT=0,120
O
OK
M
e
Note: n
O
若不用该命令设置，默认为开启自动重协商功能，自动重协商时间间隔为120s。该值需客户依据网
络环境适配；
NB模组存在IP老化问题。若较长时间（当前网络2min左右）无数据通信，模组IP将发生变化，此时
发起的数据将被存入缓存队列（缓存模式）并重新DTLS握手流程。握手成功之后将发送缓存队列里的数
据；
缓存队列空间最大为DTLS加密前2K，最多支持10条数据；
缓存队列满后将返回错误；
缓存模式时执行AT+MIPLCLOSE命令后将清空缓存队列后添加AT+MIPLCLOSE命令；
采用隧道卡的客户无IP老化问题，故应关闭DTLS自动重协商功能；
目前OneNET、CTWing、华为云物联网平台支持DTLS相关功能；
ML307M/ML307N不支持该命令。
53

中移物联网有限公司
4.9. AT+MIPLLTRDP 查询剩余登录时长
该命令用于读取登录后终端记录的剩余lifetime。
AT+MIPLLTRDP
语法 响应
成功
+MIPLLTRDP: <lifetime>
设置命令
OK
AT+MIPLLTRDP
错误
+CIS ERROR: <err>
命令描述
非登录成功状态将返回0。
参数描述
<lifetime>整型，LwM2M设备登录后剩余lifetime，单位：s。
示例
设置命令
AT+MIPLLTRDP
4
+MIPLLTRDP: 2087
7
OK
1
2
1
Note: 该命令仅适用于M5310-A模组。 _
O
M
e
n
O
54

中移物联网有限公司
4.10. AT+MIPLFOTACFG 配置FOTA可选参数
该命令用于配置LwM2M FOTA可选参数。
AT+MIPLFOTACFG
语法 响应
设置命令（CTWing 成功
FOTA超时时间）
OK
AT
错误
+MIPLFOTACFG="timeout" [,
<timeout>] +CIS ERROR: <err>
参数描述
<cmd>字符串，命令标识符。
<timeout>整型，CTWing FOTA超时时间。参数范围1~1440小时（最大60天），默认值360小时（15天）。
CTWing FOTA将记录开始时间，模组升级开始后再次登录CTWing平台，若FOTA流程未完成且
超过<timeout>设置的时间，模组将终止本次升级并初始化FOTA状态。
示例
AT+MIPLFOTACFG="timeout" ,360
OK
4
AT+MIPLFOTACFG="timeout" 7
1
+MIPLFOTACFG: "timeout",360
2
OK
1
_
O
Note: 该命令仅适用于MN316/MN316A/MN326A/MN318模组。
M
e
n
O
55

中移物联网有限公司
4.11. +MIPLNSMI 数据发送上报
该命令用于指示报文是否成功上报至空口。
+MIPLNSMI
语法 响应
+MIPLNSMI: <ref>,<status>,<num>
参数描述
<ref>整型，LwM2M设备实例ID。
<status>整型，发送结果。
0
该数据发送失败
1
成功发送给基站空口
<num>整型，序列号，范围：0~127，。发送命令的mid与127的&运算结果。
示例
AT+MIPLNOTIFY=0,0,3200,0,5750,1,4,"test",0,0
OK
+MIPLNSMI: 0,1,0 4
AT+MIPLWRITERSP=0,18173,2 7
1
OK
2
+MIPLNSMI: 0,1,125
1
_
O
Note:
M
该命令仅对AT+MIPLNOeTIFY、AT+MIPLREADRSP、AT+MIPLWRITERSP、AT+MIPLEXECUTERSP有
效； n
O
其范围值<num>是以发送命令的<mid>与127做&运算得到；
ML305A/ML307A/ML307R/ML307C/MR880A/ML307M/ML307N暂不支持该消息。
56

中移物联网有限公司
4.12. +MIPLNMI 数据接收上报
该命令用于指示缓存模式下是否收到平台下发命令。
+MIPLNMI
语法 响应
+MIPLNMI: <ref>
参数描述
<ref>整型，LwM2M设备实例ID。
示例
AT+MIPLNMI=0,1,0
OK
+MIPLNMI: 0
Note:
AT+MIPLNMI参数<nnmi>设置为1时，平台下发命令时上报该消息。
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
57

中移物联网有限公司
5. 示例
本章主要介绍基于LwM2M连接OneNET平台，电信CTWing平台，联通雁飞平台的命令在相关业务场景中
的使用流程。
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
5.1. OneNET平台接入示例
本章详细描述了对接OneNET平台的流程和相应的AT命令流程。需注意由于OneNET平台更新，本流程仅
供参考，最新的平台流程参考https://open.iot.10086.cn/。
接入平台
AT+MIPLCREATEEX="nbiotbt.heclouds.com:5683",1,"cmiot" //创建设备
+MIPLCREATEEX: 0
OK
AT+MIPLADDOBJ=0,3200,1,"1",0,1 //订阅Object组
OK
AT+MIPLDISCOVERRSP=0,3200,1,4,"5750" //订阅Resource
OK
AT+MIPLOPEN=0,3000,30 //模组侧发起登录请求
OK
+MIPLEVENT: 0,2 //bootstrap流程成功（启用bootstrap服务方返回）
+MIPLEVENT: 0,6 //登录服务器时成功。
+MIPLOBSERVE: 0,39121,1,3200,0,-1 //OBSERVE消息
+MIPLDISCOVER: 0,39122,3200 //DISCOVER消息
+MIPLREAD: 0,39123,3,0,-1,0 //自动处理
Important:
在实际处理中，依靠判断+MIPLEVENT: 0,6消息来确认模组是否登录OneNET平台成功；
需要等待模组接收到OBSERVE消息和DISCOVER消息后，方可执行数据收发操作；
4
MR880A执行AT+MIPLCREATEEX命令，返回结果为异步上报，7即先返回OK，再返回+MIPLCREATEEX:
1
0。
2
1
_
O
数据收发
M
AT+MIPLNOTIFY=0,0,3200,0,5750,1,4,"abcd",0,0,147 //ackid上报数据
e
OK n
+MIPLEVENT: 0,26,147O
+MIPLREAD: 0,32705,3200,0,-1,0 //收到平台数据
GET http://api.heclouds.com/nbiot?imei=<xx>&obj_id=3200 HTTP/1.1 //基于http获取api-key
Host: api.heclouds.com
api-key: <api-key>
AT+MIPLREADRSP=0,32705,1,3200,0,5750,1,4,"abcd",0,0 //read响应（5秒内）
POST http://api.heclouds.com/nbiot?imei=<imei>&obj_id=3200&obj_inst_id=0&mode=2 HTTP/1.1
Host: api.heclouds.com //基于http发送数据
api-key: <api-key>
{
"data":[{
"val":"abcd",
"res_id":5750}],
}
+MIPLWRITE: 0,25845,3200,0,5750,1,4,abcd,0
OK
59

中移物联网有限公司
AT+MIPLWRITERSP=0,25845,2 //write响应
POST http://api.heclouds.com/nbiot/execute?imei=<imei>&obj_id=3200&obj_inst_id=0&res_id=5750 HTTP/1.1 //基于http执行操作
Host: api.heclouds.com
api-key: <api-key>
{
"args":"abcd"
}
+MIPLEXECUTE: 0,18166,3200,0,5750,4,abcd,0
OK
AT+MIPLEXECUTERSP=0,18166,2 //execute响应
Important:
由于buffer资源限制，每次MIPLNOTIFY上报数据Payload用户数据部分不超过1000Bytes；
每次Read操作后，模组执行回复响应上报COAP包Payload用户数据不超过1000Bytes；
每次从平台使用Write操作下发的COAP包Payload用户数据部分不超过1000Bytes；
每次从平台使用Execute操作下发的COAP包Payload用户数据部分不超过1000Bytes。
设备退出平台
AT+MIPLCLOSE=0 //退出请求
OK
+MIPLEVENT: 0,15 //指示模组退出平台成功
AT+MIPLDELETE=0 //销毁本地通信实例
4
OK 7
1
2
1
_
O
M
e
n
O
60

中移物联网有限公司
5.2. 基于DTLS加密的OneNET平台接入示例
目前支持PSK认证方式的DTLS加密接入OneNET。可以采用BS以及非BS（LwM2M server直接接入）两种
方式接入，BS过程涉及BS服务器以及LwM2M两套PSK，PSK建议采用API方式设置。
基于DTLS的bootstrap方式接入流程
创建OneNET设备及bootstrap server PSK。目前版本通过HTTP API创建设备时可添加可选字段PSK，该
字段需要满足由数字字母组成且长度为8~16个字节。该PSK为bootstrap server的PSK。以下为创建一个imei为
869975030000461，imsi为1064826326459，bootstrap server PSK为1234abcd的OneNET设备HTTP POST请
求示例。
POST http://api.heclouds.com/devices HTTP/1.1
api-key: deR2fiWKlRtIWaEaKOvto7ctQUg=
Content-Length: 225
Content-Type: application/json
Host: api.heclouds.com
{
"title":"test000",
"desc":"some description",
"tags": ["china","mobile"],
"protocol":"LWM2M",
"auth_info": {"869975030000461":"1064826326459"},
"obsv":true,
"psk":"1234abcd" 4
} 7
1
{"errno":0,"data":{"device_id":"516072121","psk":"1234abcd"},"error":"succ"} //成2功
1
_
接入OneNET流程。每次bootstrap流程都会重新生成新的LwM2M server PSK，故在一定时间内退出平台
O
后无需删除设备，直接登录可省略bootstrap流程。此例中"1234abcd"为bootstrap server PSK。
M
AT+MIPLCREATEEX="nbiotbt.heclouds.com:5684",1,,"1234abcd" //创建设备
e
+MIPLCREATEEX: 0
n
OK
O
AT+MIPLOPEN=0,3000,60 //发起登录请求
OK
+MIPLEVENT: 0,2
+MIPLEVENT: 0,6 //指示模组登录成功
登录成功后，可获取当前设备配置信息，此时地址为bootstrap后获取到的LwM2M server信息以及PSK信
息。
AT+MIPLDEVINFO? //查询信息
+MIPLDEVINFO :"183.230.40.40:5684",0,,"cPYxj0cTs9qMVL72"
OK
AT+MIPLDELETE=0 //删除当前设备
OK
61

中移物联网有限公司
创建设备，采用+MIPLDEVINFO命令获取的bootstrap后获取到的LwM2M server直接配置，客户可将此
bootstrap解析后的配置信息存入MCU flash中，该信息在模组bootstrap成功接入后1个月之内有效，失效后需要
再次通过bootstrap流程获取LwM2M信息,也可在使用获取到的LwM2M接入失败时重新尝试BS方式接入，BS成
功后更新LwM2M信息。
AT+MIPLCREATEEX="183.230.40.40:5684",0,,"cPYxj0cTs9qMVL72" //创建设备
+MIPLCREATEEX: 0
OK
AT+MIPLOPEN=0,3000,60 //发起登录请求
OK
+MIPLEVENT: 0,6
基于DTLS的非bootstrap方式接入流程
HTTP API方式创建LwM2M server PSK。当没有通过方式分配LwM2M server PSK时，可通过HTTP API方
式直接设置，以下为创建一个imei为869975030000461，LwM2M server PSK为abcdefge的HTTP POST请求示
例。
POST http://api.heclouds.com/nbiot/device/accpsk?imei=869975030000461 HTTP/1.1
api-key: deR2fiWKlRtIWaEaKOvto7ctQUg=
Content-Length: 26
Content-Type: application/json
Host: api.heclouds.com
{
"key":"abcdefge"
}
4
{"errno":0,"error":"succ"} //成功
7
1
基于DTLS的非bootstrap方式接入。 2
1
AT+MIPLCREATEEX="nbiotacc.heclouds.com:5684",0_,,"abcdefge" //创建设备
O
+MIPLCREATEEX: 0
OK M
AT+MIPLOPEN=0,3000,60 //发起登录请求
OK e
n
+MIPLEVENT: 0,6 //指示模组登录成功
O
Note:
本示例不适用于ML307M/ML307N模组；
MR880A执行AT+MIPLCREATEEX命令，返回结果为异步上报，即先返回OK，再返回+MIPLCREATEEX:
0。
62

中移物联网有限公司
5.3. CTWing平台接入示例
本章详细描述了对接CTWing平台的流程和相应的AT命令流程。需注意由于CTWing平台更新，本流程仅供
参考，最新的平台流程参考https://www.ctwing.cn/。
接入平台
AT+MIPLCREATEEX="221.229.214.202:5683",0 //创建设备
+MIPLCREATEEX: 0
OK
AT+MIPLCFG="platform",0,1 //选择CTWing平台的默认配置，默认IMEI登录。
OK
AT+MIPLADDOBJ=0,19,2,"11",0,0 //添加object 19
OK
AT+MIPLDISCOVERRSP=0,19,1,3,"1;0" // res 订阅
OK
AT+MIPLOPEN=0,3000,30 //模组侧发起登录请求
OK
+MIPLEVENT: 0,6 //登录服务器时成功
+MIPLOBSERVE: 0,61533,1,19,0,0 //OBSERVE 消息
Important:
在实际处理中，依靠判断+MIPLEVENT: 0,6消息来确认模组是否登录CTWing平台成功；
若添加了资源需要等待模组接收到OBSERVE消息，方可执行数据收发操作；
该平台无bootstrap功能； 4
7
MR880A执行AT+MIPLCREATEEX命令，返回结果为异步上报，即先返回OK，再返回+MIPLCREATEEX:
1
0。
2
1
_
O
数据收发
M
AT+MIPLNOTIFY=0,18938,19,0,0e,1,12,"003233343333",0,0,11 //带ack id上报数据
n
OK
+MIPLEVENT: 0,26,11 O //收到对应的ack id
//收到平台下发的数据
+MIPLWRITE: 0,24265,19,1,0,2,8,34353637,0
// CTWing obj 19 下行自动回复，不用再执行MIPLWRITERSP
63

中移物联网有限公司
Important:
由于buffer资源限制，每次上报数据Payload用户数据部分不超过1000Bytes；
每次Read操作后，模组执行回复响应上报COAP包Payload用户数据不超过1000Bytes；
每次从平台使用Write操作下发的COAP包Payload用户数据部分不超过1000Bytes；
每次从平台使用Execute操作下发的COAP包Payload用户数据部分不超过1000Bytes。
设备退出平台
AT+MIPLCLOSE=0 //退出请求
OK
+MIPLEVENT: 0,15 //指示模组退出平台成功
AT+MIPLDELETE=0 //删除LwM2M设备实例
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
5.4. 基于DTLS加密的CTWing平台接入示例
PSK认证方式的DTLS加密接入CTWing。可以使用非BS（LwM2M server直接接入）方式接入。
在CTWing平台上创建产品中选择加密方式为DTLS，创建设备时PSK有字符串和16进制两种格式，以下对
PSK的两种格式进行区分。
字符串格式PSK接入示例
CTWing平台上创建设备时选择PSK为字符串格式，设置PSK为”abcdefgh”。模组在使用MIPLCREATEEX
创建通讯实例时<psk_encode>参数设为0，表示输入<PSK>的格式为字符串（该参数默认为0可不设置）。
AT+MIPLCREATEEX="221.229.214.202:5684",0,,abcdefgh,,0 //<psk_encode>为0，psk格式为字符串。
+MIPLCREATEEX: 0
OK
AT+MIPLCFG="platform",0,1 //选择CTWing平台的默认配置，默认IMEI登录。
OK
AT+MIPLADDOBJ=0,19,2,"11",0,0 //添加object 19
OK
AT+MIPLDISCOVERRSP=0,19,1,3,"1;0" // res 订阅
OK
AT+MIPLOPEN=0,3000,30 //模组侧发起登录请求
OK
+MIPLEVENT: 0,6 //登录服务器成功
+MIPLOBSERVE: 0,60532,1,19,0,0 //OBSERVE 消息
4
Important: 模组在MIPLCFG命令中若不使用默认平台设置（即不使用IMEI方式登录）时，MIPLCREATEEX命
7
令的参数<PSKID>需设置为模组的IMEI号。 1
2
1
_
16进制格式PSK接入示例
O
M
CTWing平台上创建设备时选择PSK为16进制格式，设置PSK为”abcd1234”。模组在使用
MIPLCREATEEX创建通讯实例时<pesk_encode>参数设为1，表示输入<PSK>的格式为16进制。
n
AT+MIPLCREATEEXO="221.229.214.202:5684",0,,abcd1234,,1 //<psk_encode>为1，psk格式为16进制。
+MIPLCREATEEX: 0
OK
AT+MIPLCFG="platform",0,1 //选择CTWing平台的默认配置，默认IMEI登录。
OK
AT+MIPLADDOBJ=0,19,2,"11",0,0 //添加object 19
OK
AT+MIPLDISCOVERRSP=0,19,1,3,"1;0" // res 订阅
OK
AT+MIPLOPEN=0,3000,30 //模组侧发起登录请求
OK
+MIPLEVENT: 0,6 //登录服务器成功
+MIPLOBSERVE: 0,62875,1,19,0,0 //OBSERVE 消息
65

中移物联网有限公司
Important: 模组在MIPLCFG命令中若不使用默认平台设置（即不使用IMEI方式登录）时，MIPLCREATEEX命
令的参数<PSKID>需设置为模组的IMEI号。
Note:
本节示例不适用于ML305A/ML307A/ML307R/ML307C/ML307M/ML307N模组；
MR880A执行AT+MIPLCREATEEX命令，返回结果为异步上报，即先返回OK，再返回+MIPLCREATEEX:
0。
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
66

中移物联网有限公司
5.5. 雁飞平台接入示例
本章详细描述了对接联通雁飞平台的流程和相应的AT命令流程。需注意由于雁飞平台更新，本流程仅供参
考，最新的平台流程参考https://dmp.cuiot.cn/。
接入平台
AT+MIPLCREATEEX="dmp-coap.cuiot.cn:5683",0 //创建设备
+MIPLCREATEEX: 0
OK
AT+MIPLCFG="platform",0,2 //选择雁飞平台的默认配置，默认IMEI登录。
OK
AT+MIPLADDOBJ=0,3300,1,"1",0,1 //订阅Object组
OK
AT+MIPLDISCOVERRSP=0,3300,1,4,"5700" //订阅Resource
OK
AT+MIPLOPEN=0,3000,30 //模组侧发起登录请求
OK
+MIPLEVENT: 0,6 //登录服务器时成功。
+MIPLOBSERVE: 0,50785,1,3300,0,-1
Important:
在实际处理中，依靠判断+MIPLEVENT: 0,6消息来确认模组是否登录雁飞平台成功；并且，需要等待模
组接收到OBSERVE消息后，方可执行数据收发操作；
MR880A执行AT+MIPLCREATEEX命令，返回结果为异步上报，即先4返回OK，再返回+MIPLCREATEEX:
7
0。
1
2
1
_
数据收发
O
M
AT+MIPLNOTIFY=0,9286,3300,0,5700,4,4,50.8,0,0,3 //ackid上报数据
OK e
+MIPLEVENT: 0,26,3 n
+MIPLREAD: 0,7056,33O00,0,5700 //收到平台数据
AT+MIPLREADRSP=0,7056,1,3300,0,5700,4,4,30.6,0,0 //read响应（5秒内）
OK
+MIPLWRITE: 0,44027,3300,0,5700,4,4,24.5,0
AT+MIPLWRITERSP=0,44027,2 //write响应
OK
Important:
由于buffer资源限制，每次MIPLNOTIFY上报数据Payload用户数据部分不超过1000Bytes；
每次Read操作后，模组执行回复响应上报COAP包Payload用户数据不超过1000Bytes；
每次从平台使用Write操作下发的COAP包Payload用户数据部分不超过1000Bytes；
每次从平台使用Execute操作下发的COAP包Payload用户数据部分不超过1000Bytes。
67

中移物联网有限公司
设备退出平台
AT+MIPLCLOSE=0 //退出请求
OK
+MIPLEVENT: 0,15 //指示模组退出成功
AT+MIPLDELETE=0 //删除本地通信实例
O
Note: 本节示例不适用于ML307N模组。
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
5.6. 华为云物联网平台接入示例
本章描述了对接华为云物联网平台的流程和相应的AT命令流程。需注意由于华为云物联网平台的更新，本
流程仅供参考，最新的平台流程参考https://www.huaweicloud.com/。
接入平台
AT+MIPLCREATEEX="iot-coaps.cn-north-4.myhuaweicloud.com",0 //模组创建本地通讯实例
+MIPLCREATEEX: 0
OK
AT+MIPLCFG="platform",0,3 //选择华为云物联网平台的默认配置，默认IMEI登录。
OK
AT+MIPLADDOBJ=0,19,2,"11",0,0 //订阅Object组
OK
AT+MIPLDISCOVERRSP=0,19,1,3,"1;0" //订阅Resource
OK
AT+MIPLOPEN=0,3000,30 //模组侧发起登录请求
OK
+MIPLEVENT: 0,6 //登录服务器成功。
+MIPLOBSERVE: 0,63660,1,19,0,0
Important:
在实际处理中，依靠判断+MIPLEVENT: 0,6消息来确认模组是否登录华为云物联网平台成功；并
且，需要等待模组接收到OBSERVE消息后，方可执行数据收发操作；
MR880A执行AT+MIPLCREATEEX命令，返回结果为异步上报，即先4返回OK，再返回+MIPLCREATEEX:
7
0。
1
2
1
_
数据收发
O
M
AT+MIPLNOTIFY=0,0,19,0,0,2,4,0102,0,0,13 //ackid上报数据
OK e
+MIPLEVENT: 0,26,13 n
O
//收到平台下发的数据
+MIPLWRITE: 0,23550,19,1,0,6,8,05010203,0
// 华为云 obj 19 下行自动回复，不用再执行MIPLWRITERSP
Important:
由于buffer资源限制，每次MIPLNOTIFY上报数据Payload用户数据部分不超过1000Bytes；
每次Read操作后，模组执行回复响应上报COAP包Payload用户数据不超过1000Bytes；
每次从平台使用Write操作下发的COAP包Payload用户数据部分不超过1000Bytes；
每次从平台使用Execute操作下发的COAP包Payload用户数据部分不超过1000Bytes。
69

中移物联网有限公司
设备退出平台
AT+MIPLCLOSE=0 //退出请求
OK
+MIPLEVENT: 0,15 //指示模组退出成功
AT+MIPLDELETE=0 //删除本地通信实例
OK
Note: 本节示例不适用于ML305A/ML307A/ML307R/ML307C/ML307M/ML307N模组。
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
5.7. 基于DTLS加密的华为云物联网平台接入示例
PSK认证方式的DTLS加密接入华为云物联网平台。可以使用非BS（LwM2M server直接接入）方式接入。
DTLS接入示例
在华为云物联网平台上创建产品后，创建设备时输入PSK（目前平台规定PSK格式为16进制），设置PSK
为“abcd1234”。模组在使用MIPLCREATEEX创建本地通讯实例时<psk_encode>参数设为1，表示PSK格式为
16进制。
AT+MIPLCREATEEX="iot-coaps.cn-north-4.myhuaweicloud.com:5684",0,,abcd1234,,1 //创建通讯实例
+MIPLCREATEEX: 0
OK
AT+MIPLCFG="platform",0,3 //选择华为云物联网平台的默认配置，默认IMEI登录。
OK
AT+MIPLADDOBJ=0,19,2,"11",0,0 //订阅Object组
OK
AT+MIPLDISCOVERRSP=0,19,1,3,"1;0" //订阅Resource
OK
AT+MIPLOPEN=0,3000,30 //模组侧发起登录请求
OK
+MIPLEVENT: 0,6 //登录服务器时成功。
+MIPLOBSERVE: 0,59619,1,19,0,0
Important:
4
该示例不适用于ML305A/ML307A/ML307R/ML307C/ML307M7/ML307N模组；
1
模组在MIPLCFG命令中若不使用默认平台设置（即不使用IMEI方式登录）时，MIPLCREATEEX命令
2
的参数<PSKID>需设置为模组的IMEI号；
1
MR880A执行AT+MIPLCREATEEX命令，返回_结果为异步上报，即先返回OK，再返回+MIPLCREATEEX:
O
0。
M
e
n
O
71

中移物联网有限公司
5.8. LwM2M平台FOTA升级示例
本章描述了模组基于OneNET平台和CTWing平台的FOTA流程及注意事项。需注意模组FOTA功能基于平
台当前的FOTA功能实现，详细流程请参考平台相关文档。
OneNET平台FOTA升级示例
模组OneNET FOTA功能支持以LwM2M方式进行升级，用户在平台创建升级任务后，使用模组登录平台后
由平台触发执行FOTA升级流程。
AT+MIPLCREATEEX="nbiotbt.heclouds.com:5683",1 //创建设备
+MIPLCREATEEX: 0
OK
AT+MIPLOPEN=0,3000,30 //模组侧发起登录请求
OK
+MIPLEVENT: 0,2 //bootstrap流程成功(启用bootstrap服务方返回)
+MIPLEVENT: 0,6 //登录服务器时成功
+MIPLREAD: 0,53119,3,0,9 //平台读取3/0/9设备电量，模组自动回复平台
+MIPLEVENT: 0,40 //开始下载固件
+MIPLEVENT: 0,42 //固件下载完成
+MIPLREAD: 0,53194,3,0,9 //平台再次读取3/0/9设备电量，模组自动回复平台
+MIPLEVENT: 0,43 //开始固件升级，模组升级期间无法执行其他操作
+MATREADY //升级执行后重启
+MIPLEVENT: 0,44 //升级成功
AT+MIPLCREATEEX="nbiotbt.heclouds.com:5683",1 //创建设备
+MIPLCREATEEX: 0
4
OK
7
AT+MIPLOPEN=0,3000,30 //模组侧再次登录平台 1
OK 2
+MIPLEVENT: 0,2 1
+MIPLEVENT: 0,6 _
+MIPLEVENT: 0,46 //FOTA升级流程结束 O
M
Important:
e
n
OneNET平O台触发升级后，需要预留足够的时间进行固件下载和固件升级。若升级过程中掉电或退
出平台，下次登录平台时模组将继续未完成的升级任务。
CTWing平台FOTA升级示例
模组CTWing FOTA功能支持以LwM2M方式进行升级，用户在平台创建升级任务后，使用模组登录平台后
由平台触发执行FOTA升级流程。
AT+MIPLCREATEEX="lwm2m.ctwing.cn:5683",0 //创建设备
+MIPLCREATEEX: 0
OK
AT+MIPLOPEN=0,3000,30 //模组侧发起登录请求
OK
+MIPLEVENT: 0,6
72

中移物联网有限公司
+MIPLEVENT: 0,40 //触发FOTA升级，开始下载固件
+MIPLEVENT: 0,51,30967,1024 //固件下载进度
...
+MIPLEVENT: 0,51,30967,30967 //固件最后一包
+MIPLEVENT: 0,42 //固件下载完成
+MIPLEVENT: 0,43 //开始固件升级，模组升级期间无法执行其他操作
+MATREADY //升级执行后重启
+MIPLEVENT: 0,44 //升级成功
AT+MIPLCREATEEX="lwm2m.ctwing.cn:5683",0 //创建设备
+MIPLCREATEEX: 0
OK
AT+MIPLOPEN=0,3000,30 //模组侧再次登录平台
OK
+MIPLEVENT: 0,6
+MIPLEVENT: 0,46 //FOTA升级流程结束
Important:
CTWing平台触发升级后，需要预留足够的时间进行固件下载和固件升级。若升级过程中掉电，下次
登录平台时模组将继续未完成的升级任务；
CTWing FOTA升级过程中若执行AT+MIPLCLOSE命令或者设备lifetime到期将导致模组升级失败；
模组CTWing FOTA功能默认超时时间为15天，模组上报+MIPLEVENT: 0,40时记录开始时间，模组再
次登录平台后若检查FOTA超时，将初始化FOTA状态。用户可使用AT+MIPLFOTACFG命令设置CTWing
FOTA超时时间。
4
7
1
Note: 本节示例仅适用于MN316/MN316A/MN326A/MN318模组。
2
1
_
O
M
e
n
O
73

中移物联网有限公司
6. 错误码
本章为LwM2M命令相关的错误码。
错误码 说明
100 未知错误
601 语法，句法错误。
602 设备未登录或设备登录中
651 操作不被允许
652 Uplink Busy
653 资源操作错误，申请dev时已存在，其他操作资源不存在。
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
74