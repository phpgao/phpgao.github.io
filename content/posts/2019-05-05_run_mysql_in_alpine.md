---
title: "在alpine中运行mysql"
categories: [ "服务器技术" ]
tags: [ "mysql","docker" ]
draft: false
slug: "run_mysql_in_alpine"
date: "2019-05-05 15:24:00"
url: "run_mysql_in_alpine.html"
---

搬瓦工的1G内存，想要跑一个MySQL服务，动辄500M内存就没了，剩下一半的内存让我感觉很虚，于是想把是不是把MySQL塞进alpine镜像中看看，结果一搜，果然已经有大神这样做了，使用的是MariaDB，测试下来，同样跑一个博客只需要50M内存就OK，简直完美！

[yobasystems/alpine-mariadb][1]

<!--more-->


将下面的代码中，密码和账户信息修改为你想要的，然后保存为`docker-compose.yml`，最后运行命令`docker-compose up -d`启动mysql服务。

```
version: '3'

services:

  db:
    image: yobasystems/alpine-mariadb:amd64
    command: --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: yourdb
      MYSQL_USER: yourname
      MYSQL_PASSWORD: yourpassword
    ports:
      - "33060:3306"
    volumes:
      - ./data/mysql:/var/lib/mysql
```


接下来就是连接数据库，把旧的数据库内容dump到新的数据库中，改好web配置文件就大功告成了！


  [1]: https://hub.docker.com/r/yobasystems/alpine-mariadb