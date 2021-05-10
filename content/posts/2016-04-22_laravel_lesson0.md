---
title: "Laravel-非常规教程之0-引入篇"
categories: [ "代码人生" ]
tags: [ "lesson","laravel" ]
draft: false
slug: "laravel_lesson0"
date: "2016-04-22 05:50:00"
url: "laravel_lesson0.html"
---

![laravel logo][1]

hi!大家好。老高又开始折腾laravel了 :-D

(基于Laravel 5.2)

<!--more-->

## 写在前面

此系列文章主要是记(瞎)录(扯)一些老高在研究 **laravel 5.2** 时的感悟。主要受众应该是和老高一样喜欢折腾的家伙们吧！

常规教程在这里[Laravel 5.2 中文文档][2]，在此要十分感谢[Laravel 学院][3]！没有他laravel在国内也不会这么普及(老高)。当然不要忘了Laravel还有一个子框架叫[Luman][4]，这个`路人`框架也值得学习！

> ps.Laravel老高去年就开始关注了，因为太忙(懒)了，所以一直没有深入学习这个框架。不过不要紧，该来的还是会来的。有问题请直接留言，老高会及(偶)时(然)回复你的！

## 惯例代码

代码里常见的用法，还有闭包的用法要习惯！

```php
<?php
namespace App;

interface A
{
    public function get();
}

var_dump(A::class);//string(5) "App\A"
```

```bash
php -r "var_dump((1==1) ? :false);" // bool(true)
php -r "var_dump((1==2) ? :false);" // bool(false)
```

## 安装

5.1 - 5.2的变化不是很激进，安装方法没有变化，所以安装其实没什么好说的，具体看这里[Laravel 5.2  安装][5]。laravel严重依赖的PHP的包管理系统**Composer**，是好事也是坏事，但总归是好事！(我到底在说啥？)

Cons: 需要理解包管理等一些列基础知识，如[psr0-N][6](N目前已经到13了)、Namespace、自动加载类，理解起来会有一些门槛

Pros: 极大地提高了开发效率，让大家开发程序有了包的概念，简直不能再爽！

你可能还很需要[Packagist / Composer 中国全量镜像][7]以加速包下载。

> ps.不要忘了初始化key什么的

## 配置

配置也没什么多说，老高最喜欢这个维护功能，有没有苹果发布新产品时的官网的感觉！

![维护模式][8]

实现方式就是一句话而已！

```bash
php artisan down
```

## 基础

你需要理解请求、路由等概念，具体还是先看[官方文档吧][9]

![基础知识][10]

## artisan命令行工具

灵活运用artisan命令行工具，可以帮你提高100%的效率！不要怕这么多命令，多用就熟了。

```bash
php artisan

# 节选一点内容
Laravel Framework version 5.2.30

Usage:
  command [options] [arguments]

Options:
  -h, --help            Display this help message
  -q, --quiet           Do not output any message
  -V, --version         Display this application version
      --ansi            Force ANSI output
      --no-ansi         Disable ANSI output
  -n, --no-interaction  Do not ask any interactive question
      --env[=ENV]       The environment the command should run under.
  -v|vv|vvv, --verbose  Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug
```

一些关于缓存的命令

```
// Remove the compiled class file
php artisan clear-compiled
// Flush the application cache
php artisan cache:clear
// Create a route cache file for faster route registration
php artisan route:cache
// List all registered routes
php artisan route:list
// Remove the route cache file
php artisan route:clear
// Clear all compiled view files
php artisan view:clear

```


### 中间件

Laravel的中间件功能很有意思，为了加深理解，大家可以参考下图：

![onion.png][11]

上图来自[米嘉 gobuildweb@Goper_China_2016][12]

大家可以把一个web应用想成一颗洋葱，每个请求在进入货离开web系统时，都会通过Middleware的过滤，可以说有了Middleware，系统变得更加灵活！

抓一个官方的例子，就是[用户认证][13]这里，在判断一个进入`/home`路径的时候，会有一个名为`auth`的中间件做卡控，如果检测到没有登录，系统就会这次请求重定向到登录页或者自定义页面。

## 数据库

数据库方面laravel实在是太强大了，没错，老高说的就是`Eloquent ORM`！(ORM对业务开发来说简直是福利，老高觉得过早写死SQL属于过早优化，是不科学的开发方式)

听到了ORM老高真是一把鼻涕一把泪啊，好久都没有用过了ORM了，虽然手写SQL也有好处，但是这个在业务开发初期，在架构不稳定的情况下，直接写SQL会把数据库定死，将来如果要换数据库，光是重构SQL都要花好一阵子了。

> Eloquent可以很容易的被[剥离出去单独使用][14]。

> 这里抛砖引玉提一下，ORM这里常理来讲应该对应MVC中的M，意思是模型，用简单的话将，就是讲一张表映射成一个对象，这样理解是有误的，当业务复杂的时候，这样的定义就不在适用，需要引入一层新的业务层(名字随便)，为上传请求屏蔽下层的实现。以后再详细讲，在此希望能引发你的思考，[阅读更多][15]。

本节主要是铺垫，下一节咱们跟一下系统的生命周期！

提个问题，你知道 [symfony][16] 和 `laravel` 区别和联系吗？

![symfony][17]

>end


  [1]: https://blog.phpgao.com/usr/uploads/2016/04/2314951677.jpg
  [2]: http://laravelacademy.org/laravel-docs-5_2
  [3]: http://laravelacademy.org/
  [4]: http://lumen.golaravel.com/docs/
  [5]: http://laravelacademy.org/post/2720.html
  [6]: http://www.php-fig.org/psr/
  [7]: http://pkg.phpcomposer.com
  [8]: https://blog.phpgao.com/usr/uploads/2016/04/2164625356.jpg
  [9]: http://laravelacademy.org/laravel-docs-5_2
  [10]: https://blog.phpgao.com/usr/uploads/2016/04/1887242062.png
  [11]: https://blog.phpgao.com/usr/uploads/2016/04/2155603066.png
  [12]: https://github.com/gopherchina/conference/blob/master/2016/1.6%20%E7%B1%B3%E5%98%89%20gobuildweb.pdf
  [13]: http://laravelacademy.org/post/3074.html
  [14]: http://www.golaravel.com/post/zai-laravelwai-du-li-shi-yong-eloquent/
  [15]: https://www.zhihu.com/question/29181786
  [16]: https://symfony.com/
  [17]: https://blog.phpgao.com/usr/uploads/2016/04/1563904002.jpg