---
title: "为SourceTree添加Kaleidoscope的external-diff支持"
categories: [ "代码人生" ]
tags: [ "git","sourcetree" ]
draft: false
slug: "sourcetree_kaleidoscope"
date: "2015-05-12 16:41:06"
url: "sourcetree_kaleidoscope.html"
---

编辑 ~/.gitconfig


```bash
# add
[difftool "sourcetree"]
    cmd = /usr/local/bin/ksdiff -- \"$LOCAL\" \"$REMOTE\"
    path = 
[mergetool "sourcetree"]
    cmd = /usr/local/bin/ksdiff --merge --output \"$MERGED\" --base \"$BASE\" -- \"$LOCAL\" \"$REMOTE\"
    trustExitCode = true
```

参考

[https://ruby-china.org/topics/14404][1]


  [1]: https://ruby-china.org/topics/14404