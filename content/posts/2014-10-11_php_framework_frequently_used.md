---
title: "PHP的框架常用函数"
categories: [ "代码人生" ]
tags: [ "framework","handler","autoload" ]
draft: false
slug: "php_framework_frequently_used"
date: "2014-10-11 07:58:00"
url: "php_framework_frequently_used.html"
---

spl_autoload_register、set_exception_handler和set_error_handler、get_include_path等。

## spl_autoload_register

与`__autoload`函数一样，但是更灵活。他的功能是当程序找不到当前正在使用的类时，在报错前，会调用此函数，如果找到了，就继续工作。

## set_exception_handler

接管了原本的异常处理，可以隐藏或者美化输出，可以用`throw new Exception`触发。

## set_error_handler

同上，接管了错误处理。可以用`trigger_error`触发。
