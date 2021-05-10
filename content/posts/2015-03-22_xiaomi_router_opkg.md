---
title: "小米路由器mini折腾之配置opkg篇"
categories: [ "路由器" ]
tags: [ "router","xiaomi","pandorabox","opkg" ]
draft: false
slug: "xiaomi_router_opkg"
date: "2015-03-22 15:41:00"
url: "xiaomi_router_opkg.html"
---

## 动机

opkg是openwrt里的软件包管理器，类似mac下的brew、Ubuntu下的apt-get和centos下的yum。

但是为什么有很多同学都没办法在openwrt中使用opkg，其主要原因其实嵌入式的硬件繁多，不像我们一般PC只有i386和AMD64之分。

此方法适用于OpenWRT,Pandorabox,官方系统。


<!--more-->


> ps.工具链是什么？可以参考 [http://wiki.openwrt.org/zh-cn/about/toolchain][1]

举个例子：

老高用C语言写了一个HELLO_WORLD程序，然后使用交叉编译出适合小米路由mini的程序，然后拿到小米路由中去执行时执行不了的。因为硬件不同，程序运行的环境不同。

所以一个软件的发布是个很头大的问题，下图是chinadns的下载地址，大家数数有几个平台的编译版本：

![chinadns的多平台][2]


## 如何配置opkg

其实读懂了上面的例子，解决的方法其实很简单。

小米路由器mini的CPU为`MTK MT7620A 单核580MHz`，小米路由器的CPU为`博通 BCM4709`。

mt7620a平台所需要的软件包，工具链等都能在这里找到：

 http://downloads.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/

有了以上的数据我们就可以开始配置opkg！

```bash
# 备份旧的
mv /etc/opkg.conf /etc/opkg.conf.bak

# 使用vi编辑配置文件
vi /etc/opkg.conf
```

**此处更方便的方法是后台进入 系统->软件包->配置**

![openwrt软件配置][3]

然后在输入框中输入以下内容（注意notice处，/mnt/sdb1/opkg为挂载U盘或移动硬盘的全路径）

```
dest root /
dest ram /tmp
lists_dir ext /etc/opkg-lists
option overlay_root /overlay
# notice
dest usb /mnt/sdb1/opkg

arch all 100
arch ramips_24kec 200
arch ramips 300
arch mips 400
arch unkown 500


src/gz barrier_breaker_base http://downloads.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/packages/base
src/gz barrier_breaker_luci http://downloads.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/packages/luci
src/gz barrier_breaker_management http://downloads.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/packages/management
src/gz barrier_breaker_oldpackages http://downloads.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/packages/oldpackages
src/gz barrier_breaker_packages http://downloads.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/packages/packages
src/gz barrier_breaker_routing http://downloads.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/packages/routing
src/gz barrier_breaker_telephony http://downloads.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/packages/telephony
```

**加速版本**

使用了`mirrors.ustc.edu.cn`的反代加速

```
dest root /
dest ram /tmp
lists_dir ext /etc/opkg-lists
option overlay_root /overlay
# notice
dest usb /mnt/sdb1/opkg

arch all 100
arch ramips_24kec 200
arch ramips 300
arch mips 400
arch unkown 500


src/gz barrier_breaker_base http://mirrors.ustc.edu.cn/openwrt/barrier_breaker/14.07/ramips/mt7620a/packages/base
src/gz barrier_breaker_luci http://mirrors.ustc.edu.cn/openwrt/barrier_breaker/14.07/ramips/mt7620a/packages/luci
src/gz barrier_breaker_management http://mirrors.ustc.edu.cn/openwrt/barrier_breaker/14.07/ramips/mt7620a/packages/management
src/gz barrier_breaker_oldpackages http://mirrors.ustc.edu.cn/openwrt/barrier_breaker/14.07/ramips/mt7620a/packages/oldpackages
src/gz barrier_breaker_packages http://mirrors.ustc.edu.cn/openwrt/barrier_breaker/14.07/ramips/mt7620a/packages/packages
src/gz barrier_breaker_routing http://mirrors.ustc.edu.cn/openwrt/barrier_breaker/14.07/ramips/mt7620a/packages/routing
src/gz barrier_breaker_telephony http://mirrors.ustc.edu.cn/openwrt/barrier_breaker/14.07/ramips/mt7620a/packages/telephony
```

## 更多源设置

以上源只是配置了基础的源，像[openwrt-dist][4]这种源，由于某种神秘力量导致我们无法直接使用，需要使用反代，老高发现一个不错的反代

```
src/gz openwrt_dist http://openwrt-dist.thankgfw.ml/releases/ramips/packages
src/gz openwrt_dist_luci http://openwrt-dist.thankgfw.ml/releases/luci/packages
```

> 如何使用呢？

把上面的代码加到opkg配置文件的后面就行！

## 如何使用opkg命令

配置完毕后我们就可以开始使用opkg工具了，下面老高简单的介绍几个常用命令。

```bash
# 打印帮助
opkg -h

# 更新资源列表
opkg update

# 列出已安装的包
opkg list

# 搜索包
opkg search shadowsocks

# 安装软件，以安装curl和wget为例
opkg install curl
# 安装本地软件包
opkg install /tmp/wget_1.16-1_ramips_24kec.ipk 

# 移除软件
opkg remove wget

```


## 使用opkg常见问题

> 错误 Unknown package 'redsocks2'.

解决方法：软件源配置错误，请选择合适的源

> opkg_conf_parse_file: /etc/opkg.conf:1: Ignoring invalid line: `t root /'
> * satisfy_dependencies_for: Cannot satisfy the following dependencies for redsocks2:
> * libc * libc *
> * opkg_install_cmd: Cannot install package redsocks2.

解决方法：libc需要手动安装

```
cd /tmp
# wget http://downloads.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/packages/base/libc_0.9.33.2-1_ramips_24kec.ipk

wget http://mirrors.ustc.edu.cn/openwrt/barrier_breaker/14.07/ramips/mt7620a/packages/base/libc_0.9.33.2-1_ramips_24kec.ipk

## 安装libc
opkg install libc_0.9.33.2-1_ramips_24kec.ipk
```


  [1]: http://wiki.openwrt.org/zh-cn/about/toolchain
  [2]: https://blog.phpgao.com/usr/uploads/2015/05/3794896477.png
  [3]: https://blog.phpgao.com/usr/uploads/2015/05/2478123041.jpeg
  [4]: http://openwrt-dist.sourceforge.net