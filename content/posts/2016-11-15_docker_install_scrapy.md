---
title: "docker使用笔记VII----Scrapy"
categories: [ "代码人生" ]
tags: [ "scrapy" ]
draft: false
slug: "docker_install_scrapy"
date: "2016-11-15 07:53:00"
url: "docker_install_scrapy.html"
---

最近在研究Scrapy的安装，发现在centos上安装还是挺麻烦的，于是总结出来所有的依赖和加速的问题，终于能用了！

下面看看老高是怎么在Centos下安装Scrapy！

![sc.png][1]


<!--more-->


```
From centos:7

MAINTAINER LAO GAO <endoffight#gmail.com>

RUN sed -i.backup 's/^enabled=1/enabled=0/' /etc/yum/pluginconf.d/fastestmirror.conf && \
yum update -y && \
yum install -y wget && \
yum install -y python-setuptools libxslt-devel python-devel libffi-devel openssl-devel gcc && \
yum clean all

RUN easy_install -i https://pypi.tuna.tsinghua.edu.cn/simple pip
RUN pip install -i https://pypi.tuna.tsinghua.edu.cn/simple Scrapy

CMD ["bash"]
```


  [1]: https://blog.phpgao.com/usr/uploads/2016/11/95790073.png