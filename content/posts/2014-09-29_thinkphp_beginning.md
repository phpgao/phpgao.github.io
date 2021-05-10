---
title: "thinkphp框架解析1----只是一个开始"
categories: [ "代码人生" ]
tags: [  ]
draft: false
slug: "thinkphp_beginning"
date: "2014-09-29 01:07:00"
url: "thinkphp_beginning.html"
---

接上一篇[起源][1]

在`ThinkPHP/Library/Think/`下找到App.class.php，他就是应用程序类，他会见证整个应用从创建到消亡的整个过程。

上次我们分析到系统执行`App::run()`，下面开始分析此方法：


<!--more-->


    static public function run() {
        // 应用初始化标签
        Hook::listen('app_init');
        App::init();
        // 应用开始标签
        Hook::listen('app_begin');
        // Session初始化
        if(!IS_CLI){
            session(C('SESSION_OPTIONS'));
        }
        // 记录应用初始化时间
        G('initTime');
        App::exec();
        // 应用结束标签
        Hook::listen('app_end');
        return ;
    }

看起来很简单嘛！

1. 添加APP初始化钩子
2. 执行APP初始化
3. 判断如果是cli执行，载入`SESSION_OPTIONS`配置
4. 记录初始化时间
5. 执行APP
6. 添加APP结束钩子

其实你只看到了##The tip of the iceberg##，APP的初始化也是一个比较复杂的过程。

下面我们就铺开各个执行过程，看看到底发生了什么。

首先，系统调用了`Hook::listen`方法，事件名为##app_init##。关于更多的Hook使用方法，可以参考老高的[这篇文章][2]。


  [1]: https://blog.phpgao.com/thinkphp_init.html
  [2]: https://blog.phpgao.com/thinkphp_hook.html