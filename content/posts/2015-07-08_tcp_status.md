---
title: "tcp链接的几种状态"
categories: [ "代码人生" ]
tags: [ "tcp" ]
draft: false
slug: "tcp_status"
date: "2015-07-08 04:02:00"
url: "tcp_status.html"
---

转自 [tcp链接的几种状态][1]

![tcp链接的几种状态][2]


<!--more-->


通常情况下，一个正常的TCP连接，都会有三个阶段:

1. TCP三次握手;
1. 数据传送;
1. TCP四次挥手

里面的几个概念：

- SYN: (同步序列编号,Synchronize Sequence Numbers)
- ACK: (确认编号,Acknowledgement Number)
- FIN: (结束标志,FINish)

## TCP三次握手

客户端发起一个和服务创建TCP链接的请求，这里是SYN(J)
服务端接受到客户端的创建请求后，返回两个信息： SYN(K) + ACK(J+1)
客户端在接受到服务端的ACK信息校验成功后(J与J+1)，返回一个信息：ACK(K+1)
服务端这时接受到客户端的ACK信息校验成功后(K与K+1)，不再返回信息，后面进入数据通讯阶段


## 数据通讯

客户端/服务端 read/write数据包

## TCP四次握手

1. 客户端发起关闭请求，发送一个信息：FIN(M)
1. 服务端接受到信息后，首先返回ACK(M+1),表明自己已经收到消息。
1. 服务端在准备好关闭之前，最后发送给客户端一个 FIN(N)消息，询问客户端是否准备好关闭了
1. 客户端接受到服务端发送的消息后，返回一个确认信息: ACK(N+1)
1. 最后，服务端和客户端在双方都得到确认时，各自关闭或者回收对应的TCP链接。

## 详细的状态说明

### SYN_SEND

客户端尝试链接服务端，通过open方法。也就是TCP三次握手中的第1步之后,注意是客户端状态
sysctl -w net.ipv4.tcp_syn_retries = 2 ,做为客户端可以设置SYN包的重试次数，默认5次(大约180s)引用校长的话：仅仅重试2次，现代网络够了

### SYN_RECEIVED

服务接受创建请求的SYN后，也就是TCP三次握手中的第2步，发送ACK数据包之前
注意是服务端状态,一般15个左右正常，如果很大，怀疑遭受SYN_FLOOD攻击
sysctl -w net.ipv4.tcp_max_syn_backlog=4096 , 设置该状态的等待队列数，默认1024，调大后可适当防止syn-flood，可参见man 7 tcp
sysctl -w net.ipv4.tcp_syncookies=1 ,　打开syncookie，在syn backlog队列不足的时候，提供一种机制临时将syn链接换出
sysctl -w net.ipv4.tcp_synack_retries = 2 ,做为服务端返回ACK包的重试次数，默认5次(大约180s)引用校长的话：仅仅重试2次，现代网络够了

### ESTABLISHED

客户端接受到服务端的ACK包后的状态，服务端在发出ACK在一定时间后即为ESTABLISHED
sysctl -w net.ipv4.tcp_keepalive_time = 1200 ，默认为7200秒(2小时)，系统针对空闲链接会进行心跳检查，如果超过net.ipv4.tcp_keepalive_probes * net.ipv4.tcp_keepalive_intvl = 默认11分，终止对应的tcp链接，可适当调整心跳检查频率
目前线上的监控 waring:600 , critial : 800

### FIN_WAIT1

主动关闭的一方，在发出FIN请求之后，也就是在TCP四次握手的第1步

### CLOSE_WAIT

被动关闭的一方，在接受到客户端的FIN后，也就是在TCP四次握手的第2步

### FIN_WAIT2

主动关闭的一方，在接受到被动关闭一方的ACK后，也就是TCP四次握手的第2步
sysctl -w net.ipv4.tcp_fin_timeout=30, 可以设定被动关闭方返回FIN后的超时时间，有效回收链接，避免syn-flood.

### LASK_ACK

被动关闭的一方，在发送ACK后一段时间后(确保客户端已收到)，再发起一个FIN请求。也就是TCP四次握手的第3步

### TIME_WAIT

主动关闭的一方，在收到被动关闭的FIN包后，发送ACK。也就是TCP四次握手的第4步
sysctl -w net.ipv4.tcp_tw_recycle = 1 , 打开快速回收TIME_WAIT，Enabling this option is not recommended since this causes problems when working with NAT (Network Address Translation)
sysctl -w net.ipv4.tcp_tw_reuse =1, 快速回收并重用TIME_WAIT的链接, 貌似和tw_recycle有冲突，不能重用就回收?
net.ipv4.tcp_max_tw_buckets: 处于time_wait状态的最多链接数，默认为180000.

## 相关说明

主动关闭方在接收到被动关闭方的FIN请求后，发送成功给对方一个ACK后,将自己的状态由FIN_WAIT2修改为TIME_WAIT，而必须再等2倍的MSL(Maximum Segment Lifetime,MSL是一个数据报在internetwork中能存在的时间)时间之后双方才能把状态 都改为CLOSED以关闭连接。目前RHEL里保持TIME_WAIT状态的时间为60秒

keepAlive策略可以有效的避免进行三次握手和四次关闭的动作

  [1]: http://xinaction.iteye.com/blog/1180541
  [2]: https://blog.phpgao.com/usr/uploads/2015/07/503433726.jpg