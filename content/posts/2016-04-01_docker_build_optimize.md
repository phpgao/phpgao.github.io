---
title: "Docker打包优化"
categories: [ "性能优化" ]
tags: [ "optimize","docker" ]
draft: false
slug: "docker_build_optimize"
date: "2016-04-01 03:50:00"
url: "docker_build_optimize.html"
---

老高最近快被工作掩埋了，各种赶上线，各种修BUG，真凄惨！
今天来说说Docker打包的优化问题。为什么要说这个问题呢？请听我慢慢道来。

![Docker][1]



<!--more-->

## 为什么要优化



先说说的老高的工作流程

### 工作流程

```
             
     +---------------------------------------------------+
     |                    BAD CODE                       |
     |                                                   |
     |                                                   |
+----v------+    +-----------+    +-----------+    +-----+-----+
|           | GP |           | GC |           | DX |           |
| GOOD CODE +---->  GIT REPO +---->  JENKINS  +---->  QA'S ENV |
|           |    |           |    |           |    |           |
+-----+-----+    +-----------+    +-----------+    +-+---+-----+
      |                                              ^   |
      |                                              |   | PASSED
      +----------------------------------------------+   |
                   SEND SINGLE FILE                      |
                                                         |
                                                   +-----v-----+
                                                   |           |
                                                   |  GIT TAG  |
                                                   |           |
                                                   +-----------+
GP = git push
GC = git checkout
DX = docker export
```

老高把写好的代码提交到GIT仓库，之后使用Jenkins自动化构建，构建出的Docker包会给QA人员测试。
当然为了更快的解决问题，我也可以提供散文件给QA解决一些小问题。

QA反馈某些功能不通过，我会继续修改直到通过测试，通过测试的代码会被合并到主分支并打tag留存。


### 问题出在哪儿

问题出在代码产出到Docker镜像的产出的过程中，因为Docker镜像的构建出错频率太多，导致频繁打包失败。有时候某一行代码的修改需要等几个小时才能等到新的包出来，这一点十分蛋疼。

于是老高实在忍不住了，主动提出要优化此处。

## 怎么优化

其实简单的想想，出错的地方一般都出在`apt-get/yum install`等安装包命令上，如`yum install -t nginx mysql-server php5`，而这些命令一般都是为了构建基础的运行环境。

而程序猿每次构建基本上都是代码的改动，所以每次构建都跑一次安装LAMP的过程实在没有必要！那到底怎么实现每次构建都只是把新的代码(如PHP源码)拷贝到指定地方，而不做其他的基础构建动作呢？

### 分离/缓存

老高想到了两个办法：

#### 分离

分离构建步骤，使用两个Dockerfile:`Dockerfile_base`和`Dockerfile`。

第一步构建出base镜像，base镜像中包含lamp的运行环境，第二次构建直接以base镜像为`base`，再把源码等频繁变动的文件拷贝进入新镜像即可！

这样做即分离了基础环境和执行环境。

而执行`docker build`命令是可以使用`-f`指定Dockerfile，即：

```bash
# 建立基础镜像
docker build -t lamp:base -f Dockerfile_base
# 建立新镜像
docker build -t lamp:new .
```

#### 缓存

在[最佳实践][2]中讲到:

> Docker匹配镜像决定是否使用缓存的规则如下:
> 1. 从缓存中存在的基础镜像开始，比较所有子镜像，检查它们构建的指令是否和当前的是否完全一致。如果不一致则缓存不匹配。
> 1. 多数情况中，使用其中一个子镜像来比较Dockerfile中的指令是足够的。然而，特定的指令需要做更多的判断。
> 1. ADD COPY 指令中，将要添加到镜像中的文件也要被检查。通常是检查文件的校验和(checksum)。
> 1. 缓存匹配检查并不检查容器中的文件。例如，当使用 RUN apt-get -y update 命令更新了容器中的文件，并不会被缓存检查策略作为缓存匹配的依据。

所以想要优雅的使用缓存，需要好好对之前的Dockerfile做一次大保健，参照最佳实践中的指导：

 - 合并安装包命令

```Dockerfile
RUN apt-get update \
	&& apt-get install -y postgresql-common \
	&& sed -ri 's/#(create_main_cluster) .*$/\1 = false/' /etc/postgresql-common/createcluster.conf \
	&& apt-get install -y \
		postgresql-$PG_MAJOR=$PG_VERSION \
		postgresql-contrib-$PG_MAJOR=$PG_VERSION \
	&& rm -rf /var/lib/apt/lists/*
```

 - COPY等命令放到后面
 - 推荐设置base为debian:jessie
 - 设置时区和语言

```Dockerfile
RUN echo "Asia/Shanghai" > /etc/timezone \
        && dpkg-reconfigure -f noninteractive tzdata \
        && dpkg-reconfigure locales  \
        && locale-gen C.UTF-8 \
        && /usr/sbin/update-locale LANG=C.UTF-8 \
        && ENV LC_ALL C.UTF-8
```

 - 软件源优化

```
#RUN sed -i "s/archive\.ubuntu\.com/mirrors\.163\.com/g" /etc/apt/sources.list
#RUN sed -i "s/archive\.ubuntu\.com/mirrors\.aliyun\.com/g" /etc/apt/sources.list
#RUN sed -i "s/archive\.ubuntu\.com/tw\.archive\.ubuntu\.com/g" /etc/apt/sources.list
#RUN sed -i "s/archive\.ubuntu\.com/cn\.archive\.ubuntu\.com/g" /etc/apt/sources.list
RUN sed -i "s/archive\.ubuntu\.com/mirrors\.ustc\.edu\.cn/g" /etc/apt/sources.list
```

## 配合Jenkins

终于我们按照最佳实践，实现了加速构建，但是为了更加灵活的控制Docker镜像构建，我们可以使用Jenkins中一些简单的功能实现如 **强制不缓存**，部分更新的功能。

首先在参数区加入一个Boolean Value，名为no-cache
Default Value留空，Description写强制不使用缓存

![配置][3]

之后添加构建步骤，Exec Shell:

```
#!/bin/bash
echo 'building base'
cd docker
if [ "$no-cache" = true ] ; then
    echo 'building base without cache'
    docker build --no-cache=true -t "lamp:base" -f Dockerfile_base .
else
    echo 'building base with cache'
    docker build --no-cache=false -t "lamp:base" -f Dockerfile_base .
fi
```

完成

## 一些问题

### DockerHub里有什么Dockerfile能够参考的？

DockerHub已经为我们提供了很多常用的基础功能镜像，如

 - [nginx][4]
 - [php][5]
 - [mysql][6]
 - [mariadb][7]
 - [postgres][8]
 - [redis][9]
 - [python][10]
 - [registry][11]
 - [elasticsearch][12]
 - [wordpress][13]
 - [golang][14]
 - [jenkins][15]
 - [更多][16]

如果你想把typecho程序也做成一个Dockerfile，其实已经有了[jimmyzhou/typecho-nginx-php][17]。

以上都是可以参考的Dockerfile！

### 源代码是COPY进去还是git clone进去?

[Understanding the Docker Cache for Faster Builds][18]

答案是都可以

### 如何不缓存Dockerfile中的某一条命令

[New feature request: Selectively disable caching for specific RUN commands in Dockerfile][19]

答案很多，但是都很麻烦，以后官方貌似会有NOCACHE的Dockerfile关键字。

### Postgresql的加速安装

```Dockerfile
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys B97B0AFCAA1A47F044F244A07FCC7D46ACCC4CF8 
     && echo "deb http://mirrors.ustc.edu.cn/postgresql/repos/apt/ trusty-pgdg main" > /etc/apt/sources.list.d/pgdg.list
RUN groupadd -r postgres --gid=999 && useradd -r -g postgres --uid=999 postgres
RUN apt-get update 
     && apt-get install -y postgresql-9.4
```

### 计划任务(crontab)怎么添加

通过老高的观察，以执行用户为 **laogao** 为例，计划任务的文件会被保存在`/var/spool/cron/crontabs/`中，文件名为执行计划任务的用户名，即laogao，权限是600，用户组为 laogao:crontab，这样我们就可以把写有计划任务的文件放到/var/spool/cron/crontabs/laogao，然后修改权限即可。

不过这种方法略显麻烦，其实只需要把写有计划任务的文件导入/tmp/laogao，然后执行`crontab -u laogao /tmp/laogao`，即可为老高用户生成计划任务了。


  [1]: https://blog.phpgao.com/usr/uploads/2016/04/1459029166.png
  [2]: http://tuxknight-notes.readthedocs.org/en/latest/docker/dockerfile_best_practices_take.html#id1
  [3]: https://blog.phpgao.com/usr/uploads/2016/04/719171091.png
  [4]: https://hub.docker.com/_/nginx/
  [5]: https://hub.docker.com/_/php/
  [6]: https://hub.docker.com/_/mysql/
  [7]: https://hub.docker.com/_/mariadb/
  [8]: https://hub.docker.com/_/postgres/
  [9]: https://hub.docker.com/_/redis/
  [10]: https://hub.docker.com/_/python/
  [11]: https://hub.docker.com/_/registry/
  [12]: https://hub.docker.com/_/elasticsearch/
  [13]: https://hub.docker.com/_/wordpress/
  [14]: https://hub.docker.com/_/golang/
  [15]: https://hub.docker.com/_/jenkins/
  [16]: https://hub.docker.com/explore/
  [17]: https://hub.docker.com/r/jimmyzhou/typecho-nginx-php/
  [18]: http://thenewstack.io/understanding-the-docker-cache-for-faster-builds/
  [19]: https://github.com/docker/docker/issues/1996