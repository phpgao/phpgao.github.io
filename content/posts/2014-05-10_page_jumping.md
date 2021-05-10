---
title: "网页跳转方法集合"
categories: [ "性能优化" ]
tags: [ "redirect" ]
draft: false
slug: "page_jumping"
date: "2014-05-10 01:23:00"
url: "page_jumping.html"
---

有些网站经常要用到跳转，比如商务通啊，支付宝啊，下面是我总结的几种方法，包括HTML,JS,PHP 

## PHP的header()

    <?php
    //重定向浏览器
    header("Location: http://www.phpgao.com");
    //确保重定向后，后续代码不会被执行
    exit();//or die;
    ?>

## Meta标签

    
    </head>
    <body>
    页面只停留一秒……
    </body>
    </html>   

## **JS的location.href**

    //注：此方法容易被拦截</span>**
    
    <script>
    url='http://www.phpgao.com';
    window.open(url,'_self'); //更改'_self' 就可以实现跳转限制原窗口还是父窗口,子窗口或者新窗口
    </script>
