---
title: "Laravel-非常规教程之1-生命周期"
categories: [ "代码人生" ]
tags: [ "lesson","laravel" ]
draft: false
slug: "laravel_lesson1"
date: "2016-04-22 11:16:00"
url: "laravel_lesson1.html"
---

上一节老高零(瞎)散(扯)的讲了一下laravel的基本知识，不知道你现在搞清楚symfony vs laravel的关系了吗？其实没多大关系，l借用了s的组件，laravel的屏蔽了框架复杂的内部实现，让程序猿们注重业务的开发，而symfony的学习曲线很陡峭，需要你掌握框架的运行机制和各种组件的关系。

本节关注一次请求在Laravel中的生命周期！就是从接收到请求到返回请求整个过程中都发生了哪些比较重要的事件。

<!--more-->


## 入口

让我们先把目光移向程序的入口，index.php的内容如下

```
<?php
# 自动加载类
require __DIR__.'/../bootstrap/autoload.php';
# $app就是后面要提到的容器
$app = require_once __DIR__.'/../bootstrap/app.php';
# kernel 实现了 Illuminate\Foundation\Http\Kernel 接口
$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);

# 处理请求，返回处理响应类，其基类来自symfony，命名空间为 Symfony\Component\HttpFoundation
# 路径为 vendor/symfony/http-foundation/Response.php
$response = $kernel->handle(
    $request = Illuminate\Http\Request::capture()
);
# 调用send方法，返回header和content
$response->send();
# 完成收尾工作，其实最终调的是$app的terminate方法
$kernel->terminate($request, $response);
```

## app.php

略过自动加载，我们现在看看`bootstrap/app.php`文件。

```
<?php
# class Application extends Container implements ApplicationContract, HttpKernelInterface
# Application直接继承了Container，并实现了ApplicationContract, HttpKernelInterface两个契约(接口)
# 在初始化的时候，该类做了很多事情。老高稍后会分析
$app = new Illuminate\Foundation\Application(
    realpath(__DIR__.'/../')
);

// 注册一个单例Illuminate\Contracts\Http\Kernel::class的实现
$app->singleton(
    Illuminate\Contracts\Http\Kernel::class,
    App\Http\Kernel::class
);
// 注册一个单例Illuminate\Contracts\Console\Kernel::class的实现
$app->singleton(
    Illuminate\Contracts\Console\Kernel::class,
    App\Console\Kernel::class
);
// 注册一个单例ExceptionHandler::class的实现
$app->singleton(
    Illuminate\Contracts\Debug\ExceptionHandler::class,
    App\Exceptions\Handler::class
);

return $app;
```

singleton是单例的意思，在绑定时的逻辑和bind一样，只是多了一个置share的值为true。这个share值会影响make的时候的某一步逻辑，具体代码如下，针对singleton的实例化之后会被$app缓存起来，提高了性能，而通过bind方法绑定的类被实例化时每次返回的都是不同的对象。

```
        if ($this->isShared($abstract)) {
            $this->instances[$abstract] = $object;
        }
```

对bind和make的逻辑不清楚的同学可以参考一下[laravel的启动过程中**服务容器——laravel真正的核心**][1]一节。


  [1]: http://www.cnblogs.com/wish123/p/4756669.html#%E6%9C%8D%E5%8A%A1%E5%AE%B9%E5%99%A8----laravel%E7%9C%9F%E6%AD%A3%E7%9A%84%E6%A0%B8%E5%BF%83