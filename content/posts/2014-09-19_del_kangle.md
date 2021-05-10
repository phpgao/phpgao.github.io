---
title: "在Liunx上彻底删除kangle服务器"
categories: [ "服务器技术" ]
tags: [ "kangle" ]
draft: false
slug: "del_kangle"
date: "2014-09-19 03:21:00"
url: "del_kangle.html"
---

之前单位用的kangle服务器，便于快速搭建网站。

但是现在不想用了，如何删除呢？

其实很简单：

## 停止服务

    service kangle stop

## 删除服务启动项

    chkconfig kangle off

## 删除服务

    chkconfig --del kangle

## 删除残留文件

    rm -rf  /vhs
    rm -f /etc/init.d/kangle

现在重启看看，是不是已经删除的很干净了！

##ps.这个删除流程也可以针对其他服务使用！##

顺便提一下

删除Apache可以用`rpm -qa|grep apache|xargs rpm -e`，一次删除所有与Apache有关的包