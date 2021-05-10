---
title: "使用ssh的反向隧道管理内网路由器"
categories: [ "服务器技术" ]
tags: [ "Linux","ssh","vps","openwrt","tunnel" ]
draft: false
slug: "ssh-reverse-tunnel"
date: "2015-10-02 04:04:00"
url: "ssh-reverse-tunnel.html"
---

> SSH协议真是了不得！

![pandorabox][1]


光是看标题，大家会看的云里雾里的，倾听老高细细道来！

<!--more-->



## 起因

自从老高“下海”以后，就再也没有碰过家里的小米路由器了，国庆期间突然想把之前在路由器中远程下载的电影拿来看看。说着简单，实际操作起来，还是有一定难度的。

## 分析

完成这项任务，主要需要克服几个难点！

1. 家里的IP是坑爹地10.0开头，也就是传说中的大局域网，几百个家庭使用一个公网IP上网，真是天朝的特色。
1. 就算拿到了公网IP，经过老高的扫描，发现已经被封锁了所有端口，意思就是只能让你请求外部网络，无法直接对外提供服务，所以如何ssh到我的路由器呢？
1. 就算是有公网IP，并且老天开恩，给你开了某一个端口，终于了一对外提供服务了，可是每次路由器重启，公网IP可能会变化，怎么样才能准确定位家里的路由器呢？

答：

第三个问题可以参考老高的[小米路由器mini折腾之DDNS动态域名解析篇][2]解决。


第一、二问题可以通过申请公网IP解决，老高也尝试过，开始联通只给我留了一个21端口，一个端口你说是给80还是ssh呢？虽然需要我二选一，但起码还是可以有一个端口用了。没想到过了一段时间，联通干脆直接封了所有端口，再问候完联通全家后，只能花钱买花生棒了。

花生棒确实能解决一些问题，但入手后发现我还是太嫩了，不要忘了无奸不商，贝锐这货再花生棒里也作了N多限制，总结一下就是，如果你没有继续花钱买服务，你只能：

1. 使用他提供的域名做映射
1. 只能创建2个端口映射
1. 一个月2G上下行流量

哈哈，又一个坑爹货。既然花钱不成，咱们干脆直接用免费的得了，下面的篇幅主要是关于**使用ssh的隧道技术再内网环境中打洞**！


## 解决问题

有请我们今天的主角SSH上场。

### 原理

其实我们很早就使用SSH的隧道功能FQ了，它的数据流向如下：

> 你的机器 ----> 代理软件 ----> 本地代理 ----> 你的VPS

但是，今天我们需要实现的是：

> 你的机器 ----> 你的VPS ----> 你的内网机器

有点像nginx反向代理，我们操作VPS的时候就像操作我们内网的机器一样，SSH为我们提供了一个隧道，把请求都转发至内网机器。

### 需要什么

我们需要的元素如下：

1. 会输入linux命令并懂一点网络的人
1. 一台装好linux的VPS
1. 目标机器为刷好openwrt的小米路由器


## 动手吧

### VPS

首先，我们开启SSHD的GatewayPorts选项。

```bash
vim /etc/ssh/sshd_config
# 找到GatewayPorts选项，将其变为yes
# GatewayPorts yes

# 重启sshd服务
service sshd restart
```

### 路由器

首先使用各种办法登陆路由器后台，老高使用的是花生棒远程登录，如果你在路由器身边，那更好不过了！

然后我们看看路由器上`ssh`的帮助信息：

```bash
[root@PandoraBox_E1BB:/root]#ssh -h
Dropbear SSH client v2014.63 https://matt.ucc.asn.au/dropbear/dropbear.html
Usage: ssh [options] [user@]host[/port] [command]
-p <remoteport>
-l <username>
-t    Allocate a pty
-T    Don't allocate a pty
-N    Don't run a remote command
-f    Run in background after auth
-y    Always accept remote host key if unknown
-y -y Don't perform any remote host key checking (caution)
-s    Request a subsystem (use by external sftp)
-i <identityfile>   (multiple allowed)
-A    Enable agent auth forwarding
-L <[listenaddress:]listenport:remotehost:remoteport> Local port forwarding
-g    Allow remote hosts to connect to forwarded ports
-R <[listenaddress:]listenport:remotehost:remoteport> Remote port forwarding
-W <receive_window_buffer> (default 24576, larger may be faster, max 1MB)
-K <keepalive>  (0 is never, default 0)
-I <idle_timeout>  (0 is never, default 0)
-J <proxy_program> Use program pipe rather than TCP connection
-c <cipher list> Specify preferred ciphers ('-c help' to list options)
-m <MAC list> Specify preferred MACs for packet verification (or '-m help')
```

看起来很复杂很强大，其实我们的命令很简单：

假设VPS的IP为1.1.1.1，远程转发的端口号为11111（端口号最好高于1024，否则需要使用root权限），远程服务器的ssh端口为7777，登陆用户为username；
继续假设本地路由器需要转发的端口为22，路由器的在LAN中的IP地址为192.168.1.1。

当我们确认好了各个数字的意义后，就可以开始替换下面的命令了。

```bash
ssh -Nfg -R 11111:192.168.1.1:22 1.1.1.1 -p 7777 -l username
```

> ps.这一步需要输入username的密码，如果想要加入开机脚本，记得配置ssh的公钥，让路由器变得可信。


当完成以上配置，我们在VPS上使用命令`netstat -nlp|grep sshd`，就可以发现有两个监听端口号为11111的sshd进程，至此说明隧道搭建完毕！

### 测试

通过以上配置，我们在vps上建立了一个隧道，监听端口11111，并将此端口的数据映射到内网openwrt的路由器上，我们在外网使用一下命令即可完成登陆。

```bash
ssh username@1.1.1.1 -p 11111

# 打印调试信息
ssh username@1.1.1.1 -p 11111 -v
```


  [1]: https://blog.phpgao.com/usr/uploads/2015/10/2363969333.png
  [2]: https://blog.phpgao.com/xiaomi_router_ddns.html