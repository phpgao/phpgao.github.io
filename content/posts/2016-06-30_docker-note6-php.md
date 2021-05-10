---
title: "docker使用笔记VI----PHP"
categories: [ "服务器技术" ]
tags: [ "PHP","docker" ]
draft: false
slug: "docker-note6-php"
date: "2016-06-30 10:07:00"
url: "docker-note6-php.html"
---

在docker里构建一个PHP环境，很简单吗？我可不觉得。。。赶紧记录一下！


<!--more-->


```
from php:7-fpm

RUN cp /etc/apt/sources.list /etc/apt/sources.list.bak \
    && cp /usr/src/php/php.ini-development /usr/local/etc/php/php.ini

RUN { \
        echo 'deb http://mirrors.163.com/debian/ jessie main non-free contrib'; \
        echo 'deb http://mirrors.163.com/debian/ jessie-updates main non-free contrib'; \
        echo 'deb http://mirrors.163.com/debian/ jessie-backports main non-free contrib'; \
        echo 'deb-src http://mirrors.163.com/debian/ jessie main non-free contrib'; \
        echo 'deb-src http://mirrors.163.com/debian/ jessie-updates main non-free contrib'; \
        echo 'deb-src http://mirrors.163.com/debian/ jessie-backports main non-free contrib'; \
        echo 'deb http://mirrors.163.com/debian-security/ jessie/updates main non-free contrib'; \
        echo 'deb-src http://mirrors.163.com/debian-security/ jessie/updates main non-free contrib'; \
        } | tee /etc/apt/sources.list

RUN echo "Asia/Shanghai" > /etc/timezone && \
        dpkg-reconfigure -f noninteractive tzdata

# Fix docker-php-ext-install script error
RUN sed -i 's/docker-php-\(ext-$ext.ini\)/\1/' /usr/local/bin/docker-php-ext-install

# Install other needed extensions
# ubuntu 

# RUN apt-get update && apt-get install -y libfreetype6 libjpeg8-dev libjpeg8 libmcrypt4 libpng12-0 sendmail --no-install-recommends && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y libfreetype6 libjpeg62-turbo-dev libjpeg62-turbo libmcrypt4 libpng12-0 sendmail --no-install-recommends && rm -rf /var/lib/apt/lists/*

RUN buildDeps=" \
		libfreetype6-dev \
		libjpeg-dev \
		libldap2-dev \
		libmcrypt-dev \
		libpng12-dev \
		zlib1g-dev \
		libxpm4 \
		libltdl7 \
		libltdl-dev \
		libgd-dev \
		libgd3 \
	"; \
	set -x \
	&& apt-get update && apt-get install -y $buildDeps --no-install-recommends && rm -rf /var/lib/apt/lists/* \
	&& docker-php-ext-configure gd --enable-gd-native-ttf --with-jpeg-dir=/usr/lib/x86_64-linux-gnu --with-png-dir=/usr/lib/x86_64-linux-gnu --with-freetype-dir=/usr/lib/x86_64-linux-gnu \
	&& docker-php-ext-install gd \
	&& docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu \
	&& docker-php-ext-install ldap \
	&& docker-php-ext-install mbstring \
	&& docker-php-ext-install mcrypt \
	&& docker-php-ext-install iconv \
	&& docker-php-ext-install zip \
	&& docker-php-ext-install pdo_mysql mysqli \
	&& apt-get purge -y --auto-remove $buildDeps \
	&& cd /usr/src/php \
	&& make clean

```


接下来就可以参考 [docker使用笔记IV -- 使用docker的风格分离Nginx PHP Mysql][1]。


  [1]: https://blog.phpgao.com/docker-note4-nginx-php-mysql-html.html