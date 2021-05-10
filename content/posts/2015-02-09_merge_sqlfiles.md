---
title: "在windows和linux下合并sql文件"
categories: [ "服务器技术" ]
tags: [ "windows","sql","Linux","merge" ]
draft: false
slug: "merge_sqlfiles"
date: "2015-02-09 08:34:00"
url: "merge_sqlfiles.html"
---

合并多个sql文件

win:

```bash
copy *.sql merged.sql
# \b 指二进制合并，可用于图片等二进制文件
# \a 指ASCII合并
copy \b *.sql merged.sql

# 小技巧，可实现文本隐藏
copy 1.jpg/b + 2.txt/a 3.jpg
```


linux:

```bash
# 合并所有扩展名为.sql的文件
cat *.sql>>merged.sql
```