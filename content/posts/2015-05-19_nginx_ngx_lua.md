---
title: "nginx编译安装ngx_lua模块"
categories: [ "服务器技术" ]
tags: [ "nginx","safety","ngx_lua","luajit","ngx_lua_waf" ]
draft: false
slug: "nginx_ngx_lua"
date: "2015-05-19 06:11:00"
url: "nginx_ngx_lua.html"
---

nginx lua模块淘宝开发的nginx第三方模块,它能将lua语言嵌入到nginx配置中,从而使用lua就极大增强了nginx的能力.


<!--more-->


## 准备

http://www.nginx.org  nginx 1.8.0
http://luajit.org/download.html   LuaJIT-2.0.4



```bash
cd /tmp
wget http://nginx.org/download/nginx-1.8.0.tar.gz
wget http://luajit.org/download/LuaJIT-2.0.4.tar.gz
git clone https://github.com/openresty/lua-nginx-module.git lua-nginx-module
git clone https://github.com/loveshell/ngx_lua_waf.git waf
```

## 开始编译

### 编译LuaJIT

```bash
tar xf ..
cd ..
make && make install

# lib 
ln -s /usr/local/lib/libluajit-5.1.so.2 /lib64/libluajit-5.1.so.2

export LUAJIT_LIB=/usr/local/lib
export LUAJIT_INC=/usr/local/include/luajit-2.0
```

### nginx

```bash
tar xf ..
cd ..

./configure --prefix=/usr --sbin-path=/usr/sbin/nginx --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --pid-path=/var/run/nginx/nginx.pid --user=nginx --group=nginx --with-http_ssl_module --with-http_flv_module --with-http_gzip_static_module --http-log-path=/var/log/nginx/access.log --http-client-body-temp-path=/var/tmp/nginx/client --http-proxy-temp-path=/var/tmp/nginx/proxy --http-fastcgi-temp-path=/var/tmp/nginx/fcgi --with-http_stub_status_module --with-http_sub_module --with-http_spdy_module --add-module=/tmp/lua-nginx-module

make && make install
```

### ngx_lua_waf

下面可以使用ngx_lua_waf来做一些安全设置了，具体方法可以参考

https://github.com/loveshell/ngx_lua_waf.git

http块配置文件

```nginx
include vhost/*.conf;
include blocksip.conf;
lua_need_request_body on;
lua_package_path "/etc/nginx/vhost/waf/?.lua";
lua_shared_dict limit 10m;
init_by_lua_file  /etc/nginx/vhost/waf/init.lua;
access_by_lua_file /etc/nginx/vhost/waf/waf.lua;
```

## 补充

老高在操作的时候突然遇到502，经过错误日志查询，原来是因为post请求过大，导致多余的内容被写入temp文件中，而Lua不支持从文件中获取请求数据，所以导致报错。

> requesty body in temp file not supported

解决办法很简单：

在http块加入以下代码即可

```nginx
client_max_body_size 1m;    #允许客户端请求的最大单文件字节数
client_body_buffer_size 128k;  #缓冲区代理缓冲用户端请求的最大字节数
```

下面是老高的nginx配置备份

```
user  nginx;
worker_processes  auto;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

events {
    use epoll;
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    server_tokens   off;
    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;
    reset_timedout_connection on;

    #ip limit
    limit_conn_zone $binary_remote_addr zone=perip:10m;
    limit_conn_zone $server_name zone=perserver:10m;

    gzip  on;
    #gzip_min_length 1k;
    gzip_min_length 0;
    gzip_buffers 4 16k;
    gzip_comp_level 3;
    gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png application/vnd.ms-fontobject application/x-font-ttf image/svg+xml;
    gzip_vary off;
    gzip_disable "MSIE [1-6]\.";

    add_header X-Cache-CFC "$upstream_cache_status - $upstream_response_time";
    fastcgi_temp_path /data/nginx/tmp_cache;
    fastcgi_cache_path /data/nginx/cache levels=1:2 keys_zone=phpgao:50m inactive=10m max_size=2m;
    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 300;
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 128k;
    client_max_body_size 1m;    #允许客户端请求的最大单文件字节数
    client_body_buffer_size 128k;  #缓冲区代理缓冲用户端请求的最大字节数，

    server{
        listen 80 default;
        return 500;
    }

    include vhost/*.conf;
    include blocksip.conf;

    # 开启lua
    lua_need_request_body on;
    lua_package_path "/etc/nginx/vhost/waf/?.lua";
    lua_shared_dict limit 10m;
    init_by_lua_file  /etc/nginx/vhost/waf/init.lua;
    access_by_lua_file /etc/nginx/vhost/waf/waf.lua;
}
```