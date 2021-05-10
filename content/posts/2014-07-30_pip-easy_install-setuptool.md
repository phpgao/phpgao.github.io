---
title: "升级python2.7和安装pip,easy_install和setuptool"
categories: [ "代码人生" ]
tags: [ "python","pip","setuptools","easy_install" ]
draft: false
slug: "pip-easy_install-setuptool"
date: "2014-07-30 08:04:00"
url: "pip-easy_install-setuptool.html"
---

# 升级python和安装pip,easy_install和setuptool

新机子装环境什么的最麻烦了，在此记录一下python的升级和包(模块)管理软件


<!--more-->


## 升级python

本次选择版本2.7.8，需要其他版本请移步:[官方FTP][1]

    wget https://www.python.org/ftp/python/2.7.8/Python-2.7.8.tar.xz
    #如果tar版本过低，请使用xz -d xx.tar.xz 然后 tar -xf xxx.tar
    tar -xvf Python-2.7.8.tar.xz
    cd Python-2.7.8
    ./configure
    make
    make install
    make clean
    make distclean

安装好了再看一看python版本，`2.7.8`，妥妥的。

**接下来一步最重要！** 这样才能使这次升级真正生效！否则python命令还是原来的`2.6.6`版

    #备份老python
    mv /usr/bin/python /usr/bin/python2.6.6
    #建立软连接
    ln -s /usr/local/bin/python2.7 /usr/bin/python

升级python以后`yum`的使用可能会受到影响，需要进行一下设置

    vi /usr/bin/yum

找到第一行`#!/usr/bin/python` 改为 `#!/usr/bin/python2.6.6`

至此升级完成！

## pip

pip 是一个安装和管理 Python 包的工具，是 easy_install 的一个替换品。pip 的目标非常明确 – 取代 easy_install. easy_install 有很多不足:

 - 安装事务是非原子操作
 - 只支持 svn
 - 没有提供卸载命令
 - 安装一系列包时需要写脚本

pip 解决了以上问题，已俨然成为新的事实标准.

Pypi源：[Pypi源地址][2]

### 安装

pip的安装可以参考[pip安装文档][3]

#### 系统支持

> pip works with CPython versions 2.6, 2.7, 3.1, 3.2, 3.3, 3.4 and also
> pypy.
> 
> pip works on Unix/Linux, OS X, and Windows.

#### 安装命令

On Debian and Ubuntu:

    sudo apt-get install python-pip

On Fedora and Redhat(Centos):

    sudo yum install python-pip
    
还可以这样安装
    
    cd ~ && wget -O - "https://bootstrap.pypa.io/get-pip.py" | python

#### 升级pip

On Linux or OS X:

    pip install -U pip

On Windows:

    python -m pip install -U pip

### pip使用

    # 安装包
    pip install Scrapy
    # 通过使用==, >=, <=, >, <来指定一个版本号。
    $ pip install 'Markdown<2.0'
    # 卸载包
    pip uninstall Scrapy
    # 升级包
    pip install --upgrade Scrapy
    pip install -U Scrapy
    # 查询包
    pip search "Markdown"
    # 列出已安装包
    pip freeze

包安装后的py文件路径：/usr/local/lib/python2.7/dist-packages

更多使用方法请参考[PIP文档][4]



## setuptool

### 安装

安装帮助->[官方文档][5]

#### linux

##### 直接安装

Debian/Ubuntu:

    sudo apt-get install python-setuptools

RedHat/Centos:

    sudo yum install setuptool  

##### 脚本安装

    wget https://bootstrap.pypa.io/ez_setup.py -O - | python
    unzip setuptools-5.4.1.zip
    cd setuptools-5.4.1
    move ../setuptools-5.4.1.zip .
    python ez_setup.py

安装方法还有源代码编译和egg安装，在此不作赘述。

#### windows

windows下安装直接下载脚本，之后

    # https://bootstrap.pypa.io/ez_setup.py
    python ez_setup.py
    
至此安装完毕！

> easy_install是setuptools自带的安装脚本，也就是一旦 setuptools 安装完毕， easy_install 也便可用. 最大的特点是自动查找 Python 官方维护的包源 PyPI ， 安装第三方 Python 包非常方便。

### 使用

    # 安装包
    easy_install Scrapy
    # 卸载包
    easy_install -m Scrapy
    # 升级包
    easy_install --upgrade Scrapy

使用easy_install安装的包会保存在

linux : `/usr/lib/python2.X/site-packages/easy-install.pth`
Windows：`C:\Python25\Lib\site-packages\easy-install.pth`


最新参考

http://toomuchdata.com/2014/02/16/how-to-install-python-on-centos/

  [1]: https://www.python.org/ftp/python/
  [2]: https://pypi.python.org/pypi/pip
  [3]: http://pip.readthedocs.org/en/latest/installing.html
  [4]: http://pip.readthedocs.org/en/latest/installing.html
  [5]: https://pypi.python.org/pypi/setuptools