---
title: "php不使用中间变量交换两个变量的值"
categories: [ "代码人生" ]
tags: [ "PHP","substitution","variables" ]
draft: false
slug: "php_substitution_of_variables"
date: "2014-08-13 08:35:00"
url: "php_substitution_of_variables.html"
---

方法一：

    $a = $a^$b;
    $b = $b^$a;
    $a = $a^$b;

这就是位运算带给我们的奇妙之处！（这种方法只适合于int型和string型，而且位数不能超过8位）

方法二：


    list($var1, $var2) = array($var2, $var1);

