---
title: "git支持socks5和HTTP代理"
categories: [ "代码人生" ]
tags: [ "git" ]
draft: false
slug: "git_on_socks5"
date: "2015-12-10 07:30:00"
url: "git_on_socks5.html"
---

![git logo][1]

一直以来，老高都是用proxychains用的git，原来发现git本来就支持socks5代理!

<!--more-->


## 安装shadowsocks

```
curl https://bootstrap.pypa.io/get-pip.py | python
pip install shadowsocks

# 服务器端
ssserver -p 8088 -k passwd -m rc4-md5 -d start

# 客户机
sslocal -s 1.1.1.1 -p 8088 -k passwd -m rc4-md5 -d start
```

## 设置git代理

```
# socks5
git config --global http.proxy socks5://127.0.0.1:1080
git config --global https.proxy socks5://127.0.0.1:1080

# http
git config --global http.proxy https://127.0.0.1:61827
git config --global https.proxy https://127.0.0.1:61827
```


  [1]: https://blog.phpgao.com/usr/uploads/2015/12/3997949415.png