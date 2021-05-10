---
title: "mysql忘记root密码解决方法"
categories: [ "代码人生" ]
tags: [ "windows","mysql","Linux","password" ]
draft: false
slug: "iforget_mysql"
date: "2014-05-10 01:27:00"
url: "iforget_mysql.html"
---

## Windows

步骤如下：
1.停止mysql服务(以管理员身份,在cmd命令行下运行) 

```cmd
net stop mysql 
```

2.使用 mysqld –skip-grant-tables 命令启动mysql数据库 

```cmd
>D:\>net stop mysql
MySQL 服务正在停止.
MySQL 服务已成功停止。
D:\>mysqld --skip-grant-tables</pre> 
```

3.新开一个cmd窗口，进行如下操作 

```cmd
D:\>mysql -uroot
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.1.26-rc-community MySQL Community Server (GPL)

Type 'help;' or '\h' for help. Type '\c' to clear the buffer.

mysql> update mysql.user set password=password('root') where user='root';
Query OK, 1 row affected (0.02 sec)
Rows matched: 2  Changed: 1  Warnings: 0

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

4.打开任务管理器，停止mysql,mysqld进程，使用net start mysql启动mysqld服务，就可以使用root用户 root密码进入数据库了。

```bash
mysql -u root -p root
```

## Linux

linux下解决办法和win下差不多，都是先开启一个没有权限验证的mysql，修改密码后直接重启mysql即可！

```bash
# 停用mysql
service mysqld stop


--------------------------------


# 使用无权限验证启动mysql
mysqld_safe --user=mysql --skip-grant-tables --skip-networking &

# 如果启动失败可以采用修改mysql配置文件的方法
vim /etc/my.ini

[mysqld] 
datadir=/var/lib/mysql 
socket=/var/lib/mysql/mysql.sock
# ADD 
skip-grant-tables

# 重启mysql
service mysql restart

--------------------------------

# 修改密码操作同上


# 重启mysql

service mysqld restart

```
## 常见密码：

以下是常见的mysql密码，修改的时候就不用使用password()再生成了。

```mysql

SELECT PASSWORD('123456');  -  *6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9
SELECT PASSWORD('mysql');  -  *E74858DB86EBA20BC33D0AECAE8A8108C56B17FA
SELECT PASSWORD('admin');  -  *4ACFE3202A5FF5CF467898FC58AAB1D615029441
SELECT PASSWORD('root');  -  *81F5E21E35407D884A6CD4A731AEBFB6AF209E1B
SELECT PASSWORD('toor');  -  *9CFBBC772F3F6C106020035386DA5BBBF1249A11
SELECT PASSWORD('new-password');  -  *4A82FDF1D80BA7470BA2E17FEEFD5A53D5D3B762
SELECT PASSWORD('kangle');  -  *39534390859F20B4F7591340666D1B1FF1DC0F30
SELECT PASSWORD('asdqwe');  -  *7E1FF641317801DCCB60031184404B45E8A84BCC

```


2015年1月19日更新：


增加linux解决方法

补充常见密码