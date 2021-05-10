---
title: "bad-interpreter-没有那个文件或目录"
categories: [ "代码人生","服务器技术" ]
tags: [ "python" ]
draft: false
slug: "bad_interpreter"
date: "2014-07-29 01:56:00"
url: "bad_interpreter.html"
---

bad interpreter: 没有那个文件或目录


## 起因

今天在linux上运行一个python脚本，总是报错`bad interpreter`，开始我以为是解释器的路径问题，可是`whereis python`告诉我路径没有错！

google之，问题解决：

在windows上编辑的脚本，到linux上执行，会提示上面的错误

## 解决办法

dos2unix  myfilename

报错的话执行一下`yum install dos2unix -y`


## 原因

在windows下编辑的脚本，是dos格式的，即每一行的行尾以\r\n来标识, 其ASCII码分别是0x0D, 0x0A。

以后再windows上的写脚本一定要三思！