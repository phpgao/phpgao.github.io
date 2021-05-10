---
title: "Centos7-安装最新版-NGINX,PHP,Mysql,Mariadb"
categories: [ "服务器技术" ]
tags: [ "mysql","PHP","nginx","mariaDB" ]
draft: false
slug: "all_in_centos7"
date: "2017-02-24 06:40:00"
url: "all_in_centos7.html"
---

自己下载源代码编译很麻烦有没有？注意系统是**Centos7**！


<!--more-->

## NGINX

```bash
rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm

yum install -y nginx

systemctl start nginx
systemctl status nginx
systemctl enable nginx
```


## PHP

```bash
yum install -y yum-utils wget


wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
wget http://rpms.remirepo.net/enterprise/remi-release-7.rpm
rpm -Uvh remi-release-7.rpm epel-release-latest-7.noarch.rpm

# 激活remi
yum-config-manager --enable remi-php72

yum install -y \
php \
php-devel \
php-json \
php-mysqlnd \
php-fpm \
php-gd \
php-mbstring \
php-pdo \
php-pecl-swoole \
php-pgsql \
php-mcrypt \
php-soap \
php-pecl-redis \
php-pecl-memcache \
php-pecl-memcached \
php-pecl-xdebug


systemctl start php-fpm
systemctl status php-fpm
systemctl enable php-fpm
```


## Mariadb

### 5.X

```
# 如果先安装的10版本，需要执行 yum downgrade

vi /etc/yum.repos.d/MariaDB.repo
# add

# MariaDB 5.5 CentOS repository list - created 2017-03-01 02:14 UTC
# http://downloads.mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/5.5/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1

yum install MariaDB-server MariaDB-client
```

### 10.1

```bash
vi /etc/yum.repos.d/MariaDB.repo
# add
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.1/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1


yum install -y MariaDB-server MariaDB-client

systemctl start mariadb
systemctl status mariadb
systemctl enable mariadb
```

## Mysql

```bash
yum localinstall -y https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm

yum install -y mysql-community-server


systemctl start mysqld
systemctl status mysqld
systemctl enable mysqld
```


Reference:

 - https://blog.remirepo.net/pages/Config-en
 - https://blog.remirepo.net/post/2017/02/17/PHP-version-7.0.16-and-7.1.2
 - https://www.nginx.com/resources/wiki/start/topics/tutorials/install/
 - https://mariadb.com/kb/en/mariadb/yum/
 - https://downloads.mariadb.org/mariadb/repositories/#mirror=limestone&distro=CentOS&distro_release=centos7-amd64--centos7&version=10.1
 - https://blog.remirepo.net/post/2017/12/04/Install-PHP-7.2-on-CentOS-RHEL-or-Fedora