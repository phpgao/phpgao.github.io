---
title: "PHP清除html格式"
categories: [ "代码人生" ]
tags: [ "PHP","html","clear","format" ]
draft: false
slug: "html_tag_clear"
date: "2014-05-18 10:14:00"
url: "html_tag_clear.html"
---

做采集的都知道，一般采集过来的内容难免会带有html标签，如果有太多的标签会影响之后的数据分析或提取，所以需要过滤掉！PHP已经为我们提供了很多清除html格式的方法了，下面就让老高介绍一下。

## strip_tags

strip_tags($str) 去掉 HTML 及 PHP 的标记
语法: string strip_tags(string str);
传回值: 字串
函式种类: 资料处理
内容说明 :
解析：本函式可去掉字串中包含的任何 HTML 及 PHP 的标记字串。若是字串的 HTML 及 PHP 标签原来就有错，例如少了大于的符号，则也会传回错误。这个函数和 `fgetss()` 有着相同的功能

例子

```php
echo strip_tags("Hello <b>world!</b>");
# Hello world!
```

## htmlspecialchars

这个函数把html中的标签转换为[html实体][1]，博客的代码展示就必须使用这个函数，要不贴出来的代码就会被执行了。
预定义的字符是：
& （和号） 成为 &
” （双引号） 成为 ”
‘ （单引号） 成为 ‘
< （小于） 成为 < > （大于） 成为 >

例子

```php
$new = htmlspecialchars("<a href='test'>Test</a>", ENT_QUOTES);
echo $new; 
# &lt;a href=&#039;test&#039;&gt;Test&lt;/a&gt;
# 如果需要展现<br>，那么浏览器解析HTML的时候会自动将他变为换行
# 但是通过htmlspecialchars就可以让< 变为 &#039;
```

> 与htmlspecialchars功能相反的函数是htmlspecialchars_decode，他会把HTML实体转化为字符！

## 后补函数

PHP去除html、css样式、js格式的方法很多，但发现，它们基本都有一个弊端：空格往往清除不了
经过不断的研究，最终找到了一个理想的去除html包括空格css样式、js 的PHP函数。

```php
$descclear = str_replace("\r","",$descclear);//过滤换行
$descclear = str_replace("\n","",$descclear);//过滤换行
$descclear = str_replace("\t","",$descclear);//过滤换行
$descclear = str_replace("\r\n","",$descclear);//过滤换行
$descclear = preg_replace("/\s+/", " ", $descclear);//过滤多余回车
$descclear = preg_replace("/<[ ]+/si","<",$descclear); //过滤<__("<"号后面带空格)
$descclear = preg_replace("/<\!--.*?-->/si","",$descclear); //过滤html注释
$descclear = preg_replace("/<(\!.*?)>/si","",$descclear); //过滤DOCTYPE
$descclear = preg_replace("/<(\/?html.*?)>/si","",$descclear); //过滤html标签
$descclear = preg_replace("/<(\/?head.*?)>/si","",$descclear); //过滤head标签
$descclear = preg_replace("/<(\/?meta.*?)>/si","",$descclear); //过滤meta标签
$descclear = preg_replace("/<(\/?body.*?)>/si","",$descclear); //过滤body标签
$descclear = preg_replace("/<(\/?link.*?)>/si","",$descclear); //过滤link标签
$descclear = preg_replace("/<(\/?form.*?)>/si","",$descclear); //过滤form标签
$descclear = preg_replace("/cookie/si","COOKIE",$descclear); //过滤COOKIE标签
$descclear = preg_replace("/<(applet.*?)>(.*?)<(\/applet.*?)>/si","",$descclear); //过滤applet标签
$descclear = preg_replace("/<(\/?applet.*?)>/si","",$descclear); //过滤applet标签
$descclear = preg_replace("/<(style.*?)>(.*?)<(\/style.*?)>/si","",$descclear); //过滤style标签
$descclear = preg_replace("/<(\/?style.*?)>/si","",$descclear); //过滤style标签
$descclear = preg_replace("/<(title.*?)>(.*?)<(\/title.*?)>/si","",$descclear); //过滤title标签
$descclear = preg_replace("/<(\/?title.*?)>/si","",$descclear); //过滤title标签
$descclear = preg_replace("/<(object.*?)>(.*?)<(\/object.*?)>/si","",$descclear); //过滤object标签
$descclear = preg_replace("/<(\/?objec.*?)>/si","",$descclear); //过滤object标签
$descclear = preg_replace("/<(noframes.*?)>(.*?)<(\/noframes.*?)>/si","",$descclear); //过滤noframes标签
$descclear = preg_replace("/<(\/?noframes.*?)>/si","",$descclear); //过滤noframes标签
$descclear = preg_replace("/<(i?frame.*?)>(.*?)<(\/i?frame.*?)>/si","",$descclear); //过滤frame标签
$descclear = preg_replace("/<(\/?i?frame.*?)>/si","",$descclear); //过滤frame标签
$descclear = preg_replace("/<(script.*?)>(.*?)<(\/script.*?)>/si","",$descclear); //过滤script标签
$descclear = preg_replace("/<(\/?script.*?)>/si","",$descclear); //过滤script标签
$descclear = preg_replace("/javascript/si","Javascript",$descclear); //过滤script标签
$descclear = preg_replace("/vbscript/si","Vbscript",$descclear); //过滤script标签
$descclear = preg_replace("/on([a-z]+)\s*=/si","On\\1=",$descclear); //过滤script标签
$descclear = preg_replace("/&#/si","&＃",$descclear); //过滤script标签，如javAsCript:alert();
//使用正则替换
$pat = "/<(\/?)(script|i?frame|style|html|body|li|i|map|title|img|link|span|u|font|table|tr|b|marquee|td|strong|div|a|meta|\?|\%)([^>]*?)>/isU";
$descclear = preg_replace($pat,"",$descclear);
```

## 总结

采集这个东西说简单很简单，但说难真的很难。一旦遇到错误，就会让人很抓狂！

想要成为采集高手，你不仅需要了解从一个计算机发出的基于TCP的HTTP请求到最终得到请求的文件的整个过程，而且能够使用一系列的工具来协助你跟踪数据的去处，同时需要考虑你写出的采集任务的效率！

如果你需要采集twitter或者Facebook的数据，可以参考[使用shadowsocks轻松搭建FQ环境][2]


  [1]: http://www.w3school.com.cn/tags/html_ref_entities.html
  [2]: https://blog.phpgao.com/shadowsocks_on_linux.html