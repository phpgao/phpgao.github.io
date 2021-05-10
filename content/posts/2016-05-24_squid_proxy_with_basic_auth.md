---
title: "Centos7下使用Squid快速搭建带认证的HTTP代理服务器"
categories: [ "服务器技术" ]
tags: [ "proxy","squid" ]
draft: false
slug: "squid_proxy_with_basic_auth"
date: "2016-05-24 03:47:00"
url: "squid_proxy_with_basic_auth.html"
---

在centos下很容易就可以搭建好一个HTTP代理服务器，[搭建一个socks5代理更是简单的不用说][1]！

不管是测试用途还是自己使用，Squid都是一个很不错的代理工具！他支持正向代理，反向代理，还有透明代理！老高最近搭建了一个简单的squid的正向代理，随便记记笔记！

> 如果只是想使用代理而不需要认证功能，**tinyproxy**也是不错的选择！


<!--more-->


## 安装

安装狠简单！

```bash
yum install squid -y
yum install httpd-tools -y
```

## 生成密码文件

```bash
mkdir /etc/squid3/
# laogao 是用户名
htpasswd -cd /etc/squid3/passwords laogao
# 提示输入密码，在此老高设密码为 phpgao
# 注意密码不要超过8位
```

## 测试密码文件

```bash
/usr/lib64/squid/basic_ncsa_auth /etc/squid3/passwords
# 输入 用户名 密码
laogao phpgao
# 提示OK说明成功，ERR是有问题，请检查一下之前步骤
OK

# 测试完成，crtl + c 打断
```

## 配置

```
vim /etc/squid/squid.conf

# 在最后添加

auth_param basic program /usr/lib64/squid/basic_ncsa_auth /etc/squid3/passwords
auth_param basic realm proxy
acl authenticated proxy_auth REQUIRED
http_access allow authenticated

# 这里是端口号，可以按需修改
# http_port 3128 这样写会同时监听ipv6和ipv4的端口，推荐适应下面的配置方法。
http_port 0.0.0.0:3128
```

### 权限控制

squid的权限控制很灵活，具体配置方法可以参考[官方文档][2]，或者[Squid中文权威指南][3]，具体工作原理有点像iptables，用规则去卡控流量。默认的配置只能允许内网用户访问，如果有更多需求，你还可以指定很多规则！

```
acl Foo src 8.8.8.88
# 以下例子来自Squid中文权威指南
acl Foo src 172.16.44.21/255.255.255.255 
acl Foo src 172.16.44.21/32
acl Foo src 172.16.44.21
acl Xyz src 172.16.55.32/255.255.255.248   
acl Xyz src 172.16.55.32/28
acl Bar src 172.16.66.0/255.255.255.0
acl Bar src 172.16.66.0/24
acl Bar src 172.16.66.0
```

如果看不懂，并且只是想用暂时试试squid的功能，老高有一个万金油方法：

```
# 这个IP地址池匹配所有可能的IP地址，但是这样做是有风险的，因为所有人都可以使用你的代理了，不要怪老高没有提醒啊！
acl localnet src 0.0.0.1-255.255.255.255
```

### 日志

squid的日志默认是打开的，位于目录`/var/log/squid/`，当然这个地址还有日志的格式都是可以完全自定义的！具体还是看文档吧。

```
liguangping ll /var/log/squid/
total 52K
-rw-r----- 1 squid squid  14K May 25 11:50 access.log
-rw-r----- 1 squid squid 6.0K May 20 17:14 access.log-20160522.gz
-rw-r----- 1 squid squid  23K May 25 11:50 cache.log
-rw-r----- 1 squid squid 1.2K May 20 11:22 cache.log-20160522.gz
```


## 启动服务

```bash
# 启动
systemctl start squid.service
# 停止
systemctl stop squid.service
# 重启
systemctl restart squid.service
```


  [1]: https://blog.phpgao.com/shadowsocks_on_linux.html
  [2]: http://www.squid-cache.org/Doc/config/acl/
  [3]: http://home.arcor.de/pangj/squid/chap06.html