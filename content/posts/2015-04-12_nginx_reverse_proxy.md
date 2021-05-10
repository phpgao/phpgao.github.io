---
title: "使用nginx做反向代理"
categories: [ "服务器技术" ]
tags: [ "google","nginx","proxy","openwrt" ]
draft: false
slug: "nginx_reverse_proxy"
date: "2015-04-12 04:21:00"
url: "nginx_reverse_proxy.html"
---

很多同学喜欢用nginx做反向代理访问某些网站，原因大家都懂的，今天老高记录一下如何使用nginx做反向代理以及如何配置和优化nginx的反向代理。


<!--more-->


## 准备工作

首先，你需要一个稳定的国外的便宜的VPS，老高推荐搬瓦工，当然你有DO等更快的更贵的VPS就例外了，老高的博客就建立在这个VPS上的。

如何获取你的VPS，请移步 [30元一年，打造自己的翻墙VPS][1]

假设你已经有了自己的VPS，我们以后假定安装了centos。那么安装前，一定要执行命令 `yum update -y` ，让系统处于最新的状态！

下面我们就开始折腾吧！

## 下载和安装nginx

当然要使用最新稳定版的nginx，其他版本也行。

然后我们开始编译和安装。

具体操作请参考 [安装编译nginx][2] ， 注意nginx的编译参数。其中`--with-http_spdy_module`是Google的SPDY，还不是很稳定，所以如果你不需要这个功能，编译的时候请删除。

当安装完毕后应该能够访问到这个页面

![Welcome to nginx!][3]

然后参考 [nginx配置详解][4] 让需要反代的域名工作起来，比如绑定域名等工作。

## 配置反代

简单的反代就是http，也没有什么缓存控制等高级功能，仅仅一个简单的代理，比如反代老高的博客 `http://www.phpgao.com`。

其实nginx+php也是反代，如下：

```
location ~ \.php$ {
    fastcgi_pass   127.0.0.1:9000;
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include    fastcgi_params;
}
```

而复杂点的反代应该就是反代Google一类的https网站，访问量可能会比较大，有缓存等功能，比如 `https://www.google.com`。

### 简单反代

下面老高的域名`www.phpgao.com`代理某网站为例，介绍如何使用反代

编辑`/etc/nginx.conf`文件，新建一个server，输入以下内容

```
server {
    listen       80;
    server_name  www.phpgao.com;

    #charset koi8-r;
    #access_log  /var/log/nginx/log/www.phpgao.com.access.log  main;
    error_log   /var/log/nginx/log/www.phpgao.com.error.log;
    location / {
        sub_filter t66y.com www.phpgao.com; # 地址重写
        proxy_pass        http://t66y.com;
        #proxy_redirect     off;
        #proxy_set_header   Host $host;
        proxy_set_header   X-Real-IP  $remote_addr;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   Referer http://$host;
    }
    error_page  404              /404.html;
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```

然后重启nginx

```
service nginx restart
```

**折腾了一番，让我们来看看成果吧！**

![1024你懂得][5]

## 需要注意

 - `sub_filter`需要ngx_http_sub_module模块，编译时需要加上对应参数。


Reference：

http://nginx.com/resources/admin-guide/reverse-proxy/


  [1]: https://blog.phpgao.com/vps.html
  [2]: https://blog.phpgao.com/nginx_installation.html
  [3]: https://blog.phpgao.com/usr/uploads/2015/04/276254367.png
  [4]: https://blog.phpgao.com/nginx_conf.html
  [5]: https://blog.phpgao.com/usr/uploads/2015/04/934508212.png