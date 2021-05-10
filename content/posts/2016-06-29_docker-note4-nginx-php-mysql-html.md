---
title: "docker使用笔记IV----使用docker的风格分离Nginx-PHP--Mysql"
categories: [ "服务器技术" ]
tags: [ "mysql","PHP","nginx","docker" ]
draft: false
slug: "docker-note4-nginx-php-mysql-html"
date: "2016-06-29 04:45:00"
url: "docker-note4-nginx-php-mysql-html.html"
---

今天老高动手实践一下分离nginx和php，还有mysql。即三个image: g-nginx,g-php,g-mysql。

![php7][1]

<!--more-->


## 准备工作

### 镜像文件

- [docker_hub - nginx][2]
- [docker_hub - php][3]
- [docker_hub - mariadb][4]
- [docker_hub - mysql][5]

```bash
docker pull nginx
docker pull php:7-fpm
docker pull mysql

或者用Daocloud加速

dao pull nginx
dao pull php:7-fpm
```

### 一些文件

/tmp/host.conf，他就是虚拟主机的配置文件

```nginx
server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/log/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.php index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    location ~ \.php$ {
        root   /usr/share/nginx/html;
        fastcgi_pass   php-fpm:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

/tmp/html/index.php，他是web根目录的文件。

```php
<?php
phpinfo();
```

/tmp/html/index.php，他是测试mysql的文件。

```php
<?php

$hasMySQL = false;
$hasMySQLi = false;
$withMySQLnd = false;
$sentence = '';
if (function_exists('mysql_connect')) {
    $hasMySQL = true;
    $sentence.= "(Deprecated) MySQL is installed ";
} else
    $sentence.= "(Deprecated) MySQL is not installed ";

if (function_exists('mysqli_connect')) {
    $hasMySQLi = true;
    $sentence.= "and the new (improved) MySQL is installed . ";
} else
    $sentence.= "and the new (improved) MySQL is not installed . ";

$sentence.="\r\n";

if (function_exists('mysqli_fetch_all')) {
    $withMySQLnd = true;
    $sentence.= "This server is using MySQLnd as the driver.";
} else
    $sentence.= "This server is using libmysqlclient as the driver.";

$sentence.="\r\n";


echo $sentence;


$pdo = new PDO('mysql:host=mysql;dbname=mysql', 'root', '123456');
if (strpos($pdo->getAttribute(PDO::ATTR_CLIENT_VERSION), 'mysqlnd') !== false) {
    echo 'PDO MySQLnd enabled!';
}

?>
```

### 配置PHP

默认PHP的镜像是不启用mysql等功能的，需要手动启用，所以我们使用Dockerfile创建一个新的PHP镜像

```dockerfile
FROM php:7-fpm
MAINTAINER  LAOGAO <endoffight#gmail.com>

RUN cp /usr/src/php/php.ini-production /usr/local/etc/php/php.ini
RUN docker-php-ext-install pdo_mysql mysqli
# RUN sed 's/^;extension=php_pdo_mysql\.dll/extension=pdo_mysql.so/' -i /usr/local/etc/php/php.ini
# RUN sed 's/^;extension=php_mysqli\.dll/extension=mysqli.so/' -i /usr/local/etc/php/php.ini
```

将上线的内容保存到文件Dockerfile，然后执行`docker build -t php:laogao .`即可完成镜像构建！

## docker命令

docker的命令中有一个link选项，可以直接让两个容器联通，并制定一个host，用于容器内的网络连接，所以以上的配置中我们将原本nginx的conf中的127.0.0.1:9000替换为php-fpm:9000，php-fpm就是设定的主机名。

--volumes-from意思是将某个容器的挂载同步，其实就是少打几个-v而已！

mysql与php是强链接，php和nginx是强链接，于是我们可以先运行mysql，然后运行php与mysql联通，最后把nginx与php联通！


```bash
docker run -d --name g-mysql -e MYSQL_ROOT_PASSWORD=123456 mysql:latest

docker run --link=g-mysql:mysql -v /tmp/html:/usr/share/nginx/html -d --name g-php php:laogao

docker run -d -v /tmp/host.conf:/etc/nginx/conf.d/default.conf --volumes-from=g-php --link=g-php:php-fpm -p 80:80 --name=g-nginx nginx
```


访问mysql.php文件，得到下面的图片：

![mysql][6]

Reference:

[phpinfo says MySQLnd is the active driver or am i confused][7]


  [1]: https://blog.phpgao.com/usr/uploads/2016/06/20358963.png
  [2]: https://hub.docker.com/_/nginx/
  [3]: https://hub.docker.com/_/php/
  [4]: https://hub.docker.com/_/mariadb/
  [5]: https://hub.docker.com/_/mysql/
  [6]: https://blog.phpgao.com/usr/uploads/2016/06/3933763571.png
  [7]: http://stackoverflow.com/questions/21889865/phpinfo-says-mysqlnd-is-the-active-driver-or-am-i-confused