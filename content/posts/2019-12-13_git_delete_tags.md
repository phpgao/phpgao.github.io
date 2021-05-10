---
title: "git-批量删除tag"
categories: [ "代码人生" ]
tags: [ "git","tag" ]
draft: false
slug: "git_delete_tags"
date: "2019-12-13 02:12:21"
url: "git_delete_tags.html"
---

一句话代码

```
# 批量删除远程tag
git tag -l|xargs git push origin --delete tag

# 批量删除本地tag
git tag -l|xargs git tag -d
```