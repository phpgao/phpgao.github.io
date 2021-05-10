---
title: "开启phpMyAdmin的远程登录"
categories: [ "代码人生" ]
tags: [ "phpmyadmin","remote" ]
draft: false
slug: "phpmyadmin_remote_server"
date: "2014-03-05 14:49:00"
url: "phpmyadmin_remote_server.html"
---

phpMyAdmin一般只能本地登录，即地址为localhost，如果想管理如192.168.1.254（非本机IP）的mysql应该如何做到呢？其实很简单，phpMyAdmin</span>已经实现，但是默认并没有开启此功能。
3步搞定 

 1. 在phpMyAdmin目录下`libraries`目录中`config.default.php`文件并打开
 2. 找到此选项`$cfg['AllowArbitraryServer'] = false;`
 3. 将false改为true 补充： 如果登录的是本地服务器，那么登录的时候可以把服务器一栏留空。