---
title: "小米路由器mini折腾之DNS查询失败的排查方法"
categories: [ "路由器" ]
tags: [ "shadowsocks","router","chinadns","xiaomi","redsocks2","tcpdump" ]
draft: false
slug: "xiaomi_router_dns_dump"
date: "2015-04-14 15:05:00"
url: "xiaomi_router_dns_dump.html"
---

想必大家为了科学上网，已经为自己的路由器刷上了openwrt/pandorabox了吧？

经过老高的统计，目前关于浏览器访问量最高的是这两篇文章

[小米路由器mini折腾之自动翻墙篇][1]
[小米路由器mini折腾之安装shadowsocks-libev-spec(解决DNS污染)][2]

不知道大家是否能够通过两篇文章的学习研究实现了科学上网呢？


<!--more-->


## 问题来了

目前以老高科学上网所用到的工具，总结起来有以下几种：

1. shadowsocks-libev/shadowsocks-libev-spec
1. chinadns
1. redsocks2

将以上几个工具搭配起来，是目前比较成熟的科学上网途径。但是有些时候总是多多少少会出一些这样那样莫名其妙的问题，比如：

- Google莫名其妙的就打不开了
- mi.com这样的普通域名就然查找不到这样的服务器(DNS解析失败)
- 上网速度很慢，关了chinadns就好了，但是又无法科学上网了
- 总是全局FQ/根本无法FQ
- 手机无法翻墙

新手在面对这样的问题时，确实很难处理，经过老高的慢慢探索，发现我们还是能够使用一些简单的方法来一一排除错误。

下面老高就描述一下老高是怎么样排错并解决问题的。

## 问题和解决方案

### 关于ss

> 我的服务器怎么无法链接？

解决办法：

1. 仔细检查信息是否填错
1. 使用 `netstat -anptl |grep ss|grep LISTEN` 命令检查ss在服务器端是否运行，并且正在监听正确的端口。
1. 使用 `service iptables stop` 命令关闭服务器的防火墙排查是否防火墙挡住了请求

> 我怎么知道我已经顺利的连接上了服务器？

解决办法：

1. 使用QQ的代理登陆，方法：打开qq的网络设置，选择SOCKS5类型的代理，地址为192.168.1.1(路由器的IP)，端口一般为1080。只要能够登陆QQ即可说明成功！
1. 如果使用的是shadowsocks-libev，直接开启透明代理，百度关键字`ip`，如果是国外IP，即可说明成功！

### 关于chinadns

> 有些域名打不开，无法找到服务器

排查方法：

```
# 安装tcpdump前需要执行opkg update
opkg install tcpdump

# 方法一

# 查看本机对路由器的dns请求
tcpdump -i br-lan udp and src net 192.168.1.0/24 and dst host 192.168.1.1
# 查看路由器对dns请求的回应 @192.168.1.110
tcpdump -i br-lan udp and dst host 192.168.1.110

# 方法二

# 安装dig
opkg install bind-dig
# 从本机的默认的dns查询google
dig +trace www.google.com

# 查看chinadns是否正常运行
dig +trace www.google.com -p1053

# 从本机的5300端口查询
dig +trace www.google.com -p5300
# 从114查询Google
dig +trace www.google.com @114.114.114.114
```

通过查询的结果可以得到chinadns是否正常运行。

> 有时候上国内的网站都卡，有时候又很快？

解决办法：

清空DNS缓存试试

WIN:

```
ipconfig /flushdns
```

OSX:

```
# OS X Yosemite

sudo discoveryutil mdnsflushcache

# OS X Mavericks, Mountain Lion, and Lion

sudo killall -HUP mDNSResponder

# Mac OS X v10.6

sudo dscacheutil -flushcache
```

### 关于redsocks2

留坑待填



Reference：

https://support.apple.com/en-us/HT202516





  [1]: https://blog.phpgao.com/carzy_router.html
  [2]: https://blog.phpgao.com/xiaomi_router_shadowsocks_libev_spec.html