---
title: "Linux下安装trash-cli防止误删文件"
categories: [ "服务器技术" ]
tags: [ "bash","trash","trash-cli" ]
draft: false
slug: "linux-trash-cli"
date: "2015-05-28 11:43:00"
url: "linux-trash-cli.html"
---

玩过Linux的同学应该都知道这个要命的命令，此命令一出，系统几乎废了。

```bash
rm -rf /
```

虽然一再强调`rm`的时候要注意，但是还是会有同学犯错误删文件，有什么办法能够减少此类事情的发生呢？

今天老高就要介绍此工具-----  `trash-cli`。他实现了类似回收站的功能，以后删除的时候就有了后悔药。

![trash-cli][1]

<!--more-->


trash-cli的项目地址 [trash-cli][2]

## 安装

trash-cli的安装很简单

```
cd /tmp
https://github.com/andreafrancia/trash-cli
cd trash-cli
sudo python setup.py install
```

安装成功后我们的系统就有了以下工具

```
➜  ~  ll /usr/bin/|grep trash
-rwxr-xr-x   1 root root     123  5月 28 19:38 trash
-rwxr-xr-x   1 root root     125  5月 28 19:38 trash-empty
-rwxr-xr-x   1 root root     124  5月 28 19:38 trash-list
-rwxr-xr-x   1 root root     123  5月 28 19:38 trash-put
-rwxr-xr-x   1 root root     127  5月 28 19:38 trash-restore
-rwxr-xr-x   1 root root     122  5月 28 19:38 trash-rm
```

**功能说明**

```
trash == trash-put == 删除
trash-empty 清空
trash-list 列出回收站
trash-restore 恢复文件
trash-rm  删除回收站中指定文件
```

## 如何使用

### 替换命令

为了安全着想，我们先将`rm`命令替换为`trash`

```bash
vim /etc/bashrc
```

添加以下内容，替换系统删除命令。

根据作者的README文件，建议还是轻易不要把`rm`做替换，下面的替换作废。

<del>

alias log='cd /var/log'

# 安全删除
alias rm='trash'

# 列出回收站
alias rl='trash-list'

</del>

作者原文

> Can I alias rm to trash-put?
You can but you shouldn't. In the early days I thought it was good idea do that but now I changed my mind.
The interface of trash-put seems to be compatible with rm it has a different semantic that will cause you problems. For example, while rm requires -R for deleting directories trash-put does not.
But sometimes I forgot to use trash-put, really can't I?
You may alias rm to something that will remind you to not use it:
alias rm='echo "This is not the command you are looking for."; false'
If you really want use rm simply prepend a slash:
\rm file-without-hope
Note that Bash aliases are used only in interactive shells, so using this alias should not interfere with scripts that expects to use rm.


所以新的替换是

```
# 使用rm前确认
alias rm='echo " This is not the command you are looking for.If you really want use rm simply prepend a slash"; false'
```

以后执行`rm`的话，就会出现以下情况

```bash
➜  ~  rm
 This is not the command you are looking for.If you really want use rm simply prepend a slash
```

以后如果真的要删除的话，请使用`\rm 真的很没用的文件`，而使用`trash 需要删除的文件`。

完成后输入`source /etc/bashrc`是修改的别名生效。

### 常用操作

```
# 安全删除
rm ./*

# 查看回收站
➜  /tmp  rl
2015-05-28 19:59:54 /tmp/redis-stable

# 清空回收站
trash-empty

# 清空10天以前放入回收站的文件
trash-empty 10

# 还原文件
➜  /tmp  trash-restore
   0 2015-05-28 19:59:54 /tmp/redis-stable
What file to restore [0..0]: 输入对应序号还原
```

  [1]: https://blog.phpgao.com/usr/uploads/2015/05/2919137931.jpg
  [2]: https://github.com/andreafrancia/trash-cli