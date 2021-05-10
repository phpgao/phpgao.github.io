---
title: "pillow解决encoder-zip-not-available问题"
categories: [ "代码人生" ]
tags: [ "python","pillow" ]
draft: false
slug: "pillow_solve_encoder_zip_not_available"
date: "2015-04-27 03:46:00"
url: "pillow_solve_encoder_zip_not_available.html"
---

最近写程序用到了这个模块，但是貌似在 <Python2.7 的环境下用总是会出现这个问题。


<!--more-->


这个问题网上的答案都解决不了我的问题，过不初步可以确定是关于图片的库出了问题，于是这样：

## 安装命令

```
yum install python-devel -y

yum install libtiff-devel libjpeg-devel libzip-devel freetype-devel \
    lcms2-devel libwebp-devel tcl-devel tk-devel -y


pip install pillow

or

easy_install pillow
```

## 问题

在`qr.make_image`的时候出现错误

> encoder zip not available

老高是这样解决的

```
pip uninstall pillow

yum install libpng-devel libjpeg-devel -y
yum install freetype-devel freetype-demos freetype-utils -y
yum install libtiff-devel libjpeg-devel libzip-devel freetype-devel  \
lcms2-devel libwebp-devel tcl-devel tk-devel -y

pip install pillow
```

以上