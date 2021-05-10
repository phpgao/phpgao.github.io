---
title: "docker使用笔记-I----Dockerfile-build-run-kill"
categories: [ "服务器技术" ]
tags: [ "docker","note" ]
draft: false
slug: "docker-note1"
date: "2015-11-12 03:58:00"
url: "docker-note1.html"
---

![docker logo][1]

> docker -- 像安装卸载APP一样简单的部署服务器。


这些文章是老高自己使用docker是的一些笔记，偶尔会更新。

<!--more-->


docker的基本命令之前有一篇[在mac上安装并使用docker][2]

之前只是理论上的，现在来一些干货吧

## 创建一个redis服务

### 创建Dockerfile

```docker
# 选择一个服务器，这里我们选择Ubuntu
FROM ubuntu:14.04
# 维护者当然是自己了
MAINTAINER LAO GAO <endoffight#gmail.com>

# 运行一些命令
RUN    apt-get update \
&& apt-get install redis-server -y

# 需要暴露的端口
EXPOSE      6379

# 执行点，以后讲
ENTRYPOINT  ["/usr/bin/redis-server"]
```

> 小技巧，想要加速你的docker构建，请把COPY等不能缓存的操作尽量延后，更多可以[点此阅读][3](其实在Dockerfile里的每部都会被记录，但是如果build的时候`--force-rm=true`，这些临时镜像就会被删除，也就达不到缓存的效果了！)

### 精简版Dockerfile

```docker
FROM ubuntu:14.04
MAINTAINER LAO GAO <endoffight#gmail.com>
RUN    apt-get update \
&& apt-get install redis-server -y
EXPOSE      6379
ENTRYPOINT  ["/usr/bin/redis-server"]
```

将以上内容写入文件`redis.dockerfile`，或`Dockerfile`都行！

## 构建镜像

```bash

sudo docker build -h

# 大家可以在帮助里找到--rm=true 和 -t 的说明

sudo docker build --rm=true -t laogao/redis .

sudo docker build --rm=true -t laogao/redis -< redis.dockerfile
```

> 由于国情，我们的构建过程可能不是一帆分顺的，在对安全性不高的场景，我们可以适当的精简一些构建命令，比如`apt-get update`，你懂的。

当出现下面的文字时，表示创建成功！

> Removing intermediate container a8cd45890eaa
> Successfully built e458fcc69539

运行`sudo docker images`可以查看所有本地镜像。

## 镜像跑起来

### DOCKER RUN

```bash
# 查看帮助
sudo docker run --help

# -i 表示不在后台运行
sudo docker run -i -t -p 6379:6379 laogao/redis

# 后台运行并限制1G内存
sudo docker run -m 1g -d -p 6379:6379 laogao/redis
```

### DOCKER管理命令

> 如何管理无数个运行在本机的docker实例呢？

```bash
# 镜像在哪儿？
sudo ls -la /var/lib/docker/

# 查看某个容器的环境变量
sodu docker run IMAGE env

# docker的任务管理器 /  默认只显示running的，加上参数-a显示所/-q只显示唯一编号
sudo docker ps --help

# docker容器的删除命令
sudo docker rm --help

# 查看镜像
sudo docker images --help

# 删除镜像
sudo docker rmi --help

# docker的停止命令
sudo docker kill --help

# 一些常用的批量操作命令

# 杀死所有正在运行的容器
sudo docker kill $(docker ps -a -q)

# 删除所有已经停止的容器
sudo docker rm $(docker ps -a -q)
```

至此，你是已经运行起来你的第一个docker实例呢？


  [1]: https://blog.phpgao.com/usr/uploads/2015/11/2256355596.png
  [2]: https://blog.phpgao.com/docker_on_mac.html
  [3]: http://tuxknight-notes.readthedocs.org/en/latest/docker/dockerfile_best_practices_take.html#id1