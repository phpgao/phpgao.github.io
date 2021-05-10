---
title: "解决MAC远程总是Broken-pipe"
categories: [ "服务器技术" ]
tags: [ "ssh","mac","osx" ]
draft: false
slug: "mac_broken_pipe"
date: "2015-01-25 03:12:56"
url: "mac_broken_pipe.html"
---

与`ServerAliveInterval`的设置有关

```bash

vim ~/.ssh/config

# add

ServerAliveInterval 60
