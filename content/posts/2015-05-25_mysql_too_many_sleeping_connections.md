---
title: "解决MySQL中Sleep连接过多的问题"
categories: [ "服务器技术" ]
tags: [ "mysql","sleep" ]
draft: false
slug: "mysql_too_many_sleeping_connections"
date: "2015-05-25 12:19:28"
url: "mysql_too_many_sleeping_connections.html"
---

有时候你在mysql中运行`SHOW PROCESSLIST;`后会发现数据库中有很多这样的进程：

![MySQL中Sleep连接过多][1]


<!--more-->


那么造成sleep的原因，有三个，下面是mysql手册给出的解释:

1.客户端程序在退出之前没有调用mysql_close().[写程序的疏忽，或者数据库的db类库没有自动关闭每次的连接。。。]
2.客户端sleep的时间在wait_timeout或interactive_timeout规定的秒内没有发出任何请求到服务器. [类似常连，类似于不完整的tcp ip协议构造，服务端一直认为客户端仍然存在（有可能客户端已经断掉了）]
3.客户端程序在结束之前向服务器发送了请求还没得到返回结果就结束掉了. [参看：tcp ip协议的三次握手]


----------


解决的方法也很简单

在配置文件中加入

```
[mysqld]

wait_timeout=10
```

或者

```
mysql> set global wait_timeout=10;
```


参考

[http://blog.zhanxb.com/post/456/][2]


  [1]: https://blog.phpgao.com/usr/uploads/2015/05/1013249303.jpg
  [2]: http://blog.zhanxb.com/post/456/