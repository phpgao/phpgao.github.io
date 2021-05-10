---
title: "SS公共服务器的基本安全措施"
categories: [ "服务器技术" ]
tags: [ "iptables","shadowsocks","server","safe" ]
draft: false
slug: "ss-public-server"
date: "2015-06-08 08:15:00"
url: "ss-public-server.html"
---

老高以前免费分享的ss服务器 [免费shadowsocks][1] ，被攻击了。。。后台检测到有大量的垃圾邮件，是我大意了，忘记了给做一些保护措施，赶紧补上。


<!--more-->



2015-11-13更：

参考了[Securing Public Shadowsocks Server][2]一文，好好整理了一下，分享给大家


## 查看并修改文件打开数

```
ulimit -a
ulimit -n
ulimit -n 51200
```

## 限制连接数

```bash
iptables -A INPUT -p tcp --syn --dport ${SHADOWSOCKS_PORT} -m connlimit --connlimit-above 32 -j REJECT --reject-with tcp-reset
```

## 禁止链接localhost

运行时加入`--forbidden-ip 127.0.0.1,::1`参数。

## 添加非root用户

```bash
sudo useradd ssuser
sudo ssserver [other options] --user ssuser

# ubuntu 创建一个安全用户
adduser --system --disabled-password --disabled-login --no-create-home ssuer

# centos 创建一个安全用户
useradd -s /usr/sbin/nologin -r -M -d /dev/null ssuer
```

## 禁止非WEB流量

```bash
iptables -t filter -A OUTPUT -d 127.0.0.1 -j ACCEPT
iptables -t filter -m owner --uid-owner ssuser -A OUTPUT -p tcp --sport 1080 -j ACCEPT
iptables -t filter -m owner --uid-owner ssuser -A OUTPUT -p tcp --dport 80 -j ACCEPT
iptables -t filter -m owner --uid-owner ssuser -A OUTPUT -p tcp --dport 443 -j ACCEPT
iptables -t filter -m owner --uid-owner ssuser -A OUTPUT -p tcp -j REJECT --reject-with tcp-reset
```

## 禁止BT下载

### 安装nginx

```
apt-get install nginx
```

### 配置服务器

```nginx
server {
    listen 0.0.0.0:3128;
    resolver 8.8.8.8;
    location / {
        set $upstream_host $host;
    if ($request_uri ~ "^/announce.*") {
            return 403;
        }
        if ($request_uri ~ "^.*torrent.*") {
            return 403;
        }
        proxy_set_header Host $upstream_host;
        proxy_pass http://$upstream_host;
        proxy_buffering off;
    }
}
```

### 将80端口的流量导入3128端口

```bash
iptables -t nat -m owner --uid-owner ssuser -A OUTPUT -p tcp --dport 80 -j REDIRECT --to-port 3128
```

## 配置防火墙

```bash
# 出口
iptables -A OUTPUT -o lo -j ACCEPT
iptables -A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A OUTPUT -p tcp --dport 80 -j ACCEPT
iptables -A OUTPUT -p tcp --dport 443 -j ACCEPT
iptables -A OUTPUT -p tcp --dport 53 -j ACCEPT
iptables -A OUTPUT -p udp --dport 53 -j ACCEPT
# limit
iptables -A OUTPUT -m limit --limit 30/s -j ACCEPT
iptables -P OUTPUT DROP

# 进入
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT ! -i lo -d 127.0.0.0/8 -j REJECT
iptables -A INPUT  -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -p tcp -m state --state NEW --dport XXX -j ACCEPT
iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A INPUT -p tcp --dport 80 -m limit --limit 100/minute --limit-burst 100 -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -j ACCEPT
iptables -A INPUT -p tcp --dport 8080 -j ACCEPT
iptables -A INPUT -p icmp -m icmp --icmp-type 8 -j ACCEPT
iptables -P INPUT DROP


iptables -A FORWARD -j REJECT
iptables -P FORWARD DROP

# 邮件
iptables -I FORWARD -p tcp --dport 25 -j DROP
iptables -I INPUT -p tcp --dport 25 -j DROP
iptables -I OUTPUT -p tcp --dport 25 -j DROP
```


## 随机端口

### 服务端

随机端口可以有效的避免被认证...
ss监听在23，将81-1023端口的流量转发到23端口。

```bash
iptables -t nat -A PREROUTING -p tcp -m multiport --dport 81:1023 -j REDIRECT --to-ports 23
iptables -t nat -A PREROUTING -p udp -m multiport --dport 81:1023 -j REDIRECT --to-ports 23
```

### 客户端

客户端是openwrt上的ss，我们希望将目标是vps ip，端口23的流量，随机拆成端口范围81-1023的流量。

```bash
iptables -t nat -I OUTPUT 1 -d [VPS] -p tcp --dport 23 -j DNAT --to-destination [VPS]:81-1023 --random
iptables -t nat -I OUTPUT 1 -d [VPS] -p udp --dport 23 -j DNAT --to-destination [VPS]:81-1023 --random
```


## 运行

```bash
ssserver --workers 10 --pid-file /tmp/ss.pid --log-file /tmp/ss.log --user nobody -c /root/config.json -v --forbidden-ip 127.0.0.1,::1 -d start
```




Refer:



[shadowsocks的优化和安全设置总结][3]


  [1]: https://blog.phpgao.com/try_shadowsocks_via_qrcode.html
  [2]: https://github.com/shadowsocks/shadowsocks/wiki/Securing-Public-Shadowsocks-Server
  [3]: https://blog.liantian.me/2015/01/27/shadowsocks%E7%9A%84%E4%BC%98%E5%8C%96%E5%92%8C%E5%AE%89%E5%85%A8%E8%AE%BE%E7%BD%AE%E6%80%BB%E7%BB%93/