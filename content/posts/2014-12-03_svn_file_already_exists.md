---
title: "File-already-exists--filesystem-解决办法"
categories: [ "代码人生" ]
tags: [ "svn","filesystem","解决办法" ]
draft: false
slug: "svn_file_already_exists"
date: "2014-12-03 05:42:00"
url: "svn_file_already_exists.html"
---

今天用svn命令行提交版本的时候，碰到了这个比较麻烦的问题

```bash
svn: File already exists: filesystem 'xxx/svn/xxx/db'
```

搜了一下解决办法，都是需要两次commit，太麻烦。

直接在提交根目录执行以下命令

```bash
svn update path/ --accept=mine-full
```

一句话解决！


