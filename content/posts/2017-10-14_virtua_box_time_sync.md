---
title: "Virtual-box时间同步"
categories: [ "服务器技术" ]
tags: [ "virtualbox" ]
draft: false
slug: "virtua_box_time_sync"
date: "2017-10-14 16:04:00"
url: "virtua_box_time_sync.html"
---

当虚拟机安装好之后，如果有一段时间没有启动，当下一次启动时后系统时间会变得和主机环境不一致，我们需要安装一些软件来保证每次开机后会与主机时间同步。


<!--more-->

首先装载CD，选择 Devices -> Insert Guset Additions CD image


```
# 升级kernel
yum update kernel*

# 装载CD
mount -t iso9660 /dev/cdrom /media

cp -r /media /tmp/

cd /tmp/media

yum update -y kernel*

yum install gcc kernel-devel kernel-headers dkms make bzip2 perl

# 可选
sed -i '/SELINUX/s/enforcing/disabled/' /etc/selinux/config

# 重启
reboot


/tmp/media/VBoxLinuxAdditions.run

[root@localhost media]# ./VBoxLinuxAdditions.run
Verifying archive integrity... All good.
Uncompressing VirtualBox 5.1.28 Guest Additions for Linux...........
VirtualBox Guest Additions installer
Removing installed version 5.1.28 of VirtualBox Guest Additions...
Copying additional installer modules ...
Installing additional modules ...
vboxadd.sh: Starting the VirtualBox Guest Additions.

Could not find the X.Org or XFree86 Window System, skipping.

# 重启
reboot
```


参考 https://www.if-not-true-then-false.com/2010/install-virtualbox-guest-additions-on-fedora-centos-red-hat-rhel/