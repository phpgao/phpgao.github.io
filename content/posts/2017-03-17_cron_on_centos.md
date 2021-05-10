---
title: "Cron在Centos上的安装方法"
categories: [ "服务器技术" ]
tags: [ "crontab" ]
draft: false
slug: "cron_on_centos"
date: "2017-03-17 08:25:00"
url: "cron_on_centos.html"
---

Centos最小化安装时候貌似crond是不带的，需要自己手动安装。但是Centos不同的版本安装命令不一样，在此记录一下！




<!--more-->


```bash
# On CentOS 7 you need to use cronie:
yum install -y cronie

# On CentOS 6 you can install vixie-cron, but the real package is cronie:
yum install -y vixie-cron

# 启用
systemctl enable crond
systemctl restart crond
```


参考：

 - http://stackoverflow.com/questions/21802223/how-to-install-crontab-on-centos