---
title: "docker使用笔记III----docker的快速安装"
categories: [ "服务器技术" ]
tags: [ "docker" ]
draft: false
slug: "docker-note3-html"
date: "2016-04-10 14:06:00"
url: "docker-note3-html.html"
---

docker快速安装笔记


<!--more-->

## centos安装docker

ubuntu 参考[Install Docker][1]
其他系统可以参考[Install Docker Engine][2]

### 更新yum源

```bash
$ sudo tee /etc/yum.repos.d/docker.repo <<-'EOF'
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/$releasever/
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg
EOF
```

### 安装docker

```bash
$ sudo yum install docker-engine -y
```

### 优化加速

阿里云用起来还不错，Daocloud也可以。

[使用加速器将会提升您在国内获取Docker官方镜像的速度！][3] 

## 获取镜像

[nginx][4]
[php][5]
[mysql][6]

```
docker pull nginx:1
docker pull php:7-fpm
docker pull mysql:5
```

## 运行

```
# mysql
docker run -e MYSQL_ROOT_PASSWORD=laogao -p 3306:3306 -d mysql:5
# php
docker run -p 127.0.0.1:9000:9000 -d php:7-fpm
# nginx
docker run -d -p 8080:80 nginx:1
```

## 优化

这样我们启动了三个docker容器，不过到现在他们是没法一起工作的！

> ps 老高在这里报错，`address family not supported by protocol`，原因是老高的虚拟环境禁用了IPV6，而php的docker配置中绑定端口使用了`listen = [::]:9000`，如果你也遇到这个问题，就需要将它改成`listen = 0.0.0.0:9000`即可！


首先，nginx在接到动态请求后会发送请求信息给php-fpm，之后php-fpm处理请求后返回给nginx，
php在执行的时候可能会用到mysql数据库，他们之间可以使用文件交换数据，也可以使用网络交换数据。

在弄清楚了他们之间的关系后，我们尝试着修改上面的命令，已达到预期目标。

### 生成配置文件和测试文件

```
# 保存到/tmp/conf.d/default.conf
server {
    listen  80;
    root /usr/share/nginx/html;
    index index.php index.html;
    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

    location ~ \.php$ {
        try_files $uri =404;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root/$fastcgi_script_name;
        fastcgi_pass php-fpm:9000;
    }
}

# 保存到/tmp/html/index.php
<?php
phpinfo();

```

### 运行容器

```
# --name 表示将这个container命名

docker run -v /tmp/html:/usr/share/nginx/html --name php-fpm-container -d php:7-fpm

# --volumes-from 同时挂载php-fpm挂载的路径
# --link 表示将php-fpm-container，命名为php-fpm主机
# --link 会在nginx:1的/etc/hosts文件添加一条类似 172.11.11.11 php-fpm的条目，因为IP只有在运行时才确定，这个配置呼应了/tmp/conf.d/default.conf中的/tmp/conf.d/default.conf一行

docker run -d  -p 8080:80 -v /tmp/conf.d:/etc/nginx/conf.d --volumes-from php-fpm-container --link php-fpm-container:php-fpm nginx:1
```



Reference:

[PHP-FPM + nginx version #54][7]


  [1]: https://docs.docker.com/linux/step_one/
  [2]: https://docs.docker.com/engine/installation/
  [3]: https://cr.console.aliyun.com/#/docker/booster
  [4]: https://hub.docker.com/_/nginx/
  [5]: https://hub.docker.com/_/php/
  [6]: https://hub.docker.com/_/mysql/
  [7]: https://github.com/docker-library/php/issues/54