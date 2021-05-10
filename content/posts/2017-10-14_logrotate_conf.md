---
title: "Logrotate的基本使用方法"
categories: [ "服务器技术" ]
tags: [ "mysql","PHP","nginx","logrotate" ]
draft: false
slug: "logrotate_conf"
date: "2017-10-14 15:53:00"
url: "logrotate_conf.html"
---

Linux服务器上我们用Logrotate来分割归档日志文件，结合crond我们可以指定每天在某个时间自动整理日志等文档。本文主要说明了Centos下Logrotate的使用和配置的方法。

<!--more-->


## Logrotate配置文件在哪儿？

> Logrotate的配置文件位于 `/etc/logrotate.conf`。
> Logrotate的子配置文件位于文件夹 `/etc/logrotate.d/` 下，某些软件，入nginx，会在rpm命令安装后会把对应的nginx日志分割文件释放在此，用于定时整理日志文件。

## 如何使用

我们先试用帮助命令看一下，其中老高想强调的是

 - `-d`，其翻译为什么都不做，仅仅是测试，这个参数很大程度方便了我们测试配置文件而不用担心当前的配置出差错。
 - `-s`，指定`状态`文件路径，该文件默认路径在 `/var/lib/logrotate/logrotate.status`，记录了Logrotate命令的运行情况。

```bash
$ /usr/sbin/logrotate --help
Usage: logrotate [OPTION...] <configfile>
  -d, --debug               Don't do anything, just test (implies -v)
  -f, --force               Force file rotation
  -m, --mail=command        Command to send mail (instead of `/bin/mail')
  -s, --state=statefile     Path of state file
  -v, --verbose             Display messages during rotation
  -l, --log=STRING          Log file
  --version                 Display version information

Help options:
  -?, --help                Show this help message
  --usage                   Display brief usage message
```

如果想测试配置文件

```bash
# 测试所有logrotate配置
/usr/sbin/logrotate -d -v /etc/logrotate.conf

# 测试指定的logrotate配置，入nginx
/usr/sbin/logrotate -d -v /etc/logrotate.d/nginx
```

## 整理时间错乱的问题

开始使用Logrotate时，老高发现日志的切割时间总是在晚上3点的某个时间，如果你也发现了这个问题，可以按照以下步骤排查：

### 查看所有用户的计划任务


首先我们确保没有其他用户手动添加日志切割的计划任务，运行下面的命令确保找不到`logrotate`关键字！

```bash
# 查看所有用户的计划任务，使用前提是有权限的用户
for user in $(cut -f1 -d: /etc/passwd); do crontab -u $user -l; done
```

### 查看Logrotate的计划任务配置

再此我们使用`cat`命令检查/etc/cron.daily/logrotate的存在，并确认文件内容没有被修改。

> 同时我们可以推测把类似的文件丢在这里可以每日触发脚本，同理还有`/etc/cron.hourly`等，可以用`ls /etc/cron*`查看！

```bash
cat /etc/cron.daily/logrotate

# 以下是文件内容

#!/bin/sh

/usr/sbin/logrotate -s /var/lib/logrotate/logrotate.status /etc/logrotate.conf
EXITVALUE=$?
if [ $EXITVALUE != 0 ]; then
    /usr/bin/logger -t logrotate "ALERT exited abnormally with [$EXITVALUE]"
fi
exit 0
```

### 查看anacron配置

`RANDOM_DELAY` 指的是最大的延迟时间，这个时间被加在`delay in minutes`中，为最后生效的delay分钟数，也就是说每次执行日常任务是总是有一个随机5-50分钟的延时，如果设为0可以避免这个问题，但是可能会加重系统的负担，因为程序可能会在某一个时间全部运行！

`START_HOURS_RANGE` 指定任务开始的小时数，这也就说明了为什么修改时间总是在3点的问题。

读懂了配置我们就可以自行修改以符合我们对运行时间的需求了！

```bash
cat /etc/anacrontab

# 以下是文件内容

# /etc/anacrontab: configuration file for anacron

# See anacron(8) and anacrontab(5) for details.

SHELL=/bin/sh
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
# the maximal random delay added to the base delay of the jobs
RANDOM_DELAY=45
# the jobs will be started during the following hours only
START_HOURS_RANGE=3-22

#period in days   delay in minutes   job-identifier   command
1	5	cron.daily		nice run-parts /etc/cron.daily
7	25	cron.weekly		nice run-parts /etc/cron.weekly
@monthly 45	cron.monthly		nice run-parts /etc/cron.monthly
```

以下是老高总结的**cron->anacron**调用图

```             
  +------------------------+
  |                        |
  |/etc/cron.hourly/0anacro|
  |                        |
  +-----------+------------+
              |
              |if var/spool/anacron/cron.daily
              |does not run today
              |
     +--------v--------+
     |                 |
     |/usr/sbin/anacron|
     |                 |
     +--------+--------+
              |
              |read /etc/anacrontab
              |
+-------------v------------+
|                          |
|    trigger logrotate     |
|                          |
+--------------------------+
```

## Logrotate配置范例

下面是`/etc/logrotate.d/`文件夹里文件的内容，贴出来给大家参考一下!

```logrotate
$ cat /etc/logrotate.d/*
/var/log/httpd/*log {
    missingok
    notifempty
    sharedscripts
    delaycompress
    postrotate
        /bin/systemctl reload httpd.service > /dev/null 2>/dev/null || true
    endscript
}
# The log file name and location can be set in
# /etc/my.cnf by setting the "log-error" option
# in [mysqld]  section as follows:
#
# [mysqld]
# log-error=/var/log/mysqld.log
#
# For the mysqladmin commands below to work, root account
# password is required. Use mysql_config_editor(1) to store
# authentication credentials in the encrypted login path file
# ~/.mylogin.cnf
#
# Example usage:
#
#  mysql_config_editor set --login-path=client --user=root --host=localhost --password
#
# When these actions has been done, un-comment the following to
# enable rotation of mysqld's log error.
#

#/var/log/mysqld.log {
#        create 640 mysql mysql
#        notifempty
#        daily
#        rotate 5
#        missingok
#        compress
#    postrotate
#       # just if mysqld is really running
#       if test -x /usr/bin/mysqladmin && \
#          /usr/bin/mysqladmin ping &>/dev/null
#       then
#          /usr/bin/mysqladmin flush-logs
#       fi
#    endscript
#}
/var/log/nginx/*.log {
        daily
        missingok
        rotate 52
        compress
        delaycompress
        notifempty
        create 640 nginx adm
        sharedscripts
        postrotate
                if [ -f /var/run/nginx.pid ]; then
                        kill -USR1 `cat /var/run/nginx.pid`
                fi
        endscript
}
/var/log/php-fpm/*log {
    missingok
    notifempty
    sharedscripts
    delaycompress
    postrotate
	/bin/kill -SIGUSR1 `cat /run/php-fpm/php-fpm.pid 2>/dev/null` 2>/dev/null || true
    endscript
}
/var/log/redis/*.log {
    weekly
    rotate 10
    copytruncate
    delaycompress
    compress
    notifempty
    missingok
}
/var/log/yum.log {
    missingok
    notifempty
    size 30k
    yearly
    create 0600 root root
}
```