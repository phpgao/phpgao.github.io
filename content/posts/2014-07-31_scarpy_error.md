---
title: "安装Scarpy踩过的坑"
categories: [ "代码人生" ]
tags: [  ]
draft: false
slug: "scarpy_error"
date: "2014-07-31 04:50:00"
url: "scarpy_error.html"
---

#安装Scarpy踩过的坑

Scrapy是python下一个著名的爬虫，目前最新版为0.24。

这是他的帮助文档->[Scrapy 0.24 文档][1]

其中**[选择器篇][2]**需要好好研究！

帮助文档里的安装指南写得很宽泛，所以安装出错是在正常不过的事了。(再者说，安装出错确实不是Scrapy的错嘛)

So here is the doc to help you with installing Scrapy.

First of all, before installing, please make sure U have already installed these libs or apps below:

 - python version >= 2.7
 - python-devel package
 - libs:
    1. xml2-dev
    1. xslt-dev
    1. ffi-dev
    1. openssl-dev

If not, commands below may help:

on Redhat/Centos:

    yum install python-devel libxml2-devel libxslt-devel libffi-devel openssl-devel bzip2-devel 

on Debian/Ubuntu:

    apt-get install python-dev libxml2-dev libxslt1-dev libffi-dev libssl-dev libbz2-dev

If your python version is less than 2.7, this article -> [upgrade python][3] may help U in upgrading python.

I have summarized some errors when installing scrapy and hope they can be used as a reference for you.

## CompressionError: bz2 module is not available

This usually happens when you have compiled python and use `pip install Scrapy` command.

The reason is you don't have bzip2 lib installed:

on Redhat/Centos:

    yum install bzip2-devel 

on Debian/Ubuntu:

    apt-get install libbz2-dev

After you have installed the bzip2 package,you have to compile python again to make bzip module work.

Finally,use `pip install --upgrade scrapy` to finish the installation.

## Setup script exited with error: command 'gcc' failed with exit status 1

The cause is u didn't install python-dev

on Redhat/Centos:

    yum install python-devel

on Debian/Ubuntu:

    apt-get install python-dev
    
Finally,use `pip install scrapy` or `easy_install scrapy` to finish the installation.

## error: Not a recognized archive type: /tmp/easy_install-dayrGH/Twisted-14.0.0.tar.bz2

Haha,that's because you already have installed the package!


## UserWarning: You do not have the service_identity module installed.

    sudo pip install service_identity



**That's all for now.Thank u for viewing.**


  [1]: http://scrapy-chs.readthedocs.org/zh_CN/latest/index.html
  [2]: http://scrapy-chs.readthedocs.org/zh_CN/latest/topics/selectors.html#topics-selectors
  [3]: https://blog.phpgao.com/pip-easy_install-setuptool.html