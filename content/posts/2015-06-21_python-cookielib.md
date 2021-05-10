---
title: "python中的cookielib的使用方法"
categories: [ "代码人生" ]
tags: [ "python","cookie","爬虫","cookielib" ]
draft: false
slug: "python-cookielib"
date: "2015-06-21 11:18:00"
url: "python-cookielib.html"
---

![cookie][1]

(图片来自互联网)

[cookielib][2]是一个自动处理cookies的模块，如果我们在使用爬虫等技术的时候需要保存cookie，那么cookielib会让你事半功倍！他最常见的搭档模块就是python下的urllib和request。

但是老高在使用cookielib的时候总是碰到这样那样的问题，在查看cookielib的源码后，有所感悟。


<!--more-->


## 核心类

### Cookie

该类实现了Netscape and RFC 2965 cookies定义的cookie标准，基本可以理解为某一条cookie数据。

部分代码如下，很多属性是不是很眼熟？

```python
        self.domain_initial_dot = domain_initial_dot
        self.path = path
        self.path_specified = path_specified
        self.secure = secure
        self.expires = expires
        self.discard = discard
        self.comment = comment
        self.comment_url = comment_url
        self.rfc2109 = rfc2109
```

### CookiePolicy

该类的主要功能是收发cookie，即确保正确的cookie发往对应的域名，反之一样。

### DefaultCookiePolicy

该类实现了CookiePolicy的接口。

### CookieJar

CookieJar是cookie的集合，可以包含有很多Cookie类，是我们的主要操作对象。里面有一系列的方法可以支持更加细致的操作！

### FileCookieJar

该类继承自CookieJar，CookieJar只是在内存中完成自己的生命周期，FileCookieJar的子类能够实现数据持久化，定义了save、load、revert三个接口。

### MozillaCookieJar & LWPCookieJar

两个实现类，继承关系如下：

![MozillaCookieJar & LWPCookieJar][3]

## 使用

### 简单例子

一段简单的使用代码

```python
#!/usr/bin/env python
# encoding: utf-8


import requests
import cookielib


url = 'http://www.baidu.com/'
jar = cookielib.LWPCookieJar('cookie.txt')

# 试着载入cookie
# 提一个问题，为什么要加入ignore_discard属性？
try:
    jar.load(ignore_discard=True)
except:
    pass

# 建立一个session
s = requests.Session()

# 设定 headers与cookies
s.headers = {'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_7_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.93 Safari/537.36'}
s.cookies = jar

# 访问url
r = s.get(url)

# 持久化cookie
jar.save(ignore_discard=True)

# 打印cookie
for item in jar:
    print "cookie name : %s ---- value: %s" % (item.name, item.value)
```

我们得到如下cookie

```
cat cookie.txt

#LWP-Cookies-2.0
Set-Cookie3: BAIDUID="2F5340B39928231AA09353CDAE3DA14D:FG=1"; path="/"; domain=".baidu.com"; path_spec; domain_dot; expires="2083-07-09 16:27:51Z"; version=0
Set-Cookie3: BIDUPSID=2F5340B39928231AA09353CDAE3DA14D; path="/"; domain=".baidu.com"; path_spec; domain_dot; expires="2083-07-09 16:27:51Z"; version=0
Set-Cookie3: H_PS_PSSID=14872_1457_14412_14509_14444_12826_10812_14430_12868_14871_12723_14962_14919_14902_15384_12095_13937_15963; path="/"; domain=".baidu.com"; path_spec; domain_dot; discard; version=0
Set-Cookie3: PSTM=1434892424; path="/"; domain=".baidu.com"; path_spec; domain_dot; expires="2083-07-09 16:27:51Z"; version=0
Set-Cookie3: BDSVRTM=0; path="/"; domain="www.baidu.com"; path_spec; discard; version=0
Set-Cookie3: BD_HOME=0; path="/"; domain="www.baidu.com"; path_spec; discard; version=0
```

### 生成一个Cookie类

我们由Cookie的定义可以简单的生成一条cookie

```python
import cookielib

class Cookie:
    def __init__(self, version, name, value,
                 port, port_specified,
                 domain, domain_specified, domain_initial_dot,
                 path, path_specified,
                 secure,
                 expires,
                 discard,
                 comment,
                 comment_url,
                 rest,
                 rfc2109=False,
                 ):
         .....

# 初始化一个cookie

def createCookie(name, value, domain, expires=None):
    return cookielib.Cookie(
        version=None,
        name=name,
        value=value,
        port='80',
        port_specified=True,
        domain=domain,
        domain_specified=True,
        domain_initial_dot=False,
        path='/',
        path_specified=True,
        secure=False,
        expires=expires,
        discard=False,
        comment=None,
        comment_url=None,
        rest=None,
        rfc2109=False
    )


new_cookie = createCookie('phpgao', 'laogao', 'www.phpgao.com', '1434977736')

# 加入到现有cookies

mycookie = cookielib.CookieJar()
mycookie.set_cookie(new_cookie)
```

## 扩展阅读

官方文档

[cookielib][4]

[cookie][5]


  [1]: https://blog.phpgao.com/usr/uploads/2015/06/2635542683.jpg
  [2]: https://docs.python.org/2/library/cookielib.html
  [3]: https://blog.phpgao.com/usr/uploads/2015/06/2416418833.png
  [4]: https://docs.python.org/2/library/cookielib.html
  [5]: https://docs.python.org/2/library/cookie.html