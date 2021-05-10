---
title: "玩转VPS之快速搭建HTTP代理"
categories: [ "代码人生" ]
tags: [ "tinyproxy" ]
draft: false
slug: "vps_tinyproxy"
date: "2019-08-31 03:42:00"
url: "vps_tinyproxy.html"
---

起因：今天想给家里的服务器装一个MacOS，于是果断打开小兵的下载页面，找到最新的14版系统，但是发现一个问题，不论怎么操作，下载速度都不会超过200KB/S，只有氪金了(迅雷)速度才能达到5M，这可难倒了老高！

不过随着老高的不断尝试，发现使用搬瓦工VPS，直接wget下载速度是8M，稳定的8M，竟然比迅雷都快一点。好嘛，找到了突破口，下面的事情就是如何通过VPS把这个6G大的镜像运到本地！


<!--more-->


常规的做法是wget下载完毕后scp回本地，不过这么做的缺点很明显：

1. 搬瓦工的服务器硬盘很有限，比如老高的服务器，硬盘只有10G，剩余空间4G，装不下6G的文件，真是悲剧😂
2. 流量翻倍，一来一回12G流量没了，不过好在一个月500G流量无压力

综上，老高决定用最快的方法搭建一个HTTP代理服务，这样相当于文件只是经过VPS而不用保存，并且只花费6G流量，真是一举两得！于是谷歌随便翻一下，抓到一个tinyproxy镜像[dannydirect/tinyproxy][1]，使用方法简单到可怕！不过镜像有一点点问题，老高修复了一版！[endoffight/tinyproxy][2]，欢迎围观。



```bash
# 假设代理端口为7777，8888不要改
# ANY的意思是所有IP都可以建立连接
# ANY使用acl语法，所以 10.103.0.100 192.168.1.22/16都是可以的
# 保险的运行方式是先找到自己的IP地址，使用http://www.ip138.com/
# 然后把ANY改为自己的IP
# 比如老高的家里的地址为8.8.8.8

docker run -d --name='tinyproxy' -p 7777:8888 endoffight/tinyproxy ANY
# 或
docker run -d --name='tinyproxy' -p 7777:8888 endoffight/tinyproxy 8.8.8.8
```

搭建玩代理，本地如何使用呢？更简单了！

```
# 请替换proxy_ip为你的VPS地址，端口为刚才设置的7777
wget -c "https_proxy=http://proxy_ip:7777" https://mirrors.dtops.cc/iso/MacOS/daliansky_macos/macOS%20Mojave%2010.14.6%2818G87%29%20Installer%20with%20Clover%205033.dmg
```

速度如下，平均3.91 MB/s，已经很快了！

```
2019-08-31 11:12:26 (3.91 MB/s) - ‘mirrors.dtops.cc/iso/MacOS/daliansky_macos/macOS Mojave 10.14.6(18G87) Installer with Clover 5033.dmg’ saved [6121549956/6121549956]
```


  [1]: https://hub.docker.com/r/dannydirect/tinyproxy
  [2]: https://hub.docker.com/r/endoffight/tinyproxy