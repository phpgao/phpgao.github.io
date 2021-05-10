---
title: "Centos7-虚拟机配置"
categories: [ "路由器" ]
tags: [ "centos","vmware","kubernetes" ]
draft: false
slug: "centos7-vm-cluster"
date: "2017-02-18 12:10:00"
url: "centos7-vm-cluster.html"
---

一些注意的地方，留坑待填


<!--more-->


```
# 配置主机名
hostnamectl set-hostname kube-master
hostnamectl set-hostname kube-minion1
hostnamectl set-hostname kube-minion2
hostnamectl set-hostname mater
hostnamectl set-hostname slaver1
hostnamectl set-hostname slaver2
hostnamectl set-hostname minion1
hostnamectl set-hostname minion2


# 更新系统
yum update -y
yum install -y kernel-headers kernel-devel

# 安装netstat，vm-tool依赖
yum install -y net-tools 


# 安装编译工具包
yum groupinstall -y "Development Tools"

# 关闭selinux
sed -i '/SELINUX/s/enforcing/disabled/' /etc/selinux/config

# 列出服务
systemctl list-units --type=service

# 关闭防火墙服务
systemctl disable firewalld.service

# 设置时区
timedatectl set-timezone Asia/Shanghai

关闭IPV6
sysctl -w net.ipv6.conf.all.disable_ipv6=1
sysctl -w net.ipv6.conf.default.disable_ipv6=1

# 设置生效
sysctl -p

# 重启机器
reboot

# 挂载工具
mount -rt iso9660 /dev/cdrom /media

# copy到本机并解压
mkdir /tmp/vmware-tools-distrib
cp -r /media/* /tmp/vmware-tools-distrib
umount /media
/tmp/vmware-tools-distrib/VBoxLinuxAdditions.run

# 开启时间同步
vmware-toolbox-cmd timesync status
vmware-toolbox-cmd timesync enable

# 清空yum临时文件
yum clean all

# 修改host文件
vi /etc/hosts

192.168.3.125 kube-master
192.168.3.242 kube-minion1
192.168.3.202 kube-minion2
```
