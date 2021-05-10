---
title: "文章目录-Table-Of-Contents-for-Typecho"
categories: [ "typecho插件" ]
tags: [ "toc","typecho","git","plugin" ]
draft: false
slug: "table_of_contents_for_typecho"
date: "2015-05-13 14:10:00"
url: "table_of_contents_for_typecho.html"
---

Table Of Contents大家都懂得，效果可以看老高的博客。

欢迎使用！欢迎FORK！欢迎star！

![Table Of Contents][1]


<!--more-->


今天(20150513)下午赶着做出来的，**如果发现BUG，请在此页汇报！**

<!--more-->

**过一阵子老高会出一篇typecho的插件简单教程，欢迎关注！**

## 功能

废话！就是显示文章目录。

2015-05-18更新：

使用simple_html_dom，放弃了正则。

## 安装

### 命令行

进入typecho的`usr/plugins`目录

使用`git clone https://github.com/phpgao/TableOfContents.git`

这样plugins下应该会有TableOfContents文件夹，然后在后台->插件处安装。

### FTP

解压下载的文件，将TableOfContents上传至`usr/plugins`目录即可！

## 下载

[TableOfContents][2]

## 升级

请先禁用插件后再升级

## 目前的问题

 - <del>本插件的显隐功能依赖jquery</del>
 - <del>样式有些问题</del>
 - 目前只对h2和h3做解析，并且解析出的数量大于3的时候才会放出目录。


  [1]: https://blog.phpgao.com/usr/uploads/2015/05/3482950149.png "Table Of Contents 截图"
  [2]: https://github.com/phpgao/TableOfContents