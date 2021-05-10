---
title: "使用Let's-Encrypt-----免费的https证书"
categories: [ "服务器技术" ]
tags: [ "ssl","https","nginx","encrypt","certbot" ]
draft: false
slug: "let-us_encrypt"
date: "2016-12-01 03:28:00"
url: "let-us_encrypt.html"
---

![letsencrypt][1]

(图片来自网络)

老高的证书快过期了(2016-12-11)，本着节约资(R)源(MB)的精神，准备使用[Let's Encrypt][2]。


<!--more-->


由于老高的服务器在[搬瓦工搭建][3]，所以属于有shell权限的，所以老高可以使用[Certbot][4](如下图)来简化操作。

![certbot][5]

如截图所示，选择合适的web服务器和系统后就可以进行安装和部署操作了。

> ps. 看来想要顺利安装Certbot，还需要[epel-release][6]


## 安装Certbot

```bash
yum install -y epel-release
yum install -y certbot
```

## 使用webroot方式获取证书

[使用webroot的方式可以让你不需要重启][7]

操作前假设你的web目录为`/var/html/www/`

```bash
# -w 指定webroot根目录
# -d domain 想要获取的证书域名，支持多个域名
certbot certonly --webroot -w /var/html/www/ -d blog.phpgao.com -d www.phpgao.com -d phpgao.com
```

成功执行后会生成几个文件

```bash
➜  ~ ll /etc/letsencrypt/live/blog.phpgao.com/

cert.pem -> ../../archive/blog.phpgao.com/cert2.pem
chain.pem -> ../../archive/blog.phpgao.com/chain2.pem
fullchain.pem -> ../../archive/blog.phpgao.com/fullchain2.pem
privkey.pem -> ../../archive/blog.phpgao.com/privkey2.pem
```

这几个文件就是我们开启https所需要的所有文件了，更多信息可以参考[为你的网站开启ssl支持][8]



<table border="1">
<thead>
<tr>
<th>文件名</th>
<th>说明</th>
</tr>
</thead>
<tbody>
<tr>
<td>cert.pem</td>
<td>服务端证书</td>
</tr>
<tr>
<td>chain.pem</td>
<td>浏览器需要的所有证书但不包括服务端证书，比如根证书和中间证书</td>
</tr>
<tr>
<td>fullchain.pem</td>
<td>包括了cert.pem和chain.pem的内容</td>
</tr>
<tr>
<td>privkey.pem</td>
<td>证书的私钥</td>
</tr>
</tbody>
</table>

```nginx
listen 443 ssl;
ssl on;
ssl_certificate /etc/letsencrypt/live/blog.phpgao.com/fullchain.pem;
ssl_certificate_key /etc/letsencrypt/live/blog.phpgao.com/privkey.pem;
ssl_trusted_certificate /etc/letsencrypt/live/blog.phpgao.com/chain.pem;

/*
剩下的配置
*/
```

## 自动更新

根据Certbot的文档，我们先测试一下升级流程

```bash
/usr/bin/certbot renew --dry-run
```

如果结果没有问题就可以加到计划任务中了！由于Let's Encrypt的证书有效期是3个月，而`certbot renew`只会更新还有30天才会过期的证书，所以我们在每周一的中午12点检查一次即可！

```bash
0 12 * * 1 /usr/bin/certbot renew --quiet

# or with log

0 12 * * 1 /usr/bin/certbot renew >> /var/log/cerbot.log
```


补充：

renew的时候报错的解决办法

> ImportError: 'pyOpenSSL' module missing required functionality. Try upgrading to v0.14 or newer.

```
rpm --query centos-release  # centos-release-7-3.1611.el7.centos.x86_64
wget ftp://ftp.muug.mb.ca/mirror/centos/7.3.1611/cloud/x86_64/openstack-mitaka/common/pyOpenSSL-0.15.1-1.el7.noarch.rpm
rpm -Uvh pyOpenSSL-0.15.1-1.el7.noarch.rpm
yum install certbot
certbot renew
```


  [1]: https://blog.phpgao.com/usr/uploads/2016/12/2787518341.png
  [2]: https://letsencrypt.org/getting-started/
  [3]: https://blog.phpgao.com/vps.html
  [4]: https://certbot.eff.org/#centosrhel7-nginx
  [5]: https://blog.phpgao.com/usr/uploads/2016/12/1002250556.png
  [6]: https://fedoraproject.org/wiki/EPEL#How_can_I_use_these_extra_packages.3F
  [7]: https://certbot.eff.org/docs/using.html#webroot
  [8]: https://blog.phpgao.com/https_support.html#%E9%85%8D%E7%BD%AE