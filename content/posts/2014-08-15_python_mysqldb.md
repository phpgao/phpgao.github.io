---
title: "centos下安装python-MySQLdb"
categories: [ "代码人生" ]
tags: [ "centos","python","mysqldb","osx" ]
draft: false
slug: "python_mysqldb"
date: "2014-08-15 04:41:00"
url: "python_mysqldb.html"
---

源码安装：
---

点击[这里][1]下载源码

解压前线装一下必要的包

```bash
    yum install python-devel mysql-devel zlib-devel openssl-devel
```

解压

```bash
    tar xf MySQL-python-1.2.3.tar.gz
    cd MySQL-python-1.2.3
```

修改mysite.cfg，指定mysql-config的路径

使用`whereis mysql-config`找路径

```bash
    vi mysite.cfg
    #找到mysql_config = XXX
    改为你的路径
```

继续安装

```bash
    python setup.py build
    python setup.py install
```

工具安装：
---

    sudo easy_install mysql-python

    sudo pip install mysql-python
```

出现错误的解决办法
---

```bash
vi ~/.bash_profile
# add
export DYLD_LIBRARY_PATH='/usr/local/mysql/lib';
PATH="$PATH:/usr/local/mysql/bin"
```

enjoy~

Win下请直接下载编译好的安装包：

[请输入链接描述][2]


  [1]: http://sourceforge.net/projects/mysql-python/files/mysql-python/
  [2]: http://www.codegood.com/downloads