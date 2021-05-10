---
title: "ubuntu编译webkit"
categories: [ "服务器技术" ]
tags: [ "centos","gcc","ubuntu" ]
draft: false
slug: "ubuntu_webkitgtk"
date: "2014-05-29 08:50:00"
url: "ubuntu_webkitgtk.html"
---

下载安装包：<a href="http://webkitgtk.org/" target="_blank">http://webkitgtk.org/</a> 使用下面命令压缩包

    tar xvJf

安装依赖库：

    apt-get install autoconf automake autotools-dev bison flex gperf glib-networking gtk-doc-tools libenchant-dev libgail-dev libgeoclue-dev libglib2.0-dev libgstreamer-plugins-base0.10-dev libgtk2.0-dev  libicu-dev libjpeg62-dev libpango1.0-dev libpng12-dev libsoup2.4-dev libsqlite3-dev libtool libxslt1-dev libxt-dev

试试Configure一下

	./configure

可能还不够，提示找不到 libwebp，找一下吧：

	apt-cache search  libwebp
    libwebp-dev - Lossy compression of digital photographic images.
    libwebp5 - Lossy compression of digital photographic images.
    libwebpdemux1 - Lossy compression of digital photographic images.
    libwebpmux1 - Lossy compression of digital photographic images.
    python-webm - Python interface to the Google WebM video/image codec.

安装第一个即可

	apt-get install libwebp-dev

找不到GStreamer

WebKit r169462，地址： <http://nightly.webkit.org/> 下载到~/src下 然后解压

	tar jxvf WebKit-r169462.tar.bz2

安装依赖包

	./Tools/gtk/install-dependencies

GStreamer

	apt-get install gstreamer0.10-*
    apt-get install libgstreamer*

开始编译

如果提示没有编译工具就用这个安装，一次到位！

	apt-get install build-essential

  测试系统 ubuntu12.04