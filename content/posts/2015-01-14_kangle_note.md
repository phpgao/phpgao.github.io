---
title: "kangle的安装和使用"
categories: [ "服务器技术" ]
tags: [ "kangle" ]
draft: false
slug: "kangle_note"
date: "2015-01-14 08:56:00"
url: "kangle_note.html"
---

kangle是一款国人产的服务器软件，老高经常使用它搭建测试环境。

## 安装

kangle的安装主要分两大块：kangle+easypanel。

```
# centos下一键安装命令
yum -y install wget;wget http://download.kanglesoft.com/easypanel/ep.sh -O ep.sh;sh ep.sh
```

## 使用

安装完成后，使用方法:
管理面板网址:  http://服务器ip:3312/admin/
独立网站管理:  http://服务器ip:3312/vhost/

Reference:

http://www.kanglesoft.com/thread-5891-1-1.html
