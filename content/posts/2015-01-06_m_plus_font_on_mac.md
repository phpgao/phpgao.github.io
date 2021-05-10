---
title: "M+字体安装"
categories: [ "代码人生" ]
tags: [ "osx","font","m+","xz","tar" ]
draft: false
slug: "m_plus_font_on_mac"
date: "2015-01-06 14:51:00"
url: "m_plus_font_on_mac.html"
---

今天微信推了一篇文章，是讲一个叫M＋的开源字体，貌似MBPR上表现还不错。

那么如何安装M+字体呢？

正好老高还没有在OSX下安装过字体，结果发现，原来安装个字体和在win下是一样一样的。。。

## 项目地址

http://sourceforge.jp/projects/mplus-fonts/


<!--more-->


## 下载

进入后点击下载文件列表，然后下载最大的那个。

最近的提交时间为 2014-11-26 15:26。
文件名是 mplus-TESTFLIGHT-059.tar.xz

## 解压

坑爹啊，竟然是xz格式，还好我们又brew

```bash
# 安装xz
brew install xz

# 解压文件
cd ~/Downloads
xz -d mplus-TESTFLIGHT-059.tar.xz
tar -xf mplus-TESTFLIGHT-059.tar
cd mplus-TESTFLIGHT-059
open .
```

## 安装

安装方法就是双击你要的字体，安装即可！

一般选择regular和bold字体安装就OK！

![M+ font][1]


  [1]: http://ww4.sinaimg.cn/large/005yyi5Jjw1eo075xcergj316q0gogq2.jpg