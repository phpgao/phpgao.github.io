---
title: "Python程序监控脚本"
categories: [ "代码人生" ]
tags: [ "python" ]
draft: false
slug: "check_running_python"
date: "2014-07-22 06:00:00"
url: "check_running_python.html"
---

有些软件虽然卖的很贵，但是稳定性还是让人很无语，经常莫名其妙的崩溃，生产服务器怎么能允许这种事情发生！

一个监控程序运行的就这样诞生了，程序Python 2.7

思路很简单，先写一个监控的函数，判断是否在运行，然后循环调用N次，直到运行成功，实在不行，就发个通知或者记录下来

难点在于如何判断进程在运行。

思路也很简单，主要用到了这两个方法

 - os.popen
 - os.system

他们两个都可以直接运行cmd或者shell中的命令，但是前者会返回输出的内容，可以做进一步的处理

而后者只是执行，并把结果打印出来。

下面是程序，注释已经很清晰了

```python

#coding=utf-8
"""
To check program if it is running
@author PHPergao
@time 2014-7-22
"""

import re
import os
import time
import urllib2
import urllib

flag = 1

#check function
def checkhct():
    #tasklist也可换成linux下打印所有进程的命令 ps aux
	for line in os.popen('tasklist').readlines():
            #判断用正则更准确，也可以使用find或者index判断
		pattern = re.compile(r'qq.*', re.I)
		match = pattern.match(line)
		if(match):
			return True
	return False


#repeat 3 times
for x in xrange(0,3):
	if(checkhct() == False):
		print 'error'
            #程序路径
		os.system('D:\t\qq.exe')
	else:
		print 'running'
		flag = 0
		break

#if is't running

if(flag==1):
    #send email...
    ...
    ...

```