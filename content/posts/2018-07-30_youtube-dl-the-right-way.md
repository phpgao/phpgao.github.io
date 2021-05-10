---
title: "正确下载youtube视频的方式"
categories: [ "代码人生" ]
tags: [ "youtube" ]
draft: false
slug: "youtube-dl-the-right-way"
date: "2018-07-30 03:21:00"
url: "youtube-dl-the-right-way.html"
---

youtube这个**不存在**的网站上有很多有用的资料，一般来说我们是可以下载所有视频到本地以供离线的情况下的研究学习，网上有很多工具提供了下载功能，但是在试用了很多标称很好用的软件后，老高发现，真的没有一个能和youtube-dl相提并论，所以老高还是记录一下如何使用正确使用youtube-dl！


<!--more-->

<以下是草稿>
```

youtube-dl -i -o "%(playlist)s/%(playlist_index)s.%(title)s-(%(id)s).%(ext)s" --restrict-filenames --sub-lang en --write-auto-sub --write-sub --sub-format srt https://www.youtube.com/watch\?v\=eAtGqz8ytOI\&list\=PLjSwXXbVlK6IHzhLOMpwHHLjYmINRstrk





alias ytbdl="youtube-dl -ci -o \"%(playlist)s/%(playlist_index)s.%(title)s-(%(id)s).%(ext)s\" --restrict-filenames --sub-lang en --write-auto-sub --write-sub --sub-format srt"
```