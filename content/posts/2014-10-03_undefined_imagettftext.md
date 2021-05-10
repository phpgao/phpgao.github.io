---
title: "Call-to-undefined-function-imagettftext()解决方法"
categories: [ "代码人生" ]
tags: [ "PHP","freetype","imagettftext" ]
draft: false
slug: "undefined_imagettftext"
date: "2014-10-03 06:45:00"
url: "undefined_imagettftext.html"
---

老高在一个新环境中装DEDECMS的时候发现后台验证码无法显示。直接搜索一下这个错误，有人说session错误，有的说权限错误等等，这不胡扯么！只能看源代码了，定位到文件`/include/vdimgck.php`。出错的函数是`imagettftext()`，由于织梦使用了`@`将错误隐去，导致这次莫名的错误。将`@`去掉，错误立马出现：

> Fatal error: Call to undefined function imagettftext()

现在我们就明确了，出现错误的原因是**PHP编译时没有加上FreeType**。

解决办法：

首先编译安装FreeType，以2.4.0为例：

    wget http://download.savannah.gnu.org/releases/freetype/freetype-2.4.0.tar.bz2
    tar -jxf freetype-2.4.0.tar.bz2
    cd reetype-2.4.0
    # 安装到/usr/local/freetype
    ./configure --prefix=/usr/local/freetype
    make && make install

下面我们重新编译PHP，加上参数`--with-freetype-dir=/usr/local/freetype`

    ./configure \
    ... \
    ... \
    --with-freetype-dir=/usr/local/freetype

编译完成重启php

    kill -USR2 `cat /usr/local/php/var/run/php-fpm.pid`

再GD库中找到`FreeType Support`说明安装成功！

需要注意的是，如果服务器freetype的版本是1.*，那么你可能需要改变编译参数为`--with-ttf[=DIR]`，以下转自ChinaUnix论坛：

> 字库 配置开关
FreeType 1.x 要激活 FreeType 1.x 的支持，加上 --with-ttf[=DIR]。   
FreeType 2 要激活 FreeType 2 的支持，加上 --with-freetype-dir=DIR。   
T1lib 要激活 T1lib（Type 1 字体），加上 --with-t1lib[=DIR]。   
本地 TrueType 字符串函数 要激活本地 TrueType 字符串函数的支持，加上 --enable-gd-native-ttf。

参考：

[http://bbs.chinaunix.net/thread-610205-1-1.html][1]



  [1]: http://bbs.chinaunix.net/thread-610205-1-1.html