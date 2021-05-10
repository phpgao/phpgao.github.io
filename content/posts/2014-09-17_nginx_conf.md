---
title: "nginx配置详解"
categories: [ "代码人生" ]
tags: [ "nginx","proxy","conf" ]
draft: false
slug: "nginx_conf"
date: "2014-09-17 03:28:00"
url: "nginx_conf.html"
---

#nginx配置详解

用了nginx这么久，很多配置还是记不牢，还是总结一下吧！

基本命令：

```bash
service nginx reload
service nginx start
service nginx stop
#检测配置文件是否通过，需要比较高的权限，否则可能会报 Permission denied
/usr/sbin/nginx -t
#平滑重启
nginx -s reload
```

当编译安装好nginx后，其配置文件nginx.conf可以在/etc/nginx/下找到！

此文件就是主配置文件了。

##高层的配置

```nginx
#运行用户
user www;    
#启动进程,一般等于cpu的总核数
worker_processes  auto;
#更改worker进程的最大打开文件数限制
worker_rlimit_nofile 100000; 

#全局错误日志及PID文件
error_log  /var/log/nginx/error.log;
pid        /var/run/nginx.pid;

#工作模式及连接数上限
events {
    #每个工作进程允许最大的同时连接数 
    worker_connections 65535; 
    #epoll是多路复用IO(I/O Multiplexing)中的一种方式,但是仅用于linux2.6以上内核,可以大大提高nginx的性能
    #如果你使用*BSD，你应该使用kqueue
    use epoll;
    #单个后台worker process进程的最大并发链接数
    worker_connections  1024;
    #设置是否允许，Nginx在已经得到一个新连接的通知时，接收尽可能更多的连接
    # multi_accept on; 
}
```

## HTTP 模块

HTTP模块控制着nginx http处理的所有核心特性。

```nginx
#设定http服务器，利用它的反向代理功能提供负载均衡支持
http 
{
    #并不会让nginx执行的速度更快，但它可以关闭在错误页面中的nginx版本数字，这样对于安全性是有好处的
    server_tokens  off;
    #设定mime类型,类型由mime.type文件定义
    include       /etc/nginx/mime.types;
    #设置文件使用的默认的MIME-type
    default_type text/html;
    #设置我们的头文件中的默认的字符集
    charset UTF-8;
    #设定日志格式
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                  '$status $body_bytes_sent "$http_referer" '
                  '"$http_user_agent" "$http_x_forwarded_for"';
    #日志存放地址
    access_log    /var/log/nginx/access.log;
    #告诉nginx只能记录严重的错误
    error_log /var/log/nginx/error.log crit;

    #sendfile 指令指定 nginx 是否调用 sendfile 函数（zero copy 方式）来输出文件，对于普通应用，
    #必须设为 on,如果用来进行下载等应用磁盘IO重负载应用，可设置为 off，以平衡磁盘与网络I/O处理速度，降低系统的uptime.
    #传统方式：硬盘 >> kernel buffer >> user buffer>> kernel socket buffer >>协议栈
    #sendfile：硬盘 >> kernel buffer (快速拷贝到kernelsocket buffer) >>协议栈
    sendfile        on;
    #告诉nginx在一个数据包里发送所有头文件，而不一个接一个的发送。默认已开启，可以不写
    #tcp_nopush     on;
    
    #连接超时时间
    #The parameters can differ from each other. Line Keep-Alive:timeout=time understands Mozilla and Konqueror. MSIE itself shutskeep-alive connection approximately after 60 seconds.
    keepalive_timeout  65;
    #告诉nginx不要缓存数据，而是一段一段的发送--当需要及时发送数据时，就应该给应用设置这个属性，这样发送一小块数据信息时就不能立即得到返回值
    tcp_nodelay        on;
    
    #开启gzip压缩
    gzip on;
    #设置对数据启用压缩的最少字节数
    gzip_min_length 1k;
    gzip_buffers 4 16k;
    #gzip_http_version 1.0;
    #设置数据的压缩等级，这个等级可以是1-9之间的任意数值，9是最慢但是压缩比最大的。我们设置为4，这是一个比较折中的设置
    gzip_comp_level 2;
    #gzip针对一下类型文件进行压缩，可以适当增减
    gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
    #和http头有关系，加个vary头，给代理服务器用的，有的浏览器支持压缩，有的不支持。
    #因此，为避免浪费不支持的也压缩，需要根据客户端的HTTP头来判断，是否需要压缩。
    gzip_vary off;
    #在某些浏览器中禁止gzip
    gzip_disable "MSIE [1-6]\.";


    #客户请求头缓冲大小，nginx默认会用client_header_buffer_size这个buffer来读取header值
    #如果header过大，它会使用large_client_header_buffers来读取
    #如果设置过小HTTP头/Cookie过大 会报400 错误 nginx 400 bad request
    #求行如果超过buffer，就会报HTTP 414错误(URI Too Long)
    #nginx接受最长的HTTP头部大小必须比其中一个buffer大，否则就会报400的
    client_header_buffer_size    1k;
    large_client_header_buffers  4 4k;
    
    #打开缓存的同时也指定了缓存最大数目，以及缓存的时间
    open_file_cache max=100000 inactive=20s; 
    #在open_file_cache中指定检测正确信息的间隔时间
    open_file_cache_valid 30s; 
    #定义了open_file_cache中指令参数不活动时间期间里最小的文件数
    open_file_cache_min_uses 2; 
    #指定了当搜索一个文件时是否缓存错误信息，也包括再次给配置中添加文件
    open_file_cache_errors on; 
    
    #设定负载均衡的服务器列表
    upstream servers1 {
    #weigth参数表示权值，权值越高被分配到的几率越大
    #本机上的Squid开启3128端口
    server 192.168.8.1:3128 weight=5;
    server 192.168.8.2:80  weight=1;
    server 192.168.8.3:80  weight=6;
    }
    
    #负载可以分组
    upstream servers2 {
    #weigth参数表示权值，权值越高被分配到的几率越大
    #本机上的Squid开启3128端口
    server 192.168.9.1:3128 weight=2;
    server 192.168.9.2:80  weight=3;
    server 192.168.0.3:80  weight=1;
    }
    
    ## 
    # Virtual Host Configs 
    # aka our settings for specific servers 
    #下面包含了虚拟主机的设置
    #可以将虚拟主机的配置如www.phpgao.com保存为phpgao.conf放conf.d里
    #nginx就会自动索引到他
    ## 
    include /etc/nginx/conf.d/*.conf;
}
```

## 虚拟主机配置

虚拟主机可以先指定一个空主机头`server_name _`，并return 500，以防止不带主机名的访问。

```nginx
server 
{ 
    listen 80 default; 
    server_name _;
    return 500; 
}
```

下面是某个主机的标准配置，可以将其保存到phpgao.conf文件中，放在conf.d文件夹，就可以正确的被nginx.conf识别到

```nginx
server 
{
    #限制IP访问
    allow 10.57.22.172;
    #表示除了上面allow的其他都禁止
    deny all;
    #监听端口，可以基于IP，如1.1.1.1:8080
    listen       80;
    #绑定域名，可以同时绑定多个域名
    server_name  www.phpgao.com;
    server_name  phpgao.com;
    #设定本虚拟主机的访问日志
    access_log  logs/www.phpgao.com.access.log  main;
    
    #根目录可以设为全局，也可以针对某个路径，index同理
    root /var/www/virtual/htdocs;
    index index.php index.html index.htm;
    
    #默认请求
    location / {
        root   /root;      #定义服务器的默认网站根目录位置
        index index.php index.html index.htm;   #定义首页索引文件的名称
    }

    # 定义错误提示页面
    error_page   500 502 503 504 /50x.html;
        location = /50x.html {
        root   /root;
    }

    #静态文件，nginx自己处理
    location ~ ^/(images|javascript|js|css|flash|media|static)/ {
        root /var/www/virtual/htdocs;
        #过期30天，静态文件不怎么更新，过期可以设大一点，如果频繁更新，则可以设置得小一点。
        expires 30d;
    }
    
    #PHP 脚本请求全部转发到 FastCGI处理. 使用FastCGI默认配置.
    location ~ \.php$ {
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
    
    #设定查看Nginx状态的地址
    location /NginxStatus {
        stub_status            on;
        access_log              on;
        auth_basic              "NginxStatus";
        auth_basic_user_file  conf/htpasswd;
    }
    
    #禁止访问 .htxxx 文件
    location ~ /\.ht {
        deny all;
    }
    
    #负载均衡
    #请求转向之前定义的服务器列表
    proxy_pass  http://servers2;
    #后端的Web服务器可以通过X-Forwarded-For获取用户真实IP
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    #允许客户端请求的最大单文件字节数
    client_max_body_size 10m;
    #缓冲区代理缓冲用户端请求的最大字节数
    client_body_buffer_size 128k;
    #nginx跟后端服务器连接超时时间(代理连接超时)
    proxy_connect_timeout 90;
    #后端服务器数据回传时间(代理发送超时)
    proxy_send_timeout 90;
    #连接成功后，后端服务器响应时间(代理接收超时)
    proxy_read_timeout 90;
    #设置代理服务器（nginx）保存用户头信息的缓冲区大小
    proxy_buffer_size 4k;
    #proxy_buffers缓冲区
    proxy_buffers 4 32k;
    #高负荷下缓冲大小（proxy_buffers*2）
    proxy_busy_buffers_size 64k;
    #设定缓存文件夹大小
    proxy_temp_file_write_size 64k;

}
```

ssl的配置请参考老高的另一篇文章[为你的网站开启ssl][1]

## 常用配置

301重定向，将不带www的域名转向带www：

```nginx
    server{
    
        listen    80;
        server_name  phpgao.com;
        #rewrite  ^/(.*)$ https://blog.phpgao.com/$1 permanent;
        return    301 https://www.phpgao.com$request_uri;
    }
```

rewrite，许多站点都启用了伪静态，其实原理很简单，如果发现文件或文件夹不存在，就会在URL加一个`index.php`，然后交给程序解析这个URL，整个过程是对浏览器透明的。

```nginx
location /
{
    if (-f $request_filename/index.html)
    {
        rewrite (.*) $1/index.html break;
    }
    if (-f $request_filename/index.php)
    {
        rewrite (.*) $1/index.php;
    }
    if (!-f $request_filename)
    {
        rewrite (.*) /index.php;
    }
}
```

## 总结

其实服务器的配置大同小异，只是有些功能侧重点不同而已。研究了服务器的配置后其实也大致了解了http是如何在服务器中流动的，了解这一点很重要，后期的优化就靠他了。

参考：

[http://www.th7.cn/system/lin/201306/41314.shtml][2]

[http://www.cnblogs.com/xiaogangqq123/archive/2011/03/02/1969006.html][3]


  [1]: https://blog.phpgao.com/https_support.html
  [2]: http://www.th7.cn/system/lin/201306/41314.shtml
  [3]: http://www.cnblogs.com/xiaogangqq123/archive/2011/03/02/1969006.html