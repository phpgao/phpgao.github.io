---
title: "小米路由器mini折腾之Python篇"
categories: [ "路由器" ]
tags: [ "python","router","xiaomi","pandorabox","opkg" ]
draft: false
slug: "xiaomi_router_python"
date: "2015-02-10 13:43:00"
url: "xiaomi_router_python.html"
---

上回老高写了翻墙篇，寻思着这openwrt下能不能跑个python，继续折腾！


<!--more-->


## 准备

刷了Pandorabox的小米路由器一个，原生系统没有测试，估计没有什么问题，但是需要开启SSH。

## 配置opkg源

```bash
# 备份初始conf
mv /etc/opkg.conf /etc/opkg.conf.bak

# 新建配置
vim /etc/opkg.conf

# 添加如下内容

dest root /
dest ram /tmp
lists_dir ext /var/opkg-lists
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

# wq保存

# 执行升级
opkg update
```

![升级][1]

## 安装Python

需要注意的是，小米路由器的可用空间不多，但Python的安装会占用挺大的空间，所以我们指定opkg将程序安装在U盘或硬盘上。

这时我们回到刚才的配置文件里，找到notice，这里的`dest usb /mnt/sdb1/opkg`就是指定变量`usb`为外置存储路径`/mnt/sdb1/opkg`。外置存储的路径可以用`df -h`查看。

以后我们使用`opkg -d usb install xxx`即可将程序安装至`/mnt/sdb1/opkg`。

```bash
# 先需要安装libc，需要下载下来安装
## 建立文件夹
mkdir -p /mnt/sdb1/opkg/src
cd /mnt/sdb1/opkg/src
wget http://downloads.openwrt.org/barrier_breaker/14.07/ramips/mt7620a/packages/base/libc_0.9.33.2-1_ramips_24kec.ipk
## 安装libc，最好安装到根下
opkg install libc_0.9.33.2-1_ramips_24kec.ipk

# 接着安装Python
opkg -d usb install libreadline
opkg -d usb install python
opkg -d usb install python-json
opkg -d usb install python-curl
opkg -d usb install python-openssl

# 路径
export PATH=$PATH:/mnt/sdb1/opkg/usr/bin
echo 'export PATH=$PATH:/mnt/sdb1/opkg/usr/bin' >> /etc/profile

# 别名
echo "alias opintall='opkg -d usb install'" >> /etc/profile
```


## 问题

目前已知的bug：

Python方向键乱码，无法使用。



References:

 - http://www.right.com.cn/forum/thread-158625-1-1.html
 - http://blog.sina.com.cn/s/blog_56a70c040102vaay.html

  [1]: http://ww2.sinaimg.cn/large/6735b7fatw1ep4kdjluh2j20hs064tc2.jpg