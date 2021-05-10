---
title: "获取所有用户的计划任务"
categories: [ "服务器技术" ]
tags: [ "crontab" ]
draft: false
slug: "crontab_for_everyone"
date: "2016-03-18 10:44:00"
url: "crontab_for_everyone.html"
---

一句话代码

```
cut -d: -f1 /etc/passwd|xargs -i sudo crontab -u {} -l
```