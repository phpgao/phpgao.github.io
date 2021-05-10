---
title: "在SourceTree取消被忽略的文件"
categories: [ "代码人生" ]
tags: [  ]
draft: false
slug: "source_tree_cancel_ignore_files"
date: "2016-01-05 05:44:00"
url: "source_tree_cancel_ignore_files.html"
---

![SourceTree忽略文件][1]

老高刚才不小心在SourceTree中忽略了一个上级文件夹(一样的名字没看清，并且勾选了全局忽略)，导致里面的所有文件都被忽略掉了，很是尴尬。。。



搜了半天，总算在[这里][2]找到了有用的答案，原来被SourceTree全局忽略的文件会保存在一个文件中，而不是直接写到.gitignore里，否则就不会这么麻烦了。

<!--more-->




下面是解决办法：

进入SourceTree设置 --- GIT --- 全局忽略列表 -- 编辑

然后把之前误操作的文件夹删除了就OK！


  [1]: https://blog.phpgao.com/usr/uploads/2016/01/2444613047.png
  [2]: https://answers.atlassian.com/questions/158997/how-to-track-an-ignored-file