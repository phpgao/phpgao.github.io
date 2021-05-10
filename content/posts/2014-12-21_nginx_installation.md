---
title: "安装编译nginx"
categories: [ "服务器技术" ]
tags: [ "centos","version","ssl","nginx","service","compile","rewrite","zlib","yum","installation","编译" ]
draft: false
slug: "nginx_installation"
date: "2014-12-21 13:57:00"
url: "nginx_installation.html"
---

今天老高更新了一下nginx，顺便把nginx的安装编译过程记录一下，分享给大家！

一并送上之前老高的博文：

[将nginx配置为服务][1]

[nginx配置详解][2]

## 下载

官网下载地址：[http://nginx.org/en/download.html][3]

## 准备

安装nginx时必须先安装相应的编译工具

```bash
yum -y install gcc gcc-c++ autoconf automake
yum -y install zlib zlib-devel openssl openssl-devel pcre-devel
```

centos没有安装make编译器

```bash
yum -y install gcc automake autoconf libtool make
```

建立nginx 组

```bash
groupadd -r nginx
useradd -s /sbin/nologin -g nginx -r nginx
```

注释：

zlib:nginx提供gzip模块，需要zlib库支持
openssl:nginx提供ssl功能
pcre:支持地址重写rewrite功能

## 修改版本号

再编译之前修改默认的版本号是个不错的习惯

```bash
vim src/core/nginx.h

#define NGINX_VERSION      "0.0.0"
#define NGINX_VER          "phpergao" NGINX_VERSION
```

## 编译

高大上的编译参数

```make
./configure \
--prefix=/usr \
--sbin-path=/usr/sbin/nginx \
--conf-path=/etc/nginx/nginx.conf \
--error-log-path=/var/log/nginx/error.log \
--pid-path=/var/run/nginx/nginx.pid \
--user=nginx \
--group=nginx \
--with-http_ssl_module \
--with-http_flv_module \
--with-http_gzip_static_module \
--http-log-path=/var/log/nginx/access.log \
--http-client-body-temp-path=/var/tmp/nginx/client \
--http-proxy-temp-path=/var/tmp/nginx/proxy \
--http-fastcgi-temp-path=/var/tmp/nginx/fcgi \
--with-http_stub_status_module \
--with-http_sub_module \
--with-http_spdy_module
```

## 安装

```bash
make && make install
```

## 添加服务

以下是service的脚本。

注：脚本中部分变量值可能需要修改以找到对应的文件，如果出现文件夹找不到的错误，请使用`mkdir -p`创建对应路径

```shell
# 先执行 vim /etc/init.d/nginx
# 再copy以下脚本

#!/bin/sh
#
# nginx        Startup script for nginx
#
# chkconfig: - 85 15
# processname: nginx
# config: /etc/nginx/nginx.conf
# config: /etc/sysconfig/nginx
# pidfile: /var/run/nginx.pid
# description: nginx is an HTTP and reverse proxy server
#
### BEGIN INIT INFO
# Provides: nginx
# Required-Start: $local_fs $remote_fs $network
# Required-Stop: $local_fs $remote_fs $network
# Default-Start: 2 3 4 5
# Default-Stop: 0 1 6
# Short-Description: start and stop nginx
### END INIT INFO

# Source function library.
. /etc/rc.d/init.d/functions

if [ -L $0 ]; then
    initscript=`/bin/readlink -f $0`
else
    initscript=$0
fi

sysconfig=`/bin/basename $initscript`

if [ -f /etc/sysconfig/$sysconfig ]; then
    . /etc/sysconfig/$sysconfig
fi

nginx=${NGINX-/usr/sbin/nginx}
prog=`/bin/basename $nginx`
conffile=${CONFFILE-/etc/nginx/nginx.conf}
lockfile=${LOCKFILE-/var/lock/subsys/nginx}
pidfile=${PIDFILE-/var/run/nginx.pid}
SLEEPMSEC=${SLEEPMSEC-200000}
UPGRADEWAITLOOPS=${UPGRADEWAITLOOPS-5}
RETVAL=0

start() {
    echo -n $"Starting $prog: "

    daemon --pidfile=${pidfile} ${nginx} -c ${conffile}
    RETVAL=$?
    echo
    [ $RETVAL = 0 ] && touch ${lockfile}
    return $RETVAL
}

stop() {
    echo -n $"Stopping $prog: "
    killproc -p ${pidfile} ${prog}
    RETVAL=$?
    echo
    [ $RETVAL = 0 ] && rm -f ${lockfile} ${pidfile}
}

reload() {
    echo -n $"Reloading $prog: "
    killproc -p ${pidfile} ${prog} -HUP
    RETVAL=$?
    echo
}

upgrade() {
    oldbinpidfile=${pidfile}.oldbin

    configtest -q || return
    echo -n $"Starting new master $prog: "
    killproc -p ${pidfile} ${prog} -USR2
    echo

    for i in `/usr/bin/seq $UPGRADEWAITLOOPS`; do
        /bin/usleep $SLEEPMSEC
        if [ -f ${oldbinpidfile} -a -f ${pidfile} ]; then
            echo -n $"Graceful shutdown of old $prog: "
            killproc -p ${oldbinpidfile} ${prog} -QUIT
            RETVAL=$?
            echo
            return
        fi
    done

    echo $"Upgrade failed!"
    RETVAL=1
}

configtest() {
    if [ "$#" -ne 0 ] ; then
        case "$1" in
            -q)
                FLAG=$1
                ;;
            *)
                ;;
        esac
        shift
    fi
    ${nginx} -t -c ${conffile} $FLAG
    RETVAL=$?
    return $RETVAL
}

rh_status() {
    status -p ${pidfile} ${nginx}
}

# See how we were called.
case "$1" in
    start)
        rh_status >/dev/null 2>&1 && exit 0
        start
        ;;
    stop)
        stop
        ;;
    status)
        rh_status
        RETVAL=$?
        ;;
    restart)
        configtest -q || exit $RETVAL
        stop
        start
        ;;
    upgrade)
        rh_status >/dev/null 2>&1 || exit 0
        upgrade
        ;;
    condrestart|try-restart)
        if rh_status >/dev/null 2>&1; then
            stop
            start
        fi
        ;;
    force-reload|reload)
        reload
        ;;
    configtest)
        configtest
        ;;
    *)
        echo $"Usage: $prog {start|stop|restart|condrestart|try-restart|force-reload|upgrade|reload|status|help|configtest}"
        RETVAL=2
esac

exit $RETVAL
```

```bash
mkdir -p /var/tmp/nginx/client
chmod 755 /etc/init.d/nginx
chkconfig --add nginx
```

## 使用

```bash
# 重启nginx
service nginx restart
```

现在访问你的IP或者域名就可以访问了！

![Welcome to nginx!][4]

参考链接：

http://blog.sina.com.cn/s/blog_6f2274fb01012nq5.html
http://www.cnblogs.com/suihui/archive/2013/04/13/3018557.html


  [1]: https://blog.phpgao.com/run_nginx_as_service.html
  [2]: https://blog.phpgao.com/nginx_conf.html
  [3]: http://nginx.org/en/download.html
  [4]: https://blog.phpgao.com/usr/uploads/2015/04/276254367.png