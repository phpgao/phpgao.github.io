---
title: "docker的tag实验"
categories: [ "服务器技术" ]
tags: [ "tag","docker" ]
draft: false
slug: "docker_tag"
date: "2016-01-25 11:04:00"
url: "docker_tag.html"
---

![docker tag][1]

git的tag功能是为了将代码的某个状态打上一个戳，通过tag我们可以很轻易的找到对应的提交。一般大家会在一些重要事件打tag，比如提测时，开发完成时，测试结束时，版本发布时等等。

对比git的tag，docker的tag似乎更加灵活，docker将文件等信息的变动抽象为一次次的commit，每一次commit以后可能走向不同的分支，当我们完成dockerfile的构建后，会生成一串无规则的字符串代表此次生成的ID，此时，tag的作用就是为他创建一个友好的NAME，方便我们对镜像库的管理。

有时候tag的过度友好也会对我们造成一些困惑，下面老高总结一下在使用docker tag时遇到的坑。

<!--more-->

## docker的images

`docker images`命令可以让我们查询本地的镜像，下图是老高在虚拟机上的运行结果：

![docker images][2]

关于tag，我们比较关注REPOSITORY(名称)，TAG(标签)，IMAGE ID(镜像ID)三列。

## tag的生成

tag是从哪里来的呢？

### docker pull

`docker pull`命令让我们可以方便的从镜像库中传递镜像。

```docker
docker pull ubuntu
docker pull busybox
docker pull ubuntu:14.04
```

此时如果不指定tag(类似:14.04)，那么docker默认会为我们加上**latest**(中文意思是 **最新的**)的tag，为什么会这样呢？因为老高认为这样可以保证某一个有名字的镜像一定有一个tag，这样有利于docker镜像的分发。

![latest][3]

### 由Dockerfile创建的镜像

![docker help][4]

写过Dockerfile的同学应该知道，docker build的时候有一个选项是`-t`，意思是指定镜像名和一个可选的tag，意思就是-t后面接的是我们想要取得名字，老高在[docker使用笔记 I -- Dockerfile\build\run\kill][5]中使用了laogao/redis作为第一个构建的镜像名，名字的后面还可以加上一个tag，可以指定一些额外的信息，如版本号。

```docker
# 使用Dockerfile创建名为laogao/redis的镜像
docker build --rm=true -t laogao/redis .

# 如果不指定任何信息，此时也会构建完成，但是会生成一个REPOSITORY和TAG都为<none>的记录，此时如果使用`docker tag + ID + 新名称`时，并且不指定tag时，系统会自动帮你填上**latest**
docker build --rm=true .
```

> ps. 你还记得--rm=true是什么意思吗？

### 手动指定docker tag

此时把上一步生成的*ubuntu:latest*指定为*ubuntu:16.10*，我们可以这样做。

> ps.记住，tag中不能再包含：(冒号)了!

![docker tag][6]

在命令行中我们没有给原名字打上tag，命令为`docker tag ubuntu ubuntu:16.10`，系统又帮我们加上了**latest**，也就是说，当我们不指定tag的时候，系统会自动帮我们补上latest的tag，然后去匹配，如果命中，就用对应的容器ID去创建新的tag。当我们执行刚才命令的时候，其实还是存在一个ubuntu:14.04的镜像，按理来说会发生重名，但是系统还是优先使用了ID为**613aff232d62**的镜像。

## 理解latest

这个latest其实在使用中不是**最新**的意思，而是**默认值**(defalut)的意思。也就是说，如果在tag为可选的命令中，我们没有写上tag，如 `docker pull ubuntu:14.04` vs `docker pull ubuntu` ，前者有确定的tag，而后者没有，这时系统会自动添加一个**:latest**标签，然后去匹配。这时如果latest对应的镜像不存在就会报错！

## 运行某个镜像

理解了latest，那么下面两种方式就很简单了。

```docker
# 运行默认镜像，默认会找tag
docker run ubuntu

# 运行指定tag的镜像
docker run ubuntu:14.04
```

## 打tag

```
# 给某一版本打上latest的tag
docker tag -f ubuntu:14.04 ubuntu

# 打默认tag，-f 意思是强制覆盖
docker tag -f ubuntu:14.04 ubuntu:latest
```

## 删除镜像的方法

同理，我们直接删除某个name，系统**只会**删除latest标签的那一条记录，如`docker rmi ubuntu`。这个删除操作不会影响相同ID的其他镜像。

## docker镜像的版本控制

如果需要升级某个docker镜像，我们可以这样做。

1. 给每个新生成的镜像都打上相应版本的tag。此时可能存在image:latest、image:v1、image:v2等。
1. 我们要从v1升级到v2，首先我们将导入的v2镜像强制重命名为image:latest，命令为`docker tag -f image:v2 image:latest`
1. `docker stop`之前正在运行的容器
1. 启用`docker run image`，此时**image**的等价镜像**image:latest**就是最新的V2镜像。


老高总结为load/tag/stop/run四个步骤。


  [1]: https://blog.phpgao.com/usr/uploads/2015/11/2256355596.png
  [2]: https://blog.phpgao.com/usr/uploads/2016/01/1825926261.png
  [3]: https://blog.phpgao.com/usr/uploads/2016/01/472074517.png
  [4]: https://blog.phpgao.com/usr/uploads/2016/01/3975782950.png
  [5]: https://blog.phpgao.com/docker-note1.html#%E6%9E%84%E5%BB%BA%E9%95%9C%E5%83%8F
  [6]: https://blog.phpgao.com/usr/uploads/2016/01/1289883122.png