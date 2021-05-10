---
title: "git的一些特殊操作"
categories: [ "代码人生" ]
tags: [ "github","git" ]
draft: false
slug: "git_useful_command"
date: "2015-01-26 02:54:00"
url: "git_useful_command.html"
---

## 删除本地提交并推至origin

```
# 第一步
## 删除本地提交(HEAD为最近一次提交,HEAD^上上次)
git reset --hard commit-id

# 第二步
## 强制推送
git push origin +master
## 或
git push origin HEAD --force
```

## RESET

根据–soft –mixed –hard，会对working tree和index和HEAD进行重置:

> git reset –mixed：此为默认方式，不带任何参数的git reset，即时这种方式，它回退到某个版本，只保留源码，回退commit和index信息
> git reset –soft：回退到某个版本，只回退了commit的信息，不会恢复到index file一级。如果还要提交，直接commit即可
> git reset –hard：彻底回退到某个版本，本地的源码也会变为上一个版本的内容


## 关闭ISSUE

在提交的message中添加如下内容会自动与issue #id关联。是不是很方便啊！

- fixes #xxx
- fixed #xxx
- fix #xxx
- closes #xxx
- close #xxx
- closed #xxx

![效果图][1]


Reference：

http://www.cnblogs.com/shure/archive/2012/11/07/2758663.html
http://blog.csdn.net/hshl1214/article/details/8084423


  [1]: https://blog.phpgao.com/usr/uploads/2015/01/2788895670.png