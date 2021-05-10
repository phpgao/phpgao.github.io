---
title: "微信公众号内容接口"
categories: [ "代码人生" ]
tags: [ "api","spider","wechat","sogou" ]
draft: false
slug: "wechat_public_user_api"
date: "2015-01-22 08:39:00"
url: "wechat_public_user_api.html"
---

今天在V2EX上发现了一个上搜索微信内容的好网站

[http://weixin.sogou.com/][1]

练习爬虫的同学有福了，没事儿干可以试试。


接口地址：

http://weixin.sogou.com/gzhjs

获取方式:

GET

参数:

1. cb 必须 固定值 sogou.weixin.gzhcb

2. openid 必须 公众号的唯一ID，可以通过搜索结果页面获得

3. page 可选 页码 默认第一页 每页最多十条记录

4. t 可选 请求发送的时间，目测没啥大用


最近貌似对openid加密了，等高人破解吧。


```
dmsYotdg%2F1V4oas03wIdducg7w5SL9M%2BYAL3GsHdR%2Fw9aG9Qzsh6XnA%2FUqKuQ66p8tRkK

oIWsFt4Dl6kREBsD_KrMA84ThiIA

wDssoe6gmmM8o%2FDMnpcWKuHwEPHxqeOZXzd%2FXytWShm4vQlImQmVes2pA7cYWfc%2FwudCK

oIWsFt5sM7wz7isNXkl01is9M834

IAsHogvgG8dho2KKG6sO2uI3QooM18Hx%2BZF7o7%2BjinhzOVx5t3EcYmhn93gQQgsYira4N

oIWsFt0fiD095kHlyHMIXEM7PrZc

M8sGoC2gu6ZRohqri5nKnuau%2FD8g0jkqKyfAW8cjgPLn1e3wwAZSEM%2FMsVhWFQqp7%2Bz%2B9

oIWsFtwFWRis8pbm2-hOgllnpZfw

6Ss7od5gsiLBoala%2BGkVduU4PRslLs6USIzFeWxJXDR4oyMBZNvWwNovVPSmZJ6GVI7PG

oIWsFt4UdPREjjItJo-JsJhoTjSU

Qqsoo2gg7EDuovYnPkpxGu22Hv1%2FLF9MkC4AtaAGVcP%2B49dhr5tmnOpMZFpMtNQfp%2BnRv

oIWsFt-abnxH6yhUGXNtgwhtsvS4
```

  [1]: http://weixin.sogou.com/