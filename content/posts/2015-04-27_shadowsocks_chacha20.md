---
title: "安装shadowsocks-python并启用chacha20加密"
categories: [ "代码人生" ]
tags: [ "python","shadowsocks","chacha20" ]
draft: false
slug: "shadowsocks_chacha20"
date: "2015-04-27 14:29:00"
url: "shadowsocks_chacha20.html"
---

原版shadowsocks使用python写的，源码在此

https://github.com/shadowsocks/shadowsocks

下面老高写一个如何安装shadowsocks并且开启chacha20加密的方法

老高的运行环境

> centos6 + python2.6

<!--more-->

## 安装

### shadowsocks

```
yum install m2crypto
pip install shadowsocks
```

### libsodium

```
wget https://download.libsodium.org/libsodium/releases/LATEST.tar.gz
tar zxf LATEST.tar.gz
cd libsodium*
./configure
make && make install

# 修复关联
echo /usr/local/lib > /etc/ld.so.conf.d/usr_local_lib.conf
ldconfig
```

## 运行

```
/usr/bin/ssserver -p 9000 -k www.phpgao.com -m chacha20 --user nobody
```

## 开机启动

我们将刚才的命令写入开机脚本中。

```
echo '/usr/bin/ssserver -p 9000 -k www.phpgao.com -m chacha20 --user nobody' >> /etc/rc.local
```

更高级的使用方法请参考 [使用supervisor托管shadowsocks][1]

## 深入阅读

https://github.com/shadowsocks/shadowsocks/wiki/Shadowsocks-使用说明

reference

https://www.v2ex.com/t/160857


  [1]: https://blog.phpgao.com/supervisor_shadowsocks.html