---
title: "docker系统源加速命令"
categories: [ "服务器技术" ]
tags: [ "docker" ]
draft: false
slug: "docker_sed"
date: "2019-12-10 02:23:53"
url: "docker_sed.html"
---


## alpine

> 注：如果有@test或者@edge，可以先add后在sed

```bash
sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories
```

## ubuntu

```bash
sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list && \
     sed -i 's/security.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list
```
