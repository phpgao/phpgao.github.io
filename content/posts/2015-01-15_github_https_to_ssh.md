---
title: "将你的git协议由https变为ssh"
categories: [ "代码人生" ]
tags: [ "ssh","github","https","git" ]
draft: false
slug: "github_https_to_ssh"
date: "2015-01-15 12:34:00"
url: "github_https_to_ssh.html"
---

你是不是已经厌倦了每次`git push`的时候每次都要输入用户名密码，使用下面的方法可以让你使用ssh协议通过密钥验证的方式让你得到解脱。

有两种修改方法

不过再实施前，请先准备好自己的密钥

```bash
ssh-keygen -t rsa -C "your_name"
```

然后登录[https://github.com/settings/ssh][1]，添加当前计算机的`~/.ssh/id_rsa.pub`公钥内容到github。

之后我们使用`ssh git@github.com`验证是否添加成功，如果返回以下内容，即代表添加成功！

> Hi phpgao! You've successfully authenticated, but GitHub does not provide shell access.

下一步就是让我们的git使用公钥验证。

## clone

保存你的最后一次修改并提交。

删除项目

使用下面的命令clone项目

```git
# 采用ssh的方式克隆项目
# someaccount/someproject.git 中 some account为github用户名/someproject为仓库名

git clone git@github.com:phpgao/BaiduSubmit.git
```

## 修改https

```git
git remote set-url origin git@github.com:someaccount/someproject.git
```


顺便提一下，老高的`git push`总是报`warning: push.default is unset`错误，今天终于知道为啥了。原来是版本兼容性的原因，低版本的`git push`如果不指定分支名，就会全部推送，而新版只会推送当前分支。

解决的办法也很简单，我们只需要明确指定应该推送方式即可，至于选择哪种方式，It's up to you.

```git

# 全部推送
git config --global push.default matching

# 部分推送
git config --global push.default simple
```

完

  [1]: https://github.com/settings/ssh