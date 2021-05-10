---
title: "修改Linux系统语言"
categories: [ "服务器技术" ]
tags: [ "Linux","language" ]
draft: false
slug: "change_system_lang"
date: "2014-09-18 05:42:38"
url: "change_system_lang.html"
---

中文总是能引出很多麻烦，所以推荐安装系统的时候不要选择任何与中文有关的选项。

但是如果很不幸你选了中文作为系统语言，还是有补救的办法的。

很简单，请看下面的代码：

编辑i18n：

    vi /etc/sysconfig/i18n

将` LANG="zh_CN.UTF-8" `改为` LANG="en_US.UTF-8" `即可！