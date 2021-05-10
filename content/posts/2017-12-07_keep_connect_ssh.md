---
title: "保持ssh不自动断开"
categories: [ "代码人生" ]
tags: [ "ssh" ]
draft: false
slug: "keep_connect_ssh"
date: "2017-12-07 08:14:38"
url: "keep_connect_ssh.html"
---

在mac上ssh自己的服务器后总是会断开，比如wget某一个东西，或者tar一个大文件，如果你总是受此困扰，不妨尝试一下此文里的配置。


<!--more-->

其实ssh已经考虑到了此问题，但是可能考虑到安全和资源占用等因素，默认没有启用，现在我们把它打开。

## 服务端设置

找到`/etc/ssh/sshd_config`，大约126-127行，取消注释，并修改数值

```config
# 30表示30s给客户端发送一次心跳
ClientAliveInterval 30
# 3此客户端没有返回心跳，则会断开连接
ClientAliveCountMax 3
```


## 客户端配置

如果是想让主机所有用户都生效，修改`/etc/ssh/ssh_config`
如果只想让本人生效，则修改 `~/.ssh/config`

```
Host *
    ServerAliveInterval 30
    ServerAliveCountMax 3
```

> 如果此处还有一个配置项叫 SendEnv LANG LC_*，老高建议最好注释掉，否则如果本地是中文环境，而服务器没有对应的中文语言选项时系统可能会出现很多莫名其妙的BUG，所以保持原始英文语言环境为上。


## 一次性配置

如果只是想临时使用一次，完全可以不用大动干戈地找配置文件改，ssh命令支持直接注入参数，如下：

```bash
ssh -o ServerAliveInterval=30 user@host
```

## 查看是否生效

想测试是否生效，我们直接ssh到服务器后等一会儿看效果就行，但是如果想看到服务器和客户端发送心跳包的过程，可以这样

```
ssh -o ServerAliveInterval=30 -vvv user@host
```

等30s后我们应该可以看到如下字样，说明我们在指定时间发送了心跳给服务器，服务器也有应答。同理如果我们在服务器也打开了心跳，则应该是先收到服务器的心跳然后再应答。

```
debug3: send packet: type 80
debug3: receive packet: type 82
```

更多研究可以参考[Linux使用ssh超时断开连接的真正原因][1]


  [1]: http://bluebiu.com/blog/linux-ssh-session-alive.html