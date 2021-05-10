---
title: "vim中文乱码的解决办法"
categories: [ "服务器技术" ]
tags: [ "vim","vi" ]
draft: false
slug: "vim_garbled"
date: "2014-07-28 06:24:00"
url: "vim_garbled.html"
---

以centos为例，执行`yum install -y vim`后，打开php、py等文件，发现中文乱码了。。。

google之

解决办法：

    vim /etc/.vimrc

将以下内容写入

    set fileencodings=utf-8,ucs-bom,gb18030,gbk,gb2312,cp936
    set termencoding=utf-8
    set encoding=utf-8

再次打开，中文已正常显示。