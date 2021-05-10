---
title: "Linux下简单计算md5,base64,sha1,sha2"
categories: [ "代码人生" ]
tags: [ "Linux","md5","sha1" ]
draft: false
slug: "md5_sha1_sha2_in_linux"
date: "2016-06-01 03:25:00"
url: "md5_sha1_sha2_in_linux.html"
---

经常加密的同学，算个md5真是顺手的事儿。。。


<!--more-->


## 计算字符串

```bash
echo -n "123" | md5sum

echo -n "123" | base64

echo -n "123" | sha1sum

echo -n "123" | sha256sum
```
