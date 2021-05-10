---
title: "centos6新系统初始化脚本"
categories: [ "服务器技术" ]
tags: [ "mysql","PHP","centos","gcc","nginx","initial","nodejs","vpsmate","mariaDB" ]
draft: false
slug: "new_cents_shell"
date: "2015-04-27 03:57:00"
url: "new_cents_shell.html"
---

最近装机次数有点多，本来想写到这里的[centos6 优化脚本与安全脚本][1]，结果一想还是新开一个帖子吧。

centos源设置可以参考[Centos源设置][2]，老高推荐安装阿里源。

新机子先运行上面的脚本，再运行这个脚本

<!--more-->

## 初始化脚本

```bash
yum update -y

yum groupinstall "development tools" -y
yum install zlib-devel bzip2-devel pcre-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel -y
yum install crontabs ntpdate vim python-devel zsh -y


chkconfig sendmail off
chsh -s /bin/zsh

yum install python-setuptools -y
easy_install pip
pip install virtualenv
curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh

ntpdate us.pool.ntp.org

rm -f /etc/localtime
cp -f /usr/share/zoneinfo/Asia/Shanghai /etc/localtime


```

以下是一些免编译的rpm包，也就是由其他人编译好的软件包，用于迅速搭建服务器环境，如果你希望更高程度的自定义，请自行编译。

----------


## vpsmate

```bash
wget http://www.vpsmate.org/tools/install.py
python install.py
```
## nginx

```bash
# http://nginx.org/packages/centos/
# http://nginx.org/packages/centos/6/x86_64/
vi /etc/yum.repos.d/nginx.repo

[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/6/x86_64/
gpgcheck=0
enabled=1

yum update
yum install nginx
```

## nodejs

```bash
https://github.com/creationix/nvm

or

wget http://nodejs.org/dist/node-latest.tar.gz
tar zxf node-latest.tar.gz
cd node-latest
./configure
make && make install
```

## mariaDB

```bash
vi /etc/yum.repos.d/MariaDB.repo

# 下面的内容可以由 https://downloads.mariadb.org/mariadb/repositories/ 得到

# MariaDB 5.5 CentOS repository list - created 2015-04-27 05:46 UTC
# http://mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/5.5/centos6-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1

yum update -y
yum install MariaDB-client MariaDB-server MariaDB-devel -y
# 集群工具
yum install MariaDB-Galera-server MariaDB-client galera -y
service mysql start
mysql_secure_installation
```

## Mysql

```bash
cd /tmp
wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
yum localinstall mysql-community-release-el6-5.noarch.rpm
yum update -y

yum repolist enabled | grep "mysql.*-community.*"
yum install mysql-community-server

ll /usr/bin |grep mysql

mysql_secure_installation

```

## php

```bash
# CentOS/RHEL 7.x:

rpm -Uvh https://mirror.webtatic.com/yum/el7/epel-release.rpm
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm

#CentOS/RHEL 6.x:

rpm -Uvh https://mirror.webtatic.com/yum/el6/latest.rpm


# 安装php

yum install php56w php56w-opcache php56w-fpm php56w-mysqlnd php56w-pdo php56w-mssql php56w-pgsql php56w-mcrypt php56w-imap php56w-gd php56w-xml php56w-tidy php56w-soap php56w-pecl-xdebug php56w-pear php56w-pecl-memcache

# 运行php-fpm

service php-fpm restart
```

## php与nginx

此段配置不好容易发生404

```nginx
server {
    listen       80;
    # server_name  localhost;
    server_name xxxxx;

    #charset koi8-r;
    access_log  /var/log/nginx/log/host.access.log  main;


    root   /var/www/html;
    index  index.php index.html index.htm;

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    location ~ \.php$ {
        include        fastcgi_params;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    }
}
```


## gcc

在centos上升级gcc费力费时，直接用编译好的gcc是条捷径！

在此我们借助**Red Hat Developer Toolset x**

下面的脚本分4.7 & 4.8

```bash
# Tru Huynh of centos.org has built the redhat developer toolset 1.1, for centos and it contains gcc 4.7.2

cd /etc/yum.repos.d
wget http://people.centos.org/tru/devtools-1.1/devtools-1.1.repo 
yum --enablerepo=testing-1.1-devtools-6 install devtoolset-1.1-gcc devtoolset-1.1-gcc-c++

# This will install it most likely into /opt/centos/devtoolset-1.1/root/usr/bin/

# Then you can tell your compile process to use the gcc 4.7 instead of 4.4 with the CC variable

export CC=/opt/centos/devtoolset-1.1/root/usr/bin/gcc  
export CPP=/opt/centos/devtoolset-1.1/root/usr/bin/cpp
export CXX=/opt/centos/devtoolset-1.1/root/usr/bin/c++
```

4.8

Known issues:

- unsigned packages
-CentOS-6 devtoolset-2 needs devtoolset-2-ide which contains the whole Eclipse stack, but does not build yet
- CentOS-6 all the maven related file are not built either

```bash
wget http://people.centos.org/tru/devtools-2/devtools-2.repo -O /etc/yum.repos.d/devtools-2.repo
yum install devtoolset-2-gcc devtoolset-2-binutils
yum install devtoolset-2-gcc-gfortran
```




Rerference:

http://dev.mysql.com/doc/mysql-repo-excerpt/5.6/en/linux-installation-yum-repo.html
http://superuser.com/questions/381160/how-to-install-gcc-4-7-x-4-8-x-on-centos
https://webtatic.com/packages/php56/


  [1]: https://blog.phpgao.com/centos_optimize.html
  [2]: https://blog.phpgao.com/repo_for_centos.html