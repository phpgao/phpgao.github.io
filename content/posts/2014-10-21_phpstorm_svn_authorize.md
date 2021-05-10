---
title: "WIN下phpstorm不能检出svn的问题"
categories: [ "代码人生","服务器技术" ]
tags: [ "phpstorm","svn" ]
draft: false
slug: "phpstorm_svn_authorize"
date: "2014-10-21 03:09:00"
url: "phpstorm_svn_authorize.html"
---

今天使用win上的phpstrom发现不能检出svn服务器上的文件，折腾了一番才发现原来是svn设置问题。

解决办法：

进入settings

按照下图设置：

![phpstorm svn 设置][1]

这里我们指定了svn的命令行路径，`CollabNet\Subversion Client`是CollabNet出的svn客户端命令行工具，换成其他的也可以，只要路径指定正确就行。

附下载地址：

[支持各种系统的svn][2]

推荐[visualsvn][3]


  [1]: https://blog.phpgao.com/usr/uploads/2014/10/2477097151.png
  [2]: http://subversion.apache.org/packages.html#windows
  [3]: http://www.visualsvn.com/downloads/