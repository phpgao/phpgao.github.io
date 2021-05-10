---
title: "在Linux下建立自签名的https网站"
categories: [ "代码人生" ]
tags: [ "ssl" ]
draft: false
slug: "self_signed_ssl_site"
date: "2016-06-09 13:32:00"
url: "self_signed_ssl_site.html"
---

学习完了[证书，密钥，加密，rsa到底是啥？][1]一文后，我们就可以开始着手建立一个自签名的网站了！


<!--more-->


## 安装nginx

[安装编译nginx][2]

## 生成证书

```
# 建立ssl文件夹
mkdir /etc/nginx/ssl
# -subj 后面是一些创建CRT所需要的基础信息，CN一定要填写域名
openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout /etc/nginx/ssl/laogao.key -out /etc/nginx/ssl/laogao.crt -subj \
"/C=CN/ST=Shanghai/L=Shanghai/O=Qihoo 360/OU=IT Dept/CN=blog.phpgao.com/emailAddress=admin@phpgao.com"
```

## 配置nginx

```
server {
        listen 80 default_server;
        listen [::]:80 default_server ipv6only=on;

        listen 443 ssl;

        root /usr/share/nginx/html;
        index index.php index.html index.htm;

        server_name your_domain.com;
        ssl_certificate /etc/nginx/ssl/laogao.crt;
        ssl_certificate_key /etc/nginx/ssl/laogao.key;

        location / {
                try_files $uri $uri/ =404;
        }
}
```

## 测试

### curl命令

```
# 正常请求https会报错，说证书有问题
curl https://blog.phpgao.com

curl: (60) SSL certificate problem: Invalid certificate chain
More details here: http://curl.haxx.se/docs/sslcerts.html

curl performs SSL certificate verification by default, using a "bundle"
 of Certificate Authority (CA) public keys (CA certs). If the default
 bundle file isn't adequate, you can specify an alternate file
 using the --cacert option.
If this HTTPS server uses a certificate signed by a CA represented in
 the bundle, the certificate verification probably failed due to a
 problem with the certificate (it might be expired, or the name might
 not match the domain name in the URL).
If you'd like to turn off curl's verification of the certificate, use
 the -k (or --insecure) option.

# 加上-k选项即可正常访问
curl -vk https://blog.phpgao.com
```

### 浏览器

使用浏览器访问网站，会报错！

![ssl][4]

点击继续即可访问。

## 添加至系统根证书列表

```
# centos7
sudo cat /etc/nginx/ssl/nginx.crt >> /etc/pki/tls/certs/ca-bundle.crt

```


  [1]: https://blog.phpgao.com/encryption_decryption.html
  [2]: https://blog.phpgao.com/nginx_installation.html
  [4]: https://blog.phpgao.com/usr/uploads/2016/06/2978094777.jpg