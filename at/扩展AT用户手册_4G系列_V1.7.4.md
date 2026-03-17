4
扩展AT用户手册 7
1
2
1
_
O
M
e
n
4G系列 O
版本：V1.7.4
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
新增ML302A、ML307A相关内容；
V1.1.0
更新手册适用范围。
新增ML305U、ML305A相关内容；
V1.2.0
更新手册适用范围。
V1.2.1 更新手册适用范围。
V1.2.2 更新手册适用范围。
新增ML305M相关内容；
V1.3.0
更新手册适用范围。
V1.3.1 优化ML305U部分内容。
新增ML307R相关内容；
V1.4.0
更新手册适用范围。 4
7
新增ML307G相关内容； 1
2
更新手册适用范围；
1
V1.5.0 优化文档部分内容；
_
ML307A新增O支持pwm控制和设置pwm数据命令；
更正4.14小节和4.17小节标题中文释义。
M
e新增ML551Z相关内容；
n
V1.6.0 更正+MUESTATS命令反馈值"<last_sinr>"；
O
更新手册适用范围。
新增ML307M、ML307R-BL、ML307R-ML、ML307R-MC相关内容；
更新手册适用范围；
V1.7.0
优化部分内容；
更正ML307R不支持+MCHIPINFO。
新增ML307G-DC相关内容；
V1.7.1
更新手册适用范围。
新增ML307M-DA相关内容；
V1.7.2
更新手册适用范围。
新增ML307H-GU/ML307H-DU相关内容；
V1.7.3
新增ML307C-DL-CN相关内容。

中移物联网有限公司
版本 描述
新增ML307N-DC/ML307N-DL相关内容；
V1.7.4 新增ML307X-DB/ML307X-DC相关内容；
新增ML307M-DH相关内容。
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
2. AT命令概述......................................................................................................................................................................................................................9
2.1. AT命令语法.......................................................................................................................................................................................................10
2.2. AT命令响应.......................................................................................................................................................................................................11
3. 通用命令.........................................................................................................................................................................................................................12
3.1. +MATREADY 开机打印信息........................................................................................................................................................................12
3.2. AT+MPOF 关机................................................................................................................................................................................................13
3.3. AT+MREBOOT 重启.......................................................................................................................................................................................14
4. 扩展命令.........................................................................................................................................................................................................................15
4.1. AT+CGMR 查询产品版本信息....................................................................................................................................................................15
4.2. ATI 查询产品ID信息.......................................................................................................................................................................................16
4.3. AT+MLOCKFREQ 指定搜索频率...............................................................................................................................................................17
4.4. AT+MCSEARFCN 清除历史频点...............................................................................................................................................................19
4.5. AT+MUESTATS 查询UE网络信息..............................................................................................................................................................20
4.6. AT+MCCID 读取ICCID..................................................................................................................................................................................26
4
4.7. AT+MADC 读取ADC电压.............................................................................................7................................................................................27
1
4.8. AT+MGPIO 控制GPIO...................................................................................................................................................................................29
2
4.9. AT+MLED 开关指示灯功能.........................................................................................................................................................................32
1
4.10. AT+MLPMCFG 低功耗管理配置...................................._.........................................................................................................................34
4.11. AT+MBAND 设置支持的BAND及优先级....O..........................................................................................................................................39
4.12. AT+MPSRAT 设置模组制式及优M先级....................................................................................................................................................43
4.13. AT+MDIALUPCFG 设置拨号协议...........................................................................................................................................................45
e
4.14. AT+MDIALUP （上位机）拨号连网......................................................................................................................................................49
n
4.15. AT+MCHIPINOFO 获取芯片温度、电压等信息....................................................................................................................................51
4.16. AT+MEMMTIMER 使能/禁用EMM定时器URC上报.........................................................................................................................53
4.17. AT+MIPCALL 建立模组数据连接...........................................................................................................................................................55
4.18. AT+MUECONFIG 配置UE基础行为.......................................................................................................................................................57
4.19. AT+MEID 读取SIM eID...............................................................................................................................................................................65
4.20. AT+MPWMDATA 设置pwm数据............................................................................................................................................................66
4.21. AT+MPWMCTRL pwm控制.....................................................................................................................................................................67

中移物联网有限公司
1. 引言
本文档介绍了模组的扩展AT命令，包括关机、重启、GPIO、LED、ADC等硬件相关的AT命令，版本信
息、网络信息、芯片温度、SIM卡信息等查询命令，模组低功耗、频段、拨号等设置命令。
1.1. 适用型号
Table 1. 适用模组
模组系列 模组子型号
ML302A ML302A-DCLM/ML302A-DSLM/ML302A-GCLM/ML302A-GSLM
ML302S ML302S-DNLM
ML307A-DCLN/ML307A-DSLN/ML307A-GCLN/ML307A-GSLN/ML30
ML307A
7A-DL
ML307S ML307S-DNLM
ML305U ML305U-DBLN
ML305A ML305A-DC/ML305A-DS/ML305A-DL
ML305M ML305M-DSLM 4
7
ML307R ML307R-DL/ML307R-DC/ML3071R-BL/ML307R-ML/ML307R-MC
2
ML551Z ML551Z-SL 1
_
ML307G ML307G-DLO/ML307G-DC
ML307X ML3M07X-DB/ML307X-DC
e
ML307M ML307M-DL/ML307M-DA/ML307M-DH
n
O
ML307H ML307H-GU/ML307H-DU
ML307C ML307C-DL-CN
ML307N ML307N-DC/ML307N-DL
8

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
9

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
10

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
11

中移物联网有限公司
3. 通用命令
本章详细描述了通用AT命令和命令格式。
3.1. +MATREADY 开机打印信息
该命令用于显示模组开机主动上报信息，标识模组AT已初始化完成。
+MATREADY
语法 响应
URC +MATREADY
示例
开机主动上报
+MATREADY
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
3.2. AT+MPOF 关机
该命令用于控制模组关机操作。
AT+MPOF
语法 响应
成功
设置命令 OK
AT+MPOF[=<mode>] 错误
+CME ERROR: <err>
URC POWER OFF
命令描述
该命令用于设备软关机，收到POWER OFF上报后执行断电动作，流程不可逆转。
参数描述
1
<mode>整型，关机模式，默认为0。
0
软件关机
1 4
7
保存特定命令配置到模组Flash中，并进入软件关机模式。
1
2
示例
1
_
模组关机
O
AT+MPOF=0 M
OK
POWER OFF e
n
O
Note: ML551Z使用该命令执行软件关机时，不能将PowerKey引脚拉低。
1. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML305M/ML551Z/ML307G/ML307X/ML307M/ML307N仅
支持0。
13

中移物联网有限公司
3.3. AT+MREBOOT 重启
该命令用于执行重启模组操作。
AT+MREBOOT
语法 响应
成功
设置命令 OK
AT+MREBOOT[=<mode>] 错误
+CME ERROR: <err>
URC REBOOTING
命令描述
该命令用于设备软重启。默认保存配置NV和协议栈的非易变NV，擦除其他所有工作态NV。
参数描述
2
<mode>整型，重启模式，默认为0。
0
软重启
1 4
7
硬重启，外设接口将掉电重启。
1
2
示例
1
_
模组软重启
O
AT+MREBOOT=0 M
OK
REBOOTING e
n
O
2. ML302S/ML307S/ML302A/ML307A/ML307G/ML307H/ML307R/ML307C/ML305A/ML305U/ML305M/ML551Z/ML307X/ML30
7M/ML307N仅支持0。
14

中移物联网有限公司
4. 扩展命令
本章详细描述了扩展AT命令和命令格式。
4.1. AT+CGMR 查询产品版本信息
该命令用于查询模组软件版本信息。
AT+CGMR
语法 响应
成功
<revision>
执行命令
OK
AT+CGMR
错误
+CME ERROR: <err>
参数描述
<revision>字符串，产品版本信息。
4
示例
7
1
查询版本号
2
1
AT+CGMR
_
MN316-DBRS-MBRH0C00
O
OK
M
e
Note: ML302S/ML307S查询版本号返回Revision: <revision>。
n
O
15

中移物联网有限公司
4.2. ATI 查询产品ID信息
该命令用于查询当前产品ID信息：厂商信息、产品型号以及版本信息。
ATI
语法 响应
成功
<manufacturer>
<model>
执行命令
<revision>
ATI OK
错误
+CME ERROR: <err>
参数描述
<manufacturer>字符串，制造商信息。
<model>字符串，MT型号信息。
<revision>字符串，产品版本信息。
示例
查询产品ID信息
4
7
ATI
1
CMCC 2
MN316 1
MN316-DBRS-MBRH0C00 _
OK O
M
Note: ML302S/ML307S产品版e本信息返回Revision: <revision>。
n
O
16

中移物联网有限公司
4.3. AT+MLOCKFREQ 指定搜索频率
该命令用于指定频率进行搜网。
AT+MLOCKFREQ
语法 响应
成功
+MLOCKFREQ: (list of supported <mode>s),(list of supported <earfcn>s),(list of supported
测试命令 <pci>s)
OK
AT+MLOCKFREQ=?
错误
+CME ERROR: <err>
成功
+MLOCKFREQ: <mode>[,<earfcn>[,<pci>]]
读取命令 [,<earfcn>[,<pci>]…]
OK
AT+MLOCKFREQ?
错误
+CME ERROR: <err>
设置命令 成功
4
AT OK
7
+MLOCKFREQ=<mode>[,<ea 1
错误
rfcn>,[<pci>] 2
1
[,<earfcn>[,<pci>]…] +CME ERROR: <err>
_
O
命令描述
M
3
设置命令：在AT+CFUN=0的前提下进行设置，立即生效，掉电不保存。
e
n
参数描述
O
4
<mode>整型，指定搜索类型并定义提供的参数。
0
解除锁定
1
锁定
2
设置优先频点
3. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML305M/ML551Z/ML307M/ML307N：掉电后锁频设置将
保存。
ML307G/ML307H/ML307X：掉电后锁频设置不保存。
4. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML305M/ML551Z/ML307M/ML307N：不支持模式2。
17

中移物联网有限公司
AT+MLOCKFREQ
5
<earfcn>整型，表示要搜索的EARFCN。范围：1~262143。
<pci>整型，E-UTRAN物理小区ID，十进制。范围：0~503。
示例
读取锁频状态
AT+MLOCKFREQ?
+MLOCKFREQ: 0
OK
设置锁频
AT+CFUN=0 //在AT+CFUN=0的前提下锁频命令才能生效
OK
AT+MLOCKFREQ=1,3686,186 //锁定EARFCN 3686
OK
AT+CFUN=1 //打开网络协议栈
OK
Note: ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML305M/ML307G/
Ml307H/ML307M/ML307N为LTE only产品，其中ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/
ML305A/ML305U/ML307G/ML307H参数<earfcn>仅支持0~599/1200~1949/2400~2649/2750~3449/
3450~3799/5180~5279/5730~5849/6150~6449/37750~38249/38250~38649/38650~39649/
39650~41589；ML305M/ML307M/ML307N参数<earfcn>仅支持0~599/1200~41949/2400~2649/2750~3449/
7
3450~3799/37750~38249/38250~38649/38650~39649/39650~41589；ML307X参数<earfcn>仅支持
1
0~599,1200~1949,2400~2649,3450~3799,36200~36349,37750~-38249,38250~38649,38650~39649,39650~41589。
2
1
_
O
M
e
n
O
5. ML551Z支持范围1~4294967294中支持的earfcn值，如报错则为预留值。
18

中移物联网有限公司
4.4. AT+MCSEARFCN 清除历史频点
该命令用于清除存储在模组中的历史频点。
AT+MCSEARFCN
语法 响应
成功
设置命令 OK
AT+MCSEARFCN[=<mode>] 错误
+CME ERROR: <err>
命令描述
执行成功后，重启模组生效。
参数描述
<mode>整型，缺省值0。
0
清除所有历史频点记录
示例
设置命令 4
7
AT+CFUN=0 //关闭协议栈 1
OK 2
AT+MCSEARFCN=0 //清除历史频点记录 1
_
OK
O
M
Note:
e
n
ML305M/ML307M/ML307N：重启后生效。
O
ML551Z：不支持该命令。
ML307G/ML307H：应执行AT+CFUN=0后使用本命令。
19

中移物联网有限公司
4.5. AT+MUESTATS 查询UE网络信息
该命令用于查询UE（User Equipment）当前的网络信息。
AT+MUESTATS
语法 响应
成功
+MUESTATS: (list of supported <type>s)
测试命令
OK
AT+MUESTATS=?
错误
+CME ERROR: <err>
成功
<type>="radio"
LTE/eMTC/NB-IoT制式：
+MUESTATS:
"radio",<rat>,<rsrp>,<rssi>,<tx_power>,<tx_time>,<rx_time>,<last_cellid>,<last_ecl>,<last_s
inr>,<last_earfcn>,<last_pci>,<rsrq>
OK
GSM制式：
+MUESTATS:
"radio",<rat>,<rxlev>,<rssi>[,<tx_power>][,<dtx_use4d>][,<rxlevsub>][,<rxlevfull>][,<rxqualsu
b>][,<rxqualfull>] 7
1
OK
2
<type>="cell"LTE/eMTC/1NB-IoT制式：
_
+MUESTATSO:
设置命令
"scell",<rat>,<mcc>,<mnc>,<earfcn>,<earfcn_offset>,<pci>,<rsrp>,<rsrq>,<rssi>,<sinr>[,<b
M
AT+MUESTATS[=<type>] andwidth>]
e+MUESTATS:
n "ncell",[<rat>],<mcc>,<mnc>,<earfcn>,<earfcn_offset>,<pci>,<rsrp>[,<bandwidth>]
O OK
GSM制式：
+MUESTATS:
"scell",<rat>,<mcc>,<mnc>,<lac>,<cellid>,<arfcn>[,<basic>][,<dtx_used>][,<amr_acs>][,<m
aio>][,<hsn>],<rxlev>,<rssi>[,<ber_lev>][,<Ec/Io_lev>]
+MUESTATS: "ncell",[<rat>],<mcc>,<mnc>,<lac>,<cellid>,<arfcn>,<basic>,<rxlev>,<rssi>
OK
<type>="bler"
+MUESTATS: "bler",<rlc_ul_bler>,<rlc_dl_bler>,
<mac_ul_bler>,<mac_dl_bler>,<total_bytes_transmitted>,<total_bytes_received>,<transpo
rt_blocks_sent>,<transport_blocks_received>,<transport_blocks_retransmitted>,<total_a
ck/nack_received>
OK
20

中移物联网有限公司
AT+MUESTATS
<type>="thp"
+MUESTATS: "thp",<rlc_ul>,<rlc_dl>,<mac_ul>,<mac_dl>
OK
<type>="sband"
+MUESTATS: "sband",<sband>
OK
错误
+CME ERROR: <err>
命令描述
设置命令：用于获取最新的操作统计信息，可携带一个可选参数，允许显示不同的统计数据集。不携
6 7
带<type>参数时，提供<type>为radio的默认值集； <type>=all将打印所有数据。
参数描述
<type>字符串，数据类型。
"radio"
Radio特定信息，当未驻留小区时，参数取无效值。
"cell"
服务小区、邻区信息。
4
"bler"
7
1
误块率信息
2
"thp" 1
_
吞吐量
O
"sband"
M
查询当前服务小区对应的BAND
e
"all" n
O
所有信息
8
<type>="radio"相关参数。
<rat>整型，无线接入模式。
0
无效的网络
1
6. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML305M/ML307M/ML307N不支持缺损<type>参数。
7. 单制式模组
(ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML305M/ML307G/ML307H/ML307M/ML307N)<rat>参
数为固定值；ML551Z仅当驻网成功后能查询到相关网络信息。
8. ML551Z驻留GSM网络时不支持参数<tx_power>、<dtx_used>、<rxlevsub>、<rxlevfull>、<rxqualsub>与
<rxqualfull>。
21

中移物联网有限公司
AT+MUESTATS
GSM
2
WCDMA
3
TDSCDMA
4
LTE
5
eMTC
6
NB-IoT
7
CDMA
8
EVDO
<rsrp>整型，参考信号接收功率，即RSRP（Reference Signal Receiving Power），单位0.1dBm。
有效值范围：-1650~-400-1400~-440；无效值-32768。
<rssi>整型，接收的信号强度指示，即RSSI（Received Signal Strength Indication），单位0.1dBm。
9
有效值范围：-1330~-250；无效值-32768。 4
7
<rsrq>整型，参考信号接收质量，单位0.1dB。有效值范围：-400~-1108-195~-30；无效值-32768。 10
2
<rxlev>整型，接收信号电平，描述收到信号强度（电平）的1统计参数，应映射到0~63之间的数值。
_
0 O
小于-110 dBm M
1 e
n
-110~-109 dBm
O
2
-109~-108 dBm
...
...
62
-49~-48 dBm
63
大于-48 dBm
99
无效值
9. ML307X：有效值范围为：-1410~0。
10. ML307X：有效值范围为：-200~-30。
22

中移物联网有限公司
AT+MUESTATS
11
<tx_power>整型，最近一次的发射功率，单位0.1dBm。有效值范围：-400~230；无效值-32768。
12
<tx_time>整型，上行累计的发送时长，单位ms，无效值0。
13
<rx_time>整型，下行累计的接收时长，单位ms，无效值0。
<last_cellid>十六进制字符串，上一次SIB1小区信息，十六进制28bit的CELL ID，未满4字节的高位补0。
14
有效值范围：0~0xFFFFFFE；无效值0xFFFFFFFF。
<last_ecl>整型，上一次ECL值，普通覆盖还是增强覆盖。有效取值0、1、2，0为普通覆盖，1、2为增强覆
15
盖；无效值255。
16
<last_sinr>整型，上一次SINR值，单位0.1dB，有效值范围：-180~300，无效值-32768。
<last_earfcn>整型，上一次EARFCN值，对应当前服务小区的下行频点号，有效值范围：0~68535，无效值
17
为-1。
18
<last_pci>整型，上一次PCI值，对应当前小区的物理ID，有效值范围： 0~503，无效值为65535。
19
<type>="cell"相关参数。返回当前服务小区、相邻每小区信息，如果当前没有驻留小区，则仅返回OK。
<mcc>字符串，移动国家代码。
<mnc>字符串，移动网络代码。
<earfcn>整型，绝对射频频道号。有效值范围：0~68535；无效值-1。
20
<earfcn_offset>整型，频偏。取值范围：0~4。
4
7
0
1
频偏值为2 2
1
1 _
O
频偏值为-1
M
2
频偏值为-0.5 e
n
3
O
频偏值为0
11. ML302S/ML307S/ML302A/ML307A/ML307R/ML305A/ML305M/ML551Z/ML307M/ML307N不支持该参数。
12. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305M/ML307G/ML551Z/ML307M/ML307N不支持该参数。
13. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305M/ML307G/ML551Z/ML307M/ML307N不支持该参数。
14. ML302S/ML307S/ML302A/ML307A/ML305A/ML305M/ML307M/ML307N不支持该参数。
15. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305M/ML307G/ML307H/ML307M/ML307N/ML551Z：不支持
该参数。
16. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305U：有效值范围为：-600~400。ML307X：有效值范围
为：-235~400。
17. ML302S/ML307S/ML302A/ML307A/ML305A/ML305M/ML307M/ML307N不支持该参数。
18. ML302S/ML307S/ML302A/ML307A/ML305A/ML305M/ML307M/ML307N不支持该参数。
19. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML305M/ML307G/ML307M/ML307N/ML551Z不支持邻
区信息查询。
ML551Z驻留GSM网络时不支持参数<basic>、<dtx_used>、<amr_acs>、<maio>、<hsn>、<ber_lev>与<Ec/Io_lev>。
20. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML305M/ML307M/ML307N/ML551Z不支
持<earfcn_offset>参数查询。
23

中移物联网有限公司
AT+MUESTATS
4
频偏值为1
<pci>整型，小区的物理ID。有效值范围：0~503；无效值65535。
<rsrp>整型， 参考信号接收功率，单位0.1dBm。有效值范围：-1650~-400-1400~-440；无效值-32768。
21
<rsrq>整型， 参考信号接收质量，单位0.1dB。有效值范围：-400~-108-195~-30；无效值-32768。
22
<rssi>整型，收到信号强度指示，单位0.1dBm。有效值范围：-1330~-250；无效值-32768。
23
<sinr>整型， 信号与干扰加噪声比，单位0.1dB。有效值范围：-180~300，无效值-32768。
24
<bandwidth>整型，带宽。
25
<type>="bler"相关参数。
<rlc_ul_bler>整型，RLC层上行误块率，单位0.01。
<rlc_dl_bler>整型，RLC层下行误块率，单位0.01。
<mac_ul_bler>整型，物理层上行误块率，单位0.01。
<mac_dl_bler>整型，物理层下行误块率，单位0.01。
<total_bytes_transmitted>整型，传输的总字节数。
<total_bytes_received>整型，接收的总字节数。
4
<transport_blocks_sent>整型，发送的传输块。
7
1
<transport_blocks_received>整型，接收的传输块。
2
1
<transport_blocks_retransmitted>整型，重传的传输块_。
O
<total_ack/nack_received>整型，接收的总ACK/NACK消息数。
M
26
<type>="thp"相关参数。
e
n
<rlc_ul>整型，RLC层上行吞吐量，单位bps。
O
<rlc_dl>整型，RLC层下行吞吐量，单位bps。
<mac_ul>整型，物理层上行吞吐量，单位bps。
<mac_dl>整型，物理层下行吞吐量，单位bps。
21. ML307X：有效值范围为：-200~-30。
22. ML307X：有效值范围为：-1410~-0。
23. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML307G/ML307H/ML551Z有效值范围
为：-600~400。ML307X：有效值范围为：-235~400。
24. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML305M/ML307G/ML307H/ML307M/ML307N不支
持<bandwidth>参数查询。
25. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML305M/ML307G/ML307M/ML307N/ML551Z不支持该
功能与对应的参数。
26. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML305M/ML307G/ML307M/ML307N/ML551Z不支持该
功能与对应的参数。
24

中移物联网有限公司
AT+MUESTATS
<type>="sband"相关参数。
<sband>服务小区频点对应的BAND号。
示例
查询radio信息
AT+MUESTATS="radio"
+MUESTATS: "radio",6,-1015,-933,230,289,421,0D1A84FE,0,211,3684,498,-108
OK
查询小区信息
AT+MUESTATS="cell"
+MUESTATS: "scell",6,460,00,3684,,498,-920,-108,-812,235
OK
查询block error的比率信息
AT+MUESTATS="bler"
+MUESTATS: "bler",0,0,40,0,211,74,5,2,2,2
OK
查询吞吐量信息
AT+MUESTATS="thp"
+MUESTATS: "thp",521,15,1085,154
OK 4
7
查询当前服务小区对应的BAND
1
2
AT+MUESTATS="sband"
1
+MUESTATS: "sband",8 _
OK O
M
e
n
O
25

中移物联网有限公司
4.6. AT+MCCID 读取ICCID
该命令用于读取ICCID (Integrated Circuit Card Identification)，即SIM卡ID号。
AT+MCCID
语法 响应
成功
测试命令 OK
AT+MCCID=? 错误
+CME ERROR: <err>
成功
+MCCID: <ICCID>
执行命令
OK
AT+MCCID
错误
+CME ERROR: <err>
参数描述
<ICCID>字符串，USIM卡ID号码。
示例
4
7
测试命令
1
2
AT+MCCID=?
1
OK
_
O
读取ICCID
M
AT+MCCID
+MCCID: 89861118216007272115 e
OK n
O
26

中移物联网有限公司
4.7. AT+MADC 读取ADC电压
该命令用于读取对应ADC电压值。
AT+MADC
语法 响应
成功
+MADC: (list of supported<channel>s)
测试命令
OK
AT+MADC=?
错误
+CME ERROR: <err>
成功
+MADC: <voltage>
设置命令
OK
AT+MADC[=<chanel>]
错误
+CME ERROR: <err>
命令描述
该命用于从指定的ADC通道中获取电压值。
4
参数描述
7
27 1
<channel>整型，ADC通道，默认值0。
2
1
0
_
ADC0 O
1 M
ADC1
e
n
2
O
ADC2
3
28
ADC3
<voltage>整型，ADC读取电压值，单位：mV，取值范围根据不同平台而定。
示例
测试命令
27. ML307A/ML307M仅支持ADC0。
ML305A/ML305U/ML305M/ML307N/ML307N/ML307R/ML307C/ML307X支持ADC0、ADC1。
ML302A/ML305A/ML307A/ML307R/ML307C/ML307G/ML307H不支持缺省<channel>参数。
28. 仅ML302A支持ADC3。
27

中移物联网有限公司
AT+MADC
AT+MADC=?
+MADC: (0-1)
OK
查询ADC1电压
AT+MADC=0
+MADC: 977
OK
Note: ML302S/ML307S/ML551Z暂不支持该命令。
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
28

中移物联网有限公司
4.8. AT+MGPIO 控制GPIO
该命令用于配置GPIO模式和输出电平，读取GPIO状态。
AT+MGPIO
语法 响应
成功
+MGPIO: (list of supported<pin>s),(list of supported<control>s),(list of supported<pull>s)
测试命令
OK
AT+MGPIO=?
错误
+CME ERROR: <err>
成功
只设置<pin>，查询指定GPIO配置：
+MGPIO: <pin>,<dir>[,<pull>]
OK
设置命令 <control>=0，读取指定GPIO输入：
AT +MGPIO: <pin>,<value>
+MGPIO=<pin>[,<control>[,< OK
pull>]]
其他模式：
4
OK
7
错误 1
2
1
+CME ERROR: <err>
_
O
命令描述
M
当只设置<pin>时，用于查询指定GPIO配置；
当<control>设置为0~2时，用于设e置指定GPIO模式，并可设置参数<pull>；
n
当<control>设置为3时，用于设置高阻态，不可设置参数<pull>。
O
参数描述
29
<pin>整型，GPIO对应的模组引脚。
<control>整型，引脚控制。
29. ML302S支持Pin2、Pin7、Pin31、Pin35、Pin 42~Pin 46。
ML302A支持0~14（实际pin脚为：41、42、43、44、50、51、78、89、90、91、92、96、135、136、137）。
ML307A/ML307R-DC/ML307R-DL/ML307M-DL/ML307M-DH支持0~3（实际pin脚为：76、77、86、87）。
ML307C支持0~2（实际pin脚为76、86、87）。
ML307M-DA/ML307N支持0、1、3 (实际pin脚为：76、77、87)。
ML307R-BL支持0~4。
ML305U支持0~15（实际pin脚为：1、2、3、4、10、14、16、56、57、60、62、63、64、65、66、67）。
ML305M支持1、2、7~10（实际pin脚为：10、12、63、65、66、67）。
ML307G-DL支持0~3（实际pin脚为：76、77、86、87）。
ML307H-GU支持0~2(实际pin脚为76、86、87)；ML307H-DU支持0-3(实际引脚为76、77、86、87)。
ML307X：支持Pin76、Pin77、Pin86、Pin87。
29

中移物联网有限公司
AT+MGPIO
0
读取输入
1
输出低电平
2
输出高电平
3
30
高阻态
<pull>整型，引脚模式，默认值0。
0
浮空
1
下拉
2
上拉
<dir>整型，引脚输入输出状态。
0
输入 4
7
1
1
输出 2
1
2 _
31 O
高阻态
M
<value>整型，读取输入的电平值，当<control>=0时返回。
e
0 n
O
低电平
1
高电平
示例
测试命令
AT+MGPIO=?
+MGPIO: (0-49),(0-3),(0-2)
OK
查询Pin 34当前GPIO配置
30. ML302S/ML302A/ML307A/ML307R/ML307C/ML305M/ML307G/ML307H/ML307X/ML307M/ML307N不支持。
31. ML302S/ML302A/ML307A/ML307R/ML307C/ML307G/ML307H不支持。
30

中移物联网有限公司
AT+MGPIO
AT+MGPIO=34
+MGPIO: 34,1,0
OK
设置Pin 34为输出高电平
AT+MGPIO=34,2
OK
读取Pin 34电平值
AT+MGPIO=34,0
+MGPIO: 34,1
OK
Note: ML307S/ML305A/ML551Z暂不支持该命令。
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
31

中移物联网有限公司
4.9. AT+MLED 开关指示灯功能
该命令用于设置指示灯功能。
AT+MLED
语法 响应
成功
+MLED: (list of supported<led_mode>s),(list of supported<enable>s)
测试命令
OK
AT+MLED=?
错误
+CME ERROR: <err>
成功
<enable>不设置，查询指定<led_mode>配置：
设置命令
+MLED: <led_mode>,<enable>
OK
AT
+MLED=<led_mode>[,<enabl <enable>设置，为设置命令：
e>]
OK
错误
4
+CME ERROR: <err>
7
1
命令描述
2
1
该命令控制打开/关闭模组状态指示灯与模组网络状态指示灯，配置将写入Flash，重启仍然生效。
_
O
参数描述
M
<led_mode>整型，指示灯功能选择。
e
0 n
O
模组网络状态指示灯（未注网时：灯熄灭；注网中：灯快闪，高电平50ms，低电平1000ms；注网成功：灯
32
慢闪，高电平50ms，低电平2000ms。）
1
模组运行状态指示灯
<enable>整型，是否使能指示灯，默认值0。
0
关闭指示灯
32. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML307G/ML307H/ML307M/ML307N未注网时灯快闪，高电平50ms，低电
平950ms；注网成功后灯慢闪，高电平100ms，低电平1900ms。
ML305A未注网时灯熄灭，注网成功后灯常亮。
ML305U未注网时灯快闪，高电平100ms，低电平1000m；注网成功后灯慢闪，高电平100ms，低电平2000m。
ML307X：未注网时灯快闪，高电平50ms，低电平950ms；注网成功后灯慢闪，高电平100ms，低电平1900ms，睡眠、关机时网络
灯熄灭。
32

中移物联网有限公司
AT+MLED
1
开启指示灯
示例
测试命令
AT+MLED=?
+MLED: (0-1),(0-1)
OK
ML302S测试命令
AT+MLED=?
+MLED: <led_mode>,<enable>
OK
设置
AT+MLED=0,1 //使能网络状态灯
OK
AT+MLED=1,1 //使能模组状态灯
OK
查询
AT+MLED=0 //查询网络状态灯使能状态
+MLED: 0,1
OK 4
7
AT+MLED=1 //查询模组状态灯使能状态
1
+MLED: 1,1
2
OK
1
_
O
Note: ML307S/ML551Z暂不支持该命令。
M
e
n
O
33

中移物联网有限公司
4.10. AT+MLPMCFG 低功耗管理配置
该命令用于低功耗相关功能项的配置。
AT+MLPMCFG
语法 响应
成功
测试命令 +MLPMCFG: (list of supported<cmd>s)
AT+MLPMCFG=? 错误
+CME ERROR: <err>
成功
仅设置"psind"，查询协议栈低功耗状态是否上报：
设置命令（配置协议栈低功耗
+MLPMCFG: "psind",<psind_enable>
状态是否上报URC） OK
AT 设置<psind_enable>，为设置命令：
+MLPMCFG="psind"[,<psind
OK
_enable>]
错误
+CME ERROR: <err>
4
成功
7
仅设置"sleepind"，查询进入、退出1睡眠是否上报：
2
设置命令（配置进入、退出睡
+MLPMCFG: "sleepind",<slee1pind_report>
眠是否上报URC） OK _
O
AT 设置<sleepind_report>，为设置命令：
M
+MLPMCFG="sleepind"[,<sle
OK
epind_report>] e
n错误
O
+CME ERROR: <err>
成功
仅设置"sleepmode"，查询睡眠锁定或解除模式：
设置命令（配置睡眠锁定或解
除模式） +MLPMCFG: "sleepmode",<sleep_mode>
OK
AT
设置<sleep_mode>和<permanent>，为设置命令：
+MLPMCFG="sleepmode"[,<
sleep_mode>[,<permanent OK
>]]
错误
+CME ERROR: <err>
34

中移物联网有限公司
AT+MLPMCFG
成功
仅设置"delaysleep"，查询延时休眠时间：
设置命令（配置达到休眠条件
+MLPMCFG: "delaysleep",<delay_sleep>
后延时进入休眠时间） OK
AT 设置<delay_sleep>，为设置命令：
+MLPMCFG="delaysleep"[,<
OK
delay_sleep>]
错误
+CME ERROR: <err>
成功
仅设置"dtr"，查询DTR引脚控制睡眠使能：
设置命令（配置DTR引脚控制 +MLPMCFG: "dtr",<enable>
33 OK
睡眠）
设置<enable>，为设置命令：
AT
+MLPMCFG="dtr"[,<enable>] OK
错误
+CME ERROR: <err>
URC（<sleepind_report>配置
4
大于1时，当进入睡眠时将上 +MLPMENTER: <sleep_level> 7
报） 1
2
URC（<sleepind_report>配置 1
_
大于1时，当退出睡眠时将上 +MLPMEXIT: <sleep_level>
O
报）
M
URC（<psind_enable>=1时，
e
协议栈低功耗状态改变时上n+MLPMPSTA: <protocol_status>
报） O
命令描述
该命令可配置和读取休眠相关模式配置。其中psind用于设置协议栈低功耗状态上报模式，sleepind用于设置
模组睡眠状态上报模式，模组进入睡眠状态为投票机制，需多方投票满足才可进入相应的睡眠等级，协议栈
进入PSM状态只是模组睡眠状态投票机制中的其中一项，如用户只关心模组睡眠状态，可只设置sleepind项配
置。
参数描述
34
<cmd>字符串，命令标识符。
33. 仅ML307X支持。
34. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML307G/ML307H：仅支持
sleepind、sleepmode、delaysleep；ML305U/ML305M/ML307M/ML307N暂时仅支持sleepmode。ML307X：仅支持
sleepind、sleepmode、delaysleep、dtr。
35

中移物联网有限公司
AT+MLPMCFG
psind
协议栈低功耗状态上报
sleepind
睡眠状态上报
sleepmode
睡眠模式
delaysleep
达到休眠条件后延时进入休眠时间
dtr
DTR引脚控制睡眠
<psind_enable>整型，协议栈低功耗状态是否开启URC上报，默认值0。
0
关闭上报
1
打开上报
35
<sleep_level>整型，睡眠等级，进入、退出对应睡眠等级。
1
浅睡眠 4
7
2
1
深度睡眠 2
1
<protocol_status>整型，协议栈低功耗状态。 _
O
0
M
连接态
e
1 n
O
Idle态
2
PSM
36
<sleepind_report>整型，睡眠等级，进入、退出对应睡眠等级是否开启URC上报，默认值0。
0
关闭上报
1
仅上报浅睡眠进入及退出
2
35. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML307G/ML307H：仅支持2。ML307X：仅支持1。
36. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML307G/ML307H：仅支持0、2。ML307X：仅支持0、1，默认值
0，且掉电后将恢复为默认值。
36

中移物联网有限公司
AT+MLPMCFG
仅上报深度睡眠进入及退出
3
上报浅、深度睡眠进入及退出
37
<sleep_mode>整型，配置模组睡眠模式，默认值2。
0
不允许进入睡眠
1
仅允许进入浅睡眠
2
允许浅睡眠和深睡眠
<permanent>整型，参数是否掉电保存，默认值0。<permanent>参数缺省时，为不保存该次配置到NV。
0
不保存
1
保存
38
<delay_sleep>整型，达到休眠条件时，延迟进入休眠的时间，范围0~300s，默认值0。
39
<enable>整型，DTR引脚控制睡眠，默认值0。
4
0
7
1
禁用
2
1 1
_
启用
O
示例 M
设置协议栈低功耗状态上报 e
n
AT+MLPMCFG="psOind",1
OK
+MLPMPSTA: 1
+MLPMPSTA: 2
设置模组休眠状态上报
AT+MLPMCFG="sleepind",2
OK
AT+MLPMCFG="sleepind"
+MLPMCFG: "sleepind",2
OK
37. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML305M/ML307G/ML307H/ML307M/ML307N：仅支持
0、2，且ML307A/ML307R/ML307C/ML305A/ML307S/ML307G/ML307H默认值0。ML307X：仅支持0、1，默认值1。
38. ML307H：延迟休眠仅通过串口唤醒有效，并且需要小于等于115200波特率下才能够唤醒。
ML307X：范围为0~255，默认值为10，解锁睡眠锁定的命令，无延迟进入睡眠时间，条件满足会立即进入睡眠。
39. 仅ML307X支持，ML307X默认置为1。
37

中移物联网有限公司
AT+MLPMCFG
+MLPMENTER: 2
+MLPMEXIT: 2
设置模组延时进入休眠
AT+MLPMCFG="delaysleep",30
OK
设置模组休眠模式
AT+MLPMCFG="sleepmode",1,1
OK
Note:
ML551Z暂不支持该命令。
ML307G/ML307H在网络不稳定的情况下，存在反复休眠唤醒的情况，属于正常现象。
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
4.11. AT+MBAND 设置支持的BAND及优先级
该命令用于网络频段相关功能项的配置及查询。
AT+MBAND
语法 响应
成功
+MBAND: (list of supported<n>s)
测试命令
OK
AT+MBAND=?
错误
+CME ERROR: <err>
成功
单制式模组:
+MBAND: n[,n[,n[…]]]
OK
读取命令
多制式模组：
AT+MBAND?
+MBAND: <key>,n[,n[,n[…]]]
OK
错误
4
+CME ERROR: <err>
7
1
成功
2
执行命令（所有制式恢复默认
1
OK
配置） _
O
错误
AT+MBAND
M
+CME ERROR: <err>
e
n成功
设置命令（单制式模O组BAND配
OK
置）
错误
AT+MBAND=n[,n[,n[…]]]
+CME ERROR: <err>
成功
设置命令（多制式模组BAND配
置） OK
AT 错误
+MBAND=<key>,n[,n[,n[…]]]
+CME ERROR: <err>
命令描述
39

中移物联网有限公司
AT+MBAND
设置命令：设置需要支持的BAND，BAND的优先级顺序为<n>的排列先后顺序，设置的BAND需要在UE能力
40
范围内，否则设置不生效。设置完后，重启生效。
读取命令：返回当前设置支持的BAND信息。
测试命令：返回UE实际支持的所有BAND信息。
参数描述
<key>字符串，模组制式，不区分大小写。
GSM
GSM制式
WCDMA
WCDMA制式
TDSCDMA
TDSCDMA制式
LTE
LTE制式
eMTC
eMTC制式
NBIOT
NB-IoT制式
4
CDMA
7
1
CDMA制式
2
<n>整型，BAND号。 1
_
GSM制式： O
M
0
恢复所有BAND e
n
1 O
GSM450
2
GSM480
3
GSM750
4
GSM850
5
GSM P900
6
40. ML305M/ML307M/ML307N/ML551Z不支持BAND的优先级顺序配置。
40

中移物联网有限公司
AT+MBAND
GSM E900
7
GSM R900
8
GSM1800
9
GSM1900
NB-IoT制式：
0
恢复所有BAND
1
BAND 1
3
BAND 3
5
BAND 5
8
BAND 8
4
20
7
BAND 20 1
2
28 1
_
BAND 28
O
41
LTE制式：
M
0
e
n
恢复所有BAND
O
1
BAND_LTE_1
2
BAND_LTE_2
3
BAND_LTE_3
4
BAND_LTE_4
5
BAND_LTE_5
6
41. ML307M/ML307N仅支持1,3,5,8,34,39,40,41。ML307X:仅支持1,3,5,8,34,38,39,40,41。
41

中移物联网有限公司
AT+MBAND
BAND_LTE_6
7
BAND_LTE_7
8
BAND_LTE_8
9
BAND_LTE_9
...
...
64
BAND_LTE_64
示例
测试命令
AT+MBAND=?
+MBAND:(3,5,8)
OK
单制式模组设置
AT+MBAND=8,5,3 //设置模组所支持的BAND为3、5、8
4
OK 7
1
多制式模组设置
2
1
AT+MBAND="NBIOT",3,5,8
_
OK O
查询 M
AT+MBAND? e
n
+MBAND: "LTE",8
O
…
OK
Note: ML302S/ML307S/ML302A/ML307A/ML305A不支持该命令。
42

中移物联网有限公司
4.12. AT+MPSRAT 设置模组制式及优先级
该命令用于模组制式相关功能项的设置及查询。
AT+MPSRAT
语法 响应
成功
+MPSRAT: (list of supported<PreferredAct>s)
测试命令
OK
AT+MPSRAT=?
错误
+CME ERROR: <err>
成功
+MPSRAT: <PreferredAct>,[<PreferredAct>…]
读取命令
OK
AT+MPSRAT?
错误
+CME ERROR: <err>
成功
设置命令
OK
AT
+MPSRAT=<PreferredAct>[,< 错误 4
7
PreferredAct>…]
+CME ERROR: <err> 1
2
1
命令描述
_
O
设置命令：设置UE制式及优先级，优先级为<PreferredAct>依次排列顺序，需要在UE能力范围内，否则设置
不生效。设置完后，重启生效。 M
读取命令：读取UE制式及优先级。
e
n
参数描述
O
<PreferredAct>字符串，配置模组无线接入制式。
GSM
GSM制式
WCDMA
WCDMA制式
TDSCDMA
TDSCDMA制式
LTE
LTE制式
eMTC
eMTC制式
43

中移物联网有限公司
AT+MPSRAT
NBIOT
NB-IoT制式
CDMA
CDMA制式
NR
NR制式
示例
设置
AT+MPSRAT="LTE","GSM" //设置模组支持的制式优先级为LTE、GSM。
OK
Note: ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML305M/ML307M/
ML307N/ML551Z/ML307G/ML307H/ML307X不支持该命令。
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
4.13. AT+MDIALUPCFG 设置拨号协议
该命令用于拨号功能相关设置及查询。
AT+MDIALUPCFG
语法 响应
成功
+MDIALUPCFG: "mode",(list of supported<mode>s)
+MDIALUPCFG: "workmode",(list of supported<workmode>s)
+MDIALUPCFG: "nicmaxcnt",(list of supported<nicmaxcnt>s)
+MDIALUPCFG: "auto",(list of supported<enable>s),(list of
测试命令 supported<conn_interface>s),(list of supported<cid>s)
[+MDIALUPCFG: "dhcpv4",(list of supported<dhcp_en>s),,(list of
AT+MDIALUPCFG=?
supported<dhcp_start>s),(list of supported<dhcp_end>s),(list of
supported<lease_time>s)]
OK
错误
+CME ERROR: <err>
成功
不设置<mode>，查询当前拨号方式配置：
设置USB拨号方式 +MDIALUPCFG: "mode",<mode>
OK
4
AT
设置<mode>： 7
+MDIALUPCFG="mode"[,<mo 1
de>] OK 2
1
错误 _
O
+CME ERROR: <err>
M
成功
e
n不设置<workmode>，查询当前拨号拨号工作模式：
O
设置拨号工作模式 +MDIALUPCFG: "workmode",<mode>
OK
AT
设置<workmode>：
+MDIALUPCFG="workmode"
[,<workmode>] OK
错误
+CME ERROR: <err>
设置最大支持网卡数量 成功
不设置<nicmaxcnt>，查询当前最大支持网卡数据配置：
AT
+MDIALUPCFG="nicmaxcnt"[ +MDIALUPCFG: "netnum",<netnum>
,<nicmaxcnt>] OK
45

中移物联网有限公司
AT+MDIALUPCFG
设置<nicmaxcnt>：
OK
错误
+CME ERROR: <err>
成功
不设置<enable>,<conn_interface>,<cid>，查询当前自动拨号配置：
设置开机自动拨号
+MDIALUPCFG: "auto",<enable>,<conn_interface>[,<cid>]
OK
AT
+MDIALUPCFG="auto"[,<ena 设置<enable>[,<conn_interface>[,<cid>]]：
ble>[,<conn_interface>[,<cid
OK
>]]]
错误
+CME ERROR: <err>
成功
不设置<dhcp_en>,<dhcp_ip>,<dhcp_start>,<dhcp_end>,<lease_time>，查
询当前dhcpv4配置：
设置DHCP服务
+MDIALUPCFG: "dhcpv4",<dhcp_en>,<dhcp_ip>,<dhcp_start>,<dhcp_end>,<lease_time>
AT
OK
+MDIALUPCFG="dhcpv4"[,<
4
dhcp_en>,<dhcp_ip>,<dhcp_ 设置<dhcp_en>,<dhcp_ip>,<dhcp_star7t>,<dhcp_end>,<lease_time>：
start>,<dhcp_end>,<lease_ti 1
OK
me>] 2
1
错误
_
O
+CME ERROR: <err>
M
成功
e
n+MDIALUPCFG: "mode",<mode>
O +MDIALUPCFG: "workmode",<workmode>
+MDIALUPCFG: "nicmaxcnt",<nicmaxcnt>+MDIALUPCFG:
查询命令 "auto",<enable>,<conn_interface>[,<cid>]
[+MDIALUPCFG:
AT+MDIALUPCFG?
"dhcpv4",<dhcp_en>,<dhcp_ip>,<dhcp_start>,<dhcp_end>,<lease_time>]
OK
错误
+CME ERROR: <err>
命令描述
设置命令，用于模组拨号协议配置。设置完后，重启生效。拨号需要使用的PDP上下文和鉴权信息请使用
AT+CGDCONT和AT+CGAUTH命令设置，也可使用AT+MUECONFIG="defapn"命令设置默认PDP上下文信息。
参数描述
46

中移物联网有限公司
AT+MDIALUPCFG
42
<mode>整型，拨号模式，默认值0。
0
RNDIS
1
ECM
2
NCM
11仅 支持该模式。
ML307X
43
自适应
<enable>整型，开机自动拨号标识，默认0。
0
关闭自动拨号
1
打开自动拨号
44
<workmode>整型，拨号工作模式，默认0。
0
路由模式
4
1
7
网卡模式 1
2
<nicmaxcnt>整型，设置最大支持网卡数量，设置成功后重1启生效，范围参考测试命令返回结果，默认值为
_
1。
O
<conn_interface>整型，自动拨号物理接口。
M
0
e
n
USB
O
1
网口(以太网卡)
<cid>整型，pdp上下文标识符，范围见AT+CGDCONT命令。打开自动拨号时需设置，关闭自动拨号是不可设
45
置。
46
<dhcp_en>整型，DHCP开关。
42. ML302S/ML307S/ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML305M/ML551Z/ML307G/Ml307H/ML307M/ML3
07X不支持NCM拨号模式；其中ML307X默认值11，优先RNDIS；ML307M
RNDIS仅支持上位机Windows系统拨号，ECM仅支持上位机Linux系统拨号。
43. 仅ML307X支持该模式。
44. ML302A/ML302S/ML305A/ML307A/ML307R/ML307C/ML307S/ML305M/ML551Z/ML307G/Ml307H/ML307X/ML307M不支持。
45. ML302A/ML302S/ML305A/ML307A/ML307R/ML307C/ML307S/ML305M/ML551Z/ML307G/ML307H/ML307X/ML307M不支持。
46. ML302A/ML302S/ML305A/ML307S/ML305M/ML307M/ML307X不支持该参数；ML307A/ML307R/ML307C/ML307G/ML307H不支
持0。
47

中移物联网有限公司
AT+MDIALUPCFG
0
关闭dhcp
1
打开dhcp
<dhcp_ip>字符串，DHCP服务器IP。
<dhcp_start>整型，地址池起始IP的最后一段地址，范围：0-255，默认100。
<dhcp_end>整型，地址池终止IP的最后一段地址，范围：0-255，默认200。
<lease_time>整型，租赁期限，范围：3600-86400，默认86400。
示例
设置拨号方式
AT+MDIALUPCFG="mode",1
OK
查询拨号方式
AT+MDIALUPCFG="mode"
+MDIALUPCFG: "mode",1
OK
47
查询所有配置
4
AT+MDIALUPCFG? 7
1
+MDIALUPCFG: "mode",1
2
+MDIALUPCFG: "workmode",1
1
+MDIALUPCFG: "nicmaxcnt",1
_
+MDIALUPCFG: "auto",0,0
O
OK
M
测试命令
e
AT+MDIALUPCFG=? n
+MDIALUPCFG: "modeO",(0-2)
+MDIALUPCFG: "auto",(0-1),(0-1),(1)
OK
Note:
ML302S/ML307S仅支持设置拨号方式，不支持设开机自动拨号。
ML302A/ML302S/ML305A/ML307S/ML305M/ML307M/ML307X不支持DCHP配置。
ML307N不支持该命令。
47. ML302A/ML305A/ML307A/ML307R/ML307C不支持。
48

中移物联网有限公司
4.14. AT+MDIALUP （上位机）拨号连网
该命令用于控制拨号连网功能。
AT+MDIALUP
语法 响应
成功
+MDIALUP: (list of supported<cid>s),(list of
测试命令 supported<connect>s)
OK
AT+MDIALUP=?
错误
+CME ERROR: <err>
成功
[+MDIALUP: <cid>,<connect>[,<ipv4>,<v4_gw>,<v4_dns1>[,<v4_dns2>]]]
读取命令 [+MDIALUP: <cid>,<connect>[,<ipv6>,<v6_gw>,<v6_dns1>[,<v6_dns2>]]]
OK
AT+MDIALUP?
错误
+CME ERROR: <err>
成功
设置命令
4
OK
7
AT
1
错误
+MDIALUP=<cid>,<connect> 2
1
+CME ERROR: <err>
_
O
[+MDIALUP: <cid>,<connect>[,<ipv4>,<v4_gw>,<v4_dns1>[,<v4_dns2>]]]
URC
[+MMDIALUP: <cid>,<connect>[,<ipv6>,<v6_gw>,<v6_dns1>[,<v6_dns2>]]]
e
命令描述
n
O
设置置命令，进行拨号或断开操作，操作结果上报中，<connect>为0表示拨号失败，<connect>为1表示拨号
成功。
查询命令用来查询拨号成功后的网络信息，用于配置上位机网络。
拨号需要使用的PDP上下文和鉴权信息请使用AT+CGDCONT和AT+CGAUTH命令设置，也可使
用AT+MUECONFIG="defapn"命令设置默认PDP上下文信息。
Ethernet拨号时只上报状态，不上报ip地址。
参数描述
<cid>整型，pdp上下文标识符，暂仅为1有效。
<connect>整型，操作类型。
0
断开连接
49

中移物联网有限公司
AT+MDIALUP
1
建立连接
<ipv4>字符串，网卡ipv4地址。
<v4_gw>字符串，网关地址，为0.0.0.0时表示不可见。
<v4_dns1>字符串，DNS服务器首选地址。
<v4_dns2>字符串，DNS服务器备选地址。
<ipv6>字符串，网卡ipv6地址。
<v6_gw>字符串，网关地址。
<v6_dns1>字符串，DNS服务器首选地址。
<v6_dns2>字符串，DNS服务器备选地址。
示例
拨号上网
AT+MDIALUP=1,1
OK
+MDIALUP: 1,1,"10.43.159.12","10.43.159.12","183.230.126.225","183.230.126.224"
+MDIALUP: 1,1,"2409:8961:2a03:8797::1","2409:8961:2a03:8797::1","2409:8060:20ea:0201::1","2409:8060:20ea:0101::1"
断网 4
7
AT+MDIALUP=1,0 1
OK 2
1
查询拨号信息 _
O
AT+MDIALUP?
M
+MDIALUP: 1,1,"10.43.159.12","10.43.159.12","183.230.126.225","183.230.126.224"
+MDIALUP: 1,1,"2409:8961:2a03:8797::1","2409:8961:2a03:8797::1","2409:8060:20ea:0201::1","2409:8060:20ea:0101::1"
e
OK
n
O
Note:
ML307M仅激活一路<cid>；且<cid>为1时，不允许使用AT+MDIALUP命令进行断开连接操作。
ML307N不支持该命令。
50

中移物联网有限公司
4.15. AT+MCHIPINFO 获取芯片温度、电压等信息
该命令用于查询芯片相关的部分信息。
AT+MCHIPINFO
语法 响应
成功
+MCHIPINFO: (list of supported<cmd>s)
测试命令
OK
AT+MCHIPINFO=?
错误
+CME ERROR: <err>
成功
+MCHIPINFO: "vbat",<vbat>
查询芯片电压
OK
AT+MCHIPINFO="vbat"
错误
+CME ERROR: <err>
成功
+MCHIPINFO: "temp",<temperature>
查询芯片温度
OK
4
AT+MCHIPINFO="temp"
错误 7
1
+CME ERROR: <err> 2
1
_
命令描述
O
该命令用于获取VBAT电压值以及芯片温度值。
M
参数描述 e
n
<cmd>字符串，命令O标识符。
vbat
48
查询芯片电压
temp
49
查询芯片温度
<vbat>整型，VBAT电压值，单位：mV。
<temperature>整型，温度值，单位：摄氏度。
示例
设置
48. ML302S/ML307S/ML302A/ML307A/ML305A不支持该参数。
49. ML307R/ML307C不支持该参数。
51

中移物联网有限公司
AT+MCHIPINFO
AT+MCHIPINFO="vbat" //查询VBAT电压
+MCHIPINFO: "vbat",3971
OK
AT+MCHIPINFO="temp" //查询芯片温度
+MCHIPINFO: "temp",25
OK
Note: ML551Z/ML307R/ML307C暂不支持该命令。
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
52

中移物联网有限公司
4.16. AT+MEMMTIMER 使能/禁用EMM定时器URC上报
该命令用于设置EMM定时器的URC上报功能。
AT+MEMMTIMER
语法 响应
成功
设置命令 OK
AT+MEMMTIMER=<enable> 错误
+CME ERROR: <err>
URC +MEMMTIMER: <backoff_timerId>,<event>,<period>,<remaining>
命令描述
该命令用于使能/禁用EMM层相关定时器（Timer）的URC上报。当启用URC上报时，模块在对应的定时器启
动、超时或停止时，会上报URC以指示当前的定时器状态，并能够在定时器停止时，指示定时器剩余的时
间。
参数描述
<enable>整型，使能/禁用URC上报，默认值0。
0
4
禁用上报 7
1
1
2
使能上报 1
_
<backoff_timerId>整型，使能/禁用URC上报，O默认值0。 50
M
0
T3346 e
n
1 O
T3347
2
T3348
3
T3396
51
<event>整型，指示对应定时器发生的事件。
50. T3346用 于 EMM拥塞控制和APN拥塞控制，用于记录在Attach Reject/TAU Reject/RAU Reject/Service Reject之后，到终端再次发起
Attach/TAU/RAU/Service请求的时间。T3448用于控制面传送用户数据的拥塞控制，Attach Accept、TAU
Accept、Service Accept消息携带了非零的定时器T3448。SERVICE REJECT消息携带的原因为EMM cause #22
“Congestion”，以及携带了非零的定时器T3448。T3396如果ESM原因是＃26“资源不足”，则网络可能包含此IE。
ML307G/ML307H仅支持T3346/T3396。
51. ML307X：仅支持参数值0、1、2。
53

中移物联网有限公司
AT+MEMMTIMER
0
启动定时器
1
停止定时器
2
定时器超时
3
去使能定时器，仅适用于T3346和T3396。
<period>整型，定时器的时间。单位：毫秒。
<remaining>整型，定时器停止时剩余的时间。单位：毫秒。
示例
测试
AT+MEMMTIMER=?
+MEMMTIMER: (0-1)
OK
设置
AT+MEMMTIMER=1 //使能EMM定时器URC上报
OK
4
+MEMMTIMER: 1,0,10800000,10800000 //T3412 EXT定时器退出 7
1
2
Note: ML302S/ML307S/ML302A/ML307A/ML307R/ML1307C/ML305A/ML305U/ML305M/ML551Z/
_
ML307M/ML307N不支持该命令。
O
M
e
n
O
54

中移物联网有限公司
4.17. AT+MIPCALL 建立模组数据连接
AT+MIPCALL
语法 响应
成功
+MIPCALL: (list of supported<operation>s),(list of supported<cid>s)
测试命令
OK
AT+MIPCALL=?
错误
+CME ERROR: <err>
成功
设置命令
OK
AT +MIPCALL: <cid>,<status>,<IP1>[,<IP2>]
+MIPCALL=<operation>[,<ci
错误
d>,]
+CME ERROR: <err>
命令描述
发起建立连接请求，如果此contextID已建立连接，则直接返回OK；执行结果以URC形式上报，连接成功返回
连接成功状态及本地的IP地址，如果通过+CGDCONT命令设置为IPv4v6双栈且获取到IPv4及IPv6地址，此处
返回上述两个地址。操作的cid如果未配置需要先使用+CGDCONT命令配置。
4
如果未指定参数<cid>，表示对所有配置了PDP上下文的cid进行操作。
7
1
参数描述
2
1
<cid>整型，指定连接所属PDP上下文（范围根据实际确定）。
_
O
<operation>整型，操作类型。
M
0
e
断开连接
n
1 O
建立连接
<IP1>字符串，建立连接后本地对应的IPv4地址。
<IP2>字符串，建立连接后本地对应的IPv6地址。
<status>整型，连接状态。
0
断开连接
1
已连接
示例
55

中移物联网有限公司
AT+MIPCALL
测试命令
AT+MIPCALL=?
+MIPCALL: (0-1),(0-16)
OK
激活contextID
AT+MIPCALL=1,1
OK
+MIPCALL: 1,1,"10.81.41.153"
Note:
ML302A/ML307A/ML307R/ML307C/ML302S/ML307S/ML307G/ML307H/ML307M/ML307N/
ML307X设置命令不支持参数<cid>缺损；ML551Z暂不支持该命令；ML307M/ML307N/ML307X仅激活
一路<cid>时，不支持使用AT+MIPCALL命令对该路<cid>进行断开连接操作。
若AT+MIPCALL=1,1命令执行成功，则模组和上位机均建立数据连接，若AT+MIPCALL=0,1命令执行
成功，则上位机断开数据连接。
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
56

中移物联网有限公司
4.18. AT+MUECONFIG 配置UE基础行为
该命令用于配置UE基础行为。其参数包含一个功能和一个控制该功能操作的值。
AT+MUECONFIG
语法 响应
成功
+MUECONFIG: (list of supported <cmd>s)
测试命令
OK
AT+MUECONFIG=?
错误
+CME ERROR: <err>
成功
不设置<defapn>，则查询模组默认APN：
设置默认APN +MUECONFIG: "defapn",<PDP_type>,<defapn>
OK
AT
设置<defapn>：
+MUECONFIG="defapn"[,<PD
P_type>[,<defapn>]] OK
错误
+CME ERROR: <err>
4
7
成功
1
不设置<autoconn>，则查询开2机或重启是否自动连接网络：
1
设置自动连接网络 +MUECONFIG: "autoco_nn",<autoconn>
OK O
AT
设置M<autoconn>：
+MUECONFIG="autoconn"[,<
autoconn>] eOK
n
O错误
+CME ERROR: <err>
成功
不设置<release_version>，则查询模组当前发布版本：
设置发布版本 +MUECONFIG: "relver",<release_version>
OK
AT
设置<release_version>：
+MUECONFIG="relver"[,<rele
ase_version>] OK
错误
+CME ERROR: <err>
57

中移物联网有限公司
AT+MUECONFIG
成功
不设置<as_rai>，则查询模组当前是否支持AS RAI：
设置AS RAI +MUECONFIG: "asrai",<as_rai>
OK
AT
设置<as_rai>：
+MUECONFIG="asrai"[,<as_r
ai>] OK
错误
+CME ERROR: <err>
成功
不设置<two_harq>，则查询模组当前否支持2HARQ和2536TBS：
设置2HARQ和2536TBS +MUECONFIG: "2harq",<two_harq>
OK
AT
设置<two_harq>：
+MUECONFIG="2harq"[,<two
_harq>] OK
错误
+CME ERROR: <err>
成功
4
不设置<multi_carrier>，则查询模组当前7否支持Multi Carrier R14增强功能：
1
设置Multi Carrier R14增强功能 +MUECONFIG: "emulticar",<emulti2_carrier>
OK 1
_
AT
设置<multi_cOarrier>：
+MUECONFIG="emulticar"[,<
M
emulti_carrier>] OK
e错误
n
O +CME ERROR: <err>
成功
不设置<reestablish>，则查询模组当前是否支持重建连接：
设置重建连接功能 +MUECONFIG: "reestablish",<reestablish>
OK
AT
设置<reestablish>：
+MUECONFIG="reestablish"[,
<reestablish>] OK
错误
+CME ERROR: <err>
成功
设置高精度授时功能
不设置<hpt_enable>，<hpt_certainty>，则查询高精度授时参数：
58

中移物联网有限公司
AT+MUECONFIG
+MUECONFIG: "hpt",<hpt_enable>,<hpt_certainty>
OK
AT 设置<hpt_enable>，<hpt_certainty>：
+MUECONFIG="hpt"[,<hpt_e
OK
nable>,<hpt_certainty>]
错误
+CME ERROR: <err>
成功
不设置<simenable>，<simtype>，则查询模组当前SIM是否自动激活：
设置SIM卡激活功能 +MUECONFIG: "simenable",<simenable>,<simtype>
OK
AT
设置<simenable>，<simtype>：
+MUECONFIG="simenable"[,
<simenable>[,<simtype>]] OK
错误
+CME ERROR: <err>
成功
不设置<simswap>，<simslot>，则查询模组当前卡位及是否支持热插拔功
能：
设置SIM卡卡位及热插拔功能
+MUECONFIG: "simswap",<simswap>,<simslot>
4
OK
AT 7
1
+MUECONFIG="simswap"[,<s 设置<simslot>，<hotplugen>：
2
imswap>[,<simslot>]] 1
OK
_
错误 O
M
+CME ERROR: <err>
e
成功
n
O不设置<ims_enable>，<ims_rat>，则查询模组当前是否启用IMS域，以及当
前网络制式：
启用/禁用IMS域能力
+MUECONFIG: "ims",<ims_enable>[,<ims_rat>]
OK
AT
+MUECONFIG="ims"[,<ims_e 设置<ims_enable>，<ims_rat>：
nable>[,<ims_rat>]]
OK
错误
+CME ERROR: <err>
成功
启用/禁用网络MAC控制器功能
不设置<mac_enable>，则查询网络MAC控制器功能使能情况：
59

中移物联网有限公司
AT+MUECONFIG
+MUECONFIG: "mac",<mac_enable>
OK
AT
设置<mac_enable>：
+MUECONFIG="mac"[,<mac_
OK
enable>]
错误
+CME ERROR: <err>
命令描述
该命令用于配置默认APN、发布版本、R14特性等UE基础行为，该命令配置均保存至模组Flash，重启后仍然
生效。
参数描述
52
<cmd>字符串，命令标识符。
defapn
配置模组默认APN，并保存Flash。
autoconn
配置模组在上电或重启后是否自动尝试连接到网络。启用时，它将设
置AT+CFUN=1并从USIM读取PLMN，并且它将使用由网络提供的APN。
relver
4
7
配置发布版本，仅支持版本R13和R14，并保存Flash。
1
asrai 2
1
配置模组是否支持AS RAI，并保存Flash，R14特性，需在<release_version>配置为14前提下生效。
_
2harq O
配置模组是否支持2HARQ和2536TMBS，并保存Flash，R14特性，需在<release_version>配置为14前提下
生效。
e
n
emulticar
O
配置模组是否支持Multi Carrier
R14增强功能，包含RA和paging，并保存Flash，R14特性，需在<release_version>配置为14前提下生效。
reestablish
配置模组是否支持重建连接，并保存Flash，R14特性，需在<release_version>配置为14前提下生效。
hpt
配置高精度授时功能。
simenable
配置SIM卡激活与去激活功能。
simswap
配置SIM卡卡位及热插拔功能。
52. ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML305M /ML307M/ML307N仅支持autoconn。ML307X：仅支持
defapn、autoconn、relver、emulticar。
60

中移物联网有限公司
AT+MUECONFIG
ims
启用/禁用IMS域能力，设置后需执行重新驻网操作(重启、AT+CFUN=0等)生效。
mac
启用/禁用网络MAC控制器功能。
53
<PDP_type>字符串，PDP类型，保存至Flash。
IP
IPv4协议
IPV6
IPv6协议
IPV4V6
IPv4/v6协议
PPP
Point to Point Protocol (IETF STD 51 [104])
Non-IP
无IP
54
<defapn>字符串，模组默认APN配置，保存至Flash。
<autoconn>整型，在上电或重启后是否自动尝试连接到网络，保存至NV。默认值1。
0 4
7
不自动尝试连接到网络
1
2
1
1
自动尝试连接到网络 _
O
55
<release_version>整型，模组R13/R14/R15/R16版本选择。
M
13
e
R13 n
O
14
R14
15
R15
16
R16
56
<as_rai>整型，R14特性，是否支持AS RAI，并保存NV。
0
53. ML302A/ML307A/ML307R/ML307C/ML305A/ML305U不支持。ML307X：不支持PPP、Non-IP。
54. ML302A/ML307A/ML307R/ML307C/ML305A/ML305U不支持。
55. ML302A/ML307A/ML307R/ML307C/ML305A/ML305U不支持；ML307G/ML307H仅支持13；ML307X：支持8~14。
56. ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML307G/Ml307H/ML307X不支持。
61

中移物联网有限公司
AT+MUECONFIG
禁用AS RAI
1
启用AS RAI
57
<two_harq>整型，R14特性，是否支持混合自动重传请求，并保存NV。
0
禁用混合自动重传
1
启用混合自动重传
58
<emulti_carrier>整型，R14特性，是否支持多载波调制，并保存NV。
0
禁用多载波调制
1
启用多载波调制
59
<reestablish>整型，R14特性，是否支持连接态重建，并保存NV。
0
禁用连接态重建
1
4
启用连接态重建
7
<hpt_enable>整数类型，表示高精度授时功能开关。 1
2
0 1
_
关闭高精度授时功能 O
1 M
开启高精度授时功能
e
n
<hpt_certainty>整数类型，表示精度，范围：0 ~ 2^32-1，单位25ns。
O
<simenable>整数类型，表示激活或去激活vSIM虚拟卡/SIM硬卡。
0
去激活对应<simtype>的卡，Modem将不会使用对应的卡注册网络。
1
激活对应<simtype>的卡，Modem将使用对应的卡注册网络。
<simtype>整数类型，vSIM卡/SIM硬卡信息的索引值，默认值为0。
0
SIM硬卡
57. ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML307G/ML307H/ML307X不支持。
58. ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML307G/ML307H不支持。
59. ML302A/ML307A/ML307R/ML307C/ML305A/ML305U/ML307G/ML307H/ML307X不支持。
62

中移物联网有限公司
AT+MUECONFIG
1
vSIM虚拟卡
60
<simswap>整数类型，表示当前卡槽是否支持热插拔，默认值为1。
0
当前卡座不支持热插拔
1
当前卡座支持热插拔
<simslot>整数类型，表示当前生效的SIM卡卡槽编号。
<ims_enable>整数类型，打开或关闭IMS域能力，默认值为1。
0
关闭
1
打开
<ims_rat>整数类型，所需控制的网络制式，默认值为0。选择单个制式仅配置该制式使能情况，不影响其
他制式结果，如：仅使能NR制式IMS域功能，关闭LTE制式IMS域功能需执行AT+MUECONFIG="ims",1,11
及AT+MUECONFIG="ims",0,4两条指令。
0
模组所支持的所有制式 4
7
4 1
2
LTE
1
11 _
O
NR
M
<mac_enable>整数类型，启用/禁用网络MAC控制器功能，使用网口(以太网卡)拨号前，需先使能该控制器功
e
能，默认值为0。
n
O
0
关闭
1
打开
示例
测试
AT+MUECONFIG=?
+MUECONFIG: "defapn",,
+MUECONFIG: "autoconn",(0,1)
+MUECONFIG: "relver",(13,14)
+MUECONFIG: "asrai",(0,1)
+MUECONFIG: "2harq",(0,1)
60. ML307R-BL的SIM2支持热插拔。
63

中移物联网有限公司
AT+MUECONFIG
+MUECONFIG: "reestablish",(0,1)
+MUECONFIG: "emulticar",(0,1)
OK
设置
AT+MUECONFIG="defapn","IPV4V6","CMNBIOT" //配置默认APN
OK
Note:
ML302S/ML307S/ML551Z暂不支持该命令。
ML307M/ML307N设置<autoconn>为1时，若开机成功连接到网络则模组和上位机均建立数据连接。
ML307X设置<autoconn>为0时，无论是执行AT+CFUN=0/AT+CFUN=1重启协议栈或是重启模组，都
仅小区驻留，不自动发起注册建立模组数据连接，需用户使用AT+MIPCALL命令重新建立模组数据连接。
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
4.19. AT+MEID 读取SIM eID
该命令用于请求读取SIM卡的eID信息。
AT+MEID
语法 响应
成功
+MEID: <eID>
执行命令
OK
AT+MEID
错误
+CME ERROR: <err>
成功
+MEID: <eID>
读取命令
OK
AT+MEID?
错误
+CME ERROR: <err>
命令描述
该命令用于读取SIM卡的eID。
4
参数描述
7
1
<eID>字符串，SIM卡的eID。
2
1
示例
_
O
设置
M
AT+MEID
+MEID: 112103FFFFF783265665 e
OK n
O
65

中移物联网有限公司
4.20. AT+MPWMDATA 设置pwm数据
该命令用于设置pwm数据。
AT+MPWMDATA
语法 响应
成功
读取命令
+MPWMDATA: <channel0>,<period>,<duty>,<period1>,<duty1>...,<periodN>,<dutyN>
AT+MPWMDATA? +MPWMDATA: <channel1>,<period>,<duty>,<period1>,<duty1>...,<periodN>,<dutyN>
...
设置命令
AT
成功
+MPWMDATA=<channel>,<p
eriod>,<duty>,<period1>,<d OK
uty1>...,<periodN>,<dutyN
>61
设置命令 +MPWMDATA: <channel>,<period>,<duty>,<period1>,<duty1>...,<periodN>,<dutyN>
AT+MPWMDATA=<channel> 通道未配置则报错
命令描述
4
如果只是单个波形重复，只需要输入一组周期及占空比，否则根据需要增7加数据组数；查询命令列出当前所
有有波形数据配置的通道数据；仅指定通道时查询当前指定通道的配1置数据。
2
1
参数描述
_
O
<channel>pwm通道号，0~1，以模组实际支持的通道数量为准。
M
62
<period>单个pwm波周期，单位us。
e
<duty>单个pwm波占空比，n单位百分比。
O
Note:
仅ML307A/ML307R-DC/ML307C/ML307R-DL/ML307G/ML307H/ML307X支持该命令。
ML307G/ML307H的pwm波周期只能是2.56us的整数倍，输入命令时向上取整。
61.ML307A系列仅支持单个波形重复。
62. ML307A/ML307C为10~10000；ML307G为3~655；ML307H为10~2521。需注意较低周期时，某些暂空比有一定误差，请以测试为
准。
66

中移物联网有限公司
4.21. AT+MPWMCTRL pwm控制
该命令用于pwm控制。
AT+MPWMCTRL
语法 响应
设置命令
成功
AT
+MPWMCTRL=<channel>,<o OK
noff>[,<cycles>]
命令描述
该命令用于pwm控制。
参数描述
<onoff>pwm控制开关。
0
关闭
1
启动
63
<cycles>pwm数据组循环次数。 4
7
0 1
2
一直循环输出
1
_
＞0
O
波形组个数
M
e
Note: 仅ML307A/ML307R-DC/ML307C/ML307R-DL/ML307G/ML307H/ML307X支持该命令。
n
O
63. ML307A/ML307C暂不支持该参数。
67