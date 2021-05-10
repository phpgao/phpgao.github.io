---
title: "#!-usr-bin-env与#!-usr-bin-python的区别"
categories: [ "代码人生" ]
tags: [ "python" ]
draft: false
slug: "usr_bin_python"
date: "2014-07-29 01:54:00"
url: "usr_bin_python.html"
---


今天写了个采集脚本，使用了`BeautifulSoup`，所以在代码中有下面的`import`语句

    from BeautifulSoup import BeautifulSoup as BS

在win下执行，没问题，但是在linux下执行，就报这个错误

 > ImportError: No module named BeautifulSoup

google之

转自[快乐&&平凡](http://blog.csdn.net/wh_19910525/article/details/8040494)

脚本语言的第一行，目的就是指出，你想要你的这个文件中的代码用什么可执行程序去运行它，就这么简单

	#!/usr/bin/python是告诉操作系统执行这个脚本的时候，调用/usr/bin下的python解释器;
	#!/usr/bin/env python这种用法是为了防止操作系统用户没有将python装在默认的/usr/bin路径里。当系统看到这一行的时候，首先会到env设置里查找python的安装路径，再调用对应路径下的解释器程序完成操作。
	#!/usr/bin/python相当于写死了python路径;
	#!/usr/bin/env python会去环境设置寻找python目录,推荐这种写法。

而我的`/usr/bin/python`是2.6版本，但是安装BeautifulSoup的是2.7。。。

    #mv /usr/bin/python /usr/bin/python2.6.6
    #ln -s /usr/local/bin/python2.7 /usr/bin/python

成功解决!