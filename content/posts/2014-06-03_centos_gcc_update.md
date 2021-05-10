---
title: "centos升级gcc至4.8"
categories: [ "服务器技术" ]
tags: [ "centos","gcc" ]
draft: false
slug: "centos_gcc_update"
date: "2014-06-03 08:17:00"
url: "centos_gcc_update.html"
---

webkitgtk+的编译要用到4.8+的GCC 人家ubuntu都有编译好的gcc。。。可怜我centos只能编译源代码了。。。

废话不说，速度干活 

## 下载

先下载需要的GCC源码，<a href="http://gcc.gnu.org/" target="_blank">http://gcc.gnu.org/</a>

以gcc-4.8.3为例 先解压文件 

    cd ~
    mkdir src
    cd src
    wget http://自己写吧
    tar xf gcc-4.8.3.tar.gz
    
到这一步停下，还有软件包没有下载安装，难点重点就在这儿了，这三个软件包的安装顺序不能乱，而且安装后一个软件之前必须指定前面安装的路径，很麻烦！

下载源代码可以执行gcc-4.8.3/contrib下的download_prerequisites脚本，即可自动下载，到时候也好一起编译

<!--more-->

**GMP** GMP is the GNU Multiple Precision Arithmetic Library. 	

    cd gmp
    ./configure --disable-shared --enable-static --prefix=/tmp/gcc
    make && make check && make install
    
**MPFR** MPFR is the GNU Multiple-precision floating-point rounding library. It depends on GMP. 

    cd mpfr
    ./configure --disable-shared --enable-static --prefix=/tmp/gcc --with-gmp=/tmp/gcc
    make && make check && make install
    
**MPC** MPC is the GNU Multiple-precision C library. It depends on GMP and MPFR. 

    cd mpc
    ./configure --disable-shared --enable-static --prefix=/tmp/gcc --with-gmp=/tmp/gcc --with-mpfr=/tmp/gcc
    make && make check && make install 下面才是主角GCC，他的编译需要在另一个文件夹中进行 

## 编译

接下来开始编译gcc

    cd /home/src
    mkdir gcc-build
    cd gcc-build
    ../gcc-4.8.3/configure --enable-checking=release --enable-languages=c,c++ --disable-multilib --with-gmp=/tmp/gcc --with-mpfr=/tmp/gcc --with-mpc=/tmp/gcc
    make -j4 && make check && make install

可能出现的错误： 

1./usr/include/gnu/stubs.h:7:27: fatal error: gnu/stubs-32.h: No such file 
解决办法：

	yum install glibc-devel
    yum install glibc-devel.i686
    
#Ubuntu下：
apt-get install libc6-dev-i386 

2.找不要依赖包 这个嘛。。。自己yum search吧！ 

ps. 安装之前最好安装一下开发环境

Ubuntu:

    apt-get install build-essential

centos:

    yum groupinstall "Development Tools" 
    
3.检查gcc版本 

    gcc -v 

2014-6-18更新快键安装代码(请以root权限运行) 

    cd ~
    mkdir src
    cd src
    wget http://ftp.gnu.org/gnu/gcc/gcc-4.8.3/gcc-4.8.3.tar.bz2
    tar jxf gcc-4.8.3.tar.bz2
    cd gcc-4.8.3
    ./contrib/download_prerequisites
    cd ..
    mkdir gcc-build
    cd gcc-build
    ../gcc-4.8.3/configure --enable-checking=release --enable-languages=c,c++ --disable-multilib
    make -j4
    make check
    make install