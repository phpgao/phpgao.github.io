---
title: "MAC下快速安装PHP"
categories: [ "代码人生" ]
tags: [ "PHP","install" ]
draft: false
slug: "mac-install-php"
date: "2015-06-26 05:59:00"
url: "mac-install-php.html"
---

![PHP logo][1]

[请安装Homebrew先][2]

```
# 添加源
brew tap josegonzalez/homebrew-php

# 一键安装php各个版本
brew install php54 php54-mcrypt
brew install php55 php55-xdebug
brew install php56
```


  [1]: https://blog.phpgao.com/usr/uploads/2015/06/2558499356.png
  [2]: http://brew.sh/index_zh-cn.html