---
title: "小米路由器mini折腾之自动翻墙篇"
categories: [ "路由器" ]
tags: [ "shadowsocks","router","redsocks","chinadns","xiaomi","pandorabox","openwrt" ]
draft: false
slug: "carzy_router"
date: "2015-01-04 14:47:00"
url: "carzy_router.html"
---

趁着节假日，终于搞定了路由器的自动翻墙，终于有自己的翻墙路由器了，之后一发而不可收，记录了一些折腾的文章，索引在此

[openwrt路由器折腾教程索引][1]

附赠 [翻越功夫网教程索引][2]

自从发现Pandorabox在`r355版`在固件中整合了ss，redsocks2和ChinaDNS-C，省下了不少事儿。

**有同学反应r355-20150114被移除，刚好老高有存货，文中有百度网盘下载**

**本教程同样适用于其他openwrt系统**。

推荐查看**更好的方案**一节！

你可能根据一下关键词找到此文章：

 - pandorabox openwrt 安装redsocks2
 - **翻墙路由器**
 - 路由器翻墙教程
 - pandorabox openwrt 路由器翻墙
 - 小米路由器翻墙 教程
 - 打造自动翻墙的路由器
 - 设置路由器翻墙

<!--more-->


----------


## 重要更新

感谢@Question提醒

刷完系统后配置好chinadns后，将wan6接口的协议切换成6to4协议，保存后可直连 ipv6.google.com，貌似DNS污染也一并解决了，但是不知为何直连速度不快，不如走SS。


<!--more-->


## 准备工作

### shadowsocks

shadowsocks是目前扶墙利器，原理是通过一台`自由`的服务器做sock5代理，将你的请求都转发到服务器，服务器会把你需要的内容加密返回给你，安全又稳定。

如果你需要自己搭建一个，有下面的教程供你参考，VPS通用。

[不到30元使用一年，超性价比扶墙VPS][3]

[使用shadowsocks轻松搭建FQ环境][4]

这里可以试用 [免费shadowsocks][5]


### 智能路由器

随着小米路由器的发布，想要入手一个入门的智能路由器(能刷openwrt、dd-wrt等基于Linux可定制系统)，就很方便快捷了。而且刷机简单，机器性价比也不错。此文不仅适用于小米路由器，还有很多路由器都可以参考此文，如百度的newifi、极路由等。

如果现在要购买小米路由器，网友@Yacyin有以下建议：

> 还有就是江浙沪的朋友，最好买易迅的版本，京东的版本是4月份的，做工明显糙很多，符合小米一贯质次价高的特点，要不是现在不好更新，我肯定买极路由2S了


----------


**刷机**

小米路由器如何刷机请参考[小米路由器刷潘多拉固件教程][6] 和 [潘多拉固件下载地址][7]，等开启了路由器SSH权限后我们就可以开始刷机了！

> 有些固件版本你可能找不到了，老高这儿还有存货。链接: 链接: [http://pan.baidu.com/s/1mgnBcIc][8] 密码: yvi7

**刷机注意**

> 注意千万不能用官方的刷机方法，把PandoraBox重命名为xxx.bin，然后插U盘刷机，刷完你会哭的。
正确的步骤是官方固件开启ssh后使用命令刷机

**最后**就是刷机了，刷机的命令在下面，其中`/tmp/PandoraBox-ralink-xiaomi-mini-r327-20141226.bin`是刷机包的文件名。

```bash
mtd -r write /tmp/PandoraBox-ralink-xiaomi-mini-r327-20141226.bin firmware

# garu同学反应以上命令无法刷机，如果出现类似情况，请使用以下命令刷
mtd -r write /tmp/PandoraBox-ralink-xiaomi-mini-r327-20141226.bin OS1
```

## 科学上个网配置

刷完机器的路由器就有以下`技能`，不过技能可能随着你刷的版本而变，目前(20150526)还是建议刷稳定版(stable)，版本号r512。

![skill][9]

其中shadowsocks负责代理，是搬运工；
ChinaDNS负责解析域名，防止DNS污染，功夫网最近升级，目测ChinaDNS工作起来不太稳定；
最后的redsocks2负责判断线路是否正常，否则走代理访问，即自动翻墙，redsocks2是目前比较只能的扶墙工具，但是缺点也很明显，你会感到明显的网络延迟，不推荐玩游戏等对延迟有高要求的用户使用。

废话真多，赶紧进入正题吧！

----------


### opkg源

`opkg`类似centos的`yum`，Ubuntu的`apt-get`，我们用它安装编译好的软件，省时省力。想要自己编译对应平台的软件，请参考[编译小米路由mini的openwrt固件][10]。

配置opkg这一步需要十分重要，后续的很多操作都依赖此项，针对小米或同种类的CPU`MTK MT7620A`，我们可以在pandorabox的后台位于 系统->软件包->配置。

配置方法可以参考[小米路由器mini折腾之配置opkg篇][11]。

> 配置好opkg后可以点击刷新列表来更新软件列表，也可以在后台运行命令  opkg update

**注意**，更新完毕后一定解决的问题-------安装libc。否则会遇到类似下面的错误！

> satisfy_dependencies_for: Cannot satisfy the following dependencies for redsocks2:libc * libc *


### 配置shadowsocks客户端

shadowsocks的服务端配置也很简单，请参考[使用shadowsocks轻松搭建FQ环境][12]。

**下面我们主要将客户端的配置**

当我们在远程服务器配置完毕后 或者 购买他人提供的shadowsocks服务后，我们能够得到以下配置：

1. ip->服务器IP地址
1. prot->服务器开放的端口号
1. 密码->服务器验证密码
1. method->加密方式

有了以上信息，我们可以开始基本配置，如下图：

![基本配置][13]

下面我们开启本地sock5代理，以供测试配置是否生效，设置如下图：

![本地代理设置][14]

这两步配置相当于运行了以下命令(ss-local是客户端的命令行工具)

```bash
/usr/bin/ss-local -s 11.11.11.11 -p 12345 -l 1080 -m des-cfb -k passwd -u -f /var/run/ss-local.pid
```

----------

**最重要的一步在此，请注意**

透明代理如何配置？

透明代理即默认将所有数据请求都转发给ss完成，然而这样做会浪费流量，所以我们开启IP白名单（white list），IP白名单中全是中国的IP，在白名单中的IP不会转发至代理服务器，保证了访问速度，节省了流量，并基本实现了自动翻墙。

**此方法的缺点是不够只能，IP白名单需要经常升级。**

此配置的难点是如何获取国内IP段，我们使用如下简单的技巧可以获取最新的国内IP段。

> ps.IP白名单利用了dnsmasq中的ipset

```bash
# 从ftp中获取中国IP段
curl 'http://ftp.apnic.net/apnic/stats/apnic/delegated-apnic-latest' | grep ipv4 | grep CN | awk -F\| '{ printf("%s/%d\n", $4, 32-log($5)/log(2)) }' > /etc/chinadns_chnroute.txt
```

以上的命令会把数据保存在`/etc/chinadns_chnroute.txt`中，而ChinaDNS-C的菜单中会读取这一文本，所以我们只要移步至ChinaDNS-C菜单并拷贝*ChinaDNS-C*里的信息至shadowsocks透明代理的IP白名单中即可！如果没有luci界面的同学可以使用`scp`命令将/etc/chinadns_chnroute.txt导出再复制粘贴。

最终的配置如下图：

![透明代理][15]

### ChinaDNS

ChinaDNS需要指定国内IP和污染IP段，需要使用脚本定期更新维护，否则可能会出现解析异常。

通过后台执行`ps|grep chinadns`，找到chinadns使用的资源文件。

> chinadns -l /etc/chinadns_iplist.txt -c /etc/chinadns_chnroute.txt -d -p 1053 -s 114.114.114.114,8.8.8.8

下面是更新命令，可以作为计划任务运行，计划任务位于  `系统 --->  计划任务`

```bash
# 安装curl

opkg install curl

# 手动更新
curl 'http://ftp.apnic.net/apnic/stats/apnic/delegated-apnic-latest' | grep ipv4 | grep CN | awk -F\| '{ printf("%s/%d\n", $4, 32-log($5)/log(2)) }' > /etc/chinadns_chnroute.txt

# 自动更新
# 每天凌晨4点更新文件，注意文件路径
0 4 * * * curl 'http://ftp.apnic.net/apnic/stats/apnic/delegated-apnic-latest' | grep ipv4 | grep CN | awk -F\| '{ printf("%s/%d\n", $4, 32-log($5)/log(2)) }' > /etc/chinadns_chnroute.txt
```

![计划任务更新ChinaDNS的IP段][16]


**更高级的配置**可以参考[OpenWrt-dist的Wiki][17]里面的DNS篇。

老高的设置

![chinadns配置][18]

检查命令，192.168.1.1为路由器IP，1053为ChinaDNS的端口。

```bash
dig @192.168.1.1 www.phpgao.com -p1053
```

### redsock2

更多redsocks2的配置可以参考[小米路由器mini折腾之redsocks2的配置篇][19]

前面的配置基本能够满足自动翻墙的需要了，但是使用ss白名单的不好之处就是国内IP需要定时更新，否则可能造成由于IP判断错误而打不开网页的情况，那么如果我很懒，不想经常去更新IP表，有什么更好的解决方案吗？

答案是有的。

redsocks2实现了自动翻墙，原理是先由iptables转发所有tcp请求至预先设定的端口，然后程序会尝试访问目标，如果在指定的时间内服务器有反应，那么就继续完成请求，否则让此次请求走代理线路，所以需要ss的配合。

由于有些版本的固件移除了redsocks2，我们需要把他找回来，以下是找回的方法：

```bash
# 安装redsocks2
opkg install redsocks2
# 安装redsocks2后台luci菜单
cd /tmp
# wget 如果404，请移步 http://sourceforge.net/projects/openwrt-dist/files/luci-app/redsocks2/ 自己下载ipk，然后安装
wget http://iweb.dl.sourceforge.net/project/openwrt-dist/luci-app/redsocks2/luci-app-redsocks2_1.3.0-1_all.ipk
opkg install luci-app-redsocks2_1.3.0-1_all.ipk
```

redsocks2运行后会修改系统的防火墙。

![iptables配置][20]

图片说明：所有非内网的tcp流量都转发至1081端口，当然，这些工作都是后台自动完成的。

redsock2的具体配置说明如下图，当然你还可以参考[小米路由器mini折腾之redsocks2的配置篇][21]。

![redsock2的配置][22]

效果图如下，不需要在手机上做任何设置

![redsocks2][23]

## 更好的方案

shadowsocks-libev-spec是shadowsocks-libev针对openwrt的优化版本，其中UDP转发可以彻底解决DNS污染问题，自动翻墙的配置更加简单，只需要配置好shadowsocks-libev-spec就可以实现！

请参考下面的链接⬇️

[小米路由器mini折腾之安装shadowsocks-libev-spec][24]

## 远程访问

小米论坛有一篇讲开启8080端口的教程，[玩转路由：开启Lamp,用小米路由器抢建你的私人博客][25]

其中最重要的就是这个：

```bash
vi /etc/config/firewall

# ADD
config rule 'httpdwan'
option src 'wan'
option dest_port '8088'
option proto 'tcp'
option target 'ACCEPT'
option name ''\''httpd wan accept tcp port 8088'\'''
```

这样就开启了8080，配合[DDNS][26]就可以完美远程管理路由器了

具体想到的就这么多了，有什么疑问请留言。

## FAQ

> 安装某个软件时总是提示类似**libc libgcc**找不到？

请参考[小米路由器mini折腾之配置opkg篇][27] 文末。

> 我的界面怎么和你的不一样？

系统 - 语言和界面 - 主题 改成 Bookstrap，默认的是Luci

> 新版固件内核不兼容？还是换回稳定版吧。

```
r858
Linux PandoraBox 3.14.42 #1 Sun May 17 20:39:27 CST 2015 mips GNU/Linux

r820
Linux PandoraBox 3.14.40 #1 Wed May 13 20:38:46 CST 2015 mips GNU/Linux

r512
Linux PandoraBox 3.10.70 #15 Mon Mar 9 19:33:59 CST 2015 mips GNU/Linux
```



2015年01月17日更：

潘多拉小米mini版已升级到stable，推荐更新。下载地址：

[http://downloads.openwrt.org.cn/PandoraBox/Xiaomi-Mini-R1CM/stable/][28]

2015年02月12日更：

resocks2的安装和使用

2015年03月04日更：

补充被删除的固件PandoraBox-ralink-xiaomi-mini-r355-20150114

[PandoraBox-ralink-xiaomi-mini-r355-20150114.bin][29]

2015年04月13日更：

更好的方案一节。

2015年05月26日更：

重铸此文。


  [1]: https://blog.phpgao.com/xiaomi_router.html
  [2]: https://blog.phpgao.com/slip_across_gfw.html
  [3]: https://blog.phpgao.com/vps.html
  [4]: https://blog.phpgao.com/shadowsocks_on_linux.html
  [5]: https://blog.phpgao.com/try_shadowsocks_via_qrcode.html
  [6]: http://www.miui.com/thread-2036705-1-1.html
  [7]: http://downloads.openwrt.org.cn/PandoraBox/Xiaomi-Mini-R1CM/testing/
  [8]: http://pan.baidu.com/s/1mgnBcIc
  [9]: http://ww4.sinaimg.cn/large/6735b7fatw1enw9m9pa5nj208u0gyaam.jpg
  [10]: https://blog.phpgao.com/xiaomi_router_openwrt.html
  [11]: https://blog.phpgao.com/xiaomi_router_opkg.html
  [12]: https://blog.phpgao.com/shadowsocks_on_linux.html
  [13]: http://ww4.sinaimg.cn/large/6735b7fatw1eq24hkpo3sj20hs0cpab4.jpg
  [14]: http://ww3.sinaimg.cn/large/6735b7fatw1eq260c9nd8j208w033t8k.jpg
  [15]: http://ww4.sinaimg.cn/large/6735b7fatw1eq26k2sbbjj20d50dcaao.jpg
  [16]: https://blog.phpgao.com/usr/uploads/2015/05/3816985260.png
  [17]: http://sourceforge.net/p/openwrt-dist/wiki/DNS/
  [18]: http://ww4.sinaimg.cn/large/6735b7fatw1enyjs2vbs3j208r0dcq3f.jpg
  [19]: https://blog.phpgao.com/redsocks_config.html
  [20]: http://ww4.sinaimg.cn/large/6735b7fatw1enyjy47watj20m80ddwg2.jpg
  [21]: https://blog.phpgao.com/redsocks_config.html
  [22]: http://ww1.sinaimg.cn/large/6735b7fatw1ep6xh17vo9j20ce0dc75c.jpg
  [23]: http://ww3.sinaimg.cn/large/6735b7fatw1epy2vbgkdxj20690b4wew.jpg
  [24]: https://blog.phpgao.com/xiaomi_router_shadowsocks_libev_spec.html
  [25]: http://www.miui.com/thread-1788492-1-1.html
  [26]: https://blog.phpgao.com/xiaomi_router_ddns.html
  [27]: https://blog.phpgao.com/xiaomi_router_opkg.html
  [28]: http://downloads.openwrt.org.cn/PandoraBox/Xiaomi-Mini-R1CM/stable/
  [29]: http://pan.baidu.com/s/1bn3zeFX