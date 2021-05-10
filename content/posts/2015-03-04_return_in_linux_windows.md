---
title: "linux下windows的换行符"
categories: [ "代码人生" ]
tags: [ "windows","Linux","mac" ]
draft: false
slug: "return_in_linux_windows"
date: "2015-03-04 03:31:00"
url: "return_in_linux_windows.html"
---

Unix/MAC和windows下换行总会出现以下问题：

> Unix/MAC下打开windows下的文本会多出一个^M，win下打开Unix/MAC文本会变成一行。

原因是换行机制不一样，Unix下是`\n`(0A)，mac下是`\r`(0D)，win下是`\r\n`(0D0A)。导致的结果是在程序中会造成一定的混乱。

![十六进制的换行][1]


<!--more-->


**解决办法：**

程序中：

换行符在系统初始化的时候由变量替换。

系统中：

使用工具转换文本文件

linux下使用unix2dos或dos2unix
windows下编辑器自行搞定


Reference:

http://blog.csdn.net/wjcquking/article/details/6634504


  [1]: http://ww4.sinaimg.cn/large/6735b7fagw1eptjmcjvynj20je03mwh3.jpg