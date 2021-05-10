---
title: "VPS安全之防火墙设置"
categories: [ "服务器技术" ]
tags: [ "iptables","vps","shadowsocks","security" ]
draft: false
slug: "vps_iptables"
date: "2014-12-29 03:31:00"
url: "vps_iptables.html"
---

iptables是基于内核的防火墙，功能非常强大，iptables内置了filter，nat和mangle三张表。

filter负责过滤数据包，包括的规则链有，input，output和forward；

nat则涉及到网络地址转换，包括的规则链有，prerouting，postrouting和output；

mangle表则主要应用在修改数据包内容上，用来做流量整形的，默认的规则链有：INPUT，OUTPUT，NAT，POSTROUTING，PREROUTING；

input匹配目的IP是本机的数据包；
forward匹配流经本机的数据包；
prerouting用来修改目的地址用来做DNAT；
postrouting用来修改源地址用来做SNAT。


<!--more-->


## iptables的语法

```bash
iptables -A INPUT -j ACCEPT
-A --append # 向规则链中添加一条规则，默认被添加到末尾
iptables -D INPUT -j ACCEPT
-D --delete # 删条规则链


-A 向规则链中添加一条规则，默认被添加到末尾
-T 指定要操作的表，默认是filter
-D 从规则链中删除规则，可以指定序号或者匹配的规则来删除
-R 进行规则替换
-I 插入一条规则，默认被插入到首部
-F 清空所选的链，重启后恢复
-N 新建用户自定义的规则链
-X 删除用户自定义的规则链
-p 用来指定协议可以是tcp，udp，icmp等也可以是数字的协议号，
-s 指定源地址
-d 指定目的地址
-i 进入接口
-o 流出接口
-j 采取的动作，accept，drop，snat，dnat，masquerade
--sport 源端口
--dport 目的端口，端口必须和协议一起来配合使用

-P 定义预设规则(Policy)

语法: iptables -P [INPUT,OUTPUT,FORWARD] [ACCEPT,DROP]

iptables -P INPUT DROP
iptables -P OUTPUT ACCEPT
iptables -P FORWARD DROP

```

## iptables常用操作

```bash
iptables -L  #列出iptables规则
iptables -F  #清除iptables内置规则
iptables -X  #清除iptables自定义规则
# 可以加入-n查看


/etc/rc.d/init.d/iptables save  # 保存规则，只有保存规则后重启机器才不会生效
/etc/init.d/iptables status     # 查看规则
/etc/init.d/iptables stop       # 停用防火墙
chkconfig –level 35 iptables off  # 停止防火墙服务
```

## 检查iptables是否生效

```bash
# 检测是否打开了22端口
telnet www.phpgao.com 22
# 返回telnet: connect to address 104.224.144.xxx: Connection refused
```

## iptables规则的导出和还原

```bash
iptables-save > somefile
iptables-restore < somefile
```

## 推荐的规则

```bash
*filter

#  Allow all loopback (lo0) traffic and drop all traffic to 127/8 that doesn't use lo0
-A INPUT -i lo -j ACCEPT
#-A OUTPUT -o lo -j ACCEPT
-A INPUT ! -i lo -d 127.0.0.0/8 -j REJECT

#  Accept all established inbound connections
-A INPUT  -m state --state ESTABLISHED,RELATED -j ACCEPT
#-A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

#  Allow all outbound traffic - you can modify this to only allow certain traffic
-A OUTPUT -j ACCEPT

#  Allow HTTP and HTTPS connections from anywhere (the normal ports for websites and SSL).
-A INPUT -p tcp --dport 80 -j ACCEPT
-A INPUT -p tcp --dport 443 -j ACCEPT

#  DNS(not need this rule, as we only accept the input with [ESTABLISHED,RELATED])
#-A INPUT  -p udp --dport 53 -j ACCEPT
#  DNS
#-A OUTPUT -p udp --dport 53 -j ACCEPT

#  Allow ports for testing
#-A INPUT -p tcp --dport 8080:8090 -j ACCEPT

#  Allow ports for MOSH (mobile shell)
#-A INPUT -p udp --dport 60000:61000 -j ACCEPT

#  Allow SSH connections
#  The -dport number should be the same port number you set in sshd_config
-A INPUT -p tcp -m state --state NEW --dport xxx -j ACCEPT

#  Allow ping
-A INPUT -p icmp -m icmp --icmp-type 8 -j ACCEPT
#-A OUTPUT -p icmp --icmp-type echo-request -j ACCEPT
#-A OUTPUT -p icmp --icmp-type echo-request -j ACCEPT

#  DDoS
-A INPUT -p tcp --dport 80 -m limit --limit 25/minute --limit-burst 100 -j ACCEPT

#  Log
-A INPUT  -m limit --limit 5/min -j LOG --log-prefix "iptables denied: " --log-level 7
-A OUTPUT -m limit --limit 5/min -j LOG --log-prefix "iptables denied: " --log-level 7

#  Log iptables denied calls
# -A INPUT -m limit --limit 5/min -j LOG --log-prefix "iptables denied: " --log-level 7

# Allow shadowsocks connections
#  The -dport number should be the same port number you set in config.json
-A INPUT -p tcp --dport xxx -j ACCEPT

#  Reject all other inbound - default deny unless explicitly allowed policy
-A INPUT -j REJECT
-A FORWARD -j REJECT
#-A OUTPUT -j DROP

COMMIT
```

Reference:

http://www.cnblogs.com/argb/p/3535179.html
http://www.cnblogs.com/rockee/archive/2012/05/17/2506671.html
http://yijiu.blog.51cto.com/433846/1356254
http://linux.chinaunix.net/techdoc/net/2008/02/14/978344.shtml