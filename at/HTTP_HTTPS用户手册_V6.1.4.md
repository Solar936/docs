4
HTTP/HTTPS用户7手册
1
2
1
_
O
M
e
n
版本：V6.1O.4
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
V1.0.0 初版
V2.0.0 新增ML302S、ML307S相关内容。
V3.0.0 新增ML307A相关内容。
V4.0.0 新增ML302A相关内容。
V5.0.0 新增ML305U相关内容。
V6.0.0 新增MN328相关内容。
V6.0.1 新增ML305A相关内容。
V6.0.2 新增MN319、ML307A-DL相关内容。
V6.0.3 新增ML305A-DL相关内容。
V6.0.4 新增ML305M相关内容。
4
新增ML307R相关内容；
7
V6.0.5 新增MN316A相关内容； 1
2
移除MN328相关内容。
1
_
V6.0.6 新增MN326相关内容。
O
V6.0.7 新增ML307G相关内容。
M
V6.0.8 e新增ML307M-DL、ML307R-BL、ML307R-ML、ML307R-MC相关内容。
n
V6.0.9 O新增ML307G-DC相关内容。
V6.1.0 新增ML307M-DA相关内容。
新增ML307M <header> 长度为1~1024字节的脚注信息；
V6.1.1
新增MR880A相关内容。
V6.1.2 新增ML307H-DU、ML307H-GU相关内容。
V6.1.3 新增ML307C-DL-CN相关内容。
新增ML307N-DC/ML307N-DL相关内容；
新增ML307M-DH相关内容；
V6.1.4
新增ML307X-DB/ML307X-DC相关内容；
新增MN326A-DG相关内容。

中移物联网有限公司
目录
服务与支持.............................................................................................................................................................................................................................ii
文档声明................................................................................................................................................................................................................................iii
关于文档.................................................................................................................................................................................................................................v
1. 引言.....................................................................................................................................................................................................................................7
1.1. 适用型号...............................................................................................................................................................................................................7
2. AT命令概述......................................................................................................................................................................................................................9
2.1. AT命令语法.......................................................................................................................................................................................................10
2.2. AT命令响应.......................................................................................................................................................................................................11
3. HTTP/HTTPS协议AT命令.........................................................................................................................................................................................12
3.1. AT+MHTTPCFG 参数设置...........................................................................................................................................................................13
3.2. AT+MHTTPCREATE 创建HTTP实例.......................................................................................................................................................21
3.3. AT+MHTTPHEADER 设置HTTP特定报头..............................................................................................................................................22
3.4. AT+MHTTPCONTENT 设置HTTP CONTENT数据............................................................................................................................24
3.5. AT+MHTTPREQUEST 发送HTTP请求....................................................................................................................................................26
3.6. AT+MHTTPREAD 读取HTTP数据.............................................................................................................................................................28
3.7. AT+MHTTPDEL 删除HTTP实例................................................................................................................................................................30
3.8. AT+MHTTPTERM 终止HTTP传输............................................................................................................................................................31
3.9. AT+MHTTPDLFILE HTTP下载文件.........................................................................................................................................................32
3.10. +MHTTPURC URC信息上报....................................................................................................................................................................35
4
4. 示例...................................................................................................................................................7...............................................................................38
1
4.1. HTTP示例..........................................................................................................................................................................................................39
2
4.2. HTTPS示例.......................................................................................................................................................................................................42
1
4.3. 缓存模式...................................................................................._........................................................................................................................44
4.4. HTTP下载文件.......................................................O..........................................................................................................................................45
5. 错误码.................................................................M.............................................................................................................................................................47
e
n
O

中移物联网有限公司
1. 引言
本文档详细介绍了中移物联网基于HTTP/HTTPS通信协议定义的相关AT命令及其操作流程。文档中如有未
尽细节，请咨询中移物联网技术支持。
1.1. 适用型号
Table 1. 适用模组
模组系列 模组子型号
MN318 MN318-BX/MN318-LC/MN318-LX
MN316 MN316-DBRS/MN316-DLVS
MN316-S MN316-S-DLVS
MN316A MN316A-D/MN316A-DC
MN326A MN326A-DG
MN319 MN319-DL
MN326 MN326-X
4
ML302A ML302A-DCLM/ML302A-DSLM/ML302A-GCLM/ML302A-GSLM
7
1
ML302S ML302S-DNLM
2
1
ML307A-DCLN/ML307A-DSLN/ML307A-GCLN/ML307A-GSLN/ML30
ML307A _
7A-DL O
ML307S ML3M07S-DNLM
e
ML307X ML307X-DB/ML307X-DC
n
O
ML305U ML305U-DBLN
ML305A ML305A-DC/ML305A-DS/ML305A-DL
ML305M ML305M-DSLM
ML307R ML307R-DC/ML307R-DL/ML307R-BL/ML307R-ML/ML307R-MC
ML307G ML307G-DL/ML307G-DC
MR880A MR880A/MR880A Mini-PCIe/MR880A M.2
ML307M ML307M-DL/ML307M-DA/ML307M-DH
ML307H ML307H-DU/ML307H-GU
ML307C ML307C-DL-CN
7

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
+CME ERROR: <err>或者+CMS ERROR:
<err> 或者 +CIS ERROR: <err>或者+CME 启用了扩展错误报告（+CMEE），其中<err>表示错
ERROR:<err>或者+CMS ERROR:<err> 或者 +CIS 误码或详细错误信息
ERROR:<err>
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
3. HTTP/HTTPS协议AT命令
本章详细描述了HTTP/HTTPS协议相关的AT命令和命令格式。
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
3.1. AT+MHTTPCFG 参数设置
该命令用于设置客户端实例相关的通用配置参数。
AT+MHTTPCFG
语法 响应
成功
+MHTTPCFG: (list of supported<cmd>s),(list of supported<httpid>s),
测试命令
OK
AT+MHTTPCFG=?
错误
+CME ERROR: <err>
成功
[+MHTTPCFG: "header",<httpid>[,<header_len>,<header>]
+MHTTPCFG: "chunked",<httpid>,<chunked_mode>
+MHTTPCFG: "cached",<httpid>,<cached>[,<cache_length>]
+MHTTPCFG: "timeout",<httpid>,<conn_timeout>,<rsp_timeout>,<input_timeout>
+MHTTPCFG: "encoding",<httpid>,<input_format>,<output_format>
读取命令 +MHTTPCFG: "ssl",<httpid>,<ssl_enable>[,<ssl_id>]
+MHTTPCFG: "cid",<httpid>[,<cid>]
AT+MHTTPCFG?
+MHTTPCFG: "fragment",<httpid>,<frag_size>,<interval>
+MHTTPCFG: "urlencode",<httpid>,<urlencode_mode>
[...]]
OK 4
7
错误 1
2
+CME ERROR: <err> 1
_
成功 O
<htMtpid>和<header>不设置，查询所有实例的通用报头：
e[+MHTTPCFG: "header",<httpid>,<header_len>,<header>
n[…]]
O OK
设置命令（通用报头设置）
<httpid>设置，<header>不设置，查询指定实例的通用报头：
AT
[+MHTTPCFG: "header",<httpid>,<header_len>,<header>]
+MHTTPCFG="header"[,<htt
OK
pid>[,<header>]]
所有参数都设置，设置通用报头：
OK
错误
+CME ERROR: <err>
成功
设置命令（传输模式设置）
<httpid>和<chunked_mode>不设置，查询所有实例的传输模式：
13

中移物联网有限公司
AT+MHTTPCFG
[+MHTTPCFG: "chunked",<httpid>,<chunked_mode>
[…]]
OK
<httpid>设置，<chunked_mode>不设置，查询指定实例的传输模式：
AT +MHTTPCFG: "chunked",<httpid>,<chunked_mode>
+MHTTPCFG="chunked"[,<ht OK
tpid>[,<chunked_mode>]] 所有参数都设置，设置传输模式：
OK
错误
+CME ERROR: <err>
成功
<httpid>、<cached>、<cache_length>不设置，查询所有实例的缓存模式：
[+MHTTPCFG: "cached",<httpid>,<cached>[,<cache_length>]
[…]]
OK
<httpid>设置，<cached>、<cache_length>不设置，查询指定实例的缓存模
式：
设置命令（缓存模式设置）
+MHTTPCFG: "cached",<httpid>,<cached>[,<cache_length>]
AT
OK 4
+MHTTPCFG="cached"[,<htt
7
pid>[,<cached>[,<cache_len
<httpid>和<cached>设置，<cache
1
_length>不设置，设置缓存模式，不设置
缓存空间大小： 2
gth>]]]
1
OK _
O
所以参数都设置，设置缓存模式和缓存空间大小：
M
OK
e
n错误
O
+CME ERROR: <err>
成功
<httpid>、<conn_timeout>、<rsp_timeout>、<input_timeout>不设置，查询
设置命令（超时时间设置） 所有实例的超时时间：
AT [+MHTTPCFG: "timeout",<httpid>,<conn_timeout>,<rsp_timeout>[,<input_timeout>]
[…]]
+MHTTPCFG="timeout"[,<htt
OK
pid>[,<conn_timeout>[,<rsp_
timeout>[,<input_timeout>] <httpid>设置，<conn_timeout>、<rsp_timeout>、<input_timeout>不设置，
]]] 查询指定实例的超时时间：
+MHTTPCFG: "timeout",<httpid>,<conn_timeout>,<rsp_timeout>[,<input_timeout>]
OK
14

中移物联网有限公司
AT+MHTTPCFG
<httpid>设置，<conn_timeout>、<rsp_timeout>、<input_timeout>部分设置
或全部设置，设置相应的超时时间：
OK
错误
+CME ERROR: <err>
成功
<httpid>、<input_format>、<output_format>不设置，查询所有实例的输入
输出编码模式：
[+MHTTPCFG: "encoding",<httpid>,<input_format>,<output_format>
[…]]
OK
设置命令（输入输出编码模式
设置） <httpid>设置，<input_format>、<output_format>不设置，查询指定实例的
输入输出编码模式：
AT
+MHTTPCFG="encoding"[,<h +MHTTPCFG: "encoding",<httpid>,<input_format>,<output_format>
ttpid>[,<input_format>[,<out OK
put_format>]]]
<httpid>设置，<input_format>、<output_format>部分设置或全部设置，设
置相应的编码模式：
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
<httpid>、<sOsl_enable>、<ssl_id>不设置，查询所有实例的SSL模式：
[+MMHTTPCFG: "ssl",<httpid>,<ssl_enable>[,<ssl_id>]
[…]]
e
OK
n
设置命令（SSL模式O设置）
<httpid>设置，<ssl_enable>、<ssl_id>不设置，查询指定实例的SSL模式：
AT +MHTTPCFG: "ssl",<httpid>,<ssl_enable>[,<ssl_id>]
+MHTTPCFG="ssl"[,<httpid>[ OK
,<ssl_enable>[,<ssl_id>]]] <httpid>设置，<ssl_enable>、<ssl_id>部分设置或全部设置，设置SSL模
式：
OK
错误
+CME ERROR: <err>
成功
设置命令（cid设置）
<httpid>、<cid>不设置，查询所有实例的cid：
15

中移物联网有限公司
AT+MHTTPCFG
[+MHTTPCFG: "cid",<httpid>[,<cid>]
[…]]
OK
<httpid>设置，<cid>不设置，查询指定实例的cid：
AT +MHTTPCFG: "cid",<httpid>[,<cid>]
+MHTTPCFG="cid"[,<httpid> OK
[,<cid>]] <httpid>、<cid>设置，设置cid：
OK
错误
+CME ERROR: <err>
成功
<httpid>、<frag_size>、<interval>不设置，查询所有实例的数据输出流控模
式：
[+MHTTPCFG: "fragment",<httpid>,<frag_size>,<interval>
[…]]
OK
设置命令（数据输出流控设
置） <httpid>设置，<frag_size>、<interval>不设置，查询指定实例的数据输出流
控模式：
AT
+MHTTPCFG="fragment"[,<h +MHTTPCFG: "fragment",<httpid>,<frag_size>,<inte4rval>
ttpid>[,<frag_size>[,<interval OK 7
1
>]]] <httpid>设置，<frag_size>、<2interval>部分设置或全部设置，设置数据输出
1
流控模式：
_
O
OK
M
错误
e
n+CME ERROR: <err>
O
成功
<httpid>、<urlencode_mode>不设置，查询所有实例的URL编码模式：
[+MHTTPCFG: "urlencode",<httpid>,<urlencode_mode>
[…]]
设置命令（URL编码设置）
OK
AT
<httpid>设置，<urlencode_mode>不设置，查询指定实例的URL编码模式：
+MHTTPCFG="urlencode"[,<
+MHTTPCFG: "urlencode",<httpid>,<urlencode_mode>
httpid>[,<urlencode_mode
OK
>]]
<httpid>、<urlencode_mode>设置，设置URL编码模式：
OK
错误
16

中移物联网有限公司
AT+MHTTPCFG
+CME ERROR: <err>
命令描述
读取命令将返回所有已创建实例的所有配置参数；设置命令中，如果只设置<cmd>（命令标识符），将返回
所有已创建实例的当前配置参数，如果只设置<cmd>和<httpid>（客户端实例id），将返回当前实例的当前配
置参数，如果其他参数也设置，将设置相应模式。需创建实例后，才可进行参数设置。
参数描述
1
<cmd>命令标识符。
header
通用报头设置，应用于实例生命周期，设置时需按key: value的形式分字段进行设置，模组会保存所有设置字
段。
chunked
传输模式设置。普通模式（Content-Length模式）与Chunked模式的使用流程存在差异。普通模式先设置
content数据，后发送请求；Chunked模式先发送请求，后设置content数据。
cached
缓存模式设置，设置为缓存模式时，接收数据将缓存在模组中，请求完成时上报提示，用户手动读取。
timeout
超时时间设置，包含连接超时、响应超时、数据输入超时。
encoding
4
输入输出数据编码设置。 7
1
ssl
2
1
SSL使能设置，用于HTTPS。
_
cid O
PDP上下文cid设置。 M
fragment
e
数据输出流控设置，用于n数据输出打印时控制分包输出。
O
urlencode
URL编码设置，使能后，模组将自动对请求的path进行编码，不对字母数字以及-_.!~*'();/?:@&=+$,⼈进行编
码。
2
<httpid>客户端实例id，AT+MHTTPCREATE创建成功后返回，0~3。
<header_len>通用报头长度。
<header>通用报头，应用于实例生命周期，设置时需按key: value的形式分字段进行设置，模组会保存所有设置
3
字段。
<chunked_mode>传输模式，默认值0。
1. MN318/ML307H-GU：不支持"ssl"设置，不支持ssl加密传输。MN316/MN326：不支持"cid"设置。MN319/MN316A/MN326A：不支
持"ssl"和"cid"设置，不支持ssl加密传输。
2. MN319/MN316A/MN326A：<httpid>参数范围0~1。
3. ML302A/ML305A/ML307A/ML307G/ML307M/ML307R/ML307C/ML307H/ML307N：<header>参数输入长度范围为1~1024。
17

中移物联网有限公司
AT+MHTTPCFG
0
content_length模式
1
chunked模式
<cached>缓存模式，默认值0。
0
非缓存模式
1
缓存模式
4
<cache_length>缓存空间，默认值4K（4096字节），1~8192字节。
<conn_timeout>连接超时时间，0~180s，默认值60s。
<rsp_timeout>服务器响应超时时间，0~60s，默认值0s（不超时）。
5
<input_timeout>数据输入超时时间，0~180s，默认值10s。（AT命令输入数据，并采用>模式输入时。）
6
<input_format>数据输入编码模式，默认值0。模组将按设置的编码格式把输入数据转换为原始数据。
0
ASCII字符串（原始数据）
1 4
7
HEX字符串
1
2 2
1
带转义的字符串 _
O
<output_format>数据输出编码模式，默认值0。模组将按设置的编码格式把原始数据转换为指定编码格式数据
7 M
然后输出。
e
0
n
ASCII字符串（O原始数据）
1
HEX字符串
<ssl_enable>SSL使能，默认值0。
0
不使用SSL（普通连接，HTTP。）
4. 当<cached>为0时（非缓存模式），查询时不显示<cache_length>。此外，MN316A/MN326A支持范围1~4096字节。
5. MN318/MN316/MN319/MN326/MN316A/MN326A：不支持数据模式输入，因此不支持<input_timeout>参数设
置。ML302S/ML307S/ML302A/ML305A/ML307A/ML307G/ML305U/ML305M/ML307R/ML307C/ML307M/ML307H/ML307N/ML30
7X：支持范围1~120s。
6. 该参数影响如下命令的数据输入：AT+MHTTPCFG="header"、AT+MHTTPHEADER、AT+MHTTPCONTENT、AT
+MHTTPREQUEST；MR880A不支持2。
7. 该参数影响如下 URC 上报的数据输出：+MHTTPURC: "header"、+MHTTPURC: "content"。
18

中移物联网有限公司
AT+MHTTPCFG
1
使用SSL（安全连接，HTTPS。）
8
<ssl_id>SSL上下文索引号，指定当前实例使用的SSL上下文，范围可参见文档《SSL用户手册》。
9
<cid>PDP上下文id，指定当前实例使用的PDP上下文，范围与平台相关。
10
<frag_size>接收到数据后，数据上报的最大分包大小，0~1024，默认值0（实际接收包大小输出）。
<interval>接收到数据后，数据分包输出的时间间隔，0~2000ms，默认值0（无间隔）。
<urlencode_mode> URL编码使能，默认值0。
0
不编码
1
编码（不对字母数字以及-_.!~*'();/?:@&=+$,⼈进行编码。）
示例
测试命令
AT+MHTTPCFG=?
+MHTTPCFG: ("header","chunked","cached","timeout","encoding","ssl","cid","fragment","urlencode"),(0-3),
OK
header设置
4
7
AT+MHTTPCFG="header",0,"Connection: close"
1
OK
2
1
header查询
_
O
AT+MHTTPCFG="header",0
+MHTTPCFG: "header",0,19,Connection: clMose
OK
e
n
O
8. ML302A/ML305A/ML307A/ML307G/ML307S/ML305U/ML305M/ML307R/ML307C/ML307X/ML307M/ML307H-DU/ML307N：<ssl
_id>参数范围0~5。
9. MN316/MN319/MN326/MN316A/MN326A：暂不支持<cid>参数设置，默认不指定。MN318：默认不指定，指定时需保证支持cid已
激活。ML302S/ML307S：支持<cid>参数设置，默认值1。ML305U：支持<cid>参数设置，范围1~7，默认值
1。ML307G/ML302A/ML305A/ML307A/ML305M/ML307R/ML307C/ML307H/ML307X/ML307M/ML307N：支持<cid>参数设置，
默认不指定，指定时需保证支持cid已激活。MR880A：<cid>参数仅支持7。
10. 分包是在网络包的基础上进行再次分包，例如本包接收到1400字节数据，<frag_size>设置为500，则此包数据将分为3次输出
（500、500、400）。
19

中移物联网有限公司
Note:
模组默认报头中，普通模式包含Host、Content-Length字段，Chunked模式包含Host、Transfer-
Encoding字段。报头设置可通过两条命令实现，AT+MHTTPCFG="header" 用于设置通用报头，应用于实
例生命周期；AT+MHTTPHEADER 用于设置特定报头，单次请求有效，主要用于在不同请求时设置变化
的字段。如果通用报头中存在特定报头相同字段，模组将在发送请求时自动替换为特定报头设置值。
模组中单次输入AT命令的长度受平台单条命令长度限制，数据过长时建议分多次输入。
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
3.2. AT+MHTTPCREATE 创建HTTP实例
该命令用于创建HTTP实例，初始化实例相关内容。
AT+MHTTPCREATE
语法 响应
成功
测试命令 OK
AT+MHTTPCREATE=? 错误
+CME ERROR: <err>
成功
+MHTTPCREATE: <httpid>
设置命令
OK
AT+MHTTPCREATE=<host>
错误
+CME ERROR: <err>
命令描述
11
最多可同时建立4个实例，需先创建实例，才可使用其他相关命令。
参数描述
4
7
<host>服务器域名或者IP地址，格式如"http://domain.com"或"http://domain.com:80"。":80"是指定的服务器端
1
口号，当不指定端口号时，HTTP默认端口80，HTTPS默认端口443。
2
1
12
<httpid>客户端实例id，0~3。
_
O
示例
M
创建实例
e
n
AT+MHTTPCREATE="http://www.baidu.com:80"
O
+MHTTPCREATE: 0
OK
11. MN319/MN316A/MN326A：最多可同时创建2个实例。
12. MN319/MN316A/MN326A：<httpid>参数范围0~1。
21

中移物联网有限公司
3.3. AT+MHTTPHEADER 设置HTTP特定报头
该命令用于设置特定报头。
AT+MHTTPHEADER
语法 响应
成功
+MHTTPHEADER: (list of supported<httpid>s),(list of supported<eof>s),(list of
测试命令 supported<length>s),
OK
AT+MHTTPHEADER=?
错误
+CME ERROR: <err>
成功
只设置<httpid>，查询当前已设置的报头：
[+MHTTPHEADER: <httpid>,<length>,<header>]
OK
<eof>设置，<length>和<header>不设置，以CTRL+Z发送数据，以ESC取
消发送：
>(输入数据)
设置命令
OK
4
AT
<length>设置，<header>不设置，输入7数据达到指定长度或达到超时时间时
+MHTTPHEADER=<httpid>[,
发送数据： 1
<eof>[,<length>[,<header>]]] 2
>(输入数据) 1
_
OK
O
所有参数均设置：
M
OK
e
n
错误
O
+CME ERROR: <err>
命令描述
单次请求有效，请求结束后自动清空。特定报头主要为在不同请求中存在变化的字段，如果通用报头中同样
存在该字段，将在发送请求时自动替换为特定报头的值。设置命令中不带<header>参数时，模组将进入数据
13
模式，以>提示，然后输入数据。
参数描述
14
<httpid>客户端实例id，0~3。
13. AT+MHTTPCFG="encoding"中设置的<input_format>参数，只对直接命令中带<header>的输入模式有效，对数据模式输
入无效，数据模式直接使用原始数据。
14. MN319/MN316A/MN326A：<httpid>参数范围0~1。
22

中移物联网有限公司
AT+MHTTPHEADER
<eof>数据输入指示。
0
header输入结束标记，需请求完成或清空后才可再次输入。
1
后续还有header数据输入。
2
清空已有header内容。
<length>输入数据长度，0~4096，数据模式下不可设置为0，命令中直接输入数据时，设置为0，不对数据长
度进行校验，设置大于0，将对输入数据的长度进行校验（ASCII字符串和带转义的字符串输入模式下：校验
实际命令中输入字符串长度是否与指定长度相等；HEX字符串输入模式下：校验实际命令中输入字符串长度
15
是否是指定长度的两倍）。
<header>特定报头数据，单次请求有效，请求结束后自动清空，如果AT+MHTTPCFG="header"中
16
设置了同样的字段，模组内部将自动使用当前设置值。
示例
设置特定报头
AT+MHTTPHEADER=0,0,0,"api-key: FC9B69m4prHH2j7ljBbA=hHlbMI="
OK
4
Note: MN318/MN316/MN319/MN326/MN316A/MN326A：不支持>下7的数据输入模式，只能在AT命令中直
1
接输入数据，单次输入长度受平台单条命令长度限制，数据过长时建议分多次输入。MN319：AT命令参数的最
2
大长度为1560字节。ML307X总共可以写入的数据长度不超1过8192字节。
_
O
M
e
n
O
15. ML302S/ML307S/ML302A/ML305A/ML307A/ML307G/ML305M/ML307R/ML307C/ML307M/ML307H/ML307N/ML307X：数据模
式下可设置为0。ML302A/ML305A/ML307A/ML307G/ML307R/ML307C:
命令模式长度范围0~1024，数据模式长度范围0~4096。ML305U：数据模式下可设置为0，参数缺省时为0。ML307G：支持的
header总长度为4096字节。
16. ML302S/ML307S/ML302A/ML305A/ML307A/ML305U/ML305M/ML307R/ML307C/ML307G/ML307M/ML307H/ML307N/ML3
07X的字符串参数内部不能包含引号，如果字符串参数内部一定要包含引号，需使用数据模式进行发送。
23

中移物联网有限公司
3.4. AT+MHTTPCONTENT 设置HTTP CONTENT数据
该命令用于设置HTTP content数据。
AT+MHTTPCONTENT
语法 响应
成功
+MHTTPCONTENT: (list of supported<httpid>s),(list of supported<eof>s),(list of
测试命令 supported<length>s),
OK
AT+MHTTPCONTENT=?
错误
+CME ERROR: <err>
成功
只设置<httpid>，读取当前已设置的数据：
[+MHTTPCONTENT: <httpid>,<length>,<data>]
OK
<eof>设置，<length>和<data>不设置，以CTRL+Z发送数据，以ESC取消发
送：
>(输入数据)
设置命令
OK
4
AT
<length>设置，<data>不设置，输入数7据达到指定长度或达到超时时间时发
+MHTTPCONTENT=<httpid>
送数据： 1
[,<eof>[,<length>[,<data>]]] 2
>(输入数据) 1
_
OK
O
所有参数均设置：
M
OK
e
n
错误
O
+CME ERROR: <err>
命令描述
单次请求有效，请求结束后自动清空。设置命令中不带<data>参数，模组将进入数据模式，以>提示，然后输
入数据。普通传输模式时，先设置content数据，然后使用AT+MHTTPREQUEST发送请求；Chunked模式时，
17
需先使用AT+MHTTPREQUEST发送请求，然后使用该命令发送content。
参数描述
18
<httpid>客户端实例id，0~3。
<eof>数据输入指示。
17. Chunked 模式下，发送请求并接收到+MHTTPURC: "ind"上报后，使用该命令发送 content 。
18. MN319/MN316A/MN326A：<httpid>参数范围0~1。
24

中移物联网有限公司
AT+MHTTPCONTENT
0
content输入结束标记，需请求完成或清空后才可再次输入。
1
后续还有content数据输入。
2
清空已有content内容，只对非Chunked模式有效。
<length>输入数据长度，0~4096，数据模式下不可设置为0，命令中直接输入数据时，设置为0，不对数据长
度进行校验，设置大于0，将对输入数据的长度进行校验（ASCII字符串和带转义的字符串输入模式下：校验
实际命令中输入字符串长度是否与指定长度相等；HEX字符串输入模式下：校验实际命令中输入字符串长度
19
是否是指定长度的两倍）。
20
<data>content数据，单次请求有效，请求结束后自动清空。
示例
设置content数据（命令中带数据输入）
AT+MHTTPCONTENT=0,0,0,"{"id":"speed1"\r,"tags":["mobile"],"unit":"m/s"\r,"unit_symbol":"m/s"}"
OK
设置content数据（命令中不带数据输入）
AT+MHTTPCONTENT=0,0,70
>{"id":"speed1"\r,"tags":["mobile"],"unit":"m/s"\r,"unit_symbol":"m/s"}
4
OK
7
1
2
Note: MN318/MN316/MN319/MN326/MN316A/MN3261A：不支持>下的数据输入模式，只能在AT命令中直
_
接输入数据，单次输入长度受平台单条命令长度限制，数据过长时建议分多次输入。MN319：AT命令参数的最
O
大长度为1560字节。ML307X总共可以写入的数据长度不超过8192字节。
M
e
n
O
19. ML302S/ML307S/ML302A/ML305A/ML307A/ML307G/ML305M/ML307R/ML307C/ML307M/ML307H/ML307N：数据模式下可设
置为0。ML305U：数据模式下可设置为0，参数缺省时为0。ML307G：支持的content总长度为3*4096字节。
20. AT+MHTTPCFG="encoding"中设置的<input_format>参数，只对直接命令中带<data>的输入模式有效，对数据模式输入无
效，数据模式直接使用原始数
据。ML302S/ML307S/ML302A/ML305A/ML307A/ML307G/ML305U/ML305M/ML307R/ML307C/ML307M/ML307H/ML307N/ML3
07X：字符串参数内部不能包含引号，如果字符串参数内部一定要包含引号，需使用数据模式进行发送。
25

中移物联网有限公司
3.5. AT+MHTTPREQUEST 发送HTTP请求
该命令用于发送HTTP请求。
AT+MHTTPREQUEST
语法 响应
成功
+MHTTPREQUEST: (list of supported<httpid>s),(list of supported<method>s),(list of
测试命令 supported<length>s),
OK
AT+MHTTPREQUEST=?
错误
+CME ERROR: <err>
成功
<length>和<path>不设置，以CTRL+Z发送数据，以ESC取消发送：
>(输入数据)
OK
设置命令 <length>设置，<path>不设置，输入数据达到指定长度或达到超时时间时发
送数据：
AT
+MHTTPREQUEST=<httpid>, >(输入数据)
<method>[,<length>[,<path> OK
4
[,<local_path>]]]
所有参数均设置： 7
1
OK 2
1
错误 _
O
+CME ERROR: <err>
M
命令描述
e
n 21
普通传输模式先设置content数据，后发送请求；Chunked模式先发送请求，后设置content数据。
O
参数描述
22
<httpid>客户端实例id，0~3。
23
<method>请求类型。
1
GET
2
POST
3
21. 使用AT+MHTTPREQUEST下载数据到文件系统时，建议一次只使用一路下载。
22. MN319/MN316A/MN326A：<httpid>参数范围0~1。
23. ML307G：DELETE请求返回不携带配置的content数据。
26

中移物联网有限公司
AT+MHTTPREQUEST
PUT
4
DELETE
5
HEAD
<length>输入数据长度，0~4096，数据模式下不可设置为0，命令中直接输入数据时，设置为0，不对数据长
度进行校验，设置大于0，将对输入数据的长度进行校验（ASCII字符串和带转义的字符串输入模式下：校验
实际命令中输入字符串长度是否与指定长度相等；HEX字符串输入模式下：校验实际命令中输入字符串长度
24
是否是指定长度的两倍）。
25
<path>请求的url path。
26
<local_path>本地文件路径，长度范围与文件系统相关。
示例
发送GET请求
AT+MHTTPCREATE="http://120.27.12.119:10080"
+MHTTPCREATE: 0
OK
AT+MHTTPREQUEST=0,1,0,"/get"
OK
+MHTTPURC: "header",0,200,225,HTTP/1.1 200 OK
Server: nginx/1.18.0 4
Date: Fri, 23 Jul 2021 09:59:35 GMT 7
1
Content-Type: application/json
2
Content-Length: 145
1
Connection: keep-alive
_
Access-Control-Allow-Origin: *
O
Access-Control-Allow-Credentials: true
+MHTTPURC: M
"content",0,145,145,145,{"args":{},"headers":{"Connection":"close","Content-Length":"0","Host":"localhost:6000"},"origin":"127.0.0.1","url
e
":"http://localhost:6000/get"}
n
O
Note: MN318/MN316/MN319/MN326/MN316A/MN326A：不支持>下的数据输入模式，只能在AT命令
中直接输入数据，单次输入长度受平台单条命令长度限制，数据过长时建议分多次输入；MN319：AT命令
参数的最大长度为1560字节。如果请求响应存在重定向时，HTTP重定向到HTTPS，若未使能SSL（未配
置<ssl_enable>和<ssl_id>参数），模组将默认使能SSL，并采用默认配置进行连接。
24. ML302S/ML307S/ML302A/ML305A/ML307A/ML307G/ML305M/ML307R/ML307C/ML307M/ML307H/ML307N：数据模式下可设
置为0。ML305U：数据模式下可设置为0，参数缺省时为0。
25. AT+MHTTPCFG="encoding"中设置的<input_format>参数，只对直接命令中带<path>的输入模式有效，对数据模式输入无
效，数据模式直接使用原始数据。AT+MHTTPCFG="urlencode"使能后，模组将自动对请求的<path>进行编码，不对字母数字
以及-_.!~*'();/?:@&=+$,#进行编码。
26. ML302S/ML307S/ML302A/ML305A/ML307A/ML307G/ML307R/ML307C:若<local_path>第一位为正斜杠("/")则将下载文件存储
在用户输入的路径下，否则将下载文件存储在/etc/http_file/路径下。
ML305U/ML305M/ML307X:若<local_path>仅含有正斜杠("/")则将下载文件存储在用户输入的路径下，若<local_path>仅含有反
斜杠("\")或者不包含斜杠则将下载文件存储在/etc/http_file/路径下。
ML307M/ML307H/ML307N不支持<local_path>参数设置。
27

中移物联网有限公司
3.6. AT+MHTTPREAD 读取HTTP数据
该命令用于读取缓存模式下的HTTP缓存数据。
AT+MHTTPREAD
语法 响应
成功
AT+MHTTPREAD: (list of supported<httpid>s),(list of supported<type>s),(list of
测试命令 supported<read_len>s)
OK
AT+MHTTPREAD=?
失败
+CME ERROR: <err>
成功
<read_len>不设置时，返回当前可读缓存长度：
+MHTTPREAD: <httpid>,<type>,<unread_length>
设置命令
OK
AT <read_len>设置时，返回数据：
+MHTTPREAD=<httpid>,<typ
+MHTTPREAD: <httpid>,<type>,<unread_length>,<data_len>,<data>
e>[,<read_len>]
OK
错误
4
7
+CME ERROR: <err>
1
2
命令描述
1
_
缓存模式下，接收到的数据将缓存至模组中，+MHTTPURC: "recv"提示后表示请求结束，
O
27
可使用该命令读取数据，读取后自动清空，如果未及时读取，下次请求或者删除实例时也将自动清空。
M
参数描述
e
n
28
<httpid>客户端实例id，0~3。
O
<type>缓存数据类型。
0
header数据
1
content数据
<read_len>希望读取的数据长度，0~65535，为0时读取全部缓存数据。
<unread_length>剩余未读取缓存长度。
<data_len>当前读取数据长度。
27. ML307G/ML307N如果检测到需要下载的内容大于配置的缓存空间，则会报错缓存空间不足，并且不做数据缓存。
28. MN319/MN316A/MN326A：<httpid>参数范围0~1。
28

中移物联网有限公司
AT+MHTTPREAD
<data>读取的数据。
示例
读取缓存数据
AT+MHTTPCREATE="http://120.27.12.119:10080"
+MHTTPCREATE: 0
OK
AT+MHTTPCFG="cached",0,1
OK
AT+MHTTPREQUEST=0,1,0,"/get"
OK
+MHTTPURC: "recv",0,200,225,145
AT+MHTTPREAD=0,0,225
+MHTTPREAD: 0,0,0,225,HTTP/1.1 200 OK
Server: nginx/1.18.0
Date: Mon, 26 Jul 2021 01:25:15 GMT
Content-Type: application/json
Content-Length: 145
Connection: keep-alive
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
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
29

中移物联网有限公司
3.7. AT+MHTTPDEL 删除HTTP实例
该命令用于删除HTTP实例。
AT+MHTTPDEL
语法 响应
成功
+MHTTPDEL: (list of supported<httpid>s)
测试命令
OK
AT+MHTTPDEL=?
错误
+CME ERROR: <err>
成功
设置命令 OK
AT+MHTTPDEL=<httpid> 错误
+CME ERROR: <err>
命令描述
该命令用于删除已创建的实例，并清空该实例所有配置。
参数描述
4
29 7
<httpid>客户端实例id，0~3。
1
2
示例
1
_
删除实例
O
AT+MHTTPDEL=0
M
OK
e
n
O
29. MN319/MN316A/MN326A：<httpid>参数范围0~1。
30

中移物联网有限公司
3.8. AT+MHTTPTERM 终止HTTP传输
该命令用于终止当前HTTP传输。
AT+MHTTPTERM
语法 响应
成功
+MHTTPTERM: (list of supported<httpid>s)
测试命令
OK
AT+MHTTPTERM=?
错误
+CME ERROR: <err>
成功
执行命令 OK
AT+MHTTPTERM 错误
+CME ERROR: <err>
成功
设置命令 OK
AT+MHTTPTERM=<httpid> 错误
4
+CME ERROR: <err> 7
1
命令描述 2
1
该命令为中断当前请求，保留实例，执行命令终止所有_连接。该命令不主动清空客户端实例中设置的特定报
O
头和content数据（请求成功结束时自动清空），下次请求如需修改特定报头和content数据，请先使用相关命
令手动清空。 M
e
参数描述
n
O30
<httpid>客户端实例id，0~3。
示例
终止当前传输
AT+MHTTPTERM=0
OK
30. MN319/MN316A/MN326A：<httpid>参数范围0~1。
31

中移物联网有限公司
3.9. AT+MHTTPDLFILE HTTP下载文件
该命令用于下载数据到文件系统，无需创建实例。
AT+MHTTPDLFILE
语法 响应
成功
测试命令 OK
AT+MHTTPDLFILE=? 错误
+CME ERROR: <err>
设置命令 成功
AT OK
+MHTTPDLFILE=<url>,<local
错误
_path>[,<progind>[,<range>[
,<eof>[,<ssl_id>]]]] +CME ERROR: <err>
URC +MHTTPDLFILE: <downloaded>,<content_length>,<percent>[,<entity_length>]
命令描述
该命令为单步命令，不用先创建实例。该命令为GET请求，报头使用默认报头。请求响应的报头
431
以+MHTTPURC: "header"形式上报，不存入文件中。下载进度将以URC形式上报。
7
1
参数描述
2
1
<url>资源路径，包含host、端口以及path，例如
_
32
"http://example.com/abc.txt"或"http://exampleO.com:8080/abc.txt"。
<local_path>本地文件路径，长度范围M与文件系统相关。 33
<progind>下载进度上报提示长度e，下载的数据每达到指定长度时上报一次进度提示URC，默认值0，不上报
n
中间进度，只上报最后结果。
O
<range>资源下载范围（需服务器支持），参考RFC2616关于Range字段的说明，字符串格式，
如"bytes=500-900"代表传输文件的第500到900，共401字节。
34
<eof>文件写入方式，默认值0。
31. 当服务器以Content-Length模式下发数据，且Content-Length为0时，不上报下载进度。
32. ML307G如果没有指定https或者http，则默认https。
33. ML302S/ML307S/ML302A/ML305A/ML307A/ML307G/ML307R/ML307C:
若<local_path>第一位为正斜杠("/")则将下载文件存储在用户输入的路径下，否则将下载文件存储在/etc/http_file/路径下。
ML305U/ML305M/ML307X:
若<local_path>仅含有正斜杠("/")则将下载文件存储在用户输入的路径下，若<local_path>仅含有反斜杠("\")或者不包含斜杠则将
下载文件存储在/etc/http_file/路径下。
34. 当<range>不设置时，<eof>设置为1时，模组内部将自动实现断点续传，即如下载过程异常中断，下一次请求同一文件时，模组内
部将继续下载上次内容之后的数据。如果文件已下载完成，请不要按此方式重复下载，否则可能导致下载异常，可删除文件后或按覆
盖方式下载。使用断点续传功能之前，请确保已下载的数据是从第一位开始下载的。
32

中移物联网有限公司
AT+MHTTPDLFILE
0
覆盖方式写入（清空当前文件后写入）
1
追加方式写入（在当前文件尾继续写入）
<ssl_id>SSL上下文索引号，指定当前实例使用的SSL上下文，范围可参见文档《SSL用户手册》，该参数不
35
设置时，不使能SSL，设置时即使能SSL。
<downloaded>已下载长度。
<content_length>本次下载总长度，如果请求响应为Chunked模式，中间结果上报时将显示为0。
<percent>下载进度百分比，如果请求响应为chunked模式，中间结果上报时将显示为0，传输完成时提示
100。
<entity_length>下载文件的总长度，当指定<range>且服务器回复Content-Range字段时有效。
示例
下载到文件
AT+MHTTPDLFILE="http://120.27.12.119:10080/get","/test.txt"
OK
+MHTTPURC: "header",0,200,225,HTTP/1.1 200 OK
Server: nginx/1.18.0
Date: Wed, 28 Jul 2021 02:46:45 GMT
4
Content-Type: application/json
7
Content-Length: 145
1
Connection: keep-alive
2
Access-Control-Allow-Origin: *
1
Access-Control-Allow-Credentials: true _
+MHTTPDLFILE: 145,145,100 O
下载文件到用户输入的路径下 M
AT+MHTTPDLFILE="http://120.2e7.12.119:10080/get","/test.txt"
n
OK
O
+MHTTPURC: "header",2,200,225,HTTP/1.1 200 OK
Server: nginx/1.18.0
Date: Fri, 09 Sep 2022 09:39:04 GMT
Content-Type: application/json
Content-Length: 189
Connection: keep-alive
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
+MHTTPDLFILE: 189,189,100
AT+MFLIST="/"
+MFLIST: "/test.txt",189
OK
下载文件到/etc/http_file/路径下
35. ML302A/ML305A/ML307A/ML307G/ML307S/ML305U/ML305M/ML307R/ML307C/ML307X：<ssl_id>参数范围0~5。
33

中移物联网有限公司
AT+MHTTPDLFILE
AT+MHTTPDLFILE="http://120.27.12.119:10080/get","test.txt"
OK
+MHTTPURC: "header",2,200,225,HTTP/1.1 200 OK
Server: nginx/1.18.0
Date: Fri, 09 Sep 2022 09:40:13 GMT
Content-Type: application/json
Content-Length: 189
Connection: keep-alive
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
+MHTTPDLFILE: 189,189,100
AT+MFLIST="/etc/http_file/"
+MFLIST: "/etc/http_file/test.txt",189
OK
Note: MN318/MN316/MN319/MN326/MN316A/MN326A/ML307M/ML307H/ML307N不支持该命令。
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
34

中移物联网有限公司
3.10. +MHTTPURC URC信息上报
该命令为URC上报提示。
+MHTTPURC
语法 响应
报头接收URC +MHTTPURC: "header",<httpid>,<code>,<header_len>,<data>
content接收URC +MHTTPURC: "content",<httpid>,<content_len>,<sum_len>,<cur_len>,<data>
错误事件URC +MHTTPURC: "err",<httpid>,<error_code>
缓存模式请求完成URC +MHTTPURC: "recv",<httpid>,<code>,<recv_header_len>,<recv_content_len>
Chunked模式下可发送数据指
+MHTTPURC: "ind",<httpid>
示URC
下载到文件系统URC +MHTTPURC: "download",<downloaded>,<content_length>[,<entity_length>]
命令描述
该命令为请求过程中的URC，包含数据接收、请求错误、缓存接收相关事件。
参数描述
命令标识符。 4
7
header 1
2
报头接收上报（缓存模式无上报）
1
content _
O
消息体接收上报（缓存模式无上报）
M
err
e
请求错误上报，上报后将接收本次请求
n
recv O
缓存模式请求完成上报
ind
Chunked模式下可发送数据指示
download
AT+MHTTPREQUEST命令下载数据到文件系统接收上报
36
<httpid>客户端实例id，0~3。
<code>HTTP请求结果码。
<header_len>接收的报头长度。
<data>数据内容，格式受AT+MHTTPCFG="encoding"中<output_format>控制。
36. MN319/MN316A/MN326A：<httpid>参数范围0~1。
35

中移物联网有限公司
+MHTTPURC
<content_len>content数据总长度，如果响应为Chunked模式，该参数显示为0。
<sum_len>已下载content数据长度，普通传输模式时，当前长度与<content_len>相等时，表示请求完成。
<cur_len>当前下载的content数据长度，如果响应为chunked模式，该参数显示为0时，表示请求完成。
<error_code>错误事件。
1
域名解析失败
2
连接服务器失败
3
连接服务器超时
4
SSL握手失败
5
连接异常断开
6
请求响应超时
7
接收数据解析失败 4
7
8 1
2
缓存空间不足
1
9 _
O
数据丢包
M
10
e
写文件失败
n
255 O
未知错误
<recv_header_len>缓存中接收的报头长度。
<recv_content_len>缓存中接收的content长度。
<downloaded>已下载长度。
<content_length>本次下载总长度，如果请求响应为Chunked模式，中间结果上报时将显示为0。
<entity_length>下载文件的总长度，当指定<range>且服务器回复Content-Range字段时有效。
示例
普通模式发送请求
36

中移物联网有限公司
+MHTTPURC
AT+MHTTPCREATE="http://120.27.12.119:10080"
+MHTTPCREATE: 0
OK
AT+MHTTPREQUEST=0,1,0,"/get"
OK
+MHTTPURC: "header",0,200,225,HTTP/1.1 200 OK
Server: nginx/1.18.0
Date: Mon, 26 Jul 2021 03:47:04 GMT
Content-Type: application/json
Content-Length: 145
Connection: keep-alive
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
+MHTTPURC:
"content",0,145,145,145,{"args":{},"headers":{"Connection":"close","Content-Length":"0","Host":"localhost:6000"},"origin":"127.0.0.1","url
":"http://localhost:6000/get"}
缓存模式发送请求
AT+MHTTPCREATE="http://120.27.12.119:10080"
+MHTTPCREATE: 0
OK
AT+MHTTPCFG="cached",0,1
OK
AT+MHTTPREQUEST=0,1,0,"/get"
OK
+MHTTPURC: "recv",0,200,225,145
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

中移物联网有限公司
4. 示例
本章主要介绍HTTP/HTTPS命令在相关业务场景中的使用流程。
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
4.1. HTTP示例
本节主要介绍HTTP相关的操作流程，其中包含实例创建、通用参数配置、GET/PUT/POST请求等相关流
程。
创建实例和参数设置
AT+MHTTPCREATE="http://120.27.12.119:10080" //创建实例
+MHTTPCREATE: 0
OK
AT+MHTTPCFG="header",0,"Connection: close" //报头设置（根据实际需求设置）
OK
AT+MHTTPCFG="header",0
+MHTTPCFG: "header",0,19,Connection: close
OK
AT+MHTTPHEADER=0,0,0,"api-key: FC9B69m4prHH2j7ljBbA=hHlbMI="
OK
Important: 创建实例后，发送请求前，可进行相关参数设置，如上示例只进行了简单参数设置，根据实际
使用需求，用户可在此阶段进行相关参数设置。
GET请求
AT+MHTTPREQUEST=0,1,0,"/get" //GET请求
OK
4
+MHTTPURC: "header",0,200,225,HTTP/1.1 200 OK 7
Server: nginx/1.18.0 1
Date: Mon, 26 Jul 2021 06:18:46 GMT 2
Content-Type: application/json 1
Content-Length: 145 _
Connection: close O
Access-Control-Allow-Origin: *
M
Access-Control-Allow-Credentials: true
+MHTTPURC: e
"content",0,145,145,145,{"args":{n},"headers":{"Connection":"close","Content-Length":"0","Host":"localhost:6000"},"origin":"127.0.0.1","url":"
http://localhost:6000/geOt"}
POST请求
AT+MHTTPCONTENT=0,0,0,"{"id":"speed1"\r,"tags":["mobile"],"unit":"m/s"\r,"unit_symbol":"m/s"}"
//content设
置。ML302S/ML307S/ML302A/ML305A/ML307A/ML307G/ML305U/ML305M/ML307R/ML307C/ML307M/ML307H/ML307N/ML3
07X的字符串参数内部不能包含引号，如果字符串参数内部一定要包含引号，需使用数据模式进行发送。
OK
AT+MHTTPREQUEST=0,2,0,"/post" //POST请求
OK
+MHTTPURC: "header",0,200,220,HTTP/1.1 200 OK
Server: nginx/1.18.0
Date: Mon, 26 Jul 2021 06:25:23 GMT
Content-Type: application/json
Content-Length: 278
39

中移物联网有限公司
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
+MHTTPURC:
"content",0,278,278,278,{"args":{},"data":"{\"id\":\"speed1\"\\r,\"tags\":[\"mobile\"],\"unit\":\"m/s\"\\r,\"unit_symbol\":\"m/s
\"}","files":{},"form":{},"headers":{"Connection":"close","Content-Length":"70","Host":"localhost:6000"},"json":null,"origin":"127.0.0.1","url":"htt
p://localhost:6000/post"}
PUT请求
AT+MHTTPCONTENT=0,0,0,"{"id":"speed1"\r,"tags":["mobile"],"unit":"m/s"\r,"unit_symbol":"m/s"}"
//content设
置。ML302S/ML307S/ML302A/ML305A/ML307A/ML307G/ML305U/ML305M/ML307R/ML307C/ML307M/ML307H/ML307N/ML3
07X的字符串参数内部不能包含引号，如果字符串参数内部一定要包含引号，需使用数据模式进行发送。
OK
AT+MHTTPREQUEST=0,3,0,"/put" //PUT请求
OK
+MHTTPURC: "header",0,200,220,HTTP/1.1 200 OK
Server: nginx/1.18.0
Date: Mon, 26 Jul 2021 06:26:42 GMT
Content-Type: application/json
Content-Length: 277
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
+MHTTPURC:
"content",0,277,277,277,{"args":{},"data":"{\"id\":\"speed1\"\\r,\"tags\":[\"mobile\"],\"unit\":\"m/s\"\\r,\"unit_symbol\":\"m/s
4
\"}","files":{},"form":{},"headers":{"Connection":"close","Content-Length":"70","Host":"localhost:6000"},"json":null,"origin":"127.0.0.1","url":"htt
7
p://localhost:6000/put"}
1
2
1
Chunked模式下的POST请求
_
O
AT+MHTTPCREATE="http://api.heclouds.com/" //创建实例
+MHTTPCREATE: 0 M
OK
e
AT+MHTTPCFG="chunked",0,1 //设置为chunked模式
n
OK
O
AT+MHTTPCFG="encoding",0,2 //设置转义字符输入
OK
AT+MHTTPCFG="header",0,"api-key: 2ATvkByTxig2uy55rlQPLf3xxVY=" //设置报头
OK
AT+MHTTPREQUEST=0,2,0,"/devices/622957377/datastreams" //发送POST请求
OK
+MHTTPURC: "ind",0 //URC上报，指示可发送content数据。
AT+MHTTPCONTENT=0,0,0,"{"id":"speed1"\r,"tags":["mobile"],"unit":"m/s"\r,"unit_symbol":"m/s"}"//发送数据
OK
+MHTTPURC: "header",0,200,175,HTTP/1.1 200 OK
Date: Mon, 29 Nov 2021 07:33:31 GMT
Content-Type: application/json
Content-Length: 66
Connection: keep-alive
Server: Apache-Coyote/1.1
Pragma: no-cache
40

中移物联网有限公司
+MHTTPURC: "content",0,66,66,66,{"errno":11,"error":"datastream already exists: 622957377.speed1"}
请求过程中终止请求
AT+MHTTPREQUEST=0,1,0,"/get"
OK
AT+MHTTPTERM=0
OK
删除实例
AT+MHTTPDEL=0
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
41

中移物联网有限公司
4.2. HTTPS示例
本节以GET请求流程为例，描述HTTPS使用步骤。
Important: MN318/MN319/MN316A/MN326A/ML307H-GU不支持HTTPS。
SSL证书写入
AT+MSSLCERTWR="s.ca",0,1310,"-----BEGIN
CERTIFICATE-----\r\nMIIDizCCAnOgAwIBAgIJAM1N8muy2ikPMA0GCSqGSIb3DQEBCwUAMFwxCzAJBgNV\r
\nBAYTAmNuMQ4wDAYDVQQIDAVodWJlaTEOMAwGA1UEBwwFd3VoYW4xDjAMBgNVBAoM\r
\nBWNtaW90MQwwCgYDVQQLDANpb3QxDzANBgNVBAMMBlJvb3RDQTAeFw0yMDA3Mjkw\r
\nNjUyMzJaFw0zMDA3MjcwNjUyMzJaMFwxCzAJBgNVBAYTAmNuMQ4wDAYDVQQIDAVo\r
\ndWJlaTEOMAwGA1UEBwwFd3VoYW4xDjAMBgNVBAoMBWNtaW90MQwwCgYDVQQLDANp\r
\nb3QxDzANBgNVBAMMBlJvb3RDQTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoC\r
\nggEBAMUrAyt8WCylcWKuHVoakXNPwH8KlrhatzHmTgFqK4g7harm9wA4ajPfymR6\r\nuNFUj/lUB3VEj/qyKB
PXC28AA92YMkbcIJuuvv7T7scziGadx87hjBxoGa/UZELV\r
\nCOYpLDz+tcOFUDAPFpEQphAi5lEm84ZhaYcrdDrY/1jIKphwvRZGpQ+jQWdRqMcX\r
\n97XzgSDlaxIB8W+LYFeA4GP0B48PEbS5YHVNL7NAHsLjEq/xrzYTicUefAJ89Ivq\r
\nAlM8u9nNt6JqTuFP2R7iLjD8e/pUAz5PR5aYjCBCX5fM76b1VkG8CYjIf/UUFLI3\r\nwIjzgUcKInbbj1hPeTG/Aw
7V/zECAwEAAaNQME4wHQYDVR0OBBYEFFdklbpXYDuM\r
\nY7Ek6NN0wX4RwdXEMB8GA1UdIwQYMBaAFFdklbpXYDuMY7Ek6NN0wX4RwdXEMAwG\r
\nA1UdEwQFMAMBAf8wDQYJKoZIhvcNAQELBQADggEBABktBZ/Z+upMimODpxkpL0mZ\r
\np3BWrqOhFHXMXWVyGpsIcGOhKIieV5StFDWs3aqfI/5LFv4FwfjiIsoCKq53v6KM\r
\nOrj5m/vTKPKC1mL0Eu06PGahwcupeZLEiuK4xB6IoP50MS+fjLeqZSbADJTyloM2\r\n9PBog+M4PDIWpODdf
4
1r2pPnbJCdwVBYSmzaIzz7cOvzmGBAAN2AJfxDO2Zs0tQ+0\r\nqGLzukWxtPybgLbfeSvZs0RGakUs1Ir2+Ct+A
7
AXkteFGNKr6Ph020YDWNev7WJJN\r 1
\nrZvOGW3kKMHJSSJ/hB0qPO31liyJ7RWMAX0L0E9PJmazsy2eFRNhznd3TM4BBf3s=\r\n-----END
1
CERTIFICATE-----\r\n" //命令中直接输入证书（其他输入方式可参考《SSL用户手册》）
_
OK
O
M
SSL证书选择与认证等级设置
e
AT+MSSLCFG="cert",1,"s.can" //设置SSL 1使用“s.ca”证书
OK O
AT+MSSLCFG="auth",1,1 //设置SSL 1采用单项认证方式
OK
Note: 客户使用时，请在创建HTTP实例前进行SSL相应参数设置，在请求过程中不要修改SSL参数，如需
修改参数，请在使用AT+MHTTPTERM或者AT+MHTTPDEL命令终止请求后修改。
创建实例和参数配置
AT+MHTTPCREATE="https://120.27.12.119:10443"
+MHTTPCREATE: 0
OK
AT+MHTTPCFG="ssl",0,1,1 //HTTPS 使能，并指定使用SSL 1。
OK
42

中移物联网有限公司
GET请求
AT+MHTTPREQUEST=0,1,0,"/get"
OK
+MHTTPURC: "header",0,200,225,HTTP/1.1 200 OK
Server: nginx/1.18.0
Date: Mon, 26 Jul 2021 06:52:49 GMT
Content-Type: application/json
Content-Length: 145
Connection: keep-alive
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
+MHTTPURC:
"content",0,145,145,145,{"args":{},"headers":{"Connection":"close","Content-Length":"0","Host":"localhost:6000"},"origin":"127.0.0.1","url":"
http://localhost:6000/get"}
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
43

中移物联网有限公司
4.3. 缓存模式
该小节主要介绍缓存模式的使用步骤及注意事项。由于模组空间资源有限，在使用时请充分考虑模组空间
和获取内容的大小情况，建议获取大数据时不要使用缓存模式。
创建实例和参数配置
AT+MHTTPCREATE="http://120.27.12.119:10080"
+MHTTPCREATE: 0
OK
AT+MHTTPCFG="cached",0,1 //设置缓存模式
OK
AT+MHTTPCFG="cached",0
+MHTTPCFG: "cached",0,1,4096
OK
GET请求
AT+MHTTPREQUEST=0,1,0,"/get"
OK
+MHTTPURC: "recv",0,200,225,145 //请求完成
读取缓存数据
AT+MHTTPREAD=0,0,225 //读取报头
+MHTTPREAD: 0,0,0,225,HTTP/1.1 200 OK 4
Server: nginx/1.18.0 7
Date: Mon, 26 Jul 2021 06:58:31 GMT 1
2
Content-Type: application/json
1
Content-Length: 145
_
Connection: keep-alive
O
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true M
OK
e
AT+MHTTPREAD=0,1,145 //读取content
n
+MHTTPREAD:
O
0,1,0,145,{"args":{},"headers":{"Connection":"close","Content-Length":"0","Host":"localhost:6000"},"origin":"127.0.0.1","url":"http://
localhost:6000/get"}
OK
44

中移物联网有限公司
4.4. HTTP下载文件
该小节介绍下载数据到文件系统的使用步骤。
Important: MN318/MN316/MN319/MN326/MN316A/MN326A/ML307M/ML307H/ML307N不支持HTTP下载
文件。
下载到文件
AT+MHTTPDLFILE="http://120.27.12.119:10080/get","/test.txt"
OK
+MHTTPURC: "header",0,200,225,HTTP/1.1 200 OK //报头直接打印
Server: nginx/1.18.0
Date: Wed, 28 Jul 2021 02:46:45 GMT
Content-Type: application/json
Content-Length: 145
Connection: keep-alive
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
+MHTTPDLFILE: 145,145,100 //content数据下载完毕
断点续传
AT+MHTTPDLFILE="http://www.baidu.com:80/","/test.txt",1024,"bytes=0-4095"
OK
+MHTTPURC: "header",0,206,1010,HTTP/1.1 206 Partial Content 4
Cache-Control: no-cache 7
1
Connection: keep-alive
2
Content-Length: 4096
1
Content-Range: bytes 0-4095/9508
_
Content-Type: text/html
O
Date: Sat, 08 Oct 2022 07:50:38 GMT
P3p: CP=" OTI DSP COR IVA OUR IND COM "M
P3p: CP=" OTI DSP COR IVA OUR IND COM "
e
Pragma: no-cache
n
Server: BWS/1.1
O
Set-Cookie: BAIDUID=6DE111457B560799DF64D34146F8D8F7:FG=1; expires=Thu, 31-Dec-37 23:55:55 GMT;
max-age=2147483647; path=/; domain=.baidu.com
Set-Cookie: BIDUPSID=6DE111457B560799DF64D34146F8D8F7; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2147483647;
path=/; domain=.baidu.com
Set-Cookie: PSTM=1665215438; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2147483647; path=/; domain=.baidu.com
Set-Cookie: BAIDUID=6DE111457B560799C6BC553E004150D6:FG=1; max-age=31536000; expires=Sun, 08-Oct-23 07:50:38 GMT;
domain=.baidu.com; path=/; version=1; comment=bd
Traceid: 166521543806129891949891895393280359837
Vary: Accept-Encoding
X-Frame-Options: sameorigin
X-Ua-Compatible: IE=Edge,chrome=1
+MHTTPDLFILE: 1908,4096,46,9508
+MHTTPDLFILE: 3368,4096,82,9508
+MHTTPDLFILE: 4096,4096,100,9508
AT+MHTTPDLFILE="http://www.baidu.com:80/","/test.txt",1024,,1
OK
45

中移物联网有限公司
+MHTTPURC: "header",0,206,1014,HTTP/1.1 206 Partial Content
Cache-Control: no-cache
Connection: keep-alive
Content-Length: 5412
Content-Range: bytes 4096-9507/9508
Content-Type: text/html
Date: Sat, 08 Oct 2022 07:50:47 GMT
P3p: CP=" OTI DSP COR IVA OUR IND COM "
P3p: CP=" OTI DSP COR IVA OUR IND COM "
Pragma: no-cache
Server: BWS/1.1
Set-Cookie: BAIDUID=72B840BCD24911564E135B2D9EE39B24:FG=1; expires=Thu, 31-Dec-37 23:55:55 GMT;
max-age=2147483647; path=/; domain=.baidu.com
Set-Cookie: BIDUPSID=72B840BCD24911564E135B2D9EE39B24; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2147483647;
path=/; domain=.baidu.com
Set-Cookie: PSTM=1665215447; expires=Thu, 31-Dec-37 23:55:55 GMT; max-age=2147483647; path=/; domain=.baidu.com
Set-Cookie: BAIDUID=72B840BCD2491156B1186032DA772228:FG=1; max-age=31536000; expires=Sun, 08-Oct-23 07:50:47 GMT;
domain=.baidu.com; path=/; version=1; comment=bd
Traceid: 1665215447039514753010076465853275645890
Vary: Accept-Encoding
X-Frame-Options: sameorigin
X-Ua-Compatible: IE=Edge,chrome=1
+MHTTPDLFILE: 1904,5412,35,9508
+MHTTPDLFILE: 3364,5412,62,9508
+MHTTPDLFILE: 4824,5412,89,9508
+MHTTPDLFILE: 5412,5412,100,9508
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
5. 错误码
本章为HTTP/HTTPS命令相关的错误码。
错误码 说明
3 操作不被允许
23 内存分配失败
50 参数错误
650 未知错误
651 无空闲客户端
652 客户端未创建
653 客户端忙
654 URL解析失败
655 SSL未使能
656 连接失败
657 数据发送失败 4
7
658 打开文件失败 1
2
1
_
O
M
e
n
O
47