---
title: "在mac上安装并使用docker"
categories: [ "服务器技术" ]
tags: [ "mac","docker","boot2docker" ]
draft: false
slug: "docker_on_mac"
date: "2015-04-30 10:55:00"
url: "docker_on_mac.html"
---

docker最近实在火的不行，老高最近也在使用，顺便记录一下。

## 下载安装

下载和安装主要参考下面的文章

http://docs.docker.com/installation/mac/

https://community.emc.com/docs/DOC-44372


<!--more-->


老高选择使用Boot2Docker安装docker

或者你可以用下面的命令安装

```
brew update
brew install docker
brew install boot2docker
```

**安装貌似需要virtualbox**

安装完毕，会在你的应用程序文件夹中生成一个boot2docker的app，运行他就会初始化docker了！

等他完成初始化，我们就可以使用docker了！

```
# 你的第一个docker应用
docker run hello-world
```

## boot2docker基础命令

```
Usage: boot2docker [<options>] <command> [<args>]

Boot2Docker management utility.

Commands:
   init                Create a new Boot2Docker VM.
   up|start|boot       Start VM from any states.
   ssh [ssh-command]   Login to VM via SSH.
   save|suspend        Suspend VM and save state to disk.
   down|stop|halt      Gracefully shutdown the VM.
   restart             Gracefully reboot the VM.
   poweroff            Forcefully power off the VM (may corrupt disk image).
   reset               Forcefully power cycle the VM (may corrupt disk image).
   delete|destroy      Delete Boot2Docker VM and its disk image.
   config|cfg          Show selected profile file settings.
   info                Display detailed information of VM.
   ip                  Display the IP address of the VM's Host-only network.
   shellinit           Display the shell commands to set up the Docker client.
   status              Display current state of VM.
   download            Download Boot2Docker ISO image.
   upgrade             Upgrade the Boot2Docker ISO image (restart if running).
   version             Display version information.

```

## 理解docker

docker与虚拟机的区别以下文章讲的很清楚，我个人理解是docker统一些接口，使得虚拟化的移植性有了很大的提高！docker的虚拟化与之前常见的虚拟机不一样，其性能与占用空间等都比传统的虚拟机好很多。

http://stackoverflow.com/questions/16047306/how-is-docker-io-different-from-a-normal-virtual-machine

请允许老高盗一张官方图

![docker架构][1]

我们看到，不像传统的虚拟机，docker的所有程序都直接运行在宿主系统中，每个container独立运行，互不干扰。

请允许老高再盗一张官方图

![docker的文件系统][2]

乍看之下，可能毫无头绪。但其实docker玩的概念无非就是image(镜像)、container(容器)、layer(层)。

现在可以公开的情报：

1. docker可以在多个平台中运行
1. image、container的灵活程度超乎你的想象
1. 虚拟机有多重运行方式
1. docker最初只能运行在ubuntu系统中
1. image或base-image都是不可写的(readonly)
1. 再container中所做的修改都会保存在layer中

好了，不说这些虚的，下面赶紧用几个命令练练手。

## docker基本命令

```
docker -h

    attach    Attach to a running container
    build     Build an image from a Dockerfile
    commit    Create a new image from a container's changes
    cp        Copy files/folders from a container's filesystem to the host path
    create    Create a new container
    diff      Inspect changes on a container's filesystem
    events    Get real time events from the server
    exec      Run a command in a running container
    export    Stream the contents of a container as a tar archive
    history   Show the history of an image
    images    List images
    import    Create a new filesystem image from the contents of a tarball
    info      Display system-wide information
    inspect   Return low-level information on a container or image
    kill      Kill a running container
    load      Load an image from a tar archive
    login     Register or log in to a Docker registry server
    logout    Log out from a Docker registry server
    logs      Fetch the logs of a container
    port      Lookup the public-facing port that is NAT-ed to PRIVATE_PORT
    pause     Pause all processes within a container
    ps        List containers
    pull      Pull an image or a repository from a Docker registry server
    push      Push an image or a repository to a Docker registry server
    rename    Rename an existing container
    restart   Restart a running container
    rm        Remove one or more containers
    rmi       Remove one or more images
    run       Run a command in a new container
    save      Save an image to a tar archive
    search    Search for an image on the Docker Hub
    start     Start a stopped container
    stats     Display a stream of a containers' resource usage statistics
    stop      Stop a running container
    tag       Tag an image into a repository
    top       Lookup the running processes of a container
    unpause   Unpause a paused container
    version   Show the Docker version information
    wait      Block until a container stops, then print its exit code
```


----------


**常用命令**

```
# 运行某镜像(如果不存在会远程下载)
docker run hello-world
eg:
运行centos:centos6的容器并执行输出一段文字，之后退出
docker run centos:centos6 echo "hello world"


# 下载某镜像
docker pull centos:centos6

# 运行并进入bash环境
-i 容器的标准输入保持打开 -t 让Docker分配一个伪终端（pseudo-tty）并绑定到容器的标准输入上
docker run -i -t centos:centos6 /bin/bash

# 运行时将宿主文件夹挂载到/data
docker run -it -v /tmp:/data centos:centos6 /bin/bash

# 守护方式运行
docker run -idt centos-base

# 容器任务管理器
docker ps -a

# 进入容器
docker attach 3ca3de3b8b6a

# 导出容器
docker export 3ca3de3b8b6a > centos6-base.tar

# 导入容器并明命名
cat centos6-base.tar > docker import - centos-new_base

# 直接导入某镜像文件
docker import http://example.com/some_image.tgz

# 删除容器
docker rm 3ca3de3b8b6a

# 查看所有镜像
docker images

# 提交镜像
docker commit 3ca3de3b8b6a centos-new_base

# 删除镜像
docker rmi centos-new_base

# 停止当前运行着的container
docker kill $(docker ps -a -q)

# 删除没有在运行的container
docker rm $(docker ps -a -q)
```

更多使用方法请访问

<a href="http://dockerpool.com/static/books/docker_practice/introduction/README.html" target="_blank" rel="external nofollow">Docker —— 从入门到实践 </a>



  [1]: https://blog.phpgao.com/usr/uploads/2015/05/935509335.png
  [2]: https://blog.phpgao.com/usr/uploads/2015/05/87693964.png