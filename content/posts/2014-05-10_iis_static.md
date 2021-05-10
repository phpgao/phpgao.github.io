---
title: "IIS下wordpress伪静态的配置"
categories: [ "性能优化" ]
tags: [ "IIS" ]
draft: false
slug: "iis_static"
date: "2014-05-10 01:25:00"
url: "iis_static.html"
---

IIS服务器下伪静态的配置需要参照Apache.htaccess文件。 首先要使IIS支持URL Rewrite，所以必须让服务器加载ISAPI\_Rewrite！ 什么是ISAPI\_Rewrite？ ISAPI 翻译为：英特网服务器应用编程接口。

最常用的用途是URL Rewrite，辅助动态网站（主要为PHP脚本的程序）实现伪静态功能。 

IIS支持ISAPI后就剩下两个步骤了： 

1.建立一个httpd.ini文件，文件内容如下：

    [ISAPI_Rewrite]
    # 3600 = 1 hour
    CacheClockRate 3600
    RepeatLimit 32
    # Protect httpd.ini and httpd.parse.errors files
    # from accessing through HTTP
    # Rules to ensure that normal content gets through
    RewriteRule /sitemap.xml /sitemap.xml [L]
    RewriteRule /favicon.ico /favicon.ico [L]
    RewriteRule /sitemap_baidu.xml /sitemap_baidu.xml [L]
    RewriteRule /sitemap.html /sitemap.html [L]
    RewriteRule /archivers/(.*) /archivers/$1 [L]
    # For file-based wordpress content (i.e. theme), admin, etc.
    RewriteRule /wp-(.*) /wp-$1 [L]
    # For normal wordpress content, via index.php
    RewriteRule ^/$ /index.php [L]
    RewriteRule /(.*) /index.php/$1 [L]

2.wordpress后台固定链接设置为：   

    /%post_id%.html

搞定，撒花吧~