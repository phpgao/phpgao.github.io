---
title: "docker使用笔记V----FTP-与-SSH-支持"
categories: [ "服务器技术" ]
tags: [ "ssh","ftp","docker" ]
draft: false
slug: "docker-note5-ftp-and-ssh"
date: "2016-06-30 08:19:00"
url: "docker-note5-ftp-and-ssh.html"
---

给容器赋予FTP和ssh登录在调试和使用阶段都是很不错的功能！下面看老高怎么实现的。

(基于ubuntu:xenial)

<!--more-->

## FTP

参考[README.Configuration-File][1] 和 [Documentation for version 1.0.42][2]

```
FROM ubuntu:xenial

RUN sed -i "s/archive\.ubuntu\.com/mirrors\.ustc\.edu\.cn/g" /etc/apt/sources.list
RUN apt-get update \
    && apt-get install -y wget build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN wget https://download.pureftpd.org/pub/pure-ftpd/releases/pure-ftpd-1.0.42.tar.gz
RUN tar -xzf pure-ftpd-1.0.42.tar.gz
RUN cd /pure-ftpd-1.0.42; ./configure --with-everything --with-privsep --without-capabilities
RUN cd /pure-ftpd-1.0.42; make; make install

# 虚拟用户
RUN (echo 'laogao'; echo 'laogao') | pure-pw useradd www -u root -d /home/www
pure-pw mkdb

# 系统用户
RUN echo root:laogao |chpasswd


CMD ["/usr/local/sbin/pure-ftpd -p 40000:40009 -A -b -B -C 20 -D -E -H -j -4 -lpuredb:/etc/pureftpd.pdb"]
```

测试命令：

```
ftp 127.0.0.1
root
laogao
```

## SSH

```
FROM ubuntu:xenial

RUN sed -i "s/archive\.ubuntu\.com/mirrors\.ustc\.edu\.cn/g" /etc/apt/sources.list
RUN apt-get update \
    && apt-get install -y openssh-server \
    && rm -rf /var/lib/apt/lists/*

#ssh
RUN mkdir /var/run/sshd
RUN echo 'root:screencast' | chpasswd
RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd
RUN sed -i 's/Port 22/Port 22222/' /etc/ssh/sshd_config
ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

EXPOSE 22222

CMD ["/usr/sbin/sshd", "-D"]
```

测试命令

```
ssh root@127.0.0.1 -p 22222
screencast
```


## 小贴士

你的服务器可能需要运行着防火墙，下面的命令可以帮你开启端口：

```
firewall-cmd --permanent --add-service=ssh
firewall-cmd --permanent --add-port=20-21/tcp
firewall-cmd --permanent --add-port=20000-20009/tcp

firewall-cmd --reload
```

Renference:

[Dockerizing an SSH daemon service][3]


  [1]: https://download.pureftpd.org/pub/pure-ftpd/doc/README.Configuration-File
  [2]: https://download.pureftpd.org/pub/pure-ftpd/doc/README
  [3]: https://docs.docker.com/engine/examples/running_ssh_service/