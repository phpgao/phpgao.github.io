---
title: "mysql优化之日志配置"
categories: [ "服务器技术" ]
tags: [ "mysql","configure","log" ]
draft: false
slug: "mysql_log_configure"
date: "2015-05-14 11:19:00"
url: "mysql_log_configure.html"
---

如果你选mysql数据库作为数据持久化的工具，那么就需要一个合理的日志配置，这样有助于排错和数据备份及恢复！

首先我们可以通过下面的MySQL的sql查询正在运行中的日志配置。

刚好我们熟悉一下`SHOW VARIABLES LIKE`的用法！这个命令是用来查询MySQL运行时配置的语句，LIKE后面的可以接通配符查找！

还有对应的设置语法叫`SET GLOBAL`，后面我们会用到。

```
mysql> SHOW VARIABLES LIKE '%log%';
+-----------------------------------------+---------------------------------+
| Variable_name                           | Value                           |
+-----------------------------------------+---------------------------------+
| back_log                                | 50                              |
| binlog_cache_size                       | 32768                           |
| binlog_direct_non_transactional_updates | OFF                             |
| binlog_format                           | STATEMENT                       |
| expire_logs_days                        | 0                               |
| general_log                             | OFF                             |
| general_log_file                        | /var/run/mysqld/mysqld.log      |
| innodb_flush_log_at_trx_commit          | 1                               |
| innodb_locks_unsafe_for_binlog          | OFF                             |
| innodb_log_buffer_size                  | 1048576                         |
| innodb_log_file_size                    | 5242880                         |
| innodb_log_files_in_group               | 2                               |
| innodb_log_group_home_dir               | ./                              |
| innodb_mirrored_log_groups              | 1                               |
| log                                     | OFF                             |
| log_bin                                 | OFF                             |
| log_bin_trust_function_creators         | OFF                             |
| log_bin_trust_routine_creators          | OFF                             |
| log_error                               | /var/log/mysqld.log             |
*| log_output                              | FILE                            |*
| log_queries_not_using_indexes           | OFF                             |
| log_slave_updates                       | OFF                             |
| log_slow_queries                        | OFF                             |
| log_warnings                            | 1                               |
| max_binlog_cache_size                   | 18446744073709547520            |
| max_binlog_size                         | 1073741824                      |
| max_relay_log_size                      | 0                               |
| relay_log                               |                                 |
| relay_log_index                         |                                 |
| relay_log_info_file                     | relay-log.info                  |
| relay_log_purge                         | ON                              |
| relay_log_space_limit                   | 0                               |
| slow_query_log                          | OFF                             |
| slow_query_log_file                     | /var/run/mysqld/mysqld-slow.log |
| sql_log_bin                             | ON                              |
| sql_log_off                             | OFF                             |
| sql_log_update                          | ON                              |
| sync_binlog                             | 0                               |
+-----------------------------------------+---------------------------------+
38 rows in set (0.00 sec)

```

请注意log_output一行，次配置决定将日志输出到文件还是table中。

我们可以通过`SHOW VARIABLES LIKE 'log_output';`查询此配置。

## 通过文件配置

mysql的配置文件位于 `/etc/my.cnf`

```
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
user=mysql
symbolic-links=0
# 错误日志
log-error=/var/log/mysql/error_mysql.log
# Slow Query
log-slow-queries=/var/log/mysql/slow_mysql.log
long_query_time=2
# 记录没有使用索引的查询
#log-queries-not-using-indexes

# 常规查询日志，老高主要用于调试PDO参数绑定的查询
general_log=1
general_log_file=/var/log/mysql/general_mysql.log
# < MySql5.1.12
#log = /var/log/mysql/general_mysql.log


[mysqld_safe]
# syslog指将日志记录至系统日志
#syslog
pid-file=/var/run/mysqld/mysqld.pid
log_error=/var/log/mysql/error_mysqld.log

```

接下来需要重启服务器即可是配置生效！

## 热配置

有时候可能想在不重启mysql的方式开启日志，怎么办？

当然我们可以通过MySQL的sql查询开启。

以下操作假设你已经以root方式登录进入MySQL交互界面

### 常规日志

```
# 查看常规日志配置
SHOW VARIABLES LIKE 'general_log%';
```

下面我们开启他

```
# 设置日志文件路径，请先保证路径存在
SET GLOBAL general_log_file='/var/log/mysql/general_mysql.log';
# 开启日志
SET GLOBAL general_log=ON;

# 关闭日志
SET GLOBAL general_log=OFF;
```

![查看常规日志配置][1]

### 错误日志

聪明的朋友，既然已经知道`SET GLOBAL`、`SHOW VARIABLES LIKE`这两个命令，我相信其他的日志配置就难不倒你了。

所以在此老高就不啰嗦了。

  [1]: https://blog.phpgao.com/usr/uploads/2015/05/855182355.png