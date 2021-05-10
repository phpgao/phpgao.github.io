---
title: "小米路由器mini折腾之安装shadowsocks-libev-spec"
categories: [ "路由器" ]
tags: [ "shadowsocks","opkg","config","udp","dns","redsocks2" ]
draft: false
slug: "xiaomi_router_shadowsocks_libev_spec"
date: "2015-04-12 03:42:00"
url: "xiaomi_router_shadowsocks_libev_spec.html"
---

DNS污染问题很是让人烦心，小米路由器mini的pandorabox自带了shadowsocks-libev，没有UDP转发功能，在期待下次pandorabox的更新能够自带shadowsocks-libev-spec的时候，我们还可以选择手动安装，下面老高就介绍一下如何安装。

你也许能通过以下关键字找到本文：

如何安装shadowsocks-libev-spec
如何配置shadowsocks-libev-spec
openwrt\pandorabox如何转发UDP
如果解决DNS污染


<!--more-->


## 项目官网

http://openwrt-dist.sourceforge.net

## 安装前的准备

安装前我们需要删除原来的shadowsocks-libev。原来的ss可以通过 `ps |grep ss-` 和 `ll /usr/bin/|grep ss-` 找到！

### 停止进程

```
killall ss-local
killall ss-redir
killall ss-tunnel
```

### 备份配置文件

```
mv /etc/config/shadowsocks /etc/config/shadowsocks_old
```

### 备份执行文件

```bash
# 重命名
cd /usr/bin
mv ss-local ss-local.old
mv ss-redir ss-redir.old
mv ss-tunnel ss-tunnel.old
```

## 开始安装

### 更新opkg源

请参考 [配置opkg篇][1] 配置好OpenWrt-dist的源

### 开始安装

配置好opkg的源后我们先更新一下 `opkg update`

更新成功应该是如下图：

![opkg更新][2]

下面我们开始安装shadowsocks-libev-spec，先执行下面的命令安装。

```
opkg install shadowsocks-libev-spec
opkg install luci-app-shadowsocks-spec
```

> 由于最近一次shadowsocks-libev-spec的更新会导致直接用opkg安装报错！

![opkg install shadowsocks-libev-spec][3]

所以出现此类错误，[请下载老版本的shadowsocks-libev-spec][4]，老高目前使用**r512稳定版**，下载`shadowsocks-libev-spec_2.1.4-1_ramips_24kec.ipk`，可以正常安装!


![成功安装][5]


安装完毕后无需重启路由器，luci后台的已经可以看到新的ss配置信息了。

## 如何使用

shadowsocks-libev-spec的配置文件位于`/etc/shadowsocks/config.json`

配置示例如下：

```
{
    "server": "xx.x.x.x",
    "server_port": 23456,
    "local": "0.0.0.0",
    "local_port": 1080,
    "password": "xxxosihd",
    "timeout": 60,
    "method": "rc4-md5"
}
```

luci如图配置即可！

![shadowsocks-libev-spec][6]

### 配置chinadns

由于我们默认绑定了5300端口，现在需要指定解析服务器为127.0.0.1:5300，如图配置：

![chinadns][7]

### 配置DNS转发

> ps.不要忘了转发DNS解析到127.0.0.1#1053

![转发DNS解析][8]

这样设置后，DNS的查询变为

 - 设备查询dns->路由器:53->chinaDNS->114.114.114.114:53
 - 设备查询dns->路由器:53->chinaDNS->ss:5300->8.8.8.8:53

现在所有子网的设备都可以零配置翻墙了！速度还很快！

## 关于内核不兼容

请移步 https://github.com/shadowsocks/openwrt-shadowsocks/

作者提供了最新版的编译ipk下载，请使用ipk文件安装。

**补充**

## 安装redsocks2

大家还记得[路由器自动翻墙篇][9]中，讲了如何安装redsocks2，现在安装的更简单了。

```
# 安装redsocks2
opkg install redsocks2
# 安装redsocks2后台luci菜单
cd /tmp
# wget 如果404，请移步 http://sourceforge.net/projects/openwrt-dist/files/luci-app/redsocks2/ 自己下载ipk，然后安装
wget http://iweb.dl.sourceforge.net/project/openwrt-dist/luci-app/redsocks2/luci-app-redsocks2_1.3.0-1_all.ipk
opkg install luci-app-redsocks2_1.3.0-1_all.ipk
```

## nginx反代配置

还是记录一下吧，省的以后出错时又得手写。

```
server {
    listen       80;
    server_name  openwrt-dist.kxswq.ml;

    #charset koi8-r;
    location / {
        sub_filter openwrt-dist.sourceforge.net openwrt-dist.kxswq.ml;
        proxy_pass       http://openwrt-dist.sourceforge.net;
        #proxy_redirect     off;
        #proxy_set_header   Host $host;
        proxy_set_header   X-Real-IP  $remote_addr;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   Referer http://$host;
    }
    error_page  404              /404.html;
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```


  [1]: https://blog.phpgao.com/xiaomi_router_opkg.html
  [2]: https://blog.phpgao.com/usr/uploads/2015/05/3504485218.png
  [3]: https://blog.phpgao.com/usr/uploads/2015/05/678991458.png
  [4]: http://sourceforge.net/projects/openwrt-dist/files/shadowsocks-libev/
  [5]: https://blog.phpgao.com/usr/uploads/2015/05/4030858451.png
  [6]: https://blog.phpgao.com/usr/uploads/2015/04/1009344190.png
  [7]: https://blog.phpgao.com/usr/uploads/2015/04/3584082369.png
  [8]: https://blog.phpgao.com/usr/uploads/2015/04/565996981.png
  [9]: https://blog.phpgao.com/carzy_router.html