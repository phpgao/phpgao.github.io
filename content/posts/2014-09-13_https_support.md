---
title: "为你的网站开启ssl支持"
categories: [ "性能优化" ]
tags: [ "ssl","https","nginx" ]
draft: false
slug: "https_support"
date: "2014-09-13 00:47:00"
url: "https_support.html"
---

前几天再Startssl已经为我的博客申请的证书通过，今天早上学习了RSA加密原理，趁着热乎劲还没过，下午就把本站全部升级为https啦！

看到这个https，瞬间觉得高大上了许多。大家可能认为为自己的网站开启ssl功能很麻烦，其实不然。下面老高就简单的介绍一下开启的步骤，自己也做个记录。

> 不同的服务器配置ssl的方法大题思路相同，本文以nginx为例。

当然你还可以试试[使用Let's Encrypt -- 免费的https证书][1]


<!--more-->

## 准备

首先需要明确的是，证书都需要独立ip，所以[你懂的][2]。

这一步需要等待的时间很长，但需要做的东西很少。如果申请在此就不多说了，网上[教程][3]很多。

需要注意的是，进入下一步你需要准备：

    1. StartSSL操作证书(独一无二，用于续期管理，请妥善保管)
    2. 私钥  .key
    3. 域名证书

需要注意的是，私钥和证书可以自己生成，然后再提Startssl即可。命令如下：

```bash
openssl req -new -newkey rsa:2048 -nodes -out phpgao.crt -keyout phpgao.key
```

其中`phpgao`为域名，推荐使用2048位的加密方式。命令执行后会在`~/`下生成私钥和自己的签名。

下一步就是把证书内容提供给Startssl，他会用自己的私钥加密你的证书，最终返回给你域名证书，只要客户端信任诸如Startssl这样的CA（certificate authority），那么这个域名就可以信任，否则，现代浏览器就会给出证书不可信的提示！得到域名证书后我们将其保存到文件`phpgao.crt`中。

那么浏览器是如何信任CA呢？

> 所谓根证书，是ca认证中心与用户建立信任关系的基础，用户的数字证书必须有一个受信任的根证书，用户的数字证书才是有效的。从技术上讲，证书其实包含三部分，用户的信息，用户的公钥，还有ca中心对该证书里面的信息的签名，要验证一份证书的真伪（即验证ca中心对该证书信息的签名是否有效），需要用ca中心的公钥验证，而ca中心的公钥存在于对这份证书进行签名的证书内，故需要下载该证书，但使用该证书验证又需先验证该证书本身的真伪，故又要用签发该证书的证书来验证，这样一来就构成一条证书链的关系，这条证书链在哪里终结呢？答案就是根证书，根证书是一份特殊的证书，它的签发者是它本身，下载根证书就表明您对该根证书以下所签发的证书都表示信任，而技术上则是建立起一个验证证书信息的链条，证书的验证追溯至根证书即为结束。所以说用户在使用自己的数字证书之前必须先下载根证书。

ps.Startssl提供的证书为什么会被信任呢？答案在这里。

![系统默认信任的证书颁发机构 “系统默认信任的证书颁发机构”][4]

上图显示了系统默认信任的证书颁发机构，其中就有Startssl。

**注意**

为了解决FF下有些证书不被信任的bug，可能出现类似`NET::ERR_CERT_AUTHORITY`的错误，所以为了说明我们的密钥和CA的关系，我们需要将ca的信息添加到域名证书，作为新的域名证书。也就是说你的域名证书包含有两个不同的证书信息。


```bash
wget http://cert.startssl.com/certs/sub.class1.server.ca.pem
# ca.pem 在此可以合并也可以单独起来使用ssl_client_certificate指定
wget http://cert.startssl.com/certs/ca.pem

cat sub.class1.server.ca.pem >> phpgao.crt

# 如果合并，后文的ssl_client_certificate即可不指定
cat ca.pem sub.class1.server.ca.pem >> phpgao.crt
```

为什么要这样做？

> 有些浏览器不接受那些众所周知的证书认证机构签署的证书，而另外一些浏览器却接受它们。这是由于证书签发使用了一些中间认证机构，这些中间机构被众所周知的证书认证机构授权代为签发证书，但是它们自己却不被广泛认知，所以有些客户端不予识别。针对这种情况，证书认证机构提供一个证书链的包裹，用来声明众所周知的认证机构和自己的关系，需要将这个证书链包裹与服务器证书合并成一个文件。在这个文件里，服务器证书需要出现在认证方证书链的前面。

现在我们已经准备好的文件有，私钥（phpgao.key）、合并后的域名证书（phpgao.crt）、和ca.pem。我们把这三个文件上传至服务器，保存`~/ssl`即可！记得要把文件权限设为可读。

## 配置

**注意**

如果编译安装nginx的时候没有指定`–with-http_ssl_module`参数，那么你可能需要重新编译安装nginx，并加上`–with-http_ssl_module`，在此顺便加上这个`headers-more-nginx-module`。

在开始配置前我们需要理解这几个参数：

```
ssl

语法：ssl [on|off] 
默认值：ssl off 
使用字段：main, server 
开启HTTPS。

ssl_certificate

语法：ssl_certificate file 
默认值：ssl_certificate cert.pem 
使用字段：main, server 
为这个虚拟主机指定PEM格式的证书文件，这个文件可以包含其他的证书和服务器私钥，同样，密钥也必须是PEM格式，0.6.7版本以后，这里的路径为相对于nginx.conf的路径，而不是编译时的prefix路径。

ssl_certificate_key

语法：ssl_certificate_key file 
默认值：ssl_certificate_key cert.pem 
使用字段：main, server 
为这个虚拟主机指定PEM格式的私钥，0.6.7版本以后，这里的路径为相对于nginx.conf的路径，而不是编译时的prefix路径。

ssl_client_certificate

语法：ssl_client_certificate file 
默认值：none 
使用字段：main, server 
指出包含PEM格式的CA（root）证书，它将用于检查客户端证书。
```

明白了吗？刚才的三个文件分别对应ssl_certificate、ssl_certificate_key、ssl_client_certificate。

下面我们在虚拟主机中加入新的https主机：

```nginx
server{

    listen              443;
    listen              80;
    server_name         www.phpgao.com;
    
    #ssl
    ssl                 on;
    ssl_certificate     /path/to/phpgao.crt;
    ssl_certificate_key /path/to/phpgao.key;
    ssl_client_certificate /path/to/ca.pem;
    ssl_protocols       SSLv3 TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers         HIGH:!aNULL:!MD5;
    ssl_session_timeout 5m; 
    ......
}
```

检测nginx配置文件语法

```bash
nginx -t
```

重新载入

```bash
nginx -s reload
```

## 问题

### 总是输入密码

重启nginx的时候需要输入私钥的加密密码，如果觉得麻烦，可以取消密码：

```bash
openssl rsa -in phpgao.key -out phpgao.no.key
```

用新的无密码私钥phpgao.no.key即可！

### 合并端口写法

如果HTTP和HTTPS虚拟主机的功能是一致的，那么合并二者，就可以同时使用`http/https`来访问老高的博客了。

```nginx
server {
    listen              80;
    listen              443 ssl;
    server_name         www.phpgao.com;
    ssl_certificate     www.phpgao.com.crt;
    ssl_certificate_key www.phpgao.com.key;
    ...
}
```

### www跳转

ps.老高的博客默认是http访问，但是如果大家访问[phpgao.com][5]，会发生什么呢？请参考下面的配置：

```nginx
server{
    listen    80;
    server_name  phpgao.com;
    return    301 https://www.phpgao.com$request_uri;
}
```

这样下来，再浏览器的地址栏输入phpgao.com就会被强制跳转至https://www.phpgao.com/，与直接在地址栏输入https://www.phpgao.com/是一模一样的！！

### 全站https

pps.有些TX可能觉得https混着用很不爽，那么就把http跳转为https：

```nginx
server{
    listen              80;
    listen              443 ssl;
    server_name         www.phpgao.com;
    # 方法一：强制跳转
    # 判断当前是否为https再做301重定向
    if ($ssl_protocol = "") {
       rewrite ^   https://$server_name$request_uri? permanent;
    }
    # 方法二：比较温和的跳转
    # 让http请求重定向到https请求
    error_page 497  https://$host$uri?$args;
}
```

ppps.网页中的不安全因素

老高发现即使全站都开启了https，还是还是会有一些页面出现错误，chrome浏览器下绿色的https会变成灰色，FF下会变成灰色的三角叹号，Safari下直接干脆就不显示https标志，造成这个问题的原因是————老高的网站某些页面引用了非https的资源，ssl会认为此次链接不安全，不论是URL还是js、css文件。

解决方法：

1. 首先考虑把文件拷贝至自己的域名下
2. 强行修改非https为https链接，URL跳转可以自己做一个跳转系统，如`https://www.phpgao.com/go/112233`，跳转至`http://weibo.com/phpgao/`

2014年12月26日更新：

阅读[How to fix a website with blocked mixed content
][6]与[HTTPS page loading non-https images][7]两篇文章后，确定如果想保证网站的安全，必须全站使用https。

> To elaborate on this, checking that the website uses HTTPS is ultimately the sole responsibility of the user. They must expect HTTPS to be used, and used correctly, otherwise a MITM attacker could downgrade the connection to plain HTTP. (This is why automatic redirections from http:// to https:// are only partly useful; pre-loaded lists of HSTS sites can help.)

> To make sure that the content they see is rendered from what the server sent, users must check that HTTPS is used, with the site they intended to visit and never ignore SSL/TLS warnings. This is a GUI problem.

> Tolerating mixed content on a page makes it virtually impossible to check what was served over HTTPS and what wasn't. Even developers can struggle to find which resources are loaded over SSL/TLS and which ones aren't.

2015年1月23日更新：

修正一些错误。

2015年04月01日更新：

[让nginx支持SPDY][8]

参考:

[http://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature.html][9]

[http://blog.sina.com.cn/s/blog_493e7af2010083y9.html][10]

[nginx模块参考手册中文版][11]

[http://www.lsproc.com/post/nginx-ssl-config/][12]

[http://www.cnblogs.com/aLittleBitCool/archive/2011/09/22/2185418.html][13]

[http://nginx.org/en/docs/http/configuring_https_servers.html][14]


  [1]: https://blog.phpgao.com/let-us_encrypt.html
  [2]: https://blog.phpgao.com/VPS.html
  [3]: http://www.freehao123.com/startssl-ssl/
  [4]: https://blog.phpgao.com/usr/uploads/2014/09/3310489820.png
  [5]: http://phpgao.com
  [6]: https://developer.mozilla.org/en-US/docs/Security/MixedContent/How_to_fix_website_with_mixed_content
  [7]: http://webmasters.stackexchange.com/questions/30158/https-page-loading-non-https-images
  [8]: http://nginx.org/en/docs/http/ngx_http_spdy_module.html
  [9]: http://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature.html
  [10]: http://blog.sina.com.cn/s/blog_493e7af2010083y9nyifeng.com/blog/2011/08/what_is_a_digital_signature.html
  [11]: http://howtocn.org/nginx:nginx模块参考手册中文版:optionalhttpmodules:ssl
  [12]: http://www.lsproc.com/post/nginx-ssl-config/
  [13]: http://www.cnblogs.com/aLittleBitCool/archive/2011/09/22/2185418.html
  [14]: http://nginx.org/en/docs/http/configuring_https_servers.html