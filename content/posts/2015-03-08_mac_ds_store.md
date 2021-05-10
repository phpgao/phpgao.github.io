---
title: "mac关闭自动生成DS_store"
categories: [ "代码人生" ]
tags: [ "security","mac","osx","apple" ]
draft: false
slug: "mac_ds_store"
date: "2015-03-08 04:58:00"
url: "mac_ds_store.html"
---

参考了[长短短d的回答 -- 程序员如何优雅地使用 Mac？][1]


<!--more-->


禁止生成的命令：

```
defaults write com.apple.desktopservices DSDontWriteNetworkStores -bool TRUE
```

相关漏洞

[TCL某网站DS_Store文件泄露敏感信息（谨慎使用Mac系统）][2]


  [1]: http://www.zhihu.com/question/20873070/answer/41405748?group_id=557848511253651456
  [2]: http://www.wooyun.org/bugs/wooyun-2014-091869