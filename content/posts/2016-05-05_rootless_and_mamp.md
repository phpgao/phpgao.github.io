---
title: "关闭OSX的rootless和修改MAMP的php.ini配置"
categories: [ "代码人生" ]
tags: [ "PHP","osx" ]
draft: false
slug: "rootless_and_mamp"
date: "2016-05-05 11:17:00"
url: "rootless_and_mamp.html"
---

记录一下最近遇到的问题


<!--more-->


## 关闭OSX的rootless

rootless机制可以在一定程度上保护系统安全，但是老高有需求必须关掉它，需要进入recovery模式，然后执行

```
csrutil disable
```

然后重启即可！

更多信息可以阅读[OS X 10.11中Rootless的实现与解释以及关闭方法][1]

## 修改MAMP的php.ini配置

MAMP的php配置是动态生成的，所以修改的方式不是直接改生效的php.iniwen'j

```
File -> Edit Template -> PHP -> PHP 5.xx -> php.ini
```


  [1]: http://tadaland.com/os-x-rootless.html