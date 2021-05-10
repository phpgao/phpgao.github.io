---
title: "V2上看到一个很棒的网站-nginxconfig.io"
categories: [ "服务器技术" ]
tags: [ "nginx" ]
draft: false
slug: "nginxconfig"
date: "2018-12-04 04:31:00"
url: "nginxconfig.html"
---

nginx的配置老高的博客里有很多篇文章都有讲过，但是总是觉得不是很标准，前些天在V2看到了这个网站，浏览后发现这个工具简直神器！


<!--more-->


[nginxconfig.io][1]

虽然是英文界面，但是功能做的很直观，生成的nginx格式规范，简单的改一下就是`满分作文`。

不说了，老高赶紧对照着改conf去了！

经过老高半天的折腾，目前conf的结构已经很清晰了！果然用了工具就是不一样啊！

```
.
|-- fastcgi_params
|-- nginx.conf
|-- nginxconfig.io
|   |-- agent_deny.conf
|   |-- general.conf
|   |-- general_with_block.conf
|   |-- letsencrypt.conf
|   |-- php_fastcgi.conf
|   |-- ssl_cert_phpgao.com.conf
|   `-- wordpress.conf
|-- scgi_params
|-- sites-enabled
|   |-- blog.phpgao.com.conf
|   |-- phpgao.com.conf
|   `-- www.phpgao.com.conf
|-- uwsgi_params
`-- win-utf

```


  [1]: https://nginxconfig.io/