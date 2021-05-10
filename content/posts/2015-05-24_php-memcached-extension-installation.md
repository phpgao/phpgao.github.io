---
title: "Linux服务器配置memcached并启用PHP支持"
categories: [ "服务器技术" ]
tags: [ "PHP","memcache","extension","compile" ]
draft: false
slug: "php-memcached-extension-installation"
date: "2015-05-24 02:32:00"
url: "php-memcached-extension-installation.html"
---

最近在服务器上部缓存系统，记录一下PHP安装memcached扩展。


<!--more-->


```
# 安装服务端
yum install memcached -y
```

## launchpad

请于[https://launchpad.net/libmemcached/+download][1]下载目前最新版的libmemcached(20150524)

```
cd /tmp
wget https://launchpad.net/libmemcached/1.0/1.0.18/+download/libmemcached-1.0.18.tar.gz
tar zxf libmemcached-1.0.18.tar.gz
cd libmemcached-1.0.18
./configure
make
make install
```

## 安装memcached扩展

在PECL（The PHP Extension Community Library）找到并安装php的memcached扩展。

> 在PECL中你能找到以下两个容易混淆的memcache的PHP扩展，你能够分清楚他们的区别吗？你知道为什么我们要用前者吗？请读者紫星Google之！
memcached	PHP extension for interfacing with memcached via libmemcached library
memcache	memcached extension

[https://pecl.php.net/package/memcached][4] 目前最新版为`2.2.0`

```bash
mkdir /usr/src/php -p
cd /usr/src/php
wget https://pecl.php.net/get/memcached -O memcached-2.2.0.tgz
tar xf memcached-2.2.0.tgz
cd memcached-2.2.0
phpize
# 我们在配置的添加一些新的功能，使其支持json和igbinary，有空可以研究一下
./configure --enable-memcached --enable-memcached-json --enable-memcached-igbinary
make
make install
# 注意最后的输出
# Installing shared extensions:     /usr/local/php/lib/php/extensions/no-debug-non-zts-20131226/
```

下面我们将其移动到php的extension目录下

```bash
cp /usr/local/php/lib/php/extensions/no-debug-non-zts-20131226/*.so /usr/local/php/lib/php/extensions/
```

## 配置PHP.ini

打开正在生效的php.ini文件

```
vim /usr/local/php/etc/php.ini

# 添加以下配置
[Memcached]
extension=memcached.so
```

![PHP安装memcached扩展配置成功][2]


  [1]: https://launchpad.net/libmemcached/+download
  [2]: https://blog.phpgao.com/usr/uploads/2015/05/1530912403.png