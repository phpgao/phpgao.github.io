---
title: "http库测试工具---httpbin"
categories: [ "代码人生" ]
tags: [ "http","python","pip","httpbin","gunicorn","restful" ]
draft: false
slug: "how-to-httpbin"
date: "2015-06-27 11:57:00"
url: "how-to-httpbin.html"
---

![http][1]

(图片来自互联网)

> 如果一个人想学习爬虫技术，我会首先推荐他学会使用httpbin！

httpbin([官网][2]|[github][3])是一个很不错测试工具，你可以放心大胆的**黑**他，而不用担心他报复你。他有点像一个蜜罐，时刻等待着你的光临，然后根据你的请求，给你返回你想要的东西

<!--more-->

## 相同推荐

类似这种能够很方便调试接口的还有很多，但是无疑还是httpbin最好用。

[requestb][4]

[putsreq][5]

[ttpresponder][6]

[runscope][7]

## 如何使用

使用你擅长的语言，与它的接口互动。

## 接口

[接口列表][8]

<div class="table-responsive">
<table class="table table-bordered"><tbody valign="top"><tr><td>Endpoint</td><td>Description</td></tr><tr><td><a href="http://httpbin.org/">/</a></td><td>This page.</td></tr><tr><td><a href="http://httpbin.org/ip">/ip</a></td><td>Returns Origin IP.</td></tr><tr><td><a href="http://httpbin.org/user-agent">/user-agent</a></td><td>Returns user-agent.</td></tr><tr><td><a href="http://httpbin.org/headers">/headers</a></td><td>Returns header dict.</td></tr><tr><td><a href="http://httpbin.org/get">/get</a></td><td>Returns GET data.</td></tr><tr><td>/post</td><td>Returns POST data.</td></tr><tr><td>/patch</td><td>Returns PATCH data.</td></tr><tr><td>/put</td><td>Returns PUT data.</td></tr><tr><td>/delete</td><td>Returns DELETE data</td></tr><tr><td><a href="http://httpbin.org/gzip">/gzip</a></td><td>Returns gzip-encoded data.</td></tr><tr><td><a href="http://httpbin.org/deflate">/deflate</a></td><td>Returns deflate-encoded data.</td></tr><tr><td><a href="http://httpbin.org/status/418">/status/:code</a></td><td>Returns given HTTP Status code.</td></tr><tr><td><a href="http://httpbin.org/response-headers?Content-Type=text/plain;%20charset=UTF-8&amp;Server=httpbin">/response-headers</a></td><td>Returns given response headers.</td></tr><tr><td><a href="http://httpbin.org/redirect/6">/redirect/:n</a></td><td>302 Redirects <em>n</em> times.</td></tr><tr><td><a href="http://httpbin.org/redirect-to?url=http://example.com/">/redirect-to?url=foo</a></td><td>302 Redirects to the <em>foo</em> URL.</td></tr><tr><td><a href="http://httpbin.org/relative-redirect/6">/relative-redirect/:n</a></td><td>302 Relative redirects <em>n</em> times.</td></tr><tr><td><a href="http://httpbin.org/cookies">/cookies</a></td><td>Returns cookie data.</td></tr><tr><td><a href="http://httpbin.org/cookies/set?k1=v1&amp;k2=v2">/cookies/set?name=value</a></td><td>Sets one or more simple cookies.</td></tr><tr><td><a href="http://httpbin.org/cookies/delete?k1&amp;k2">/cookies/delete?name</a></td><td>Deletes one or more simple cookies.</td></tr><tr><td><a href="http://httpbin.org/basic-auth/user/passwd">/basic-auth/:user/:passwd</a></td><td>Challenges HTTPBasic Auth.</td></tr><tr><td><a href="http://httpbin.org/hidden-basic-auth/user/passwd">/hidden-basic-auth/:user/:passwd</a></td><td>404'd BasicAuth.</td></tr><tr><td><a href="http://httpbin.org/digest-auth/auth/user/passwd">/digest-auth/:qop/:user/:passwd</a></td><td>Challenges HTTP Digest Auth.</td></tr><tr><td><a href="http://httpbin.org/stream/20">/stream/:n</a></td><td>Streams <em>n</em> – 100 lines.</td></tr><tr><td><a href="http://httpbin.org/delay/3">/delay/:n</a></td><td>Delays responding for <em>n</em> – 10 seconds.</td></tr><tr><td><a href="http://httpbin.org/drip?numbytes=5&amp;duration=5&amp;code=200">/drip</a></td><td>Drips data over a duration after an optional initial delay, then (optionally) returns with the given status code.</td></tr><tr><td><a href="http://httpbin.org/range/1024">/range/:n</a></td><td>Streams <em>n</em> bytes, and allows specifying a <em>Range</em> header to select a subset of the data. Accepts a <em>chunk_size</em> and request <em>duration</em> parameter.</td></tr><tr><td><a href="http://httpbin.org/html">/html</a></td><td>Renders an HTML Page.</td></tr><tr><td><a href="http://httpbin.org/robots.txt">/robots.txt</a></td><td>Returns some robots.txt rules.</td></tr><tr><td><a href="http://httpbin.org/deny">/deny</a></td><td>Denied by robots.txt file.</td></tr><tr><td><a href="http://httpbin.org/cache">/cache</a></td><td>Returns 200 unless an If-Modified-Since or If-None-Match header is provided, when it returns a 304.</td></tr><tr><td><a href="http://httpbin.org/cache/60">/cache/:n</a></td><td>Sets a Cache-Control header for <em>n</em> seconds.</td></tr><tr><td><a href="http://httpbin.org/bytes/1024">/bytes/:n</a></td><td>Generates <em>n</em> random bytes of binary data, accepts optional <em>seed</em> integer parameter.</td></tr><tr><td><a href="http://httpbin.org/stream-bytes/1024">/stream-bytes/:n</a></td><td>Streams <em>n</em> random bytes of binary data, accepts optional <em>seed</em> and <em>chunk_size</em> integer parameters.</td></tr><tr><td><a href="http://httpbin.org/links/10">/links/:n</a></td><td>Returns page containing <em>n</em> HTML links.</td></tr><tr><td><a href="http://httpbin.org/forms/post">/forms/post</a></td><td>HTML form that submits to <em>/post</em></td></tr><tr><td><a href="http://httpbin.org/xml">/xml</a></td><td>Returns some XML</td></tr><tr><td><a href="http://httpbin.org/encoding/utf8">/encoding/utf8</a></td><td>Returns page containing UTF-8 data.</td></tr></tbody></table></div>

## 简单例子

```python
import requests
s = requests.Session()

print s.get('http://httpbin.org/ip').text

print s.get('http://httpbin.org/get').json()

print s.post('http://httpbin.org/post', {'key':'value'},headers={'user-agent':'LAOGAO'}).text

print s.get('http://httpbin.org/status/404').status_code

print s.get('http://httpbin.org/html').text

print s.get('http://httpbin.org/deny').text
```

## 技巧

httpbin给我们提供了很多HTTP基本的操作，如果你的APP组问你要接口，你手头又很忙，怎么办？当然是用httpbin按照接口文档先给他们快速写几个丢过去。

## 部署

httpbin使用flask编写，可以参考[使用nginx + uWSGI运行flask程序][9]，不过在此老高又学会了一种新的方法 -- 使用gunicorn运行flask。

> gunicorn又是一个 Python WSGI HTTP Server for UNIX!

```bash
# 如果已经安装PIP，请忽略此步骤
cd ~ && wget -O - "https://bootstrap.pypa.io/get-pip.py" | python

# 安装httpbin
pip install httpbin

# 安装服务器软件gunicorn
pip install gunicorn

# 运行httpbin，在8000端口
gunicorn -b :8000 httpbin:app

# [2015-06-27 03:16:52 +0000] [18568] [INFO] Starting gunicorn 19.3.0
# [2015-06-27 03:16:52 +0000] [18568] [INFO] Listening at: http://0.0.0.0:8000 (18568)
# [2015-06-27 03:16:52 +0000] [18568] [INFO] Using worker: sync
# [2015-06-27 03:16:52 +0000] [18573] [INFO] Booting worker with pid: 18573
```

接下来访问你的`IP:8000`，不出意外，你就能看到与官网一样的功能了！

![httpbin on localhost][10]

**使用文档**

[gunicorn文档][11]

[pip文档][12]

## 使用PHPSTORM RESTful调试

在 *Tools | Test RESTful* 中可以找到Test RESTful工具，可以方便的调试接口。

![RESTful][13]


  [1]: https://blog.phpgao.com/usr/uploads/2015/06/727407702.jpg
  [2]: http://httpbin.org
  [3]: https://github.com/Runscope/httpbin
  [4]: http://requestb.in
  [5]: http://putsreq.com
  [6]: http://httpresponder.com
  [7]: https://www.runscope.com
  [8]: https://github.com/Runscope/httpbin#endpoints
  [9]: https://blog.phpgao.com/nginx_uwsgi_flask.html
  [10]: https://blog.phpgao.com/usr/uploads/2015/06/2402402771.png
  [11]: http://docs.gunicorn.org/en/latest/index.html
  [12]: http://pip.readthedocs.org/en/latest/index.html
  [13]: https://blog.phpgao.com/usr/uploads/2015/06/4153390953.png