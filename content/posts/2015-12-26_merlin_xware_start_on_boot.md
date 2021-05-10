---
title: "梅林固件迅雷远程开机自启设置"
categories: [ "代码人生" ]
tags: [ "router","merlin","xunlei" ]
draft: false
slug: "merlin_xware_start_on_boot"
date: "2015-12-26 14:59:00"
url: "merlin_xware_start_on_boot.html"
---

![merlin_xware_start_on_boot][1]


<!--more-->


## 第一步，开启JFFS

系统管理 - 系统设置 - Enable JFFS custom scripts and configs

重启后确定存在路径 /jffs/scripts/


## 第二步，写入开机脚本

```
vi /jffs/scripts/start-xunlei.sh

#!/bin/sh
/mnt/USB3/Merlin_software/xware/portal>tmp/123.log
sleep 20
/mnt/USB3/Merlin_software/xware/portal -s
sleep 10
/mnt/USB3/Merlin_software/xware/portal>>tmp/123.log
```

##　第三步

重启

## 备份

```
cat start-xunlei.sh

#!/bin/sh
eval `dbus export xunlei`
xunleiPath=$xunlei_basic_usb/Merlin_software/xunlei
if [ ! -z "$xunleiPath" ];then
        sleep 10
        $xunleiPath/portal &
fi
``` 


  [1]: https://blog.phpgao.com/usr/uploads/2015/12/2370665736.png