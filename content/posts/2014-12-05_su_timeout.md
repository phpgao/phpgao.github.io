---
title: "su超时退出-sudo超时需要输密码的解决方法"
categories: [ "服务器技术" ]
tags: [ "tmout" ]
draft: false
slug: "su_timeout"
date: "2014-12-05 08:43:00"
url: "su_timeout.html"
---

服务器为了安全设置，使用普通用户登陆，再su至root。

而su以后超过一定时间会超时退出到普通用户，带来了一定的麻烦。

解决办法：

OS:CENTOS 6

```bash
vi /etc/profile

# 注释
#TMOUT=300

```

sudo执行的第一次需要验证密码，之后一段时间不需要输入就可以执行命令，控制超时的方法：

```
sudo visudo

# 翻到60-70行，类似

Defaults    env_reset

#改为，30000指的是超时时间是30000min，请合理设置

Defaults    env_reset,timestamp_timeout=30000

```


