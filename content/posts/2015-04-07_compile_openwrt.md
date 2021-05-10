---
title: "编译openwrt"
categories: [ "服务器技术" ]
tags: [ "compile","pandorabox","openwrt" ]
draft: false
slug: "compile_openwrt"
date: "2015-04-07 02:20:00"
url: "compile_openwrt.html"
---

一些资料


<!--more-->


[OpenWrt Buildroot – Usage][1]
[http://wiki.openwrt.org/doc/howto/obtain.firmware.sdk][2]
[OpenWrt Buildroot][3]
[How to Build a Single Package][4]
[DNSCrypt][5]
[Index of /barrier_breaker/14.07/ramips/mt7620a/][6]
[OpenWrt source repository downloads][7]
[Welcome to the OpenWrt development center][8]
[获取固件][9]
[创建软件包][10]
[OpenWrt Buildroot – 使用说明][11]
[OpenWrt简体中文Wiki » 文档][12]
[openwrt编译日志 ][13]
[在华为HG255D OpenWrt上安装和配置Shadowsocks并实现智能流量转发][14]
[为HG255D编译OpenWrt Barrier Breaker固件][15]
[【整理】搭建自己的OpenWrt开发环境][16]
[【整理】如何在OpenWRT环境下做开发][17]
[怎么编译MT7620A程序包][18]
[OpenWRT交叉编译][19]
[dnscrypt-proxy 1.3.3下载，不依赖libsodium][20]
[Openwrt研习笔记三之代码下载及编译][21]

## 编译准备

编译系统： ubuntu
软件包： `apt-get install build-essential subversion libncurses5-dev zlib1g-dev gawk gcc-multilib flex git-core get text`
源码：`git clone git://git.openwrt.org/14.07/openwrt.git`

## 开始编译

准备用户： `useradd -d /home/xxx -m xxx`
更新源：
```
./scripts/feeds update -a
./scripts/feeds install -a
```
编译配置：`make menuconfig`

编译： `make V=s`


  [1]: http://wiki.openwrt.org/doc/howto/build
  [2]: http://wiki.openwrt.org/doc/howto/obtain.firmware.sdk
  [3]: http://downloads.openwrt.org/docs/buildroot-documentation.html
  [4]: http://wiki.openwrt.org/doc/howtobuild/single.package
  [5]: http://wiki.openwrt.org/inbox/dnscrypt
  [6]: https://downloads.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/
  [7]: https://dev.openwrt.org/wiki/GetSource
  [8]: https://dev.openwrt.org/wiki
  [9]: http://wiki.openwrt.org/zh-cn/doc/howto/obtain.firmware
  [10]: http://wiki.openwrt.org/zh-cn/doc/devel/packages
  [11]: http://wiki.openwrt.org/zh-cn/doc/howto/build
  [12]: http://wiki.openwrt.org/zh-cn/doc/start
  [13]: http://blog.sina.com.cn/s/blog_5a173b4b0101fqy4.html
  [14]: http://www.shuyz.com/install-shadowsocks-on-hg255d-openwrt-and-config-nat.html
  [15]: http://demon.tw/hardware/hg255d-compile-openwrt-barrier-breaker.html
  [16]: http://hi.baidu.com/gouooo/item/5b2b11c2b1ffe3320831c66a
  [17]: http://hi.baidu.com/gouooo/item/6932bfa97d23d1981410736a
  [18]: http://www.right.com.cn/forum/thread-144355-1-1.html
  [19]: http://www.cnblogs.com/02xiaoma/p/4314592.html
  [20]: http://www.right.com.cn/forum/thread-136035-1-1.html
  [21]: http://www.ourgarden.cn/openwrt%E7%A0%94%E4%B9%A0%E7%AC%94%E8%AE%B0%E4%B8%89%E4%B9%8B%E4%BB%A3%E7%A0%81%E4%B8%8B%E8%BD%BD%E5%8F%8A%E7%BC%96%E8%AF%91/