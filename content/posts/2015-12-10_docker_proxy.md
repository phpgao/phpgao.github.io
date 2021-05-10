---
title: "让你的docker支持代理"
categories: [ "代码人生" ]
tags: [ "proxy","docker" ]
draft: false
slug: "docker_proxy"
date: "2015-12-10 09:10:00"
url: "docker_proxy.html"
---

功夫网越来越猖狂，连docker都不放过。。。

So，我们需要使用代理去下载、搜索我们需要的镜像！

需要注意的是：

> 通过老高的实验，在修改了代理设置后必须重启docker的守护进程！

<!--more-->


```
sudo vim /etc/sysconfig/docker

# 如果你的系统找不到这个文件，搜索一下

sudo find /etc -name docker

# ADD(假设10.16.13.18:8080为你的代理地址)

HTTP_PROXY="http://10.16.13.18:8080"
HTTPS_PROXY="https://10.16.13.18:8080"


# 最后重启docker守护服务

sudo service docker restart
```


最近在研究私有docker源的搭建，参考[Docker Registry][1]，部署需要https。







参考：

[Cannot download Docker images behind a proxy][2]


  [1]: https://docs.docker.com/registry/
  [2]: http://stackoverflow.com/questions/23111631/cannot-download-docker-images-behind-a-proxy