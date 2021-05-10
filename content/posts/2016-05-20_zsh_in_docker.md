---
title: "把zsh放到docker里"
categories: [ "代码人生" ]
tags: [ "docker" ]
draft: false
slug: "zsh_in_docker"
date: "2016-05-20 11:22:00"
url: "zsh_in_docker.html"
---

![zsh + oh-my-zsh][1]


每次进入docker容器内操作一些东西时，那效率简直太低！吓得老高赶紧把**zsh + oh-my-zsh**打包塞进去，终于可以愉快的开发了！zsh都装了，sshd也一起开了得了！


<!--more-->

## 安装zsh和oh-my-zsh进docker

```
# optmize
RUN apt-get update && apt-get install -y \
    zsh \
    git-core && rm -rf /var/lib/apt/lists/*
RUN git clone https://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh \
    && cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc \
    && chsh -s /bin/zsh

```

## 开启sshd

转自[Dockerizing an SSH daemon service][2]

```
FROM ubuntu:14.04
MAINTAINER Sven Dowideit <SvenDowideit@docker.com>

RUN apt-get update && apt-get install -y openssh-server
RUN mkdir /var/run/sshd
RUN echo 'root:screencast' | chpasswd
RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]
```

注意，默认的端口不一定是22，可能是22222，具体可以`cat /etc/ssh/sshd_config |grep Port`!


  [1]: https://blog.phpgao.com/usr/uploads/2016/05/3841937683.png
  [2]: https://docs.docker.com/engine/examples/running_ssh_service/