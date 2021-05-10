---
title: "跑在raspberry上的docker"
categories: [ "代码人生" ]
tags: [ "ubuntu","docker","raspberry" ]
draft: false
slug: "docker_on_raspberry_pi"
date: "2016-01-08 11:15:00"
url: "docker_on_raspberry_pi.html"
---

老高的树莓派昨天(2016-01-07)到货，配件果断买买买（其实只买了SD卡和亚克力盒子）。


![树莓派][1]


你能找到树莓派藏在哪儿吗？

<!--more-->


记录一下安装系统的过程吧


## 一些准备

几个站以后会用到

[DOWNLOADS][2] -- 官方系统下载
[Centos7 for Raspberry Pi][3]
[Hypriot Docker Image for Raspberry Pi][4] --- 让你的docker(只能跑基于ARM的镜像)跑在树莓派上的系统
[Hypriot系统的安装教程][5]
[Hypriot的docker hub][6]

## DD命令装系统

由于老高用的OSX系统，所以最方便的还是使用命令行烧录系统镜像了。基本上所有镜像都可以使用此方法。

```
# 格式化

# 树莓派2使用的是小卡，老高买的是SANDISK的16G，标称读取速度为80M/s的那款，送大卡卡托，方便MAC使用。

# SD卡在装系统前需要格式化为FAT32，mac下可以在系统的实用工具找到磁盘工具，然后格式化即可！也可以下载[Formatting Tool][7]格式化。

# Windows下就很简单了，在此就不在赘述了。

# 列出磁盘

diskutil list

# 卸载SD卡，不是弹出（eject）！

diskutil unmountdisk /dev/disk2

# if,of换一下参数就好，注意那个of=/dev/rdisk2的disk2前有个r

sudo dd if=Hypriot.img of=/dev/rdisk2 bs=1m

# 不出意外的话，系统已经安装好了！
```

## 系统优化

开机以后就是一堆堆安装软件了，老高主要做了：

### 替换源为国内源

```
cp /etc/apt/sources.list /etc/apt/sources.list.bak

vi /etc/apt/sources.list
```

```
# 清华
deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ wheezy main contrib non-free rpi 
deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ wheezy main contrib non-free rpi 

# 东软信息学院
deb http://mirrors.neusoft.edu.cn/raspbian/raspbian/ wheezy main contrib non-free rpi 
deb-src http://mirrors.neusoft.edu.cn/raspbian/raspbian/ wheezy main contrib non-free rpi 

# 中国科学技术大学
deb http://mirrors.ustc.edu.cn/raspbian/raspbian/ wheezy main contrib non-free rpi 
deb-src http://mirrors.ustc.edu.cn/raspbian/raspbian/ wheezy main contrib non-free rpi
```

然后更新软件

```
apt-get update
apt-get upgrade
```

[zsh + oh-my-zsh 默认shell的最佳替代品][8]

[让你的docker支持代理][9] -- 配置文件路径为`/etc/default/docker`


## 关于树莓派版的docker

也许你会发现，树莓派版的docker无法运行基于x86/64CPU架构构建出的镜像，因为树莓派是基于ARM的，我们如果想要创建自己的镜像，需要使用名为`resin/rpi-raspbian:wheezy`的基础镜像作为base，语法如下：

```
FROM resin/rpi-raspbian:wheezy
```

然后就可以愉快的折腾了~


  [1]: https://blog.phpgao.com/usr/uploads/2016/01/293942554.jpg
  [2]: http://www.raspberrypi.org/downloads/
  [3]: http://mirror.centos.org/altarch/7/isos/armhfp/
  [4]: http://blog.hypriot.com/downloads/
  [5]: http://blog.hypriot.com/getting-started-with-docker-and-mac-on-the-raspberry-pi/
  [6]: https://hub.docker.com/u/hypriot/
  [7]: https://www.sdcard.org/downloads/formatter_4/eula_mac/index.html
  [8]: https://blog.phpgao.com/oh-my-zsh.html
  [9]: https://blog.phpgao.com/docker_proxy.html