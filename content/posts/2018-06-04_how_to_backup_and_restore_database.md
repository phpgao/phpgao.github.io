---
title: "如何备份MySQL-PG数据库并恢复"
categories: [ "服务器技术" ]
tags: [ "mysql","database","postgresql" ]
draft: false
slug: "how_to_backup_and_restore_database"
date: "2018-06-04 04:56:00"
url: "how_to_backup_and_restore_database.html"
---

老高最近又迁移了一次数据库，期间遇到了很多问题，本文主要记录了如果备份数据库并恢复到新的数据库中！

> ps. 由于老高工作用的是PG数据库，而博客用的是MySQL，于是本篇就把二者的用法都写出来吧！


<!--more-->


## 备份数据库

备份数据库就是把当前的数据库的指定数据导出为一种固定的格式，相当于给当前的数据做了一次快照。

### MySQL

```

```

### PG




## 一些问题

### MySQL 8.0 默认打开了log_bin，导致/var/lib/mysql/下binlog文件太大，如何关闭呢？

```
systemctl set-environment MYSQLD_OPTS="--disable-log-bin"
# 要是想重新打开，执行unset命令即可
systemctl unset-environment MYSQLD_OPTS
```

### 小技巧 

如果你忘记了MySQL的root用户密码，可以执行下面的操作，进行密码重置

```
1. Stop mysql:
systemctl stop mysqld

2. Set the mySQL environment option 
systemctl set-environment MYSQLD_OPTS="--skip-grant-tables"

3. Start mysql usig the options you just set
systemctl start mysqld

4. Login as root
mysql -u root

5. Update the root user password with these mysql commands
mysql> UPDATE mysql.user SET authentication_string = PASSWORD('MyNewPassword')
    -> WHERE User = 'root' AND Host = 'localhost';
mysql> FLUSH PRIVILEGES;
mysql> quit

6. Stop mysql
systemctl stop mysqld

7. Unset the mySQL envitroment option so it starts normally next time
systemctl unset-environment MYSQLD_OPTS

8. Start mysql normally:
systemctl start mysqld

Try to login using your new password:
7. mysql -u root -p
```

### 修复数据库

```
mysqlcheck --auto-repair -A -u username -ppassword
```


Refers:

 - https://stackoverflow.com/questions/33374314/can-not-login-to-mysql-5-7-9-after-change-password
 - https://dev.mysql.com/doc/refman/5.7/en/using-systemd.html
 - https://stackoverflow.com/questions/31139397/table-mysql-proc-is-marked-as-crashed-and-should-be-repaired
 - https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#option_mysqld_log-bin