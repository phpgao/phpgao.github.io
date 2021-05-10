---
title: "在github的某次commit中close或者fix某个issue"
categories: [ "代码人生" ]
tags: [ "github" ]
draft: false
slug: "github_fix_or_close_issue"
date: "2015-11-10 05:32:00"
url: "github_fix_or_close_issue.html"
---

当你在某次提交解决了其他人提出的issue时，你可以使用github中的语法实现自动操作。


<!--more-->

以下是**动作命令**

 - close
 - closes
 - closed
 - fix
 - fixes
 - fixed
 - resolve
 - resolves
 - resolved

命令需要指定操作目标，我们可以使用 `#XXX` (XXX是需要操作的目标issue的ID)指定。

举例子：

在此次提交中关闭第八号 issue，你可以在commit信息里写到

```
......
...... 
Closes #8
```

还可以一次做多个操作

```
Fixed #8 , and closes #9
```

甚至可以操作其他仓库中的issue

```
This closes #34, closes #23, and closes example_user/example_repo#42
```



REFER:

[Closing issues via commit messages][1]


  [1]: https://help.github.com/articles/closing-issues-via-commit-messages/