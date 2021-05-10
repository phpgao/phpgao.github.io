---
title: "小米路由器mini折腾之去adbyby广告篇"
categories: [ "路由器" ]
tags: [ "router","xiaomi","pandorabox","ad" ]
draft: false
slug: "xiaomi_router_noad"
date: "2015-03-07 14:25:00"
url: "xiaomi_router_noad.html"
---

为了去广告，普通用户可在扩展插件中找到非官方插件-网页广告拦截，安装即可！

但是这种方法的缺点也很明显，无法自由控制去广告功能，而adbyby官方为我们提供了MT7620A平台的编译版本，我们只需要下载运行即可！


<!--more-->


用了一段时间，总结adbyby几点不足：

- 手机端的广告还是会存在
- 运行不能加参数

此方法适用于官方系统和pandorabox/openwrt。

## 直接运行

```bash
wget http://info.adbyby.com/download/7620n.tar.gz
tar -zxf openwrt.tar.gz
cd openwrt/bin
chmod 777 adbyby
./adbyby&
```

## 开机自动启动

```bash
vi /etc/rc.local

# 在exit(0)之前加入以下代码

cd /tmp
wget http://info.adbyby.com/download/7620n.tar.gz -P /tmp
if [ "$?" -eq "0" ] ; then
    tar zxf 7620n.tar.gz
    chmod 777 adbyby
    ./adbyby &
    process=`ps | grep adbyby | grep -v grep | wc -l`
    if [ "$process" -eq "1" ] ; then
        iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-ports 8118
    fi
fi
```

这样设置后路由器每次重启都会自动运行，使用`ps|grep adbyby`可以查看进程。

adbyby配置可参考[官方配置说明][1]。

> ps.由于adbyby运行在路由器，所以https的流量无法过滤，但是理论上效率比ABP高。

Reference：

http://jingyan.baidu.com/article/ff42efa90652d5c19e2202e8.html


  [1]: http://www.adbyby.com/setup.htm