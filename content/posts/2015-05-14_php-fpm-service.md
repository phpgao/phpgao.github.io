---
title: "将-php-fpm-配置为服务"
categories: [ "服务器技术" ]
tags: [ "PHP","script" ]
draft: false
slug: "php-fpm-service"
date: "2015-05-14 10:59:00"
url: "php-fpm-service.html"
---

请将以下脚本适当修改后命名为**php-fpm**


<!--more-->


```
#!/bin/bash
#
# Startup script for the PHP-FPM server.
#
# chkconfig: 345 85 15
# description: PHP is an HTML-embedded scripting language
# processname: php-fpm
# config: /usr/local/php/etc/php.ini

# Source function library.
. /etc/rc.d/init.d/functions

PHP_PATH=/usr/local
DESC="php-fpm daemon"
NAME=php-fpm
# php-fpm路径
DAEMON=$PHP_PATH/php/sbin/$NAME
# 配置文件路径
CONFIGFILE=$PHP_PATH/php/etc/php-fpm.conf
# PHP.ini文件路径
INI_CONFIGFILE=$PHP_PATH/php/etc/php.ini
# PID文件路径(在php-fpm.conf设置)
PIDFILE=$PHP_PATH/php/var/run/$NAME.pid
SCRIPTNAME=/etc/init.d/$NAME

# Gracefully exit if the package has been removed.
test -x $DAEMON || exit 0

rh_start() {
  $DAEMON -y $CONFIGFILE -c $INI_CONFIGFILE || echo -n " already running"
}

rh_stop() {
  kill -QUIT `cat $PIDFILE` || echo -n " not running"
}

rh_reload() {
  kill -HUP `cat $PIDFILE` || echo -n " can't reload"
}

case "$1" in
  start)
        echo -n "Starting $DESC: $NAME"
        rh_start
        echo "."
        ;;
  stop)
        echo -n "Stopping $DESC: $NAME"
        rh_stop
        echo "."
        ;;
  reload)
        echo -n "Reloading $DESC configuration..."
        rh_reload
        echo "reloaded."
  ;;
  restart)
        echo -n "Restarting $DESC: $NAME"
        rh_stop
        sleep 1
        rh_start
        echo "."
        ;;
  *)
         echo "Usage: $SCRIPTNAME {start|stop|restart|reload}" >&2
         exit 3
        ;;
esac
exit 0
```

接着运行

```
cp ./php-fpm /etc/init.d/php-fpm
chmod +x /etc/init.d/php-fpm

chkconfig --level 35 php-fpm on
service php-fpm restart
```