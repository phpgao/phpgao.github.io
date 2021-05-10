---
title: "PHP安装配置Opcache加速你的网站"
categories: [ "服务器技术" ]
tags: [ "PHP","compile","opcache","php-fpm" ]
draft: false
slug: "php-opcache-optimize"
date: "2015-05-24 03:08:00"
url: "php-opcache-optimize.html"
---

本文针对PHP5.5等高级版本，编译时需要加上`--enable-opcache`参数

编译PHP请参考[编译PHP5.6][1]

编译安装完成后，我们开始配置Opcache


<!--more-->


```
[Opcache]
zend_extension = opcache.so
opcache.enable=1
opcache.memory_consumption = 64
opcache.interned_strings_buffer = 8
opcache.max_accelerated_files = 4000
opcache.revalidate_freq = 60
opcache.fast_shutdown = 1
opcache.enable_cli = 1
```

修改后重启PHP-FPM

```
service php-fpm restart
```

最后上个图

![PHP安装配置Opcache加速你的网站 配置成功！][2]


  [1]: https://blog.phpgao.com/compile_php.html
  [2]: https://blog.phpgao.com/usr/uploads/2015/05/3074138171.png