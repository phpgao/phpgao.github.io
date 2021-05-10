---
title: "MySQL主从复制操作步骤"
categories: [ "服务器技术" ]
tags: [ "mysql","master","slave" ]
draft: false
slug: "mysql_master_slave"
date: "2014-12-04 01:56:00"
url: "mysql_master_slave.html"
---

主服务器（master）IP：192.168.0.1
从服务器（slave）IP：192.168.0.2
首先确保主从服务器上的Mysql版本相同

## 主服务器上操作

**创建用户名为repl的一个账户**

```mysql
GRANT REPLICATION SLAVE ON *.* TO 'repl'@'192.168.0.2' IDENTIFIED BY 'xxxxxxxxx';
```

**修改主数据库的配置文件my.cnf,开启BINLOG，并设置server-id的值，修改之后必须重启Mysql服务，如果不需要修改可不用重启。**

```mysql
server-id=1
log_bin = /usr/llocal/mysql/log/mysql-bin.log
```

**之后可以得到主服务器当前二进制日志名和偏移量，这个操作的目的是为了在从数据库启动后，从这个点开始进行数据的恢复**

```mysql
flush tables with read lock;  这是session级，退出就隐式 unlock tables;
show master status;
```

**生成主数据库的备份**
如果mysqldump 无法识别，则在/home/mysql/.bash_profile  添加环境变量  export PATH=$PATH:/usr/local/mysql/bin
mysqldump -p3306 -uroot –pxxxxxxxx test > test.sql
unlock tables;
**将备份出来的数据复制到从数据库**

```bash
Scp test.sql 192.168.0.2:
```

## 从服务器上操作

**将备份数据导入数据库**

```bash
Mysql –uroot –pxxxxxxxx  test < test.sql
```

**修改从数据库的my.cnf,增加server-id参数，如有更改需要重启**

```mysql
server-id=2 # 注：一定不能跟主数据库一样
```

**指定复制使用的用户,主数据库服务器的ip,端口以及开始执行复制日志的文件和位置**

```mysql
CHANGE MASTER TO MASTER_HOST='192.168.1.130', MASTER_USER='repl', MASTER_PASSWORD='456123', MASTER_LOG_FILE='mysql-bin.xxxx', MASTER_LOG_POS=xxxx;

MASTER_LOG_FILE=' mysql-bin.xxxx', MASTER_LOG_POS=xxxx 这俩个参数参见主服务器 show master status
```

**启动slave进程**

```mysql
Start slave;
```

**查看从服务器状态**

```mysql
Show slave status;
Slave_IO_Running: Yes //此状态必须YES
Slave_SQL_Running: Yes //此状态必须YES
```

## 测试

   在主数据库上插入一条数据，然后看从数据库是否有更新。然后就可以自己发挥了。总的来说就是一般用主从复制（Master-Slave）的方式来同步数据，再通过读写分离（MySQL-Proxy）来提升数据库的并发负载能力，再通过高可用性(High Availability)确保服务的稳定。