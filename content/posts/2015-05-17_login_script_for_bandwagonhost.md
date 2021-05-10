---
title: "模拟登录脚本之搬瓦工bandwagonhost"
categories: [ "代码人生" ]
tags: [ "python","login","bandwagonhost" ]
draft: false
slug: "login_script_for_bandwagonhost"
date: "2015-05-17 13:15:00"
url: "login_script_for_bandwagonhost.html"
---

老早写的，都忘了当初想干啥了。。。。

半成品，留个坑，待填。

```python
#!/usr/bin/env python
# encoding: utf-8

import cookielib
import requests


def http_send(url, post_data='', **kwargs):
    cookie_handler = cookielib.MozillaCookieJar('cookie.txt')
    try:
        cookie_handler.load(ignore_discard=1)
    except cookielib.LoadError, e:
        print e + "new cookie file"

    headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/38.0.2125.122 Safari/537.36'}
    if post_data:
        req = requests.post(url, data=post_data, cookies=cookie_handler)
    else:
        req = requests.get(url)

    print req.headers

    for c in req.cookies:
        cookie_handler.set_cookie(c)
        cookie_handler.save(ignore_discard=1)
    return req.content


if __name__ == '__main__':
    # init cookie
    print http_send('http://localhost/clientarea.php', {"A": 1})

```




```python
#!/usr/bin/env python
# encoding: utf-8

import cookielib
import requests
from bs4 import BeautifulSoup
import logging
import logging.handlers
import os
import re


logging.basicConfig(filename=os.path.join(os.getcwd(), 'log.txt'), level=logging.DEBUG)

s = requests.session()


def main():
    do_login()
    scan_list()
    check()


def do_login():
    global headers, username, password
    # get token
    # token_html = s.get(login_url).content
    headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 '
                             '(KHTML, like Gecko) Chrome/38.0.2125.122 Safari/537.36'}

    token_html = open('C:\Users\Administrator\Desktop\index.html').read()
    token = find_token(token_html)
    post_data = {'token': token, 'username': username, 'password': password}
    s.post(affiliates_url, post_data)
    print s.content


def find_token(html):
    g = re.findall('name="token"\svalue="(\w+)"\s/>', html)
    if g:
        return g[0]
    else:
        log("Could not find token value!")
        raise Exception('Could not find token value')


def scan_list():
    print 111


def check():
    print 111


def log(msg):
    logging.debug(msg)


if __name__ == '__main__':
    # 初始化参数
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/38.0.2125.122 Safari/537.36'}
    login_url = "https://bandwagonhost.com/clientarea.php"
    affiliates_url = "https://bandwagonhost.com/affiliates.php"
    username = 1111
    password = 2222
    main()
```