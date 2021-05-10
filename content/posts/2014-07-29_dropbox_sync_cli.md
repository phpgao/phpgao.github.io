---
title: "使用dropbox同步备份网站和数据库"
categories: [ "服务器技术" ]
tags: [ "database","Linux","dropbox","sync","backup" ]
draft: false
slug: "dropbox_sync_cli"
date: "2014-07-29 08:24:00"
url: "dropbox_sync_cli.html"
---

自从用了Shadowsocks，整个人都精神多了！

今天开整Dropbox，话说这货也被Q了。

废话不多说

以下内容需要翻{防屏蔽}墙使用，如果没有，可以参考[老高的这篇文章][1]搭建一个稳定又极速的翻{防屏蔽}墙环境

## 下载

在[官网][2]找到linux专用脚本，执行一下对应系统的脚本

### 32位系统

```
cd ~ && wget -O - "https://www.dropbox.com/download?plat=lnx.x86" | tar xzf -
```

### 64位系统

```
cd ~ && wget -O - "https://www.dropbox.com/download?plat=lnx.x86_64" | tar xzf -
```

<!--more-->


## 安装

运行`~/.dropbox-dist/dropboxd`，系统会提示你去登陆一下，这一步是为了**关联账户**！很重要！

登录完成，稍等片刻系统就会提示可以使用了，同时你会发现`home`下多了一个Dropbox的文件夹，没错，就是他！以后需要同步的文件都放在那里就OK！

## 使用脚本

不知道大家记住刚才官网里有一句话，`Download this CLI script to control Dropbox`，我们去把它下载下来，连接在[这儿][3]

官方wiki里有针对脚本的使用说明，[链接在此][4]

在此仅作简要说明

### 需求

- At least version 2.4 of the C library
- wget
- Python 2.x ≥ 2.5 (we do not offer support for Python 3.0)
- a web browser

### 下载脚本至~/bin

```bash
mkdir -p ~/bin
wget -O ~/bin/dropbox.py "https://www.dropbox.com/download?dl=packages/dropbox.py"
chmod +x ~/bin/dropbox.py
```

### 获取帮助

```bash
~/bin/dropbox.py help

which will show you all the commands you can use

status       获取 dropboxd 的运行状态
help         提供帮助
puburl       获取一个文件的共享链接
stop         停止 dropboxd
running      返回 dropboxd 是否在运行
start        运行 dropboxd
filestatus   获取一个或多个文件的同步状态
ls           显示文件(夹)的同步状态
autostart    用户登录时自动启动 dropboxd
exclude      同步时排除文件夹
lansync      启用禁用局域网同步
```

### 命令实例

```bash
~/bin/dropbox.py status
Indexing 317 files...

~/bin/dropbox.py help

~/bin/dropbox.py puburl ~/Dropbox/Public/file.zip
http://dl.dropbox.com/u/XXXXXXX/file.zip

~/bin/dropbox.py stop
Dropbox daemon stopped.

~/bin/dropbox.py help running
dropbox running
Returns 1 if running 0 if not running.

~/bin/dropbox.py help exclude
dropbox exclude [list]
dropbox exclude add [DIRECTORY] [DIRECTORY] ...
dropbox exclude remove [DIRECTORY] [DIRECTORY] ...
"list" prints a list of directories currently excluded from syncing.  
"add" adds one or more directories to the exclusion list, then resynchronizes Dropbox. 
"remove" removes one or more directories from the exclusion list, then resynchronizes Dropbox.
With no arguments, executes "list". 
Any specified path must be within Dropbox.
```

### 解绑

如果你的主机换了，原来的Dropbox确定不用，下面的操作可以让你轻松解绑你的账户和bropbox的关联

 - 删除linux主机上的一切有关文件(**注意备份**)
`rm -rf ~/.dropbox Dropbox .dropbox-dist`
 - 在[官网][5]登录你的账户
 - 在右上角点击你的名字
 - 在下拉菜单中找到设置(setting)
 - 选择安全(security)选项卡
 - 在网页下面找到设备(devices)
 - 点击对应设备后面的X解绑

## 开机启动

```bash
vi /etc/rc.local
su - xxx -c "~/bin/dropbox.py start"
# xxx为你的用户名
```

## 定时开关

老高的机器上运行dropbox会占用50M左右的内存，平时不需要运行，只需要在新文件生成后运行即可！

下面是运行和停止dropbox的shell脚本。

```bash
#!/bin/bash

echo $1

if [ $1 == 'start' ]; then
    su - xxx -c "~/bin/dropbox.py start"
elif [ $1 == 'stop' ]; then
    su - xxx -c "~/bin/dropbox.py stop"
fi
# xxx为你的用户名
```

我们在每天的00:05和12:05运行，然后在15min后停止运行。

```bash
# 编辑计划任务
crontab -e

# ADD
# dropbox
5 00,12 * * * sh ~/dropbox.sh start
20 00,12 * * *  sh ~/dropbox.sh stop
```

## 备份网站和数据库

下面是备份网站用的简单脚本，需要替换xxx为用户名，还有密码和数据库信息需要修改！

```bash
#/bin/bash

DATE=`date +%Y%m%d`
YESTERDAY=`date +%Y%m%d -d -1days`
MONTH=`date +%Y%m`
RQ=`date +%Y%m%d%H%M`
user="xxx"
nglogs='/var/log/nginx/logs'
# Directory
DIR="/home/xxx/Dropbox/backup/$DATE"
# Mysql files
FILE_MY="$DIR/${RQ}-mysql.tar.gz"
# Html files
FILE_HTML="$DIR/${RQ}-site.tar.gz"
# Access files
FILE_ACCESS="$DIR/${RQ}-access.tar.gz"
# Hour
HOUR=`date +%H`

# Make directory
mkdir -p $DIR


pid_path="/var/run/nginx/nginx.pid"

# 切割日志
if [ "$HOUR" == "00" ];then
    mkdir -p $nglogs/phpgao/$MONTH
    cp $nglogs/phpgao.access.log $nglogs/phpgao/$MONTH/$YESTERDAY.access.log
    mv $nglogs/phpgao.access.log /tmp/$YESTERDAY.access.log
    cd /tmp
    tar zcf $FILE_ACCESS $YESTERDAY.access.log
    rm -f /tmp/$YESTERDAY.access.log
    kill -USR1 `cat ${pid_path}`
fi


mysqldump -u xxx -p not-a-really-password --add-drop-table --databases database1 database2 > /tmp/mysql.sql

# tar mysql
cd /tmp
tar zcf $FILE_MY mysql.sql
rm -f /tmp/mysql.sql
# tar html
cd /home/
tar zcf $FILE_HTML ftp/*
chown -R $user:$user $DIR
```

dropbox太占内存，我们只需要他运行一小会儿就行，写一个计划任务！

```bash
# dropbox
5 00,12 * * * sh ~/dropbox.sh start
20 00,12 * * *  sh ~/dropbox.sh stop
```

## 解绑

解绑参考[官方问答][6]


  [1]: https://blog.phpgao.com/shadowsocks_on_linux.html
  [2]: https://www.dropbox.com/install?os=lnx
  [3]: https://www.dropbox.com/download?dl=packages/dropbox.py
  [4]: http://www.dropboxwiki.com/tips-and-tricks/using-the-official-dropbox-command-line-interface-cli
  [5]: https://www.dropbox.com/login
  [6]: https://www.dropbox.com/help/25