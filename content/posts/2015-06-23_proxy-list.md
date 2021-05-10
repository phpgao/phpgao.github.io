---
title: "代理网站列表"
categories: [ "福利" ]
tags: [ "http","proxy","socks" ]
draft: false
slug: "proxy-list"
date: "2015-06-23 09:44:00"
url: "proxy-list.html"
---

![快递 代理][1]

(图片来自互联网)

代理是个好东西！


<!--more-->


## 海外

[+ free-proxy-list][2]
[+ my-proxy][3]
[proxylists][4]
[+ sockslist][5]
[+ myiptest][6]
[+ proxyfire][7]
[+ proxytm][8]
[+ samair][9]

## 国内

[快代理][10]
[+ 纯真][11]
[+ 爬虫代理][12]

## 代理检查

[cybersyndrome][13]
[cybersyndrome 日本语][14]
[proxytm][15]

http和https代理可以使用下面的脚本测试

```python
import requests

proxies = {
    "http": "http://60.210.17.222:55336",
    "https": "http://60.210.17.222:55336"
}

r = requests.get('http://city.ip138.com/ip2city.asp', proxies=proxies, timeout=2)

r.encoding = 'gb2312'
print r.text
```

## 项目

[phpsocks5][16]  作者：[lehui99][17]


注1：带+号的项目支持socks代理！

注2：匿名代理的等级，有高度匿名(L1 Elite)、普通匿名(L2 Anony)和透明代理(L3 Trans)三种，其隐藏IP的能力由左至右依此递减，所以推荐优先使用高度匿名代理！

注3：此页面不定期更新


  [1]: https://blog.phpgao.com/usr/uploads/2015/06/4026837055.png
  [2]: http://free-proxy-list.net/
  [3]: http://www.my-proxy.com/free-proxy-list.html
  [4]: http://www.proxylists.net/
  [5]: http://sockslist.net/
  [6]: http://www.myiptest.com/staticpages/index.php/Free-SOCKS5-SOCKS4-Proxy-lists.html
  [7]: http://www.proxyfire.net/index.php?pageid=ProxyLists
  [8]: http://www.proxytm.com/index.html
  [9]: http://www.samair.ru/proxy/
  [10]: http://www.kuaidaili.com/free/
  [11]: http://www.cz88.net/proxy/
  [12]: http://pachong.org/socks.html
  [13]: http://www.cybersyndrome.net/pc.cgi
  [14]: http://www.cybersyndrome.net/env.cgi
  [15]: http://www.proxytm.com/environment-variables-checker.html
  [16]: https://code.google.com/p/phpsocks5/
  [17]: https://code.google.com/u/lehui99/