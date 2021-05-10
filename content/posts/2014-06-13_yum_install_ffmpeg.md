---
title: "记一次FFMPEG的安装"
categories: [ "服务器技术" ]
tags: [ "ffmpeg" ]
draft: false
slug: "yum_install_ffmpeg"
date: "2014-06-13 02:14:00"
url: "yum_install_ffmpeg.html"
---

## 下载源代码 

现在都不用svn了，学学git吧 

    git clone git://source.ffmpeg.org/ffmpeg.git

## configure 

在执行./configure的时候，可能会提示没有安装yasm，那么执行”yum install -y yasm“即可。 

    yum install -y yasm
    ./configure

## make 

执行make，就是编译整个ffmpeg项目了，会生成一些二进制文件，例如：ffplay、ffserver、ffmpeg、ffprobe! 

## makeinstall 

执行make install时候，会把编译生成的.a归档复制到/usr/local/lib下! 