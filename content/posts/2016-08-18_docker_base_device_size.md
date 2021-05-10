---
title: "修改docker容器的Base-Device-Size"
categories: [ "服务器技术" ]
tags: [ "docker" ]
draft: false
slug: "docker_base_device_size"
date: "2016-08-18 06:41:03"
url: "docker_base_device_size.html"
---

老高的容器在运行一段时间的时候会突然无法写入数据，报错

> docker no space left on device


Google之，发现原来是**Base Device Size**的问题，其默认值是10G，而容器经过一段时间的运行写入文件量早已超过10G，就造成无法写入数据的问题了。


下面给出解决方案：

<!--more-->


系统为Centos，其他OP应该大同小异。

首先查看docker的服务配置文件

> /etc/systemd/system/docker.service

```bash
cat /etc/systemd/system/docker.service

[Unit]
Description=Docker Application Container Engine
Documentation=http://docs.docker.com
After=network.target
Wants=docker-storage-setup.service

[Service]
Type=notify
EnvironmentFile=-/etc/sysconfig/docker
EnvironmentFile=-/etc/sysconfig/docker-storage
EnvironmentFile=-/etc/sysconfig/docker-network
Environment=GOTRACEBACK=crash
ExecStart=/usr/bin/docker daemon --registry-mirror=https://hg3r3yxx.mirror.aliyuncs.com $OPTIONS \
          $DOCKER_STORAGE_OPTIONS \
          $DOCKER_NETWORK_OPTIONS \
          $ADD_REGISTRY \
          $BLOCK_REGISTRY \
          $INSECURE_REGISTRY
LimitNOFILE=1048576
LimitNPROC=1048576
LimitCORE=infinity
MountFlags=slave
TimeoutStartSec=1min
Restart=on-failure

[Install]
WantedBy=multi-user.target
```


我们只关心其中的ExecStart一节，于是发现守护进程启动命令中已经预留**$DOCKER_STORAGE_OPTIONS**变量，并且应该存在于这个文件中 **/etc/sysconfig/docker-storage**。这就好办了，直接把老高想改成的40G写入到此文件中即可！

```
sudo echo "DOCKER_STORAGE_OPTIONS=--storage-opt dm.basesize=40G" > /etc/sysconfig/docker-storage
```