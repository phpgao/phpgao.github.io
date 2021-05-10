---
title: "使用Privoxy将socks5代理转为http代理"
categories: [ "代码人生" ]
tags: [ "http","shadowsocks","proxy","privoxy","socks5" ]
draft: false
slug: "privoxy-shadowsocks"
date: "2015-06-19 02:04:00"
url: "privoxy-shadowsocks.html"
---

![访问谷歌][1]

大家都知道shadowsocks只提供了SOCKS5的代理，而没有提供http代理，而很多软件仅提供了http代理的支持，比如老高需要使用shell命令行扶墙，或者使用git同步android的源代码，再或者更新android SDK，该怎么办呢？

老高在此介绍一个软件[Privoxy][2]，它可以作为**代理的代理**，为我们解决上述问题！

> Privoxy is a non-caching web proxy with advanced filtering capabilities for enhancing privacy, modifying web page data and HTTP headers, controlling access, and removing ads and other obnoxious Internet junk. Privoxy has a flexible configuration and can be customized to suit individual needs and tastes. It has application for both stand-alone systems and multi-user networks.

由介绍看来，privoxy的功能可远远不止http代理这么简单！

<!--more-->

教程场景：

老高的路由器刷了openwrt，并安装了shadowsocks在1080端口提供SOCKS5代理服务，现在我们使用Privoxy将其转化为http代理。

## 安装

先登录到路由器

```bash
ssh root@192.168.1.1
root@192.168.1.1's password:


BusyBox v1.22.1 (2015-06-06 07:53:28 CST) built-in shell (ash)
Enter 'help' for a list of built-in commands.


  _______________________________________________________________
 |    ____                 _                 ____               |
 |   |  _ \ __ _ _ __   __| | ___  _ __ __ _| __ )  _____  __   |
 |   | |_) / _` | '_ \ / _` |/ _ \| '__/ _` |  _ \ / _ \ \/ /   |
 |   |  __/ (_| | | | | (_| | (_) | | | (_| | |_) | (_) >  <    |
 |   |_|   \__,_|_| |_|\__,_|\___/|_|  \__,_|____/ \___/_/\_\   |
 |                                                              |
 |                  PandoraBox SDK Platform                     |
 |                  The Core of SmartRouter                     |
 |       Copyright 2013-2015 D-Team Technology Co.,Ltd.SZ       |
 |                http://www.pandorabox.org.cn                  |
 |______________________________________________________________|
  Base on OpenWrt BARRIER BREAKER (14.09, r1018)
[root@PandoraBox:/root]#
```

Privoxy在路由器上的安装很简单，一行代码即可搞定！

```bash
opkg install privoxy
```

如果提示找不到安装包，可以参考[小米路由器mini折腾之配置opkg篇][3]检查opkg的配置。

其他平台可以使用`yum apt-get`命令安装

如果解决不了，请移步官方的安装包[下载地址][4]

## 配置

Privoxy的配置文件位于`/etc/privoxy/config`，下来我们用`vim`编辑它。

注意老高的注释！

```bash
vim /etc/privoxy/config


# 如此多的配置！看来privoxy的功能很强大
confdir /etc/privoxy
logdir /var/log
filterfile default.filter
logfile privoxy
actionsfile match-all.action # Actions that are applied to all sites and maybe overruled later on.
actionsfile default.action   # Main actions file
#actionsfile user.action      # User customizations

# 监听端口为8118，如果需要其他设备访问，则需要将ip改为路由器的IP 192.168.1.1 或 0.0.0.0 或者直接 :8118
listen-address  192.168.1.1:8118
toggle  1
enable-remote-toggle  1
enable-remote-http-toggle  0
enable-edit-actions 1
enforce-blocks 0
buffer-limit 4096
forwarded-connect-retries  0
accept-intercepted-requests 0
allow-cgi-request-crunching 0
split-large-forms 0
keep-alive-timeout 300
socket-timeout 300
permit-access  192.168.1.0/24
debug   1    # show each GET/POST/CONNECT request
debug   4096 # Startup banner and warnings
debug   8192 # Errors - *we highly recommended enabling this*
#admin-address privoxy-admin@example.com
#proxy-info-url http://www.example.com/proxy-service.html

# 由于我们的ss运行于1080端口，我们加入下面的配置，不要忘了最后的点.
forward-socks5 / 127.0.0.1:1080 .
```

配置好代理设置我们就可以重启privoxy了

```bash
/etc/init.d/privoxy restart
```

如果没有报错，就说明启动成功！

## 测试

```
curl --connect-timeout 2 -x 127.0.0.1:8118 http://google.com
```

只要返回值类似以下代码即表示成功！

```html
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>
```

## 使用

最简单的使用就是chrome下的SwitchyOmega扩展，我们按照下图配置

![SwitchyOmega][5]

然后在浏览器中启用http代理，如下图

![privoxy工作正常][6]

完美运行！

既然现在我们有了一个运行在8118端口的http扶墙代理，那么现在我们想让终端扶墙怎么办？

将下面的代码添加到`~/.bashrc`即可！

```bash
export http_proxy=http://192.168.1.1:8118
export https_proxy=http://192.168.1.1:8118
```

## 解决SDK更新

配制好HTTP代理后，我们将ANDROID SDK MANAGER按照如下设置

![ANDROID SDK MANAGER][7]

## 更多

privoxy还有更多的用法等你发掘！

老高再提供一个关键字 proxychains，能够更加容易的实现命令行扶墙！


  [1]: https://blog.phpgao.com/usr/uploads/2015/06/13067004.png
  [2]: http://www.privoxy.org
  [3]: https://blog.phpgao.com/xiaomi_router_opkg.html
  [4]: http://sourceforge.net/projects/ijbswa/files/
  [5]: https://blog.phpgao.com/usr/uploads/2015/06/3978620669.png
  [6]: https://blog.phpgao.com/usr/uploads/2015/06/13067004.png
  [7]: https://blog.phpgao.com/usr/uploads/2015/06/2885105780.png