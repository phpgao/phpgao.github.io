---
title: "编译PHP5.6"
categories: [ "服务器技术" ]
tags: [ "PHP","configure","fpm","compile" ]
draft: false
slug: "compile_php"
date: "2014-09-18 06:26:00"
url: "compile_php.html"
---

## 准备

PHP的安装最头疼的就是因为某些类库没有安装而报错，所以编译前请确保以下包已安装

```bash
yum groupinstall "development tools"

yum install -y gcc gcc-c++ autoconf libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libpng libpng-devel libxml2 libxml2-devel zlib zlib-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel ncurses curl curl-devel openssl-devel gdbm-devel db4-devel libXpm-devel libX11-devel gd-devel gmp-devel readline-devel libxslt-devel expat-devel xmlrpc-c xmlrpc-c-devel
```

## 编译

高大上的编译选项

```bash
./configure  \
--prefix=/usr/local/php  \
--enable-fpm  \
--with-curl  \
--with-openssl  \
--enable-mbregex  \
--with-mysql  \
--with-mysqli  \
--with-mysql-sock  \
--enable-pdo  \
--with-pdo-mysql  \
--with-pdo-pgsql  \
--with-pdo-sqlite  \
--enable-mysqlnd  \
--with-gd  \
--enable-gd-native-ttf  \
--enable-exif  \
--with-jpeg-dir=/usr/local/jpeg  \
--with-png-dir=/usr/local/png  \
--with-freetype-dir=/usr/local/freetype  \
--enable-gd-jis-conv  \
--with-gettext  \
--with-zlib  \
--enable-zip  \
--with-bz2  \
--disable-fileinfo  \
--enable-xmlreader  \
--enable-xmlwriter  \
--with-xmlrpc  \
--enable-mbstring  \
--enable-inline-optimization \
```

20150524更新 5.6.9编译配置选项，推荐使用

```bash
./configure --prefix=/usr/local/php --with-pdo-pgsql --with-pdo-sqlite --with-zlib-dir --with-freetype-dir --enable-mbstring --with-libxml-dir=/usr --enable-xmlreader --enable-xmlwriter --enable-soap --enable-calendar --with-curl --with-mcrypt --with-zlib --with-gd --with-pgsql --disable-rpath --enable-inline-optimization --with-bz2 --with-zlib --enable-sockets --enable-sysvsem --enable-sysvshm --enable-pcntl --enable-mbregex --enable-exif --enable-bcmath --with-mhash --enable-zip --with-pcre-regex --with-mysql --with-pdo-mysql --with-mysqli --with-mysql-sock --enable-mysqlnd --with-jpeg-dir=/usr --with-png-dir=/usr --enable-gd-native-ttf --with-openssl --enable-ftp --with-imap=/usr/local/php-imap --with-imap-ssl --with-kerberos --with-gettext --with-xmlrpc --with-xsl --enable-opcache --enable-fpm --with-fpm-user=www --with-fpm-group=www --disable-fileinfo
```

编译中出了问题请参考

[彻底解决编译PHP找不到libc-client.a的问题][1]

[PHP编译错误的解决办法][2]

如果内存小于1G，需要加上`--disable-fileinfo`

## 找到二进制文件

添加环境变量

```bash
whereis php
#/usr/local/bin/php
echo $PATH
#/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
export PATH=/usr/local/php/bin:$PATH
echo $PATH
#/usr/local/php/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin
php -v
#PHP 5.6.0 (cli) (built: Sep 10 2014 23:54:43)
```

## 编辑profile

> ps./etc/profile:在登录时,操作系统定制用户环境时使用的第一个文件,此文件为系统的每个用户设置环境信息,当用户第一次登录时,该文件被执行

> pss./etc/environment:在登录时操作系统使用的第二个文件,系统在读取你自己的profile前,设置环境文件的环境变量。

> pss.~/.bash_profile:在登录时用到的第三个文件是.profile文件,每个用户都可使用该文件输入专用于自己使用的shell信息,当用户登录时,该 文件仅仅执行一次!默认情况下,他设置一些环境变游戏量,执行用户的.bashrc文件。/etc/bashrc:为每一个运行bash shell的用户执行此文件.当bash shell被打开时,该文件被读取.

> pss.~/.bashrc:该文件包含专用于你的bash shell的bash信息,当登录时以及每次打开新的shell时,该该文件被读取。

```bash
vim /etc/profile
```

在最后一行加上:`export PATH="/usr/local/php/bin:$PATH"`

最后：

```bash
source /etc/profile
```

或者直接把php拷贝至`/usr/local/bin/`下

```bash
cp /usr/local/php/bin/php /usr/local/bin/
```

## 配置文件

```bash
cp /tmp/php-5.6.0/php.ini-production /usr/local/php/etc/php.ini
```

## 启动php-fpm

```bash
/usr/local/php/sbin/php-fpm
```

运行时指定配置文件

```bash
/usr/local/php/sbin/php-fpm -c /usr/local/php/etc/php.ini -y /usr/local/php/etc/php-fpm.conf
```

开机自启动

```bash
echo "/usr/local/php/sbin/php-fpm -c /usr/local/php/etc/php.ini -y /usr/local/php/etc/php-fpm.conf" >> /etc/rc.local
```

或者

```bash
vim /etc/rc.local

# ADD
/usr/local/php/sbin/php-fpm -c /usr/local/php/etc/php.ini -y /usr/local/php/etc/php-fpm.conf
```

## 配置php-fpm

配置文件路径`/usr/local/php/etc/php-fpm.conf`

## 扩展阅读

[PHP安装配置Opcache加速你的网站][3]

[PHP安装memcached扩展][4]

[PHP之负载均衡下的session共用(Memcache实现)][5]


  [1]: https://blog.phpgao.com/libc-client-404.html
  [2]: https://blog.phpgao.com/php_configuration_error.html
  [3]: https://blog.phpgao.com/php-opcache-optimize.html
  [4]: https://blog.phpgao.com/php-memcached-extension-installation.html
  [5]: https://blog.phpgao.com/memcache_session.html