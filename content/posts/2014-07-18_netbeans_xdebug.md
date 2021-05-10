---
title: "netbeans开启xdebug"
categories: [ "代码人生" ]
tags: [ "PHP","xdebug","netbeans" ]
draft: false
slug: "netbeans_xdebug"
date: "2014-07-18 04:04:00"
url: "netbeans_xdebug.html"
---

一直在用everedit做开发，今天开始高大上，用一下netbeans

# 安装

 - 在[netbeans下载页面][1]找到PHP版的netbeans，下载下来并安装，表忘了JRE也要装的

 - 在[xdebug下载页面][2]下载对应PHP版本的xdebug，然后解压至php目录下的ext目录下，同时新建d:\xdebug文件夹

# 配置

## PHP.INI

在phpinfo()页面中找到INI文件，在文件最后加入以下代码
    
    ;注意：我们的版本号可能不同
    zend_extension="ext\php_xdebug-2.2.5-5.4-vc9.dll" 
    [Xdebug]
    ;是否开启自动跟踪
    xdebug.auto_trace= On
    ;是否开启异常跟踪
    xdebug.show_exception_trace= On
    ;是否开启远程调试自动启动
    xdebug.remote_autostart= On
    ;是否开启远程调试
    xdebug.remote_enable= On
    ;允许调试的客户端IP
    ;xdebug.remote_host= 192.168.1.1
    ;远程调试的端口（默认9000）
    xdebug.remote_port=9000
    ;调试插件dbgp
    xdebug.remote_handler=dbgp
    ;是否收集变量
    xdebug.collect_vars= On
    ;是否收集返回值
    xdebug.collect_return= On
    ;是否收集参数
    xdebug.collect_params= On
    ;跟踪输出路径
    xdebug.trace_output_dir="d:\xdebug"
    ;是否开启调试内容
    xdebug.profiler_enable=On
    ;调试输出路径
    xdebug.profiler_output_dir="d:\xdebug"
    ;调试的域名
    xdebug.remote_host=localhost

修改以后重启服务器，看看有没有出现xdebug，如果出现了，那就成功了一大半

## netbeans

设置的地方在tool->option里找到debug标签，在这里可以修改PHP.EXE的路径，还有调试的工具和端口号等。

## 注意

 - 如果你用的远程调试，务必打开`xdebug.remote_enable`
 - 如果你用的域名调试，那么请修改`xdebug.remote_host`为你的域名，不要忘了加引号，不用带http://
 - 如果总是显示等待xdebug，那么就换一个web浏览器试试，在tool->option->general里可以找到
 - 如果还不行，请留言，老高和你一起搞！

# 调试

哈哈，这还用讲吗？上个图

![netbeans开启xdebug][3]


  [1]: https://netbeans.org/downloads/index.html
  [2]: http://www.xdebug.org/download.php
  [3]: https://blog.phpgao.com/usr/uploads/2014/07/2952762931.jpg