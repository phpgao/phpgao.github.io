---
title: "服务器状态"
categories: [ "代码人生" ]
tags: [  ]
draft: true
slug: "server_management"
date: "2015-04-29 11:59:00"
url: "server_management.html"
---

65
ss * 2
二维码  nginx + uWSGI + flask


64
博客
备份脚本
dropbox
签到脚本
openwrt各种代理



## 变密码脚本

```
#!/bin/bash
kill `cat /tmp/ss.pid`
date +%s%N | md5sum | base64 | head -c 10 > /root/.kiwivm-shadowsocks-password
/usr/bin/ssserver -p `cat /root/.kiwivm-shadowsocks-port` -k `cat /root/.kiwivm-shadowsocks-password` -m `cat /root/.kiwivm-shadowsocks-encryption` --user nobody --workers 2 --pid-file /tmp/ss.pid -d start
```

## crontab

*/5 * * * * sh /root/change_pass.sh >> /tmp/change.log

## rc.local

```
uwsgi --http-socket 127.0.0.1:9090 --wsgi-file /root/qrcode-for-ss/ss_qrcode.py --callable app -H /root/qrcode-for-ss/venv &


/usr/bin/ssserver -p `cat /root/.kiwivm-shadowsocks-port` -k `cat /root/.kiwivm-shadowsocks-password` -m `cat /root/.kiwivm-shadowsocks-encryption` --user nobody --workers 2 --pid-file /tmp/ss.pid -d start

/usr/bin/ssserver -p 8080 -k phpgao.com -m rc4-md5 --user nobody
ulimit -SHn 102400
```