---
title: "git仓库删除敏感信息"
categories: [ "代码人生" ]
tags: [ "data","git","sensitive" ]
draft: false
slug: "git_remove_sensitive_data"
date: "2015-02-13 01:59:00"
url: "git_remove_sensitive_data.html"
---

如果不小心再某一次commit了一个含有敏感信息的文件，如公共的数据库配置。然后又不小心上传至github或者其他远程仓库中，那么想要彻底把此文件删除确实不是一件简单的事情。

因为如果只是在下一次提交中删除或修改了某文件，在git提交的历史中还是能够找到修改前的版本，这个操作无非是掩耳盗铃。

听说前一阵有人专门写爬虫，专门爬云储存的用户名密码，不少人都被黑了。所以如果不小心在git中可能包含敏感信息，commit前一定要仔细检查。

那么如何用正确的姿势删除git中的敏感信息呢？下面看看官方给出的解决步骤：

[git删除敏感信息][1]

Reference:

https://help.github.com/articles/remove-sensitive-data/


  [1]: https://help.github.com/articles/remove-sensitive-data/