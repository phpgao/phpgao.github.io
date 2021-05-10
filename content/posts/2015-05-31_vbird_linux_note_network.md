---
title: "鸟哥的私房菜学习笔记----网络速查"
categories: [ "服务器技术" ]
tags: [ "Linux","note","network" ]
draft: false
slug: "vbird_linux_note_network"
date: "2015-05-31 03:32:00"
url: "vbird_linux_note_network.html"
---

多图杀猫，流量党渗入


<!--more-->

以下内容总结自[鸟哥的 Linux 私房菜 -- 服务器(第三版)][1]，同时推荐喜欢Linux的同学们学习阅读。

## OSI 七层协议

![OSI 七层协议][2]

<table width="95%" bgcolor="lightyellow" border="1" cellpadding="3" cellspacing="0">
<tbody><tr align="center" bgcolor="#182448"><td width="120"><font color="#FFFFFF">分层</font></td><td><font color="#FFFFFF">负责内容</font></td></tr>
<tr><td align="center">Layer 1<br>物理层<br>Physical Layer</td>
	<td>由于网络媒体只能传送 0 与 1 这种位串，因此物理层必须定义所使用的媒体设备之电压与讯号等，
	同时还必须了解数据讯框转成位串的编码方式，最后连接实体媒体并传送/接收位串。</td></tr>

<tr bgcolor="lightgreen"><td align="center">Layer 2<br>数据链结层<br>Data-Link Layer</td>
	<td>这一层是比较特殊的一个阶层，因为底下是实体的定义，而上层则是软件封装的定义。因此第二层又分两个子层在进行数据的转换动作。
	在偏硬件媒体部分，主要负责的是 MAC (Media Access Control) ，我们称这个数据报裹为 MAC 讯框 (frame)， MAC 
	是网络媒体所能处理的主要数据报裹，这也是最终被物理层编码成位串的数据。MAC 必须要经由通讯协议来取得媒体的使用权，
	目前最常使用的则是 IEEE 802.3 的以太网络协议。详细的 MAC 与以太网络请参考下节说明。<br><br>

	至于偏向软件的部分则是由逻辑链接层 (logical link control, LLC) 所控制，主要在多任务处理来自上层的封包数据 (packet) 并转成 MAC 的格式，
	负责的工作包括讯息交换、流量控制、失误问题的处理等等。</td></tr>

<tr><td align="center">Layer 3<br>网络层<br>Network Layer</td>
	<td>这一层是我们最感兴趣的啰，因为我们提及的 IP (Internet Protocol) 就是在这一层定义的。
	同时也定义出计算机之间的联机建立、终止与维持等，数据封包的传输路径选择等等，因此这个层级当中最重要的除了 IP 
	之外，就是封包能否到达目的地的路由 (route) 概念了！</td></tr>

<tr bgcolor="lightgreen"><td align="center">Layer 4<br>传送层<br>Transport Layer</td>
	<td>这一个分层定义了发送端与接收端的联机技术(如 TCP, UDP 技术)，
	同时包括该技术的封包格式，数据封包的传送、流程的控制、传输过程的侦测检查与复原重新传送等等，
	以确保各个数据封包可以正确无误的到达目的端。</td></tr>

<tr><td align="center">Layer 5<br>会谈层<br>Session Layer</td>
	<td>在这个层级当中主要定义了两个地址之间的联机信道之连接与挂断，此外，亦可建立应用程序之对谈、
	提供其他加强型服务如网络管理、签到签退、对谈之控制等等。如果说传送层是在判断资料封包是否可以正确的到达目标，
	那么会谈层则是在确定网络服务建立联机的确认。</td></tr>

<tr bgcolor="lightgreen"><td align="center">Layer 6<br>表现层<br>Presentation Layer</td>
	<td>我们在应用程序上面所制作出来的数据格式不一定符合网络传输的标准编码格式的！
	所以，在这个层级当中，主要的动作就是：将来自本地端应用程序的数据格式转换(或者是重新编码)成为网络的标准格式，
	然后再交给底下传送层等的协议来进行处理。所以，在这个层级上面主要定义的是网络服务(或程序)之间的数据格式的转换，
	包括数据的加解密也是在这个分层上面处理。</td></tr>

<tr><td align="center">Layer 7<br>应用层<br>Application Layer</td>
	<td>应用层本身并不属于应用程序所有，而是在定义应用程序如何进入此层的沟通接口，以将数据接收或传送给应用程序，最终展示给用户。
	</td></tr>
</tbody></table>

## TCP/IP 四层协议

![TCP/IP 四层协议][3]

## AB/AA/BB线序

<table width="95%" bgcolor="lightyellow" border="1" cellpadding="3" cellspacing="0">
<tbody><tr align="center" bgcolor="#182448"><td><font color="#FFFFFF">接头名称\蕊线顺序</font></td><td width="8%"><font color="#FFFFFF">1</font></td><td width="8%"><font color="#FFFFFF">2</font></td><td width="8%"><font color="#FFFFFF">3</font></td><td width="8%"><font color="#FFFFFF">4</font></td><td width="8%"><font color="#FFFFFF">5</font></td><td width="8%"><font color="#FFFFFF">6</font></td><td width="8%"><font color="#FFFFFF">7</font></td><td width="8%"><font color="#FFFFFF">8</font></td></tr>
<tr align="center"><td>568A</td><td>白绿</td><td>绿</td><td>白橙</td><td>蓝</td><td>白蓝</td><td>橙</td><td>白棕</td><td>棕</td></tr>
<tr align="center"><td>568B</td><td>白橙</td><td>橙</td><td>白绿</td><td>蓝</td><td>白蓝</td><td>绿</td><td>白棕</td><td>棕</td></tr>
</tbody></table>

- 跳线：一边为 568A 一边为 568B 的接头时称为跳线，用在直接链接两部主机的网络卡。
- 并行线：两边接头同为 568A 或同为 568B 时称为并行线，用在链接主机网络卡与集线器之间的线材；

## 以太网络的传输协议：CSMA/CD

1. 监听媒体使用情况 (Carrier Sense)：A 主机要发送网络封包前，需要先对网络媒体进行监听，确认没有人在使用后， 才能够发送出讯框；
1. 多点传输 (Multiple Access)：A 主机所送出的数据会被集线器复制一份，然后传送给所有连接到此集线器的主机！ 也就是说， A 所送出的数据， B, C, D 三部计算机都能够接收的到！但由于目标是 D 主机，因此 B 与 C 会将此讯框数据丢弃，而 D 则会抓下来处理；
1. 碰撞侦测 (Collision Detection)：该讯框数据附有检测能力，若其他主机例如 B 计算机也刚好在同时间发送讯框数据时， 那么 A 与 B 送出的数据碰撞在一块 (出车祸) ，此时这些讯框就是损毁，那么 A 与 B 就会各自随机等待一个时间， 然后重新透过第一步再传送一次该讯框数据。

## MAC 的封装格式

![MAC 的封装格式][4]

> 由于网络卡卡号是跟着网络卡走的，并不会因为重灌操作系统而改变， 所以防火墙软件大多也能够针对网络卡来进行抵挡的工作喔！ 不过抵挡网卡仅能在局域网络内进行而已，因为 MAC 不能跨 router 嘛！！

## 什么是全双工/半双工(full-duplex, half-duplex)

前面谈到网络线时，我们知道八蕊的网络线实际上仅有两对被使用，一对是用在传送，另一对则是在接收。 如果两端的 PC 同时支持全双工时，那表示 Input/Output 均可达到 10/100Mbps， 亦即数据的传送与接收同时均可达到 10/100bps 的意思，总带宽则可达到 20/200Mbps 啰 (其实是有点语病的，因为 Input 可达 10/100Mbps， output 可达 10/100Mbps ， 而不是 Input 可直接达到 20/200Mbps 喔！)如果你的网络环境想要达到全双工时， 使用共享媒体的 Hub 是不可能的，因为网络线脚位的关系，无法使用共享媒体来达到全双工的！ 如果你的 switch 也支持全双工模式，那么在 switch 两端的 PC 才能达到全双工喔！

## IP 封包的封装

目前因特网社会的 IP 有两种版本，一种是目前使用最广泛的 IPv4 (Internet Protocol version 4, 因特网协定第四版)， 一种则是预期未来会热门的 IPv6 。IPv4 记录的地址由于仅有 32 位，预计在 2020 年前后就会分发完毕，如此一来， 新兴国家或者是新的网络公司，将没有网络可以使用。为了避免这个问题发生，因此就有 IPv6 的产生。 IPv6 的地址可以达到 128 位，可以多出 2 的 96 次方倍的网址数量，这样的 IP 数量几乎用不完啦！虽然 IPv6 具有前瞻性，但目前主流媒体大多还是使用 IPv4 ，因此本文主要谈到的 IP 都指 IPv4 而言喔！(注13)

我们在前一小节谈到 MAC 的封装，那么 IP 封包的封装也得要来了解一下，才能知道 IP 到底是如何产生的啊！ IP 封包可以达到 65535 bytes 这么大，在比 MAC 大的情况下，我们的操作系统会对 IP 进行拆解的动作。至于 IP 封装的表头数据绘制如下：(下图第一行为每个字段的 bit 数)

<table style="border-width: 3px; border-color: black; border-style: none;">
<tbody><tr align="center">
	<td style="width: 58px; border-width: 3px; border-style: none solid;">4 bits</td>
	<td style="width: 58px; border-width: 3px; border-style: none solid none none;">4 bits</td>
	<td style="width: 108px; border-width: 3px; border-style: none solid none none;">8 bits</td>
	<td style="width: 43px; border-width: 3px; border-style: none solid none none;">3 bits</td>
	<td style="width: 178px; border-width: 3px; border-style: none solid none none;">13 bits</td>
</tr>
</tbody></table>
<table class="text_import2" width="480" bgcolor="lightyellow" border="1" cellpadding="3" cellspacing="0">
<tbody><tr align="center">
	<td width="60">Version</td>
	<td width="60">IHL</td>
	<td width="120">Type of Service</td>
	<td colspan="2" width="240">Total Length</td>
</tr>
<tr align="center">
	<td colspan="3">Identification</td>
	<td width="45">Flags</td>
	<td width="195">Fragmentation Offset</td>
</tr>
<tr align="center">
	<td colspan="2" bgcolor="lightblue">Time To Live</td>
	<td bgcolor="lightblue">Protocol</td>
	<td colspan="2">Header Checksum</td>
</tr>
<tr align="center">
	<td colspan="5" bgcolor="lightblue">Source Address</td></tr>
<tr align="center">
	<td colspan="5" bgcolor="lightblue">Destination Address</td></tr>
<tr align="center">
	<td colspan="4">Options</td>
	<td>Padding</td>
</tr>
<tr align="center">
	<td colspan="5" height="50" bgcolor="lightgreen">Data</td></tr>
</tbody></table>

在上面的图示中有个地方要注意，那就是『每一行所占用的位数为 32 bits』， 各个表头的内容分别介绍如下：

## IP表头介绍

- Version(版本)
宣告这个 IP 封包的版本，例如目前惯用的还是 IPv4 这个版本就在这里宣告。

- IHL(Internet Header Length, IP表头的长度)
告知这个 IP 封包的表头长度，使用的单位应该是字组 (word) ，一个字组为 4bytes 大小喔。

- Type of Service(服务类型)
这个项目的内容为『PPPDTRUU』，表示这个 IP 封包的服务类型，主要分为：
PPP：表示此 IP 封包的优先度，目前很少使用；
D：若为 0 表示一般延迟(delay)，若为 1 表示为低延迟；
T：若为 0 表示为一般传输量 (throughput)，若为 1 表示为高传输量；
R：若为 0 表示为一般可靠度(reliability)，若为 1 表示高可靠度。
UU：保留尚未被使用。
举例来说，gigabit 以太网络的种种相关规格可以让这个 IP 封包加速且降低延迟，某些特殊的标志就是在这里说明的。

- Total Length(总长度)
指这个 IP 封包的总容量，包括表头与内容 (Data) 部分。最大可达 65535 bytes。

- Identification(辨别码)
我们前面提到 IP 袋子必须要放在 MAC 袋子当中。不过，如果 IP 袋子太大的话，就得先要将 IP 再重组成较小的袋子然后再放到 MAC 当中。而当 IP 被重组时，每个来自同一个 IP 的小袋子就得要有个标识符以告知接收端这些小袋子其实是来自同一个 IP 封包才行。 也就是说，假如 IP 封包其实是 65536 那么大 (前一个 Total Length 有规定)， 那么这个 IP 就得要再被分成更小的 IP 分段后才能塞进 MAC 讯框中。那么每个小 IP 分段是否来自同一个 IP 资料，呵呵！那就是这个标识符的功用啦！

- Flags(特殊旗标)
这个地方的内容为『0DM』，其意义为：
D：若为 0 表示可以分段，若为 1 表示不可分段
M：若为 0 表示此 IP 为最后分段，若为 1 表示非最后分段。

- Fragment Offset(分段偏移)
表示目前这个 IP 分段在原始的 IP 封包中所占的位置。就有点像是序号啦，有这个序号才能将所有的小 IP 分段组合成为原本的 IP 封包大小嘛！透过 Total Length, Identification, Flags 以及这个 Fragment Offset 就能够将小 IP 分段在收受端组合起来啰！

- Time To Live(TTL, 存活时间)
表示这个 IP 封包的存活时间，范围为 0-255。当这个 IP 封包通过一个路由器时， TTL 就会减一，当 TTL 为 0 时，这个封包将会被直接丢弃。说实在的，要让 IP 封包通过 255 个路由器，还挺难的～ ^_^

- Protocol Number(协定代码)
来自传输层与网络层本身的其他数据都是放置在 IP 封包当中的，我们可以在 IP 表头记载这个 IP 封包内的资料是啥， 在这个字段就是记载每种数据封包的内容啦！在这个字段记载的代码与相关的封包协议名称如下所示：
<table width="80%" border="1" cellpadding="3" cellspacing="0">
<tbody><tr align="center" bgcolor="#182448"><td width="100"><font color="#FFFFFF">IP 内的号码</font></td><td><font color="#FFFFFF">封包协议名称(全名)</font></td></tr>
<tr bgcolor="lightyellow"><td align="center">1</td><td>ICMP (Internet Control Message Protocol)</td></tr>
<tr><td align="center">2</td><td>IGMP (Internet Group Management Protocol)</td></tr>
<tr><td align="center">3</td><td>GGP (Gateway-to-Gateway Protocol)</td></tr>
<tr><td align="center">4</td><td>IP (IP in IP encapsulation)</td></tr>
<tr bgcolor="lightyellow"><td align="center">6</td><td>TCP (Transmission Control Protocol)</td></tr>
<tr><td align="center">8</td><td>EGP (Exterior Gateway Protocol)</td></tr>
<tr bgcolor="lightyellow"><td align="center">17</td><td>UDP (User Datagram Protocol)</td></tr>
</tbody></table>
当然啦，我们比较常见到的还是那个 TCP, UDP, ICMP 说！

- Header Checksum(表头检查码)
用来检查这个 IP 表头的错误检验之用。

- Source Address
还用讲吗？当然是来源的 IP 地址，从这里我们也知道 IP 是 32 位喔！

- Destination Address
有来源还需要有目标才能传送，这里就是目标的 IP 地址。

- Options (其他参数)
这个是额外的功能，提供包括安全处理机制、路由纪录、时间戳、严格与宽松之来源路由等。

- Padding(补齐项目)
由于 Options 的内容不一定有多大，但是我们知道 IP 每个数据都必须要是 32 bits，所以，若 Options 的数据不足 32 bits 时，则由 padding 主动补齐。

你只要知道 IP 表头里面含有： TTL, Protocol, 来源地址与目标地址也就够了！而这个 IP 表头的来源与目标 IP ，以及那个判断通过多少路由器的 TTL ，就能了解到这个 IP 将被如何传送到目的端吶。后续各小节我们将介绍 IP 的组成与范围，还有 IP 封包如何传送的机制 (路由) 等等。

## 特殊的 loopback IP 网段

好了，那么除了这个预留的 IP 网段的问题之外，还有没有什么其他的怪东西呢？当然是有啦！不然鸟哥干嘛花时间来唬 XX 呢？没错，还有一个奇怪的 Class A 的网域，那就是 lo 这个奇怪的网域啦 (注意：是小写的 o 而不是零喔)！这个 lo 的网络是当初被用来作为测试操作系统内部循环所用的一个网域，同时也能够提供给系统内部原本就需要使用网络接口的服务 (daemon) 所使用。

简单的说，如果你没有安装网络卡在的机器上面， 但是你又希望可以测试一下在你的机器上面设定的服务器环境到底可不可以顺利运作，这个时候怎么办， 嘿嘿！就是利用这个所谓的内部循环网络啦！这个网段在 127.0.0.0/8 这个 Class A，而且默认的主机 (localhost) 的 IP 是 127.0.0.1 呦！所以啰，当你启动了你的 WWW 服务器，然后在你的主机的 X-Window 上面执行 http://localhost 就可以直接看到你的主页啰！而且不需要安装网络卡呢！测试很方便吧！

此外，你的内部使用的 mail 怎么运送邮件呢？例如你的主机系统如何 mail 给 root 这个人呢？嘿嘿！也就是使用这一个内部循环啦！当要测试你的 TCP/IP 封包与状态是否正常时，可以使用这个呦！(所以哪一天有人问你嘿！你的主机上面没有网络卡， 那么你可以测试你的 WWW 服务器设定是否正确吗？这个时候可得回答：当然可以啰！使用 127.0.0.1 这个 Address 呀！ ^_^ )

## ICMP 协定

ICMP 的全名是『 Internet Control Message Protocol, 因特网讯息控制协议 』。 基本上，ICMP 是一个错误侦测与回报的机制，最大的功能就是可以确保我们网络的联机状态与联机的正确性！ ICMP 也是网络层的重要封包之一，不过，这个封包并非独立存在，而是纳入到 IP 的封包中！也就是说， ICMP 同样是透过 IP 封包来进行数据传送的啦！因为在 Internet 上面有传输能力的就是 IP 封包啊！ ICMP 有相当多的类别可以侦测与回报，底下是比较常见的几个 ICMP 的类别 (Type)：

<table width="95%" bgcolor="lightyellow" border="1" cellpadding="3" cellspacing="0">
<tbody><tr align="center" bgcolor="#182448"><td width="80"><font color="#FFFFFF">类别代号</font></td><td><font color="#FFFFFF">类别名称与意义</font></td></tr>
<tr class="text_import1"><td align="center">0</td><td>Echo Reply (代表一个响应信息)</td></tr>
<tr><td align="center">3</td><td>Destination Unreachable (表示目的地不可到达)</td></tr>
<tr><td align="center">4</td><td>Source Quench (当 router 的负载过高时，此类别码可用来让发送端停止发送讯息)</td></tr>
<tr><td align="center">5</td><td>Redirect (用来重新导向路由路径的信息)</td></tr>
<tr class="text_import1"><td align="center">8</td><td>Echo Request (请求响应消息)</td></tr>
<tr><td align="center">11</td><td>Time Exceeded for a Datagram (当数据封包在某些路由传送的现象中造成逾时状态，此类别码可告知来源该封包已被忽略的讯息)</td></tr>
<tr><td align="center">12</td><td>Parameter Problem on a Datagram (当一个 ICMP 封包重复之前的错误时，会回复来源主机关于参数错误的讯息)</td></tr>
<tr><td align="center">13</td><td>Timestamp Request (要求对方送出时间讯息，用以计算路由时间的差异，以满足同步性协议的要求)</td></tr>
<tr><td align="center">14</td><td>Timestamp Reply (此讯息纯粹是响应 Timestamp Request 用的)</td></tr>
<tr><td align="center">15</td><td>Information Request (在 RARP 协议应用之前，此讯息是用来在开机时取得网络信息)</td></tr>
<tr><td align="center">16</td><td>Information Reply (用以响应 Infromation Request 讯息)</td></tr>
<tr><td align="center">17</td><td>Address Mask Request (这讯息是用来查询子网 mask 设定信息)</td></tr>
<tr><td align="center">18</td><td>Address Mask Reply (响应子网 mask 查询讯息的)</td></tr>
</tbody></table>

## 可靠联机的 TCP 协议

在前面的 OSI 七层协议当中，在网络层的 IP 之上则是传送层，而传送层的数据打包成什么？ 最常见的就是 TCP 封包了。这个 TCP 封包数据必须要能够放到 IP 的数据袋当中才行喔！ 所以，我们将图 2.1-4 简化一下，将 MAC, IP 与 TCP 的封包数据这样看：

![各封包之间的相关性][5]

想当然尔，TCP 也有表头数据来记录该封包的相关信息啰？没错啦～ TCP 封包的表头是长这个样子的：

<table class="text_import2" bgcolor="lightyellow" border="1" cellpadding="3" cellspacing="0">
<tbody><tr align="center" bgcolor="lightblue">
	<td colspan="3">Source Port</td>
	<td>Destination Port</td>
</tr>
<tr align="center">
	<td colspan="4">Sequence Number</td>
</tr>
<tr align="center">
	<td colspan="4">Acknowledge Number</td>
</tr>
<tr align="center">
	<td width="60">Data<br> Offset</td>
	<td width="90">Reserved</td>
	<td width="90" bgcolor="lightblue">Code</td>
	<td width="252">Window</td>
</tr>
<tr align="center">
	<td colspan="3">Checksum</td>
	<td>Urgent Pointer</td>
</tr>
<tr align="center">
	<td colspan="3">Options</td>
	<td>Padding</td>
</tr>
<tr align="center">
	<td colspan="4" height="40" bgcolor="lightgreen">Data</td>
</tr>
</tbody></table>

上图就是一个 TCP 封包的表头数据，各个项目以 Source Port, Destination Port 及 Code 算是比较重要的项目，底下我们就分别来谈一谈各个表头数据的内容吧！

## TCP表头

- Source Port & Destination Port (来源埠口 & 目标端口)
什么是埠口(port)？我们知道 IP 封包的传送主要是藉由 IP 地址连接两端， 但是到底这个联机的通道是连接到哪里去呢？没错！就是连接到 port 上头啦！ 举例来说，鸟哥的网站有开放 WWW 服务器，这表示鸟站的主机必须要启动一个可以让 client 端连接的端口，这个端口就是 port (中文翻译成为埠口)。同样的，客户端想要连接到鸟哥的鸟站时，就必须要在 client 主机上面启动一个 port ，这样这两个主机才能够利用这条『通道』来传递封包数据喔！这个目标与来源 port 的纪录，可以说是 TCP 封包上最重要的参数了！

- Sequence Number (封包序号)
由于 TCP 封包必须要带入 IP 封包当中，所以如果 TCP 数据太大时(大于 IP 封包的容许程度)， 就得要进行分段。这个 Sequence Number 就是记录每个封包的序号，可以让收受端重新将 TCP 的数据组合起来。

- Acknowledge Number (回应序号)
为了确认主机端确实有收到我们 client 端所送出的封包数据，我们 client 端当然希望能够收到主机方面的响应，那就是这个 Acknowledge Number 的用途了。 当 client 端收到这个确认码时，就能够确定之前传递的封包已经被正确的收下了。

- Data Offset (资料补偿)
在图 2.4-2 倒数第二行有个 Options 字段对吧！那个 Options 的字段长度是非固定的，而为了要确认整个 TCP 封包的大小，就需要这个标志来说明整个封包区段的起始位置。

- Reserved (保留)
未使用的保留字段。

- Code (Control Flag, 控制标志码)
当我们在进行网络联机的时候，必须要说明这个联机的状态，好让接收端了解这个封包的主要动作。 这可是一个非常重要的句柄喔！这个字段共有 6 个 bits ，分别代表 6 个句柄，若为 1 则为启动。分别说明如下：

    1. URG(Urgent)：若为 1 则代表该封包为紧急封包， 接收端应该要紧急处理，且图 2.4-1 当中的 Urgent Pointer 字段也会被启用。
    1. ACK(Acknowledge)：若为 1 代表这个封包为响应封包， 则与上面提到的 Acknowledge Number 有关。
    1. PSH(Push function)：若为 1 时，代表要求对方立即传送缓冲区内的其他对应封包，而无须等待缓冲区满了才送。 
    1. RST(Reset)：如果 RST 为 1 的时候，表示联机会被马上结束，而无需等待终止确认手续。这也就是说， 这是个强制结束的联机，且发送端已断线。
    1. SYN(Synchronous)：若为 1，表示发送端希望双方建立同步处理， 也就是要求建立联机。通常带有 SYN 标志的封包表示『主动』要连接到对方的意思。
    1. FIN(Finish)：若为 1 ，表示传送结束，所以通知对方数据传毕， 是否同意断线，只是发送者还在等待对方的响应而已。
    其实每个项目都很重要，不过我们这里仅对 ACK/SYN 有兴趣而已，这样未来在谈到防火墙的时候，你才会比较清楚为啥每个 TCP 封包都有所谓的『状态』条件！那就是因为联机方向的不同所致啊！底下我们会进一步讨论喔！ 至于其他的数据，就得请您自行查询网络相关书籍了！

- Window (滑动窗口)
主要是用来控制封包的流量的，可以告知对方目前本身有的缓冲器容量(Receive Buffer) 还可以接收封包。当 Window=0 时，代表缓冲器已经额满，所以应该要暂停传输数据。 Window 的单位是 byte。

- Checksum(确认检查码)
当数据要由发送端送出前，会进行一个检验的动作，并将该动作的检验值标注在这个字段上； 而接收者收到这个封包之后，会再次的对封包进行验证，并且比对原发送的 Checksum 值是否相符，如果相符就接受，若不符就会假设该封包已经损毁，进而要求对方重新发送此封包！

- Urgent Pointer(紧急资料)
这个字段是在 Code 字段内的 URG = 1 时才会产生作用。可以告知紧急数据所在的位置。

- Options(任意资料)
目前此字段仅应用于表示接收端可以接收的最大数据区段容量，若此字段不使用， 表示可以使用任意资料区段的大小。这个字段较少使用。

- Padding(补足字段)
如同 IP 封包需要有固定的 32bits 表头一样， Options 由于字段为非固定， 所以也需要 Padding 字段来加以补齐才行。同样也是 32 bits 的整数。

## TCP 的三向交握

TCP 被称为可靠的联机封包，主要是透过许多机制来达成的，其中最重要的就是三向交握的功能。 当然， TCP 传送数据的机制非常复杂，有兴趣的朋友请自行参考相关网络书籍。 OK，那么如何藉由 TCP 的表头来确认这个封包有实际被对方接收，并进一步与对方主机达成联机？ 我们以底下的图示来作为说明。

![三向交握之封包连接模式][6]

在上面的封包连接模式当中，在建立联机之前都必须要通过三个确认的动作， 所以这种联机方式也就被称为三向交握(Three-way handshake)。 那么我们将整个流程依据上面的 A, B, C, D 四个阶段来说明一下：

- A:封包发起
当客户端想要对服务器端联机时，就必须要送出一个要求联机的封包，此时客户端必须随机取用一个大于 1024 以上的端口来做为程序沟通的接口。然后在 TCP 的表头当中，必须要带有 SYN 的主动联机(SYN=1)，并且记下发送出联机封包给服务器端的序号 (Sequence number = 10001) 。

- B:封包接收与确认封包传送
当服务器接到这个封包，并且确定要接收这个封包后，就会开始制作一个同时带有 SYN=1, ACK=1 的封包， 其中那个 acknowledge 的号码是要给 client 端确认用的，所以该数字会比(A 步骤)里面的 Sequence 号码多一号 (ack = 10001+1 = 10002)， 那我们服务器也必须要确认客户端确实可以接收我们的封包才行，所以也会发送出一个 Sequence (seq=20001) 给客户端，并且开始等待客户端给我们服务器端的回应喔！

- C:回送确认封包
当客户端收到来自服务器端的 ACK 数字后 (10002) 就能够确认之前那个要求封包被正确的收受了， 接下来如果客户端也同意与服务器端建立联机时，就会再次的发送一个确认封包 (ACK=1) 给服务器，亦即是 acknowledge = 20001+1 = 20002 啰。

- D:取得最后确认
若一切都顺利，在服务器端收到带有 ACK=1 且 ack=20002 序号的封包后，就能够建立起这次的联机了。

也就是说，你必须要了解『网络是双向的』这个事实！ 所以不论是服务器端还是客户端，都必须要透过一次 SYN 与 ACK 来建立联机，所以总共会进行三次的交谈！ 在设定防火墙或者是追踪网络联机的问题时，这个『双向』的概念最容易被忽略， 而常常导致无法联机成功的问题啊！切记切记！

## 非连接导向的 UDP 协议

UDP 的全名是：『User Datagram Protocol, 用户数据流协议』，UDP 与 TCP 不一样， UDP 不提供可靠的传输模式，因为他不是面向连接的一个机制，这是因为在 UDP 的传送过程中，接受端在接受到封包之后，不会回复响应封包 (ACK) 给发送端，所以封包并没有像 TCP 封包有较为严密的检查机制。至于 UDP 的表头资料如下表所示：

<a name="fig_udp"></a>
<table style="border-width: 3px; border-color: black; border-style: none;">
<tbody><tr align="center">
	<td style="width: 242px; border-width: 3px; border-style: none solid;">16 bits</td>
	<td style="width: 242px; border-width: 3px; border-style: none solid none none;">16 bits</td>
</tr>
</tbody></table>

<table class="text_import2" bgcolor="lightyellow" border="1" cellpadding="3" cellspacing="0">
<tbody><tr align="center" bgcolor="lightblue">
	<td width="240">Source Port</td>
	<td width="240">Destination Port</td>
</tr>
<tr align="center">
	<td width="240">Message Length</td>
	<td width="240">Checksum</td>
</tr>
<tr align="center" bgcolor="lightgreen">
	<td colspan="2" height="40">Data</td>
</tr>
</tbody></table>

TCP 封包确实是比较可靠的，因为通过三向交握嘛！不过，也由于三向交握的缘故， TCP 封包的传输速度会较慢。 至于 UDP 封包由于不需要确认对方是否有正确的收到数据，故表头数据较少，所以 UDP 就可以在 Data 处填入更多的资料了。同时 UDP 比较适合需要实时反应的一些数据流，例如影像实时传送软件等， 就可以使用这类的封包传送。也就是说， UDP 传输协议并不考虑联机要求、联机终止与流量控制等特性， 所以使用的时机是当数据的正确性不很重要的情况，例如网络摄影机！

另外，很多的软件其实是同时提供 TCP 与 UDP 的传输协议的，举例来说，查询主机名的 DNS 服务就同时提供了 UDP/TCP 协议。由于 UDP 较为快速，所以我们 client 端可以先使用 UDP 来与服务器联机。 但是当使用 UDP 联机却还是无法取得正确的数据时，便转换为较为可靠的 TCP 传输协议来进行数据的传输啰。 这样可以同时兼顾快速与可靠的传输说！

  [1]: http://vbird.dic.ksu.edu.tw/linux_server/
  [2]: https://blog.phpgao.com/usr/uploads/2015/05/1820639952.png
  [3]: https://blog.phpgao.com/usr/uploads/2015/05/585976962.png
  [4]: https://blog.phpgao.com/usr/uploads/2015/05/1070527092.png
  [5]: https://blog.phpgao.com/usr/uploads/2015/05/835328775.png
  [6]: https://blog.phpgao.com/usr/uploads/2015/05/2472601151.png