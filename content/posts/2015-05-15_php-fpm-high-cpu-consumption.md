---
title: "排查PHP-FPM占用CPU过高"
categories: [ "代码人生" ]
tags: [ "PHP","cpu","strace" ]
draft: false
slug: "php-fpm-high-cpu-consumption"
date: "2015-05-15 10:48:00"
url: "php-fpm-high-cpu-consumption.html"
---

最近老高发现服务器的CPU总是被某个php-fpm占用过高，记录一下如何排查。


## 发现

如何发现的呢？当然是使用`top`命令，发现系统的load average>3，这说明系统已经处于比较高的负载中。

## 尝试解决

当我把php-fpm重启后，没过一会儿又开始cpu狂飙！这是什么鬼？

## 开始排查

首先，我们开启在php-fmp.conf中开启错误日志，慢执行日志还有常规日志

```
error_log = /var/log/php/error.log
access.log = /var/log/php/access.$pool.log
access.format = "%R - %u %t \"%m %r%Q%q\" %s %f %{mili}d %{kilo}M %{system}C%%"
slowlog = /var/log/php/slow.$pool.log
request_slowlog_timeout = 3s
```

重启php-fpm后我们开始监视日志

```
# 查看慢执行日志

[15-May-2015 12:50:22]  [pool www] pid 1819
script_filename = /home/ftp/phpergao/wwwroot/index.php
[0x00007f2d286c2790] replace() /home/ftp/phpergao/wwwroot/usr/plugins/CdnHelper/Plugin.php:72
[0x00007fff78ab00f0] replace() unknown:0
[0x00007f2d286c2420] call_user_func_array() /home/ftp/phpergao/wwwroot/var/Typecho/Plugin.php:489
[0x00007fff78ab0430] __call() unknown:0
[0x00007f2d286c1f78] contentEx() /home/ftp/phpergao/wwwroot/var/Widget/Abstract/Contents.php:141
[0x00007f2d286c1b78] ___content() /home/ftp/phpergao/wwwroot/var/Typecho/Widget.php:385
[0x00007fff78ab0850] __get() unknown:0
[0x00007f2d286c1870] content() /home/ftp/phpergao/wwwroot/var/Widget/Abstract/Contents.php:783
[0x00007f2d286c1628] content() /home/ftp/phpergao/wwwroot/var/Widget/Archive.php:1401
[0x00007f2d286c14d0] content() /home/ftp/phpergao/wwwroot/usr/themes/just/index.php:32
[0x00007f2d286c10f8] +++ dump failed

[15-May-2015 19:18:48]  [pool www] pid 5597
script_filename = /home/ftp/phpergao/wwwroot/index.php
[0x00007ff17fcf0168] __call() /home/ftp/phpergao/wwwroot/var/Typecho/Plugin.php:483
[0x00007fff915493c0] __call() unknown:0
[0x00007ff17fcefca8] ___title() /home/ftp/phpergao/wwwroot/var/Typecho/Widget.php:387
[0x00007fff915497e0] __get() unknown:0
[0x00007ff17fcef960] title() /home/ftp/phpergao/wwwroot/var/Widget/Abstract/Contents.php:809
[0x00007ff17fcef6d0] title() /home/ftp/phpergao/wwwroot/usr/themes/just/index.php:23
[0x00007ff17fcef2f8] +++ dump failed
```

其中**contentEx**引起了我的注意，这个方法是一个钩子，系统在获取到文章内容后执行，老高的有几个插件都挂载在此，突然就有想法了。

于是立即暂停有关的插件，过一阵负载变为load average: 0.39, 0.29, 0.42。

## 记录程序运行细节

### 记录程序运行时间

```
<?php
$start = microtime(true);

//index.php

$end = microtime(true);
$time = number_format(($end - $start), 2);

echo 'This page loaded in ', $time, ' seconds';
?>
```

### 跟踪php的系统调用

老高使用strace查看php主进程以及fork出的子进程的系统调用，并输出到/tmp/output.txt

```
strace -o /tmp/output.txt -T -tt -F -e trace=all -p 31920
```

将输出的文件用scp拷贝到本地电脑上，经过分析，并发+插件几乎拖死了CPU。

## 结论

1. 某些数据的展示与否最好把性能也考虑上
1. 正则的效率不高，能不用尽量不用
1. 主题中如果同一个变量要使用多次，请将其先保存至一个临时变量
1. 缓存很重要
1. strace是个好工具

这篇文章也可以学习一下  [PHP高效率写法（详解原因）][1]


  [1]: http://www.open-open.com/lib/view/1332904714233#_label0