---
title: "Centos源设置"
categories: [ "服务器技术" ]
tags: [ "centos","repo" ]
draft: false
slug: "repo_for_centos"
date: "2014-06-18 06:41:00"
url: "repo_for_centos.html"
---

## 起因

刚刚装好的centos网卡也配好了，就是解析不了mirror.centos.org，估计是DNS的问题，索性换了163源。

[网易源地址][1]

[使用帮助][2]

## 方法

很简单，注意备份即可

```bash
cd /etc/yum.repos.d
mv CentOS-Base.repo CentOS-Base.repo.backup
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.163.com/.help/CentOS5-Base-163.repo #centos5
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.163.com/.help/CentOS6-Base-163.repo #centos6
yum clean all
yum makecache
```

## 意外

在执行update的时候又报错

> GPG key retrieval failed: [Errno 12] Timeout on http://mirror.centos.org/centos/RPM-GPG-KEY-CentOS-6: (28, 'connect() timed out!')

貌似需要在源服务器验证RPM的合法性 本办法，只好祭出终极武器，host大法!

```bash
vi /etc/hosts
```

加入 74.53.10.146      mirror.centos.org 解决！

## 阿里源

阿里的速度也不错！

repo下载地址： [http://mirrors.aliyun.com/repo/][3]

```bash

mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup

# 根据系统版本下载对应版本的repo
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo

或

curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo


# 更新缓存
yum clean all
yum makecache
```


  [1]: http://mirrors.163.com/
  [2]: http://mirrors.163.com/.help/centos.html
  [3]: http://mirrors.aliyun.com/repo/