---
title: "使用ss的多用户模式"
categories: [ "代码人生" ]
tags: [ "shadowsocks" ]
draft: false
slug: "ss-mutil_users"
date: "2015-11-13 05:53:00"
url: "ss-mutil_users.html"
---

多用户模式是什么？

就是使用一个配置文件，存放多个端口+密码的信息，如果你在共享你的ss，那么这个功能将十分实用。


<!--more-->


如果你的英文不错，可以直接参考[官方文档][1]


> 需要注意的是，目前只有`Python`和`Go`写的服务器端才支持此特性。

下面是官方给出的配置例子:

```json
{
    "server": "0.0.0.0",
    "port_password": {
        "8381": "foobar1",
        "8382": "foobar2",
        "8383": "foobar3",
        "8384": "foobar4"
    },
    "timeout": 300,
    "method": "aes-256-cfb"
}
```

运行的时候指定`-c config.json`即可！


ss还有一个运行时动态修改配置，[参考在此][2]


  [1]: https://github.com/shadowsocks/shadowsocks/wiki/Configure-Multiple-Users
  [2]: https://github.com/shadowsocks/shadowsocks/wiki/Manage-Multiple-Users