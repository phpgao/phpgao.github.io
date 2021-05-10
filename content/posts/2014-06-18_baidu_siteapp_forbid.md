---
title: "如何防止你的网站被百度转码"
categories: [ "性能优化" ]
tags: [ "baidu","siteapp","forbid" ]
draft: false
slug: "baidu_siteapp_forbid"
date: "2014-06-18 03:40:00"
url: "baidu_siteapp_forbid.html"
---

现在只要新一点的HTML页面都会支持响应式，但是在移动端百度会把本来整齐的页面强行转码成他所需要的格式来呈现，简直是丑爆了！而且URL也会变的很长，简直对不起我的伪静态！！最近UC（也可以说是阿里，更可以说是马大帅）也开始搞这个事儿了   <!--more-->

## 针对百度 

如自己的站点不希望被转码，可该页面上添加此协议，当用户通过移动搜索进入第三方网站时，会直接进入原网页。 

```html
<!--禁止百度快照-->
<meta name="robots" content="noarchive">
<!--禁止百度转码-->
<meta http-equiv="Cache-Control" content="no-transform"/>
<meta http-equiv="Cache-Control" content="no-siteapp"/>
```

如果想让搜索引擎自动引导用户，跳转至你已经制作好的WAP页面，那可以在head中加入 

```bash
<link rel="alternate" type="application/vnd.wap.xhtml+xml" media="handheld" href="http://m.phpgao.com/"/>
```
    
这样就告诉百度如果是移动端来访，自动跳转至WAP页面即可！ 

## 针对UC 

看完霸气的UC的转码声明，表示无解 

[UC的转码声明][1]

大体的意思是老子就是要转你，你有本事告我呀！ （ps.UC貌似最近把默认搜索引擎改成自己的神马搜索） 

> 神马搜索是UC和阿里2013年已经成立合资公司推出的移动搜索引擎。

参考：[http://www.chinaz.com/web/2014/0113/335167.shtml][2]


  [1]: http://tc.uc.cn/?src=http%3A%2F%2Fuc.gre%2Fgre%2Fucshare%2Fhtml%2Fshengming.html
  [2]: http://www.chinaz.com/web/2014/0113/335167.shtml