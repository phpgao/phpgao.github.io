---
title: "Charles的代理设置"
categories: [ "代码人生" ]
tags: [ "charles" ]
draft: false
slug: "setup_charles"
date: "2019-07-08 02:12:00"
url: "setup_charles.html"
---

MacOS下的开发少不了抓包调试，而Charles绝对是开发者必不可少的抓包工具！

不过老高在使用Charles时遇到了不少坑，下面总结一下再MacOS下Charles的使用方法吧。


<!--more-->


## 信任证书

为什么要信任证书？这不废话么，Charles利用了中间人攻击，所以需要先让你的机器信任他的证书才可以进行正常劫持。

这一点Charles已经做到很傻瓜了。

```
# 为你的MacOS安装证书
Help --> SSL Proxying --> Install Charles Root Certificate
```

如果要给手机安装证书，那么可以参考下面一段话：

```
Configure your device to use Charles as its HTTP proxy on 192.168.2.198:8888, then browse to chls.pro/ssl to download and install the certificate.

Note that on iOS 10 and later you must then go into Settings > General > About > Certificate Trust Settings and enable the Charles certificate to be trusted.
```

大意：

1. 确保你的设备的代理设置为 `192.168.2.198:8888`，**不同的机器IP代理IP和端口不同!**
2. 用手机访问 `chls.pro/ssl`，然后下载并安装证书即可！
3. 额外的，IOS10+的手机，需要到，设置>通用>关于>证书信任，信任Charles的证书

done!

## 自动代理

在设置Charles代理的时候，Charles有一个选项，在 `Proxy --> macOS Proxy`。如果这个选项打勾，Charles会帮你自动设置MacOS的http和https代理为Charles提供的代理。

这个功能虽然很方便，但是也会影响一些APP的使用。比如老高在开启Charles后，使用`pip3 install scrapy`命令的时候，就会被抛出下面的错误，很明显，pip在程序中检查了证书，所以导致无法安装新包。

```
WARNING: Retrying (Retry(total=4, connect=None, read=None, redirect=None, status=None)) after connection broken by 'SSLError(SSLError("bad handshake: Error([('SSL routines', 'tls_process_server_certificate', 'certificate verify failed')])"))': /pypi
```

解决的办法也很简单，取消打勾就行。

在日常的开发中，老高还是偏向明确指定代理地址，而不是在程序执行的环境中设置，除非你明确的知道这样做的影响和带来的后果。

## 高级操作

### 仅代理部分url

Proxy --> SSL Proxying Settings

不要使用`*:443`，而是针对需要调试的域名，比如`*.googe.com:443` `www.phpgao.com:443`

### throttle

这个词可以理解为，油门，阀门，用来控制流量的，即访问网络的速度。在设置界面我们可以改的很多，这一点不展开讲了

### Map功能

有两个：

Map Remote 和Map Local，其功能是把指定的请求映射到另一个远程或者本地的地址。是不是有点像反向代理？

### Rewrite

这个功能支持修改req和resp中的信息，因为http对于我们已经是明文了，所以想改什么都可以。


### 更多

待续
