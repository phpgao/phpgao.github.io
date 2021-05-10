---
title: "openwrt的免密登录"
categories: [ "路由器" ]
tags: [ "openwrrt" ]
draft: false
slug: "openwrt_ssh_without_passwd"
date: "2017-01-16 13:39:00"
url: "openwrt_ssh_without_passwd.html"
---

openwrt的ssh和标准ssh所需要的秘钥格式不同(ssh-kengen产生的是PEM格式)，想要生成能够正常工作的秘钥，使用下面的命令。


<!--more-->


```
dropbearkey -t rsa -f ~/.ssh/id_rsa
dropbearkey -t rsa -f ~/.ssh/id_rsa -s 2048
```

结合老高N久以前写的文章[使用ssh的反向隧道管理内网路由器][1]，我们使用如下命令就可以实现，假设我们把路由器的22端口映射到VPS上的2222端口，假设我们已经建立了到VPS的免登陆。

```
ssh -gfTN -R 0.0.0.0:2222:lu.you.qi.ip:22 v.p.s.ip -p ssh.port -l username -K 30
```

解释：

> 0.0.0.0 指 映射到IPV4
11111 指 将要映射到的端口
lu.you.qi.ip 指 路由器的LAN口IP
v.p.s.ip 指 VPS的IP
ssh.port 指 登录VPS的端口
username 就不说了
-K 30 指的是每隔30s发送一段加密数据以保持链接

开机启动：

vi /etc/init.d/ssh

```
#!/bin/sh /etc/rc.common
# Mr.Gao endoffight@gmail.com
# Copyright (C) 2007 OpenWrt.org
START=99
STOP=15
VPS_IP=blog.phpgao.com
VPS_PORT=22
VPS_USERNAME=laogao
LOCAL_PORT=192.168.1.1:22
REMOTE_PORT=0.0.0.0:2222
PROG=/usr/bin/ssh
KEEP_ALIVE=30
start() {
        $PROG -gfTNR $REMOTE_PORT:$LOCAL_PORT $VPS_IP -p $VPS_PORT -l $VPS_USERNAME -K $KEEP_ALIVE
}
stop() {
        killall -q -9 ssh
        return 0
}
```

接着执行

```
chmod a+x /etc/init.d/ssh
/etc/init.d/ssh enable
/etc/init.d/ssh start
```

参考：

[Init Scripts][2]
[Dropbear Configuration][3]
[Open SSH Server connection drops out after few or N minutes of inactivity][4]
[What options `ServerAliveInterval` and `ClientAliveInterval` in sshd_config exactly do?][5]
[Keep Your Linux SSH Session From Disconnecting][6]


  [1]: https://blog.phpgao.com/ssh-reverse-tunnel.html
  [2]: https://wiki.openwrt.org/doc/techref/initscripts
  [3]: https://wiki.openwrt.org/doc/uci/dropbear
  [4]: https://www.cyberciti.biz/tips/open-ssh-server-connection-drops-out-after-few-or-n-minutes-of-inactivity.html
  [5]: http://unix.stackexchange.com/questions/3026/what-options-serveraliveinterval-and-clientaliveinterval-in-sshd-config-exac
  [6]: http://www.howtogeek.com/howto/linux/keep-your-linux-ssh-session-from-disconnecting/