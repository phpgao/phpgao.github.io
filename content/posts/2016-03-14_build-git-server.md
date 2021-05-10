---
title: "Centos搭建GIT服务器"
categories: [ "服务器技术" ]
tags: [ "git","server" ]
draft: false
slug: "build-git-server"
date: "2016-03-14 05:36:00"
url: "build-git-server.html"
---

## server

1.首先安装git

```bash
yum install git
```

<!--more-->


2.新建一个linux用户，起名为git

```bash
adduser git
```

3.在git用户目录中新建目录 .ssh

```bash
cd /home/git/
mkdir .ssh
```

4.在/home/git/.ssh/目录中新建authorized_keys文件，并将客户端提供的公钥(id_rsa.pub)黏贴到该文件中
touch authorized_keys 或 vi authorized_keys

5.在项目目录创建一个git裸仓库，假如当前项目目录为/home/git/project.git
git init --bare project.git

6.将项目目录和git用户目录下的.ssh目录的所有者和所属组都设置成git

```bash
chown -R git.git project.git  
chown -R git.git /home/git/.ssh/
```

7.为了安全考虑，禁用git用户的shell登录

```bash
vi /etc/passwd
注释 ##git:x:500:500::/home/git:/bin/bash 
改为 git:x:500:500:git version control:/home/git:/usr/bin/git-shell
```

8.git服务器打开RSA认证

```bash
vi /etc/ssh/sshd_config
下面3个打开
 1.RSAAuthentication yes     
 2.PubkeyAuthentication yes     
 3.AuthorizedKeysFile  .ssh/authorized_keys
```

## client


1.生成公钥,并复制到剪切板

```bash
ssh-keygen -t rsa
pbcopy < ~/.ssh/id_rsa.pub
```

此步骤要将公钥发给git服务器管理员

2.在本地新建git仓库

```bash
git init
```

3.新建一个文件并推送到服务器

```bash
touch readme.txt
git add readme.txt
git commit -m "readme"
git remote add origin git@xxx.xxx.xxx.xxx:/home/git/project.git
git push origin master
```

注：如果提示需要密码，请检测公钥是否配置成功或RSA是否开启。
