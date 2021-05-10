---
title: "小米路由器mini折腾之redsocks2的配置篇"
categories: [ "路由器" ]
tags: [ "router","redsocks","xiaomi" ]
draft: false
slug: "redsocks_config"
date: "2015-05-17 04:45:00"
url: "redsocks_config.html"
---

刚刚(2015-05-17 12:00:15)老高刷了目前最新的小米路由mini的pandorabox固件(**r820**)

很多朋友说这个版本的redsocks2升级为加强版，选项太多，于是不会配置了。

下面老高就来讲讲如何配置redsocks2，以及如何与chinadns联合配置。

如果需要了解更详细的配置，请参考老高的另一篇 [小米路由器mini折腾之自动翻墙篇][1]


<!--more-->


## 首先

> 我们需要了解redsocks2是做什么的？

其实很简单，redsocks2在路由器中为我们扮演了一个**代理者**的角色。

> 什么？shadowsocks不也是代理么？

是的，但redsocks2是代理(shadowsocks)的上级代理。redsocks2作为代理的大**BOSS**，掌握着各种代理的使用权。

> 尼玛，啥叫代理的代理？

意思就是将原本我们直接扶墙**代理**给redsocks2，让redsocks2来决定下一步走什么代理去联网。redsocks2会智能判断线路是否需要使用shadowsocks。目前路由器上的redsocks2支持本地的socks5代理以及ss直连。

> 这样会不会给CPU带来负担

redsocks2目前算是很智能的扶墙方式了，在判断是否需要扶墙的时候当然会占用一定的CPU，不过经过老高的测试，redsocks2的CPU占用已经不会在占用大量的CPU了。

但是与传统方式相比会有明显的延迟。


> 这次多出来的UDP转发是什么意思？

简单的说，可以理解为加密DNS查询，也就是通过redsocks2发出国外IP的DNS查询，防止功夫网来污染。

> socks5代理以及ss直连到底用哪个？

如果你的服务器信息变了，socks5代理只需要改一次配置，ss直连需要改多次。

## 如何配置

### 直连ss

配置透明代理

![配置透明代理][2]

配置UDP转发

![配置UDP转发][3]

### socks5配置

socks5的配置基本同上面一样

变化如下图

![socks5配置][4]

## 配置redsocks2 + chinadns

能上图的话我就不说话

![配置redsocks2 + chinadns][5]


  [1]: https://blog.phpgao.com/carzy_router.html
  [2]: https://blog.phpgao.com/usr/uploads/2015/05/2896992765.png
  [3]: https://blog.phpgao.com/usr/uploads/2015/05/1805906916.png
  [4]: https://blog.phpgao.com/usr/uploads/2015/05/78218841.png
  [5]: https://blog.phpgao.com/usr/uploads/2015/05/1280219727.png