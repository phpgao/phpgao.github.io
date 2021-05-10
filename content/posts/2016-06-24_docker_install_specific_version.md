---
title: "安装指定版本的docker"
categories: [ "服务器技术" ]
tags: [ "docker" ]
draft: false
slug: "docker_install_specific_version"
date: "2016-06-24 03:38:00"
url: "docker_install_specific_version.html"
---

安装指定版本的docker，在Google上搜索关键字**docker install specific version**，搜到的解决办法基本都没法用。于是自己动手，发现一个安装指定版本的捷径！

![安装指定版本的docker][1]

<!--more-->


在老高的这个文章中[docker使用笔记III -- docker的快速安装][2]，有一个网址引起了老高的注意:

> https://yum.dockerproject.org/repo/main/centos

打开它，就能发现原来docker的安装包(centos)都藏在这儿！二话不说，先下载下来。

![FTP][3]

```bash
# 我们以安装docker 1.9.1为例
DOCKER_VERSION=1.9.1

# 下载 docker-engine
wget https://yum.dockerproject.org/repo/main/centos/7/Packages/docker-engine-${DOCKER_VERSION}-1.el7.centos.x86_64.rpm

# 下载 docker-engine-selinux
wget https://yum.dockerproject.org/repo/main/centos/7/Packages/docker-engine-selinux-${DOCKER_VERSION}-1.el7.centos.noarch.rpm

yum install -y libtool-ltdl policycoreutils-python

# 先安装 docker-engine-selinux，再安装 docker-engine-selinux
rpm -ivh docker-engine-selinux-${DOCKER_VERSION}-1.el7.centos.noarch.rpm
rpm -ivh docker-engine-${DOCKER_VERSION}-1.el7.centos.x86_64.rpm

# 下面就可以开启docker服务了
systemctl start docker
systemctl enable docker
```

> 小提示，如果没有wget，可以使用命令`yum install -y wget`安装。

补充一个更快捷的方法：

```
yum provides docker
yum install docker-1.9.1-25.el7.centos.x86_64
```


  [1]: https://blog.phpgao.com/usr/uploads/2016/06/799230615.png
  [2]: https://blog.phpgao.com/docker-note3-html.html
  [3]: https://blog.phpgao.com/usr/uploads/2016/06/3551025973.png