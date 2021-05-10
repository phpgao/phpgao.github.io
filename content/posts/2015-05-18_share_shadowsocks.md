---
title: "用ss-manyuser和ss-panel搭建shadowsocks分享平台"
categories: [ "服务器技术" ]
tags: [ "shadowsocks","ss-panel" ]
draft: false
slug: "share_shadowsocks"
date: "2015-05-18 14:43:00"
url: "share_shadowsocks.html"
---

ss-manyuser做前端，ss-panel做后端，运行环境centos6。

## 项目地址

感谢以下大神提供源码

manyuser：

[shadowsocks manyuser branch][1]

ss-panel:

[ss-panel][2]

## 安装

```
mkdir /data
cd /data
git clone https://github.com/mengskysama/shadowsocks.git  backend
git clone https://github.com/orvice/ss-panel.git  front
```

## 配置

### front

```
# 1. 创建数据库，并建立一个本地用户shadowsocks@localhost，并赋予权限
CREATE DATABASE IF NOT EXISTS shadowsocks DEFAULT CHARSET utf8 COLLATE utf8_general_ci;
GRANT ALL PRIVILEGES ON shadowsocks.* TO 'shadowsocks'@'localhost' IDENTIFIED BY 'password';

# 2. 导入sql
cd front
cat sql/*.sql > merged.sql

# 登录mysql
mysql>use shadowsocks;
mysql>source path/to/merged.sql;

# 3. 将数据库配置信息写入lib/config.php
```

这里要提一下：

userid=1的用户是管理员，最好把他的端口将50000改为15000，密码是直接md5，可以自己修改。

passwd字段是ss的连接密码。

### backend

```
# 准备环境
yum -y install python-pip m2crypto
pip install cymysql


# 2. 修改数据库配置
cd shadow socks
vim Config.py

# 2.5 可选操作
# 由于ss可能分布于多个服务器，我们需要一个等够远程登录的账户管理mysql
# 刚才的数据库名为shadowsocks，我们现在建立一个能够远程登录的账号并赋予权限
# 名字就叫 laogao 吧！
CREATE USER laogao@'%' IDENTIFIED BY 'laogao';

# 赋予合适的权限
GRANT select,update on phpergao.* to laogao@'%';

# 3. 运行ss
python server.py
```

  [1]: https://github.com/mengskysama/shadowsocks.git
  [2]: https://github.com/orvice/ss-panel.git