---
title: "在路由器下载文件"
categories: [ "代码人生" ]
tags: [ "路由器","wget","魔兽世界" ]
draft: false
slug: "download_on_router"
date: "2015-01-15 15:06:00"
url: "download_on_router.html"
---

昨天晚上用`wget`命令下载了WOW客户端，要是搁到往常，还必须开机电脑下载，现在只需要再路由器的ssh里执行以下即可！


离线下载BT和ED2K还在研究中

```bash
wget http://wow.zip 2>&1 >/dev/null &
```