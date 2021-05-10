---
title: "pip下使用代理安装包"
categories: [ "代码人生" ]
tags: [ "pip","proxy" ]
draft: false
slug: "pip_via_proxy"
date: "2015-09-09 09:09:00"
url: "pip_via_proxy.html"
---

一句话代码


```python
pip install --proxy x.x.x.x:8080 requsets
pip install --proxy x.x.x.x:8080 --upgrade pip
```

帮助文件

```
pip -h


--proxy <proxy>             Specify a proxy in the form
                              [user:passwd@]proxy.server:port.
```