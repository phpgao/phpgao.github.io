---
title: "git使用笔记"
categories: [ "代码人生" ]
tags: [ "git","phpstorm" ]
draft: false
slug: "note_for_git"
date: "2014-11-02 13:04:00"
url: "note_for_git.html"
---

GIT虽然概念比较难理解，但不得不说他是一款开发利器。

老高总结出了一些GIT中很常见的操作命令，分享给大家。但由于GIT命令繁多，所以我将分为基础和进阶两部分。

## 基础篇：

### 帮助

```bash
git help # 获取帮助，内容如下

usage: git [--version] [--exec-path[=GIT_EXEC_PATH]] [--html-path]
           [-p|--paginate|--no-pager] [--no-replace-objects]
           [--bare] [--git-dir=GIT_DIR] [--work-tree=GIT_WORK_TREE]
           [--help] COMMAND [ARGS]

The most commonly used git commands are:
   add        Add file contents to the index
   bisect     Find by binary search the change that introduced a bug
   branch     List, create, or delete branches
   checkout   Checkout a branch or paths to the working tree
   clone      Clone a repository into a new directory
   commit     Record changes to the repository
   diff       Show changes between commits, commit and working tree, etc
   fetch      Download objects and refs from another repository
   grep       Print lines matching a pattern
   init       Create an empty git repository or reinitialize an existing one
   log        Show commit logs
   merge      Join two or more development histories together
   mv         Move or rename a file, a directory, or a symlink
   pull       Fetch from and merge with another repository or a local branch
   push       Update remote refs along with associated objects
   rebase     Forward-port local commits to the updated upstream head
   reset      Reset current HEAD to the specified state
   rm         Remove files from the working tree and from the index
   show       Show various types of objects
   status     Show the working tree status
   tag        Create, list, delete or verify a tag object signed with GPG
```

### 配置git

```bash
# 查看配置
git config -l/--list

# 以下是可能出现的配置
core.symlinks=false
core.autocrlf=true
color.diff=auto
color.status=auto
color.branch=auto
color.interactive=true
pack.packsizelimit=2g
help.format=html
...
...

# 配置全局信息
git config --global user.name=phpgao

# 配置局部信息
git config --system user.email=endoffight@gmail.com

# 查看某一个配置信息
git config user.email

```

### 初始化仓库

```bash
git init # 在当前目录初始化一个git仓库

git init --bare # 在当前目录初始化一个git裸仓库
```

### 查看

```bash
git status # 显示工作流中的状态

git diff # 显示工作目录(Working tree)和暂存区域快照(index)之间的差异

git diff --stat # 简报

git diff --cached # 显示已经暂存起来的文件(staged)和上次提交时的快照之间(HEAD)的差异

git diff --staged # 下一次commit时会提交到HEAD的内容(不带-a情况下)

git diff dev # 比较当前目录和dev分支 

git diff HEAD # 工作目录和HEAD的差别

git diff HEAD^ HEAD # 比较上次和上上次提交的不同

git diff dev master # 比较两个分支最新提交

git diff dev..master # 同上

git diff dev...master # 比较从分支开始时至今所有的修改

git log --pretty=oneline # 显示日志

### 美化格式一

git log --pretty=format:"%h %ad | %s%d [%an]" --graph --date=short

### 美化格式二

git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr)%Creset' --abbrev-commit --date=relative 
```

## 增删提

先读懂这个图

![提交关系图][1]

```bash
git add # 添加工作区修改的文件提交至Stage(index)

git commit -m "comment" # 将Stage(index)中的文件提交至本地库中(commit)，并添加注释

git commit -am "comment" # 省略了add步骤，直接提交Working Directory和Stage(index)中的内容

git rm <文件名> # 删除库中的文件

git reset --hard # 恢复最近一次提交过的状态，即放弃上次提交后的所有本次修改

git reset -- . # 从暂存区恢复到工作文件

```

## 分支与合并

```bash
git branch <分支名> <老分支名># 根据分支创建新分支

git branch -r # 查看远程分支

git branch -v # 查看各分支最近的提交

git branch -d <分支名> # 删除一个分支

git br -D <分支名> # 强制删除某个分支 (未被合并的分支被删除的时候需要强制)

git branch -m <分支名> <新分支名> # 重命名一个分支

git checkout <分支名> # 切换至某分支

git checkout -b <分支名> # 创建并切换至新分支

git merge dev # 将当前分支与dev分支合并

git merge dev --no-ff # 不使用Fast-Foward合并，为了保持项目的清晰的轨迹，推荐合并时使用此选项
```

这里前者到一个概念叫分支策略，可以参考这篇文章[Git分支管理策略][2]。

## 远程操作

### clone

```bash
git clone /xx/xxx/xxx.git # 克隆某个项目

git支持很多协议，如ssh、git、https等。
```

### remote

```bash
git remote -v # 查看远程库

git remote add origin xxxx.git # 添加一个远程主机

git remote rm # 删除一个远程主机
```

### fetch

```bash
git fetch <远程主机名> # 取回所有信息

git fetch <远程主机名> <分支名> # 只取回某分支

git branch -a # 查看所有分支

git branch -r # 查看远程分支
```

### pull

```bash
git push 
```

### push

```bash

```

## jetbrains系列软件问题

### GIT Remotes "Can't push, because no remotes are defined"

软件中没有添加remote的功能，所以如果你要新加入一个远程库就需要在terminal中使用以下命令

```bash
git remote add origin "path to .git" 
```

### fatal: No existing author found with 'john doe'

先使用`git config -l`查看配置，得到name和email如下

> user.name=aaa
user.email=bbb@cc.com

软件配置里填入

aaa <bbb@cc.com>

这样软件转化的命令就变成

```bash
git commit --author="aaa <bbb@cc.com>" -m "Note"
```


参考：

https://ruby-china.org/topics/939


  [1]: http://dl2.iteye.com/upload/attachment/0080/2435/75df9cfb-7c89-3c5e-bbab-1d8610a2e22d.jpg
  [2]: http://www.ruanyifeng.com/blog/2012/07/git.html