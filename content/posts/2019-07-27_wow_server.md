---
title: "从零开始编译wow私服"
categories: [ "代码人生" ]
tags: [  ]
draft: false
slug: "wow_server"
date: "2019-07-27 08:30:00"
url: "wow_server.html"
---

自从魔兽世界开始转为月卡时，老高就基本AFK了。。。直到偶然听到歌单里的灰熊丘陵、风暴群山的BGM，又一次的手痒了，不过这次我们不冲点卡，我们以学习的态度搭建一个专属自己的魔兽世界服务器！


<!--more-->

> 感谢[TrinityCore][1]项目，你们懂得！

## 说明

本文只针对3.3.5版本，master版本请参考官方文档。

老高的运行环境是 Debian 9，当然会docker的同学可以随意选择系统。
硬件配置

 - i3
 - 8g
 - 200G ssd

> 编译TrinityCore是一个比较漫长的过程，需要投入一定的时间和精力才能完成！

安装步骤基本都是参考[Installation Guide - TrinityCore - TrinityCore Collaboration Platform][2]这个页面，英文水平好的同学可以直接参考。

### 关于版本

在TrinityCore中我们能看到master版本和3.3.5两个版本，他们有什么不同呢？其实很简单。

 - master是最新的wow服务器版本，理论上可以用当前的国服的客户端直接登陆，只需要做一些轻微的改动，但是由于比较新的缘故，bug也会很多，而基本上每次bug修复都需要重新编译整个服务器，所以官方不推荐使用master分支的代码。
 - 3.3.5 官方推荐版本，虽然BUGFiX的提交也不少，但是提交的基本都是SQL，所以稳妥起见还是老老实实用这个版本吧！

### 关于Windows系统

由于老高多余的服务器刚好跑了OMV，基于Debian9，所以就顺势用了这个机子。win上的编译可能会麻烦一些，如果有需求，后期再上教程！


## 准备工作


### 依赖安装

```bash
apt-get update
apt-get install git clang cmake make gcc g++ libmariadbclient-dev libssl-dev libbz2-dev libreadline-dev libncurses-dev libboost-all-dev mariadb-server p7zip default-libmysqlclient-dev
update-alternatives --install /usr/bin/cc cc /usr/bin/clang 100
update-alternatives --install /usr/bin/c++ c++ /usr/bin/clang 100
```

### 创建用户

用户名为wow

```bash
adduser wow
su - wow
```

### 约定目录

以下是老高的建议目录

```bash
# 以wow权限执行
su - wow
# TrinityCore 目录
mkdir /home/wow/TrinityCore

# 服务器目录，最终包含etc,bin,data三个子目录
mkdir /home/wow/server355

# 客户端目录
mkdir /home/wow/client
```

## 下载工作

既然是网络游戏，那就一定需要C端和S端，即客户端和服务器端，需要注意的是： 

 1. 必须需要下载的必须是3.3.5(12340)的版本，其他版本可能会出现`无法验证游戏版本，这可能是由于档案已经被破坏或是其他`的问题，可能需要通过改源码重新编译的手段才能完成登录！所以一定不要下错版本了！
 1. S端优先下载，下载完后先编译

### S端

```bash
# 以wow用户执行
git clone -b 3.3.5 git://github.com/TrinityCore/TrinityCore.git /home/wow/TrinityCore
```

### C端

客户端版本：3.3.5(12340)

 - 客户端下载地址1(老高使用的版本)： https://pan.baidu.com/s/1o8TkZOY 密码: kqcd
 - 客户端下载地址2(待验证)： https://pan.baidu.com/s/13C__Fm8_rkoqB6mu7dfz_Q

百度盘下载很慢，需要自己想办法，下载解压后，需要把完成后把文件上传到Linux服务器上，用于后续步骤。


## 编译服务端

权限建议: wow

编译期间可以先跳过`提取客户端信息`，阅读`准备数据库`章节!

```bash
cd /home/wow/TrinityCore
mkdir build
cd build
cmake ../ -DCMAKE_INSTALL_PREFIX=/home/wow/server355
# 此步骤可能时间比较长，可以用nohup或者screen执行
make -j $(nproc) install

# 终于，你编译好了服务端，胜利不远了！
```

编译完成后，/home/wow/server355目录下会出现 etc和bin目录

```bash
# ls /home/wow/server355/bin
-rwxr-xr-x 1 wow  wow   25104168 Jul 26 00:17 authserver
-rwxr-xr-x 1 wow  wow    3361976 Jul 26 00:17 mapextractor
-rwxr-xr-x 1 wow  wow   19130160 Jul 26 00:17 mmaps_generator
-rwxr-xr-x 1 wow  wow   10167776 Jul 26 00:17 vmap4assembler
-rwxr-xr-x 1 wow  wow    8489448 Jul 26 00:17 vmap4extractor
-rwxr-xr-x 1 wow  wow  587311088 Jul 26 00:36 worldserver

# ls /home/wow/server355/bin
-rw-r--r-- 1 wow wow  13K Jul 25 23:42 authserver.conf.dist
drwxr-xr-x 2 wow wow 156K Jul 27 00:15 maps
drwxr-xr-x 2 wow wow 132K Jul 27 00:15 mmaps
drwxr-xr-x 2 wow wow 536K Jul 27 00:15 vmaps
-rw-r--r-- 1 wow wow 133K Jul 26 00:16 worldserver.conf.dist
```

我们顺手把**worldserver.conf.dist**和**authserver.conf.dist**复制一份，然后修改**worldserver.conf**中 `DataDir = "."` 为 `DataDir = "../data"`，修改方法在此不赘述，可以用vi命令，也可以在win下改好放上去。

```bash
cp /home/wow/server355/bin/worldserver.conf.dist worldserver.conf
cp /home/wow/server355/bin/authserver.conf.dist authserver.conf
```

## 提取客户端信息

权限建议: wow

此步骤依赖：

 1. 客户端文件
 1. 编译好的TrinityCore

### DBC and Maps files

```bash
cd /home/wow/client
/home/wow/server355/bin/mapextractor
mkdir /home/wow/server355/data
  
cp -r dbc maps /home/wow/server355/data
```

### Visual Maps (aka vmaps)

```bash
cd /home/wow/client
/home/wow/server355/bin/vmap4extractor
mkdir vmaps
/home/wow/server355/bin/vmap4assembler Buildings vmaps
cp -r vmaps /home/wow/server355/data
```

### Movement Maps (aka mmaps)

```bash
cd /home/wow/client
mkdir mmaps
/home/wow/server355/bin/mmaps_generator
cp -r mmaps /home/wow/server355/data
```

> 好了，终于胜利就在前方！

## 准备数据库

数据库存放了游戏的各种信息，所以在启动服务器钱，我们需要把数据库搭建好！由于在依赖处理步骤中我们已经安装了`mariadb-server`，所以现在我们只需要运行它即可！

> 此节需要root权限

### 运行数据库

```bash
systemctl enable mysql
systemctl restart mysql
```

如果没有报错，我们接着执行初始化脚本，这里我们需要为root用户提供一个密码，这里假设为 laogaoniubi

### 初始化

权限建议: root

```bash
mysql_secure_installation

# Enter current password for root (enter for none):
输入回车

# Change the root password? [Y/n]
Y

# 输入两次 laogaoniubi

# Remove anonymous users? [Y/n]
Y

# Disallow root login remotely? [Y/n]
# Y表示让root用户远程登录，N表示可以远程登录，老高推荐董数据库的同学选Y，觉得自己搞不定或者搞不懂的请选N
Y

Remove test database and access to it? [Y/n]
Y

Reload privilege tables now? [Y/n]
Y
```

至此，我们已经成功初始化数据库，并且成功将root用户的密码设置为laogaoniubi！

### 导入数据

第一步是把数据库建立起来，我们需要执行以下sql，sql的路径位于源码的[TrinityCore/sql/create/create_mysql.sql][3]

```sql
GRANT USAGE ON * . * TO 'trinity'@'localhost' IDENTIFIED BY 'trinity' WITH MAX_QUERIES_PER_HOUR 0 MAX_CONNECTIONS_PER_HOUR 0 MAX_UPDATES_PER_HOUR 0 ;

CREATE DATABASE `world` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;

CREATE DATABASE `characters` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;

CREATE DATABASE `auth` DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;

GRANT ALL PRIVILEGES ON `world` . * TO 'trinity'@'localhost' WITH GRANT OPTION;

GRANT ALL PRIVILEGES ON `characters` . * TO 'trinity'@'localhost' WITH GRANT OPTION;

GRANT ALL PRIVILEGES ON `auth` . * TO 'trinity'@'localhost' WITH GRANT OPTION;
```

执行方法参考：

```bash
# 登录 mysql后执行

source /home/wow/TrinityCore/sql/create/create_mysql.sql
```

至此，数据库初始化完毕

> 数据库你都能搞定，是个人才！要不要加个好友认识一下？

## 配置网络

让我们打开数据库`auth`，查看一下表`realmlist`，让我们把`address`字段改为服务器的IP，老高的服务器IP为`192.168.111.111`。

```
(`id`, `name`, `address`, `localAddress`, `localSubnetMask`, `port`, `icon`, `flag`, `timezone`, `allowedSecurityLevel`, `population`, `gamebuild`) VALUES ('1', 'Trinity', '192.168.111.116', '192.168.111.111', '255.255.255.0', '8085', '0', '0', '1', '0', '0', '12340');

```

## 最后的服务配置

权限: wow

> 不要急，胜利已经是你的了！

之前的准备工作在这一步汇总，到收获的季节了！

### authserver

> 可以公开的情报：authserver即认证服务，我们登陆账户都是到这里认证后才会请求worldserver，也就是说authserver只有在登陆的时候才起作用，登录后这个进程就可以关闭了！

# 我们先运行authserver看看

```
/home/wow/server355/bin/authserver
# or
/home/wow/server355/bin/authserver -c /home/wow/server355/etc/authserver.conf
```

以下输出即为成功，很不错！

```
DatabasePool 'auth' opened successfully. 2 total connections running.
Started auth database connection pool.
Loading IP Location Database...
Added realm "Trinity" at 192.168.111.111:8085.
```

### worldserver

```
/home/wow/server355/bin/worldserver
```

以下输出即为。。。什么！竟然又报错了！看来之前少下载了一个文件，名字叫`TDB_full_world_335.19071_2019_07_15.sql`，这个文件在哪里下载呢？

[下载传送门，请对号入座][4]

下载下来的文件后缀为7z，我们需要先解压，得到sql文件后直接放在`/home/wow/server355/bin/worldserver`的同级目录即可！

再一次，我们运行`worldserver`，如果得到以下输出，那么恭喜你，你已经沐浴到胜利的阳光了！

```bash
World initialized in 0 minutes 8 seconds
TrinityCore rev. 7e10438b2759 2019-07-25 17:00:03 +0200 (3.3.5 branch) (Unix, RelWithDebInfo, Static) (worldserver-daemon) ready...
TC>TC>
```

### 添加账号

上一节我们终于进入了`worldserver`，下面我们给我们的主角创建一个NO1的账号，使用以下命令格式

```bash
account create <user> <pass>
```

```
World initialized in 0 minutes 8 seconds
TrinityCore rev. 7e10438b2759 2019-07-25 17:00:03 +0200 (3.3.5 branch) (Unix, RelWithDebInfo, Static) (worldserver-daemon) ready...
# 这里我们创建了一个用户名为test，密码也为test的账户
TC>TC> account create test test
# 紧接着我们把test账户升级为gm，豪嘛！这老NB了！
TC>TC> account set gmlevel test 3 -1
```

## 登录

让我们回到Windows的怀抱，跑到wow客户端下，把下面的代码保存为wow.bat并执行，别管360的提醒，直接允许。

```bash
echo y | rd /s "Cache"
echo SET realmlist "192.168.111.111" > Data\zhTW\realmlist.wtf
echo SET realmlist "192.168.111.111" > Data\enTW\realmlist.wtf
echo SET realmlist "192.168.111.111" > Data\zhCN\realmlist.wtf
echo SET realmlist "192.168.111.111" > Data\enCN\realmlist.wtf
echo SET realmlist "192.168.111.111" > Data\enUS\realmlist.wtf
echo SET realmlist "192.168.111.111" > realmlist.wtf
start Wow.exe
goto end
```

![wow_login][5]

幽灵虎。。。

![ghost_tiger][6]


## 参考

 - https://trinitycore.atlassian.net/wiki/spaces/tc/pages/10977288/Linux+Requirements
 - https://trinitycore.atlassian.net/wiki/spaces/tc/pages/2130065/GM+Commands
 - https://xspio.com/%E9%AD%94%E5%85%BD%E4%B8%96%E7%95%8C%E5%AE%98%E6%96%B9%E5%AE%A2%E6%88%B7%E7%AB%AF%E4%B8%8B%E8%BD%BD


  [1]: https://www.trinitycore.org/
  [2]: https://trinitycore.atlassian.net/wiki/spaces/tc/pages/2130077/Installation+Guide
  [3]: https://github.com/TrinityCore/TrinityCore/blob/3.3.5/sql/create/create_mysql.sql
  [4]: https://github.com/TrinityCore/TrinityCore/releases
  [5]: https://blog.phpgao.com/usr/uploads/2019/07/2129653272.png
  [6]: https://blog.phpgao.com/usr/uploads/2019/07/3124592762.png