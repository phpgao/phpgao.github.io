---
title: "chrome再一次隐藏了http(s)和www"
categories: [ "代码人生" ]
tags: [ "chrome" ]
draft: false
slug: "chrome_hide_https_www"
date: "2019-08-10 09:50:00"
url: "chrome_hide_https_www.html"
---

Google又出来作恶了，虽然比上一次能好那么一点，只隐藏了www一个二级域名，话不多说，赶紧改回来！


![https://blog.phpgao.com][1]

<!--more-->


打开浏览器，在地址栏输入下面的地址，把三个选项都置为`disable`即可!

```
chrome://flags/#omnibox-ui-hide-steady-state-url-scheme


chrome://flags/#omnibox-ui-hide-steady-state-url-trivial-subdomains
chrome://flags/#omnibox-ui-hide-steady-state-url-path-query-and-ref
```


refer:

 - https://news.ycombinator.com/item?id=20626807


  [1]: https://blog.phpgao.com/usr/uploads/2019/08/751830664.png