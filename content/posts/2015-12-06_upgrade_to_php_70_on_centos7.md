---
title: "将你的PHP程序升级到PHP7.0"
categories: [ "服务器技术" ]
tags: [ "PHP","centos" ]
draft: false
slug: "upgrade_to_php_70_on_centos7"
date: "2015-12-06 13:20:00"
url: "upgrade_to_php_70_on_centos7.html"
---

![PHP][1]

(以上图片来自互联网)

这两天抽空把系统升到7.0，把PHP也升到7.0，随便记一点笔记吧！

<!--more-->


> 操作环境centos7-minimal，其他的系统可能不适用

## 准备工作

第一步备份配置

 - nginx的所有配置
 - PHP的所有配置
 - 网站的备份脚本
 - 网站的全备份

```bash
export LC_ALL=en_US.UTF-8
yum -y update
yum -y install zsh git net-tools vim
chsh -s /bin/zsh
/bin/zsh
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
timedatectl set-timezone Asia/Shanghai
```

## 数据库 mariadb(mysql)

```
yum -y install mariadb mariadb-server
systemctl start mariadb.service
systemctl enable mariadb.service

# 初始化mariadb
mysql_secure_installation

# 新建数据库
CREATE DATABASE IF NOT EXISTS db DEFAULT CHARSET utf8 COLLATE utf8_general_ci;

# 新用户并赋予权限
GRANT ALL PRIVILEGES ON db.* TO 'phpgao'@'localhost' IDENTIFIED BY 'phpgao';
FLUSH PRIVILEGES;

# 改密码
SET PASSWORD FOR 'phpgao'@'localhost' = PASSWORD('phpgao');

# 安全配置
vim /etc/my.cnf
# add
# [mysqld]
# bind-address = 127.0.0.1
```


# nginx

nginx如果需要某些高级特性，比如lua支持等，可以自己下载源码编译，这里老高图快，直接用官方源yum安装了

```
rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm

yum -y install nginx

systemctl restart nginx.service
systemctl enable nginx.service

# firewall(optional)

firewall-cmd --permanent --zone=public --add-service=http
firewall-cmd --reload

# curl localhost
```


## php7.0的安装与配置

php70有两个源可以选择，一个是[webtatic][2]提供的，也就是php70w，那个w指得就是**webtatic**，另一个可以使用[remi源][3]。

### webtatic方式

```bash
rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm

yum -y install php70w php70w-opcache php70w-fpm php70w-mysql php70w-pdo php70w-pgsql php70w-xml php70w-mbstring php70w-mcrypt php70w-gd

# or goto http://www.tecmint.com/install-and-compile-php-7-on-centos-7-and-debian-8/
# goto http://my.oschina.net/dingdayu/blog/513359?fromerr=Ob30V98l

vim /etc/php.ini

# 防止漏洞
# add
# cgi.fix_pathinfo=0

sed -i 's/user = apache/user = nginx/' /etc/php-fpm.d/www.conf
sed -i 's/group = apache/group = nginx/' /etc/php-fpm.d/www.conf


systemctl enable php-fpm
systemctl start php-fpm
```

### remi方式

```
wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
wget http://rpms.remirepo.net/enterprise/remi-release-7.rpm
rpm -Uvh remi-release-7.rpm epel-release-latest-7.noarch.rpm
 
yum --enablerepo=remi update remi-release
yum --enablerepo=remi-php70

# 搜索PHP
yum search php70

# 各取所需吧
yum install php70 php70-php-devel php70-php-fpm php70-php-mbstring php70-php-mcrypt php70-php-mysqlnd php70-php-gd php70-php-opcache php70-php-pdo php70-php-json php70-php-xml php70-php-pecl-xdebug php70-php-pecl-zip


systemctl start  php70-php-fpm.service
systemctl enable  php70-php-fpm.service


sed -i 's/user = apache/user = nginx/' /etc/opt/remi/php70/php-fpm.d/www.conf
sed -i 's/group = apache/group = nginx/' /etc/opt/remi/php70/php-fpm.d/www.conf


# PHP的执行文件不再是php，而是被命名为php70，所以需要做一个软连接

# ➜  /home  ll /usr/bin/|grep php
# lrwxrwxrwx   1 root root     14 Dec  8 23:58 php -> /usr/bin/php70
# lrwxrwxrwx   1 root root     32 Dec  8 23:49 php70 -> /opt/remi/php70/root/usr/bin/php
# lrwxrwxrwx   1 root root     36 Dec  8 23:49 php70-cgi -> /opt/remi/php70/root/usr/bin/php-cgi
# lrwxrwxrwx   1 root root     33 Dec  8 23:57 php70-pear -> /opt/remi/php70/root/usr/bin/pear
# lrwxrwxrwx   1 root root     38 Dec  8 23:49 php70-phar -> /opt/remi/php70/root/usr/bin/phar.phar

# 其他以此类推
ln -s /usr/bin/php70 /usr/bin/php

# PHP的配置文件位于
ls /etc/opt/remi/php70/|grep -E '\.conf|\.ini'
# pear.conf
# php-fpm.conf
# php.ini
# 还有php-fpm.d/php.d目录下的单独配置
# 需要注意的是session的文件路径需要写入权限，在php-fpm.d/www.conf下定义，默认在 /var/opt/remi/php70/lib/php/session/ 下，opcache、wsdlcache同理。

php -v
# PHP 7.0.0 (cli) (built: Dec  1 2015 16:06:41) ( NTS )
# Copyright (c) 1997-2015 The PHP Group
# Zend Engine v3.0.0, Copyright (c) 1998-2015 Zend Technologies
#    with Zend OPcache v7.0.6-dev, Copyright (c) 1999-2015, by Zend # # # Technologies
#    with Xdebug v2.4.0RC2, Copyright (c) 2002-2015, by Derick Rethans
```


## nginx 与 php-fpm

```
mkdir -p /var/log/nginx/phpgao

# 以后将所有的虚拟主机配置放到这里
mkdir /etc/nginx/sites-available
# 将所有运行中的站点放在这里
mkdir /etc/nginx/sites-enabled

vim /etc/nginx/nginx.conf
```

### 注意下面的配置

nginx与php的配置就不多说了，

```
include /etc/nginx/sites-enabled/*.conf;

    location ~ .*\.(php|php5){
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        #fastcgi_split_path_info ^(.+?.php)(/.*)$;
        fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include    fastcgi_params;
    }
```

最后重启服务

```
# 检查配置
nginx -t
# 重启
systemctl reload nginx.service
# 或者
nginx -s reload
```

## 计划任务

```
yum -y install crontabs
systemctl start crond
systemctl enable crond
```

## 检查服务

```
systemctl list-unit-files --type=service

systemctl list-unit-files --type=service | grep enabled
systemctl list-unit-files --type=service | grep disabled
```

## 防火墙（危险操作）

```
# 检查端口
netstat -atpln
# 结果应该是监听 0.0.0.0地址的只有nginx与sshd

yum -y install firewalld
systemctl start firewalld

firewall-cmd --state
# running

# 查看所有zone
firewall-cmd --list-all
# 查看所有指定zone的所有规则
firewall-cmd --zone=public --list-all
# 查看指定zone的服务
firewall-cmd --zone=public --list-services
# 查看所有支持的zone
firewall-cmd --get-zones
# 查看全部启用的区域的特性
firewall-cmd --list-all-zones
# 查看默认zone
firewall-cmd --get-default-zone
# 查看支持的service
firewall-cmd --get-service

# 将sshd端口和http服务加入规则
firewall-cmd --permanent --zone=public --add-port=yoursshd/tcp
firewall-cmd --permanent --zone=public --add-service=http
重启
firewall-cmd --reload

# 删除指定服务
firewall-cmd --zone=public --remove-service=http

# 删除指定端口
firewall-cmd --zone=public --remove-port=22-23/tcp

```


参考：

 1. [Installing and Configuring Nginx + PHP-FPM + MariaDB on CentOS 7][4]
 1. [Nginx + PHP-FPM + MariaDB on CentOS 7 / RHEL 7][5]
 1. [How To Install Linux, Nginx, MySQL, PHP (LEMP) stack On CentOS 7][6]
 1. [server-world][7]
 1. [Upgrading PHP to PHP7.0 in a CentOS server with Vesta CP][8]
 1. [CentOS7下Firewall防火墙配置用法详解][9]


  [1]: https://blog.phpgao.com/usr/uploads/2015/12/515318547.png
  [2]: https://webtatic.com/packages/php70/
  [3]: http://rpms.famillecollet.com
  [4]: https://www.simplehelix.com/blog/uncategorized/installing-and-configuring-nginx-php-fpm-mariadb-on-centos-7/
  [5]: http://www.itzgeek.com/how-tos/linux/centos-how-tos/nginx-php-fpm-mariadb-on-centos-7-rhel-7.html
  [6]: https://www.digitalocean.com/community/tutorials/how-to-install-linux-nginx-mysql-php-lemp-stack-on-centos-7
  [7]: http://www.server-world.info/en/note?os=CentOS_7
  [8]: http://hasin.me/2015/12/06/upgrading-php-to-php7-0-in-a-centos-server-with-vesta-cp/
  [9]: http://www.centoscn.com/CentOS/Intermediate/2015/0313/4879.html