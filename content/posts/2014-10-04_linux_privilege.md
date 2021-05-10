---
title: "Linux目录和文件的权限设置"
categories: [ "服务器技术" ]
tags: [ "Linux","privilege" ]
draft: false
slug: "linux_privilege"
date: "2014-10-04 15:49:00"
url: "linux_privilege.html"
---

要求就是：

1. 将当前目录中的所有“子目录”的权限设置为755；
2. 将当前目录中的所有“文件”的权限设置为644。

解决方法：

    chmod 644 -R *
    chmod 755 `find -type d`

也可以用find彻底些：

    find /path -type f -exec chmod 644 {} /;
    find /path -type d -exec chmod 755 {} /;

转自：

[http://blog.csdn.net/study16/article/details/5896390][1]


  [1]: http://blog.csdn.net/study16/article/details/5896390