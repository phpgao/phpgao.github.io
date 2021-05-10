---
title: "nginx的优化配置"
categories: [ "服务器技术" ]
tags: [ "nginx","optimize","cache","gzip","concat" ]
draft: false
slug: "nginx_optimization"
date: "2015-05-19 01:12:00"
url: "nginx_optimization.html"
---

老高的服务器最近表示亚历山大，先祭出此篇缓解前端压力，之后再从代码中优化一下。


<!--more-->


## 基本配置

请参考老高的 [nginx配置详解][1]。

----------

用基本配置只是使用了nginx的基本特性，许多高级特性我们需要手动打开！

**强调几个配置**

```nginx
# http://nginx.org/en/docs/ngx_core_module.html#worker_processes
# auto 1.3.8 and 1.2.5 后的版本都支持
worker_processes auto;

# 可选，老高一般是先设worker_processes为auto，如果最后跑出来的worker_processes>1，再继续设定这个值。
# http://nginx.org/en/docs/ngx_core_module.html#worker_cpu_affinity
worker_cpu_affinity 0001 0010 0100 1000;

# 看性能设置吧，相关命令 ulimit -n
worker_rlimit_nofile 60000;

events {
    # 分给worker_processes的最大连接数，看你机器的性能了
    worker_connections  2048;
    use epoll;
}

http{
    sendfile        on;
    #tcp_nopush     on;
    #tcp_nodelay    on;
    keepalive_timeout  65;
    reset_timedout_connection on;
    server_tokens off;

    # limit ip
    limit_conn_zone $binary_remote_addr zone=ip:10m;
    # limit server
    limit_conn_zone $server_name zone=server:10m;

}

# 下面在某server中做出以下限制
server{
    # 限制一个IP最大连接数
    limit_conn ip 3;
    # 限制一个server的最大连接数
    limit_conn server 100;
    # 限速
    limit_rate 100k;
}
```

## 合理的压缩文件

这一点不用说了，js，css，图片最好都用对应的压缩软件压一下。

## 启用gzip

直接上老高的gzip配置，注意写到http块里！

```nginx
gzip  on;
#gzip_min_length 1k;
gzip_min_length 0;
gzip_buffers 4 16k;
gzip_comp_level 3;
gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png app    lication/vnd.ms-fontobject application/x-font-ttf image/svg+xml;
gzip_vary off;
gzip_disable "MSIE [1-6]\.";
```

## 使用nginx-http-concat合并请求

### 项目地址

[nginx-http-concat][2]

因为该模块不是标准模块，所以需要重新编译nginx，并加入`--add-module=/tmp/nginx-http-concat`即可！

### 使用方法

在location配置中开启，选项和默认值可以在项目的readme中找到。

```
location /js/ {  
    # 打开concat 功能  
    # 默认关闭  
    concat on;  
    # 允许concat最大的文件数，默认最大设置十个文件。  
    # (默认: 10)  
    # concat_max_files 10;  
    # 只允许相同类型的文件
    # 默认是开启的  
    # concat_unique on;  
    # 允许内容的类型  
    # (default: application/x-javascript, text/css)  
    # concat_types text/html;  
}
```

## 设定过期时间

```nginx
location ~ .*\.(gif|jpg|jpeg|png|bmp|swf){
    expires       30d;
}

location ~ .*\.(js|css){
    expires       15d;
}
```

## proxy_cache

使用proxy_cache缓存后端服务器生成的内容。

首先在http块中配置一个cache空间。

```nginx
proxy_cache_path /data/nginx/cache levels=1:2 keys_zone=phpgao:50m inactive=10m max_size=2m;
proxy_temp_path /data/nginx/tmp_cache;

# levels 指定几层hash目录
# keys_zone 空间名 大小 10M 空间名在后面会用到
# inactive 缓存默认时长
# max_size 最大单个文件
# loader_threshold 持续工作时间(单位 毫秒，默认200)
# loader_files 文件数量
# loader_sleeps 达到loader_files后休息时间 (单位 毫秒，默认50)

```


指定了空间名后，我们可以开始配置server了

```nginx
server{
    ...
    ...
    location / {
         #如果后端的服务器返回502、504、执行超时等错误，自动将请求转发到upstream负载均衡池中的另一台服务器，实现故障转移。
         proxy_next_upstream http_502 http_504 error timeout invalid_header;
         proxy_cache phpgao;
         #对不同的HTTP状态码设置不同的缓存时间
         proxy_cache_valid  200 304 12h;
         #以域名、URI、参数组合成Web缓存的Key值，Nginx根据Key值哈希，存储缓存内容到二级缓存目录内
         proxy_cache_key $host$uri$is_args$args;
         proxy_set_header Host  $host;
         proxy_set_header X-Forwarded-For  $remote_addr;
         proxy_pass http://backend_server;
         expires      1d;
    }

}
```

## fastcgi_cache

fastcgi_cache通常用来来缓存php生成的文件，其配置方法与proxy_cache类似。

http段：

```nginx

    add_header X-Cache-CFC "$upstream_cache_status - $upstream_response_time";

    fastcgi_temp_path /data/nginx/tmp_cache;
    fastcgi_cache_path /data/nginx/cache levels=1:2 keys_zone=phpgao:50m inactive=10m max_size=2m;
    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 300;
    fastcgi_buffer_size 64k;
    fastcgi_buffers 4 64k;
    fastcgi_busy_buffers_size 128k;
    fastcgi_temp_file_write_size 128k;
```

server段：

```
server{
    ...
    ...
    location ~ .*\.(php|php5){
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include    fastcgi_params;

        fastcgi_cache phpgao;
        fastcgi_cache_valid 200 302 301 1h;
        fastcgi_cache_valid any 1m;
        fastcgi_cache_min_uses 1;
        fastcgi_cache_methods GET HEAD;
        fastcgi_cache_use_stale error timeout invalid_header http_500;
        fastcgi_cache_key $request_method://$host$request_uri;
    }

}
```

更多fastcgi配置，请参考

http://www.cnblogs.com/shineshqw/articles/1828295.html

reference:

http://nginx.com/resources/admin-guide/caching/
http://zyan.cc/nginx_cache/5/1/
http://www.cnxct.com/several-reminder-in-nginx-fastcgi_cache-and-php-session_cache_limiter/


  [1]: https://blog.phpgao.com/nginx_conf.html
  [2]: https://github.com/alibaba/nginx-http-concat