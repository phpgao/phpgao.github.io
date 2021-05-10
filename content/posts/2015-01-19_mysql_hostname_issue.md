---
title: "mysql中localhost和127.0.0.1的区别"
categories: [ "服务器技术" ]
tags: [ "mysql","localhost","pipes" ]
draft: false
slug: "mysql_hostname_issue"
date: "2015-01-19 05:18:00"
url: "mysql_hostname_issue.html"
---

mysql的默认的root用户会有很多行，自习观察后你就会发现每行的用户名或密码可能相同，但是host一定不同，host是登陆用户的主机名，也就是说，'localhost','127.0.0.1','phpgao.local','%'都算**不同**的用户！

理解了这一点后，那么我的问题就附上水面了！

有些TX经常会遇到这个问题：

> 使用PHP连接mysql数据库，使用localhost作为主机名总是连接失败，但是使用'127.0.0.1'就可以顺利连接，这到底是为什么？

> mysql中HOST为localhost和127.0.0.1到底有什么区别？

经过一番搜索，老高总结如下：

使用到的命令

```bash
mysql>status;
mysql>show grants;
```

1. 类Unix系统下，如果不使用-h指定主机名或者使用了localhost，那么会使用unix domain socket与mysql服务器沟通，比TCP/IP快一些！所以你想使用TCP/IP协议，请将host指定为'127.0.0.1'。

2. PHP连接mysql如果使用'localhost'发生问题，首先可以明确的是PHP会试着使用unix domain socket与服务器连接，所以请检查php.ini中`mysql.default_socket = /var/mysql/mysql.sock`是否配置正确。

3. 如果想要明确连接方式，可以再配置文件中显式声明

```
protocol=tcp
```

4. 在mysql的官方文档中解释道:如果mysql在win上跑，如果系统开启了--enable-named-pipe，然后访问服务器的时候没有指定hostname，那么mysql客户端会以pipe为优先连接，如果连接失败，那么再会去尝试使用TCP/IP去连接。你可以指定hostname为`.`在win下强制使用pipes。

> If the MySQL server is running on Windows, you can connect using TCP/IP. If the server is started with the --enable-named-pipe option, you can also connect with named pipes if you run the client on the host where the server is running. The name of the named pipe is MySQL by default. If you do not give a host name when connecting to mysqld, a MySQL client first tries to connect to the named pipe. If that does not work, it connects to the TCP/IP port. You can force the use of named pipes on Windows by using . as the host name.

Reference:

http://stackoverflow.com/questions/19712307/mysql-localhost-127-0-0-1
http://stackoverflow.com/questions/3715925/localhost-vs-127-0-0-1
http://dev.mysql.com/doc/refman/5.5/en/can-not-connect-to-server.html
http://madproject.com/general/connect-to-mysql-using-localhost-instead-of-127-0-0-1-on-a-mac/
http://stackoverflow.com/questions/9714899/php-mysql-difference-between-127-0-0-1-and-localhost
http://superuser.com/questions/744972/connecting-to-mysql-from-127-0-0-1-instead-of-from-localhost
http://blog.csdn.net/xifeijian/article/details/12879395
http://blog.csdn.net/topasstem8/article/details/18357789