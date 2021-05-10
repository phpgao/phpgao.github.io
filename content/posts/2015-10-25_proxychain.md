---
title: "在MAC下使用proxychain"
categories: [ "代码人生" ]
tags: [ "proxy","proxychains" ]
draft: false
slug: "proxychain"
date: "2015-10-25 14:04:00"
url: "proxychain.html"
---

proxychain的功能很强大，让我们不需要使用export指定ssh的http代理就可以***了。

![proxychain][1]


<!--more-->


原版项目：[proxychains][2]
项目地址：[proxychains-ng][3]

> ps.ng == (new generation) 新一代的意思
> proxychains的运行原理是在程序运行时动态接管其与网络相关的库，如sockets。

如何安装：

```bash
brew install proxychains-ng

# ==> Downloading https://homebrew.bintray.com/bottles/proxychains-ng-4.10.yosemit
# Already downloaded: /Library/Caches/Homebrew/proxychains-ng-4.10.yosemite.bottle.tar.gz
# ==> Pouring proxychains-ng-4.10.yosemite.bottle.tar.gz
# /usr/local/Cellar/proxychains-ng/4.10: 8 files, 92K

# 注意与上面的路径相同 /usr/local/Cellar/proxychains-ng/4.10
# 下面的意思是把proxychains4放到系统路径，以后可以直接调用proxychains + 命令执行即可
ln -s /usr/local/Cellar/proxychains-ng/4.10/bin/proxychains4 /usr/local/bin/proxychains
```

使用方法

```
vi /usr/local/Cellar/proxychains-ng/4.10/etc/proxychains.conf

# 把最后一行改为
http 10.16.13.18 8080
# 或
# sock5 127.0.0.1 1080
```

以后运行名列前加上proxychains就可以零配置走代理了！

```bash
proxychains brew update
```

如果想让某个app直接使用到proxychains，可以这样

```
proxychains open -a Airy.app
```


  [1]: https://blog.phpgao.com/usr/uploads/2015/10/3772244129.png
  [2]: http://proxychains.sourceforge.net
  [3]: https://github.com/rofl0r/proxychains-ng