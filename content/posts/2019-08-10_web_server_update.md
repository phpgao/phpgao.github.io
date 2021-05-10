---
title: "记一次网站升级"
categories: [ "服务器技术" ]
tags: [ "server" ]
draft: false
slug: "web_server_update"
date: "2019-08-10 14:04:00"
url: "web_server_update.html"
---

老高于今年(2019)初收到了搬瓦工要停止OPENVZ的旧版服务器，同时列表中auto renew的选项被强制置灰。9.9刀的绝版服务器就这样被印上了大大的`拆`字，而且还是两台！😂

跑数据库的那台机器4月到期，于是把数据库先迁移了，跑在一台内存1G的机器上，由于上面还跑了一个git服务和ss，剩余内存也就400MB左右。下来就剩web服务器了，一看8月到期，先就这样吧！


于是就一晃到了今天。。。

<!--more-->

其实迟迟不想迁服务器的原因有很多：

 - 上海的夏天太热
 - 服务器打字有延迟，不是很爽
 - acme自动签发证书爽歪歪，中途研究过docker+acme+nginx反代的[解决方案][1]和[ssldocker][2]，还是觉得不是很完美
 - 绝版服务器被`拆`，心里不爽啊！一定要用到最后一天！
 - 不想用nginx，各种配置真心烦，但是php又对nignx依赖比较强，总之就是各种纠结
 - 。。。

## 新发现

好吧，本文的主题是就是它 --- [caddy][3]，caddy是一个基于golang实现https服务器。而且刚好看到有[caddy+php的例子][4]，扫了一眼就决定是他了！一直拖到老高服务器到期的当天，终于勉强迁移完成。


## 一些配置

刚开始用caddy的时候发现其配置文件极其简洁，同时也是因为太简单了，所以在配置上走了一些弯路。

比如老高有很多二级域名需要配置https，这种情况肯定使用通配符域名为最优，但是官网文档中没有找到关于通配符的用法，好在有Google大法！这才发现原来早在2018年caddy就支持了通配符！答案就在文章[Caddy 0.10.12 Released with ACMEv2 and Wildcard Certificates][5]中。

下面贴一下老高关于`blog.phpgao.com`的配置，其中xxx是需要填进去的，具体使用方法请参考文档！

文档：[中][6]/[en][7]

```
(wildcard_cert) {
    tls {
        dns xxx
        wildcard
    }
}

xx.phpgao.com{
    import wildcard_cert # 这一句很重要
}

blog.phpgao.com {
    on startup php-fpm7
    fastcgi / 127.0.0.1:9000 php
    rewrite {
        to {path} {path}/ /index.php?{query}
    }
    gzip
    errors stdout
    root xxx
    import wildcard_cert
    git {
        repo xxx
        key xxx
        then xxx
    }
    log / xxx "{combined}"
    expires {
        match .css$ 1m
        match .js$ 1m
        match .ico$ 1m
        match .png$ 1m
        match .jpeg$ 1m
        match .jpg$ 1m
        match .gif$ 1m
        match .txt$ 1m
        match .eot$ 1m
        match .svg$ 1m
        match .ttf$ 1m
        match .woff$ 1m
        match .woff2$ 1m
        match .html$ 5i30s
    }
}
```

## 数据库

数据库其实用mysql 8.0没有必要，内存要求太高。想老高这种没人访问的站，在老高的各种测试后，用[yobasystems/alpine-mariadb][8]这个镜像内存占用很小，所以果断把web和数据库放到一起(docker-compose)，结果喜人！

```
16c106539a3b        caddy     24.23MiB / 497.5MiB
a2e3b3098729        mysql     68.03MiB / 497.5MiB
```


目前就是web和数据库跑在一台512MB内存的$20搬瓦工的服务器上，凑活用吧！




  [1]: https://github.com/jwilder/nginx-proxy
  [2]: https://ssldocker.com/
  [3]: https://caddyserver.com/
  [4]: https://github.com/abiosoft/caddy-docker/tree/master/php
  [5]: https://caddyserver.com/blog/caddy-0_10_12-released
  [6]: https://dengxiaolong.com/caddy/zh/
  [7]: https://caddyserver.com/docs
  [8]: https://hub.docker.com/r/yobasystems/alpine-mariadb