---
title: "Laracasts视频下载工具"
categories: [ "代码人生" ]
tags: [ "laravel" ]
draft: false
slug: "laracasts_downloader"
date: "2016-04-25 13:31:00"
url: "laracasts_downloader.html"
---

最近老高在看laravel框架，巧遇laracasts，发现还是老外讲课有深度啊！这么好的视频，有很多还是免费的，果断下载下来保存。

于是，就诞生了这篇文章！


<!--more-->


简单了看了一下网站结构，发现视频下载的逻辑很简单，于是写了个简单的工具，目前能够实现直接下载整个列表页视频。

> 目前还是单线程，如果用的人多了，会考虑加上多线程

[github源码链接][1]


用法很简单，不过最好你能有一个代理！



  [1]: https://github.com/phpgao/LaracastsDownloader