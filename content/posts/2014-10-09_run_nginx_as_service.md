---
title: "将nginx配置为服务"
categories: [ "服务器技术" ]
tags: [ "nginx" ]
draft: false
slug: "run_nginx_as_service"
date: "2014-10-09 14:15:00"
url: "run_nginx_as_service.html"
---

编译安装nginx后，没有将nginx配置为服务，则无法使用诸如`service nginx restart`的命令，下面我们看看如何将其配置为服务。

## 编写脚本

    vi /etc/init.d/nginx

写入以下内容，并修改nginx路径

    #!/bin/sh   
    #   
    # nginx - this script starts and stops the nginx daemon   
    #   
    # chkconfig: - 85 15   
    # description: Nginx is an HTTP(S) server, HTTP(S) reverse   
    # proxy and IMAP/POP3 proxy server   
    # processname: nginx   
    # chkconfig: 2345 90 91   
    # description: nginx web server  
    # processname: nginx  
    # config: /opt/nginx/conf/nginx.conf  
    # pidfile: /opt/nginx/nginx.pid  
      
    # Source function library.  
    . /etc/init.d/functions  
      
    # Source networking configuration.  
    . /etc/sysconfig/network  
      
      
    if [ -f /etc/sysconfig/nginx ];then  
    . /etc/sysconfig/nginx  
    fi  
      
    # Check that networking is up.   
    [ "$NETWORKING" = "no" ] && exit 0  
      
    nginx="#改为nginx二进制的路径"   
    prog=$(basename $nginx)  
      
    NGINX_CONF_FILE="/etc/nginx/nginx.conf"  
      
    [ -f /etc/sysconfig/nginx ] && . /etc/sysconfig/nginx  
      
    lockfile=/var/lock/subsys/nginx  
      
    start() {   
    [ -x $nginx ] || exit 5   
    [ -f $NGINX_CONF_FILE ] || exit 6   
    echo -n $"Starting $prog: "   
    daemon $nginx #-c $NGINX_CONF_FILE   
    retval=$?   
    echo   
    [ $retval -eq 0 ] && touch $lockfile   
    return $retval   
    }  
      
    stop() {   
    echo -n $"Stopping $prog: "   
    killproc $prog -QUIT   
    retval=$?   
    echo   
    [ $retval -eq 0 ] && rm -f $lockfile   
    return $retval   
    killall -9 nginx   
    }  
      
    restart() {   
    configtest || return $?   
    stop   
    sleep 1   
    start   
    }  
      
    reload() {   
    configtest || return $?   
    echo -n $"Reloading $prog: "   
    killproc $nginx -HUP   
    RETVAL=$?   
    echo   
    }  
      
    force_reload() {   
    restart   
    }  
      
    configtest() {   
    $nginx -t #-c $NGINX_CONF_FILE   
    }  
      
    rh_status() {   
    status $prog   
    }  
      
    rh_status_q() {   
    rh_status >/dev/null 2>&1   
    }  
      
    case "$1" in   
    start)   
        rh_status_q && exit 0   
        $1   
        ;;   
    stop)   
    rh_status_q || exit 0   
        $1   
        ;;   
    restart)   
        $1   
        ;;   
    test)   
        configtest   
        ;;   
    reload)   
        rh_status_q || exit 7   
        $1   
        ;;   
    force-reload)   
        force_reload   
        ;;   
    status)   
        rh_status   
        ;;   
    condrestart|try-restart)   
        rh_status_q || exit 0   
        ;;   
    *)   
    echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload|test}"   
    exit 2   
    esac

## 给予执行权

    chmod a+x /etc/init.d/nginx

## 添加并打开服务

    chkconfig --add nginx
    chkconfig nginx on

## 测试

    service nginx start
    service nginx stop 
    service nginx reload

转自：

[http://binyan17.iteye.com/blog/1688308][1]


  [1]: http://binyan17.iteye.com/blog/1688308