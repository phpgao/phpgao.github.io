---
title: "解决ngx_lua_waf总是提示参数错误的问题"
categories: [ "服务器技术" ]
tags: [ "nginx","lua" ]
draft: false
slug: "ngx_lua_waf_error_500"
date: "2015-05-25 05:45:00"
url: "ngx_lua_waf_error_500.html"
---

最近服务上了ngx_lua_waf以防御攻击，但是ngx_lua_waf的参数确实有些严格，导致自己写文章都被BAN，于是看了看源码，发现可以添加url白名单功能，这不就搞定了！


<!--more-->


## 确定URL

第一步要确定你要加入白名单的URL是什么，然后写一个通用正则试着匹配。

## 学习LUA

哈哈，骗你的，不需要学

## 改脚本

### 日志路径

修改hack日志路径为`/var/log/nginx/hack`

### 修改代码

具体地址在这里[init.lua line:84][1]

修改为

```lua
if ngxmatch(ngx.var.request_uri,rule,"isjo") then
```

### 添加url

在`/waf/wafconf/whiteurl`中加上你的url即可！

```
^/route/action\?_=\w*$
```


  [1]: https://github.com/loveshell/ngx_lua_waf/blob/ee2e656e5a5a4c6fe19740a7f98979aa68d5e5ba/init.lua#L84