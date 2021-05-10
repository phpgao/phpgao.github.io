---
title: "让-PHPSTORM-更智能的支持-Laravel"
categories: [ "代码人生" ]
tags: [ "laravel" ]
draft: false
slug: "laravel_on_phpstorm"
date: "2016-05-13 07:39:00"
url: "laravel_on_phpstorm.html"
---

PHPSTORM已经很智能了，但是laravel更狡猾，比如如下的代码就没法实现动态跟踪，因为此处使用到了Laravel的Facades。


<!--more-->


```php
DB::table('users')->insert([
    'name' => str_random(10),
    'email' => str_random(10).'@gmail.com',
    'password' => bcrypt('secret'),
]);
```

于是就有了今天要说明的工具，[barryvdh/laravel-ide-helper][1]


## 安装

```
composer require barryvdh/laravel-ide-helper

# add to config/app.php
Barryvdh\LaravelIdeHelper\IdeHelperServiceProvider::class,
```



## 使用

```
php artisan ide-helper:generate - 为Facades生成PHPDoc
php artisan ide-helper:models - phpDocs for 模型
php artisan ide-helper:meta - 生成 PhpStorm Meta文件
```

是不是很好用呢？

  [1]: https://github.com/barryvdh/laravel-ide-helper