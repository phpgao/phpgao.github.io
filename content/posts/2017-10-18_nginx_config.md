---
title: "nginx配置一篇足矣"
categories: [ "代码人生" ]
tags: [ "PHP","nginx" ]
draft: false
slug: "nginx_config"
date: "2017-10-18 07:58:00"
url: "nginx_config.html"
---

nginx的一些配置记录


<!--more-->


## 全局变量

```
$args //请求中的的参数名，即“?”后面的arg_name=arg_value形式的arg_name
$arg_PARAMETER //这是参数的一个匹配模式,PARAMETER为具体的参数名,$arg_PARAMETER就表示获取具体的参数值,例如上面的$arg_name就是获取url中name的值
$is_args //判断url是否带参数，如果带，则返回一个？,否则返回一个空字符串

$http_user_agent //获取的是客户端访问代理的类型,请求头中的信息
$sent_http_content_type //获取的是http响应头中content_type的值
$sent_http_content_length //获取的是http响应头重的content_length的值

$request_filename  //该变量获取的是请求的文件在linux服务器上的完整的绝对路径
$request_method  //该表示获取的是http请求的方法
$request_uri  //该变量表示的原始请求的uri，包括参数。所谓原始请求就是即使在内部做了重定向之后也不会变化
$uri  //获取的是当前请求的uri，不包括参数
$content_length  //获取的是http请求头中Content-Length的值
$content_type   //获取的是http请求头中的Content-Type字段，不过这里也没显示。。。
$document_root   //获取的是请求url的文件所在的目录路径
$document_uri   //当前请求的uri，从上面的信息来看,和uri的效果是一样的
$remote_addr  //获取的是客户端的ip地址,这里为什么是10.0.10.11呢，因为我是在本机上用curl测试的，即使客户端也是服务器
$remote_port  //获取客户端的访问端口，这个端口是随机的
$remote_user  //获取客户端的认证用户信息，这里因为没有用认证，所谓显示为空
$server_protocol  //表示服务器端想客户端发送响应的协议
$server_addr  //服务器的地址
$server_name  //客户端访问服务端的域名，即url中的域名
$server_port //服务器端做出响应的端口号
$binary_remote_addr  //显示二进制的客户端地址
$host  //和server_name一样，表示的是域名
$hostname  //表示服务器端的主机名

$proxy_add_x_forwarded_for //获取的是客户端的真实ip地址
$proxy_host //该变量获取的是upstream的上游代理名称，例如upstream backend 
$proxy_port   //该变量表示的是要代理到的端口
$proxy_protocol_addr  //代理头部中客户端的ip地址，或者是一个空的字符串
$upstream_addr  //代理到上游的服务器地址信息
$upstream_cache_status    //proxy的缓存状态，例如这里第一次访问为MISS，第二次访问时为HIT
$upstream_response_length  //上游服务器响应报文的长度
$upstream_response_time  //上游服务器响应的时间
$upstream_status  //上游服务器响应的状态码

$scheme  //表示的是使用http的访问协议 http or https
$limit_rate  //表示当前连接的限速是多少，0表示无限制
$query_string  //表示的是查询字符串，也就是url中的参数，和$args一样
$realpath_root  //表示的是请求页面的真实所在目录的路径  和$document_root是一样的
```


## http

老高在默认的nginx主配置中加入了时区，目的是输出日志时区默认为东八区。
同时加入了空主机头，防止直接用IP访问网站。

```
user  nginx;
worker_processes  1;

# timezone
env TZ=Asia/Shanghai;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;

    server {
        listen 80 default;
        return 500;
    }
}
```


## server

```nginx
server {

	listen 8080;
	server_name laogao.com;
	root /tmp;
	index index.php index.html index.htm;
	location / {
		autoindex on;
		autoindex_localtime on;
	}
}

```

## 缓存

## 伪静态

## PHP

[如何正确配置Nginx+PHP][1]


```nginx
server {
    listen 80;
    server_name foo.com;

    root /path;
    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ /index.php$is_args$args;
    }

    location ~ \.php$ {
        try_files $uri =404;

        include fastcgi.conf;
        fastcgi_pass 127.0.0.1:9000;
    }
}
```

## location

### 匹配规则

```nginx
server {

	listen 8080;
	server_name laogao.com;
	root /tmp;
	index index.php index.html index.htm;
	# /匹配所有请求，所以其他请求如果都不匹配，就会匹配到这里
	# PHP常用
	location / {
		return 200 "/";
	}

	# 严格匹配，不区分大小写，如果匹配到此块，立即处理请求
	# 此处只匹配 /laogao ，加上一个"/"都算不一样
	# curl -v laogao.com:8080/
	location = /laogao {

		return 200 "/laogao";
	}

	# 区分大小写匹配，匹配后继续搜索，直到没有匹配为止
	# 如果有相同的区块，则谁先出现最后匹配到谁
	location ~ /NIhao {

		return 200 "NIhao";
	}
	# 叹号表示不匹配
	location !~ /NIhao {

		return 200 "NIhao";
	}

	# 不区分大小写匹配，匹配后继续搜索，直到没有匹配为止
	# 如果有相同的区块，则谁先出现最后匹配到谁
	# 以下代码匹配图片请求
	location ~* \.(jpe?g|png|gif|bmp)$ {
		valid_referers none blocked *.phpgao.com server_names ~\.google\. ~\.baidu\.;
		if ($invalid_referer) {
			return 403 'hehe';
		}
	}

	# 普通匹配，匹配到后停止搜索，很适合做访问控制
	# 此处匹配 http://laogao.com:8080/images/
	# 此处不匹配 http://laogao.com:8080/images
	location ^~ /images/ {
		return 200 "images";
	}

}
```

### 匹配顺序

[nginx doc][2]

 1. Directives with the "=" prefix that match the query exactly. If found, searching stops.
 1. All remaining directives with conventional strings. If this match used the "^~" prefix, searching stops.
 1. Regular expressions, in the order they are defined in the configuration file.
 1. If #3 yielded a match, that result is used. Otherwise, the match from #2 is used.

 1. `=` 如果直接匹配，搜索停止，匹配请求。
 1. 剩下的区块的常规字符串，最长的匹配，如果遇到`^~`，搜索停止，匹配请求。
 1. 剩下的正则匹配区块按出现顺序匹配。
 1. 如果在剩下的区块中出现一个匹配，处理请求，如果都没有匹配，就用第二条的匹配。

## 防爬虫防盗链

### 防爬虫

可以将下面的代码保存在`/etc/nginx/agent_deny.conf`，然后在server段中加入`include agent_deny.conf;`即可完成使命。

```nginx
if ($http_user_agent ~* (Scrapy|Curl|HttpClient))
{
     return 403;
}
#禁止指定UA及UA为空的访问
if ($http_user_agent ~ "FeedDemon|JikeSpider|Indy Library|Alexa Toolbar|AskTbFXTV|AhrefsBot|CrawlDaddy|CoolpadWebkit|Java|Feedly|UniversalFeedParser|ApacheBench|Microsoft URL Control|Swiftbot|ZmEu|oBot|jaunty|Python-urllib|lightDeckReports Bot|YYSpider|DigExt|YisouSpider|HttpClient|MJ12bot|heritrix|EasouSpider|Ezooms|^$" )
{
     return 403;
}
#禁止非GET|HEAD|POST方式的抓取
if ($request_method !~ ^(GET|HEAD|POST)$)
{
    return 403;
}
```

### 防盗链

匹配图片请求，然后判断referer，如果为空，phpgao.com域名，Google或百度爬虫，则放行，否则return 403。如果强硬一点，none可以去掉。

```nginx
location ~* \.(gif|jpe?g|png|swf|flv|rar|zip|bmp|mp3|swf)$ {
    valid_referers none blocked *.phpgao.com server_names ~\.google\. ~\.baidu\.;
    if ($invalid_referer) {
        return 403 'hehe';
    }
}
```

## 列出目录

```nginx
location / {

	autoindex on;
	autoindex_localtime on;
}

location / {
	autoindex on;
	autoindex_localtime on;
	# 相当于创建了一个软连接
	alias /home/wwwroot/xxx;
}
```

## 跳转



```nginx
# 80端口重定向到443
server{
    listen 80;
    server_name blog.phpgao.com;
    rewrite ^(.*)$  https://$host$1 permanent;
}

# www指向blog
server{
    listen 80;
    server_name  www.phpgao.com phpgao.com;
    return 301 https://blog.phpgao.com$request_uri;
}
```

## SSL

### Let's encryption





## 防注入

## 日志



[goaccess][3]

```bash
goaccess phpgao.access.log -da --log-format=COMBINED > 1.html
```


参考

[Nginx系列教程之四：Nginx常用变量汇总及测试][4]
[nginx配置location总结及rewrite规则写法][5]


  [1]: https://huoding.com/2013/10/23/290
  [2]: http://nginx.org/en/docs/http/ngx_http_core_module.html#location
  [3]: https://goaccess.io/faq
  [4]: http://mingyang.blog.51cto.com/2807508/1547546
  [5]: http://seanlook.com/2015/05/17/nginx-location-rewrite/