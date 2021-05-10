---
title: "修复nginx空主机头的问题"
categories: [ "服务器技术" ]
tags: [  ]
draft: false
slug: "nignx_empty_host"
date: "2018-12-05 16:41:00"
url: "nignx_empty_host.html"
---

老高最近在整理服务器的nginx配置，但是整理好之后有一个问题，就是访问一个没有绑定的域名（已解析）的时候，会自动跳转到blog.phpgao.com。为了解决这个问题，查了很多资料，发现此文档[Nginx如何处理一个请求][1]，问题解决。


<!--more-->


总结一下：

一个http请求一般都会带上host，也就是传说中的主机名，比如blog.phpgao.com，对于phpgao.com来说，就是phpgao.com域下的blog主机的意思（其实blog不是真实存在的，而是虚拟主机）。

但是如果我们在配置web服务器的时候，如果只是简单的把需要映射的域名设置好，会造成一个问题，如果我作为攻击者，把自己的域名解析到别人的服务器ip，后果就是大量的请求把被人的机器打垮。

解决问题的办法就是需要加入一个空主机头，绑定80和443端口，也就是之前文章中提到的defalut_server字段。

也就是在nginx的配置文件中加入

```nginx
server {
    listen 80 default;
    server_name _;
    return 500;
}

server {
    listen 443 default;
    server_name _;
    # 证书配置段
    return 500;
}
```

  [1]: https://tengine.taobao.org/nginx_docs/cn/docs/http/request_processing.html#how_to_prevent_undefined_server_names