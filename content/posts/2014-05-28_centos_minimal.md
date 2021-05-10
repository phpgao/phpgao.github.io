---
title: "记一次centos最小安装"
categories: [ "服务器技术" ]
tags: [ "centos","install" ]
draft: false
slug: "centos_minimal"
date: "2014-05-28 03:52:00"
url: "centos_minimal.html"
---

基本流程吧啊，以后可能会更新，以6.5为例：

下载安装必要文件，比如虚拟机virtualbox,vm等，还有镜像i386或者x86_64的，按需下载吧

设置虚拟机，开始安装，推荐使用全英文安装，速度和稳定性会好一点

完毕后网卡不会自动启动，需要手动开启

    vi /etc/sysconfig/network-scripts/ifcfg-eth0
    
    DEVICE=eth0
    HWADDR=00:0C:29:64:A5:E3
    TYPE=Ethernet
    UUID=ff171f7b-3bc5-465d-a5ba-287e200f6da6
    ONBOOT=yes
    NM_CONTROLLED=no
    BOOTPROTO=dhcp
    
然后

    /etc/init.d/network start
    
现在看看ifconfig里有没有IP信息

有了IP就能上网了，下面就是各种yum了，有兴趣的TX可以执行一下这个[优化脚本][1]


  [1]: https://blog.phpgao.com/centos_optimize.html