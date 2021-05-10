---
title: "nginx+php-上传大文件"
categories: [ "代码人生" ]
tags: [ "PHP","nginx" ]
draft: false
slug: "nginx_php_upload_big_file"
date: "2016-05-13 03:57:00"
url: "nginx_php_upload_big_file.html"
---

有时候确实需要上传一些比较大的文件，老高的服务器是nginx+php，二者都需要配置。


<!--more-->


## nginx

老高刚开始只是配置了PHP，每次传输大文件总是报错

> 413 “Request Entity Too Large”

原因是nginx限制了客户端上传文件大小，修改的方法很简单，修改`client_max_body_size`为需要的值即可！

```
# 搜索client_max_body_size，如果你的配置文件很少，就不需要搜索，直接打开配置文件的server段
grep -nr client_max_body_size .


# 修改为200M
client_max_body_size 200M;

# 保存，重启nginx
nginx -s reload
```



## PHP

PHP需要修改的地方比较多，主要是**post_max_size**和**upload_max_filesize**两个地方，还有脚本执行时间，这一点老高建议在运行时在代码中修改，方法是`set_time_limit(600);`，还有memory_limit要改，不过目前没改也没出错！

```Dockerfile
RUN sed 's/post_max_size = 8M/post_max_size = 20000M/' -i /usr/local/etc/php/php.ini \
      && sed 's/upload_max_filesize = 2M/upload_max_filesize = 20000M/' -i /usr/local/etc/php/php.ini \
      && sed 's/^;\s*max_input_vars = 1000/max_input_vars = 10000/' -i /usr/local/etc/php/php.ini 
```