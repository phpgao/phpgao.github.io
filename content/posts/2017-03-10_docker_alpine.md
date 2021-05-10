---
title: "体验Alpine-Linux-Docker-Image"
categories: [ "服务器技术" ]
tags: [ "docker","alpine" ]
draft: false
slug: "docker_alpine"
date: "2017-03-10 05:23:00"
url: "docker_alpine.html"
---

如果你已经研究过老高的[为樱花打造的KCPTUN][1]，此docker镜像就是基于Alpine最新版，当时只是简单的使用了一下，这次老高想深入研究一下docker的Alpine。

![alpine_log][2]

> Alpine做名词将意思是高山植物，估计想表达简单，耐用的意思吧。

<!--more-->


## 官网

 - [Alpine Linux Docker Image][3]
 - [alpine - Docker Store][4]
 - 目前最新tag为 3.5

## 特点

通过阅读[官方说明][5]，简单的总结一下这个镜像

 - 集成[musl libc][6]，提供完整又强大的功能
 - 集成busy_box，胜于busy_box
 - 镜像小，节省传输时间，和docker简直是天作之合！

## 获取

![alpine镜像][7]

```bash
docker pull alpine
docker images
# 由图看到只有3.99 MB大小，基于alpine而build出来的endoffight/shadowsocks-kcp也只有14.5 MB
```

## 运行

由于启用一个镜像必须有一个进程，所以我们运行ash(Bash in alpine)

![process list][8]

```bash
# 启动一个容器
docker run --name=al -itd alpine ash

# 进入系统
docker exec -it al ash
```

### APK包管理命令

```bash
# 更新源
apk add --update

# 缓存的index文件保存在/var/cache/apk/文件夹中
ls -al /var/cache/apk/

# 搜索nginx，目前nginx最新是1.11，1.10.3是最新的稳定版，看来库里的软件还是很新的
apk search nginx

nginx-1.10.3-r0
...

# libsodium 也有，这不是就shadowsocks而生的么?
apk search libsodium
libsodium-1.0.11-r0
libsodium-dev-1.0.11-r0

# 安装命令
apk add --update --no-cache python openrc

# 组安装，--virtual web_tools表示后面的程序都会被归到web_tools组，最后删除的时候可以一起被删
apk --update add --virtual web_tools nginx php7-fpm

# 删除
apk del python

# 列出已安装的程序
apk info|sort
apk -vv info|sort
```

### 服务管理

```bash
# 非docker环境的服务管理
rc-service nginx start
rc-service nginx stop
rc-service nginx php
```

## 实战

下面进入实战环节，让我们配置一个最简单的Nginx+PHP运行环境吧！

### 新建

```bash
docker run --name=web -itd alpine ash

docker exec -it web ash
```

### 添加软件

```bash
apk add --no-cache --virtual web_tools nginx php7-fpm supervisor
```

### 配置supervisor

```bash
# supervisor 前台运行
sed -i 's/\;nodaemon=false/nodaemon=true/g' /etc/supervisord.conf

vi /etc/supervisord.conf
# 添加如下内容

[program:php-fpm]
command=php-fpm7 -F
stdout_logfile=/var/log/php7/php.log
# /dev/stdout
stdout_logfile_maxbytes=1MB
stderr_logfile=/var/log/php7/php_error.log
# /dev/stderr
stderr_logfile_maxbytes=1MB

[program:nginx]
command=nginx -g 'daemon off;'
stdout_logfile=/var/log/nginx/nginx.log
stdout_logfile_maxbytes=1MB
stderr_logfile=/var/log/nginx/nginx_error.log
stderr_logfile_maxbytes=1MB
```

### 配置nginx

```bash
true > /etc/nginx/conf.d/default.conf
vi /etc/nginx/conf.d/default.conf

# add
server {
        listen                  80;
        root                    /www;
        index                   index.html index.htm index.php;
        server_name             localhost;
        client_max_body_size    32m;
        error_page              500 502 503 504  /50x.html;
        location = /50x.html {
              root              /var/lib/nginx/html;
        }
        location ~ \.php$ {
              fastcgi_pass      127.0.0.1:9000;
              fastcgi_index     index.php;
              include           fastcgi.conf;
        }
    }
```

### 新建PHP文件

```bash
vi /www/index.php

# add
<?php
echo "Hello world!\n";

```

### 运行

```bash
/usr/bin/supervisord
```

### 查看

外部新启一个终端查看

```bash
docker exec -it web ash
curl lcoalhost
# get
# Hello world!
```

完


  [1]: https://blog.phpgao.com/kcptun_on_sakura.html
  [2]: https://blog.phpgao.com/usr/uploads/2017/03/3782981747.png
  [3]: http://gliderlabs.viewdocs.io/docker-alpine/
  [4]: https://store.docker.com/images/e7e5dbc4-2103-4b7c-9409-b0ca32ce3d83
  [5]: http://gliderlabs.viewdocs.io/docker-alpine/about/
  [6]: http://www.etalabs.net/compare_libcs.html
  [7]: https://blog.phpgao.com/usr/uploads/2017/03/4196534547.png
  [8]: https://blog.phpgao.com/usr/uploads/2017/03/2007223611.png