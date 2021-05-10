---
title: "扶墙的原理"
categories: [ "服务器技术" ]
tags: [ "shadowsocks" ]
draft: false
slug: "how_to_do_gfw"
date: "2018-06-07 03:32:00"
url: "how_to_do_gfw.html"
---

## 扶墙的原理

简单的说说翻墙到底怎么翻！

<!--more-->


```
              G F W
                +
                |
                |
                |     +----------+
+----------+    |     |          |
|          |    |     |          |
|          |    |     |          |
|   PC     +---XXX---->   Google |
|          |    |     |          |
|          |    |     |          |
+-----+----+    |     |          |
      |         |     +----^-----+
      |         |          |
      |         |          |
      |         |          |
      |         |          |
      |         |     +----+-----+
      |proxy    |     |          |
      |               |          |
      |         |     | VPS  SS  |
      +--------------->          |
                |     |          |
                |     |          |
                |     +----------+
                +

```

PC就是我们的上网设备，可能是计算机，也可以是手机、路由器，当我们想访问谷歌的时候，GFW会识别你的链接并用各种方法阻断谷歌和我们之间的联系，所以我们就会遇到比如网页访问超时，网站失去连接等奇奇怪怪的提示，但是总结一下就是我们在国内无法直接访问谷歌(当然GFW偶尔也会抽风)。

早期的GFW只是很简单的干扰我们的通讯，只要我们修改一下host文件就可以轻松绕过GFW的监控，但是墙也会升级的！在大多数方法纷纷失效后，V2ex论坛上某大神使用python语言基于socks5代理协议开发了`shadowsocks`(简称SS)，并在一段时间后开源在github网址上。

SS的原理和http代理原理一样，都是"绕路走"，避免了GFW的流量筛选，是一种很稳定的扶墙方案！我们的流量都会被加密发送到某服务器(VPS)上，由代理服务器帮我们取回数据，并加密传输给我们，从此我们就可以真正的`网上冲浪`了。

虽然我们的流量是加密的，但是还是有一些流量是有特征的，随着GFW升级，一些SS代理服务器已经可以被检测到，带来的结果就是IP被封，再也无法使用了！于是`shadowsocks-rss(SSR)`出现了！SSR加强了SS的功能，并且支持混淆加密，当然，使用SSR并不代表100%能骗过GFW，流量总是会有特征，所以老高认为比较稳妥的的做法是经常变化加密方式和端口。

所以说，目前想稳定的翻墙，需要你准备一个外国的VPS(当然香港澳门的也行)，并且这个IP地址没有被封，安装Linux系统(Ubuntu、Centos 或者你熟悉的)，安装好SS，你就已经成功了一大半！