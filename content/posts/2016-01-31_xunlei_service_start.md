---
title: "迅雷远程下载的服务脚本"
categories: [ "服务器技术" ]
tags: [ "xunlei" ]
draft: false
slug: "xunlei_service_start"
date: "2016-01-31 09:28:00"
url: "xunlei_service_start.html"
---

放到`/etc/init.d`中即可。

`/home/pi/xunlei/portal`是portal的绝对路径！


<!--more-->


```
#!/bin/sh
#
# Xunlei initscript
#
### BEGIN INIT INFO
# Provides:          xunlei
# Required-Start:    $network $local_fs $remote_fs
# Required-Stop::    $network $local_fs $remote_fs
# Should-Start:      $all
# Should-Stop:       $all
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Start xunlei at boot time
# Description:       A downloader
### END INIT INFO

do_start()
{
        /home/pi/xunlei/portal
}

do_stop()
{
        /home/pi/xunlei/portal -s
}

case "$1" in
  start)
    do_start
    ;;
  stop)
    do_stop
    ;;
esac
```