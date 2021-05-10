---
title: "各个系统安装dig工具的教程"
categories: [ "服务器技术" ]
tags: [ "dns","dig" ]
draft: false
slug: "how_to_install_dig"
date: "2015-04-27 02:28:00"
url: "how_to_install_dig.html"
---

dig是一个很不错的工具，它比nslookup好用太多。

老高主要用dig判断DNS解析故障和DNS解析线路的问题。dig命令虽然好用，但是想要在各个系统中安装使用dig，安装方法还是要记录一下的。

那么如何安装呢？老高总结了linux服务器，openwrt，Windows等系统的安装dig的方法，希望能够帮助到你。


<!--more-->


## Openwwrt

```
opkg update
opkg install bind-dig
```

## Fedora / Centos

```
yum install bind-utils
```

## Ubuntu

```
sudo apt-get install dnsutils
```

## Windows

[http://www.isc.org/downloads/bind/][1]


  [1]: http://www.isc.org/downloads/bind/