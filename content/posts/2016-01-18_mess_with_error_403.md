---
title: "一个很无力的403错误"
categories: [ "代码人生" ]
tags: [ "nginx","upupw","403" ]
draft: false
slug: "mess_with_error_403"
date: "2016-01-18 03:38:23"
url: "mess_with_error_403.html"
---

![403错误][1]

老高为了省时省力，在window平台下的开发环境使用了[UPUPW][2]，这个环境使得老高可以在5min内搭建出一个完整的(nginx/apache) + PHP + my*开发环境。

但是最近在调试接口的时候，偶尔会有几个接口直接报错403，于是开始了无尽的403折磨之路。


<!--more-->


首先看是不是代码抛出的403，结果发现干脆执行不到index.php，断定是nginx的问题。

然后看日志，发现error_log的配置是致命错误才打印日志，果断开成debug模式，然后发现错误信息为：`access forbidden by rule`，说明被nignx的某一条配置给挡住了。

根据经验，一般是nginx会使用deny语句否定请求，于是祭出grep大招：

```
# cd 到nginx的配置目录
grep -rn deny .
```

结果如下：

> λ grep -rn deny .
> ./advanced_settings.conf:14:        deny all;
> ./advanced_settings.conf:25:            deny all;
> ./advanced_settings.conf:45:        deny  all;
> ./advanced_settings.conf:52:            deny all;
> ./vhosts.conf:13:        deny all;
> ./vhosts.conf:43:        deny all;
> ./vhosts.conf:70:        deny all;

继续探索发现在`advanced_settings.conf`45行有这么一条配置：

```
#禁止外部通过域名访问以下后缀的敏感文件
        location ~* (up.+|\.)(htaccess|conf|uini|log)$ {
             deny  all;
        }
```

搜嘎，突然回想一下，出错的url好像是log结尾的。。。所以解决办法有两种：

1. 改url
2. 改配置


继续撸代码去了。。。


  [1]: https://blog.phpgao.com/usr/uploads/2016/01/875541267.png
  [2]: http://www.upupw.net/