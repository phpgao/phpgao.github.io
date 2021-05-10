---
title: "安装双系统的一些记录"
categories: [ "服务器技术" ]
tags: [ "ubuntu","Linux","grub","mbr" ]
draft: false
slug: "install_multisystem_note"
date: "2015-03-06 06:27:00"
url: "install_multisystem_note.html"
---

## MBR&GRUB

MBR（Master boot record）指主引导记录，主要用于硬盘分区。GRUB就是安装在MBR中，起引导系统作用。

阶段1：POST自检后系统载入目标装置的MBR（Master boot record），即前512字节。MBR一共包含三个部分主引导程序（Boot Loader）、硬盘分区表DPT（Disk Partition table）和硬盘有效标志（55AA）。

阶段2：主引导程序被载入内存开始执行，他将继续执行加载系统内核的任务。


<!--more-->


第一阶段和第二阶段的引导加载程序一起称为 Linux Loader（LILO）或 GRand Unified Bootloader（GRUB）。

## 步骤

1. 下载镜像
1. 认清分区编号
1. 使用EasyBCD安装Neogrub
1. 配置启动项
1. 执行安装

## 分区编号

在grub下

hd0,0指的是硬件中第一块硬盘的第一个主分区，同理，hd(2,1)指第三块；
hd0,0 hd(0,1) hd(0,2) hd(0,3)是前四个主分区，编号是跳跃式的；
hd0,4从4开始即第N个逻辑分区；
以此类推。

在grub2下

hd0,1指的是硬件中第一块硬盘的第一个主分区
也就是分区部分整体加1

## 启动配置

```
title Install Ubuntu
root (hd0,4)
kernel (hd0,4)/vmlinuz.efi boot=casper iso-scan/filename=/ubuntu-11.10-i386.iso ro quiet splash locale=zh_CN.UTF-8
initrd (hd0,4)/initrd.lz
```
vmlinuz.efi是可引导的、压缩的内核。
initrd是linux在系统引导过程中使用的一个临时的根文件系统,用来支持两阶段的引导过程。

## 安装注意事项

### 忘记umount

安装ubuntu前必须执行（快捷键`ctrl+shift+t`打开终端）命令`sudo umount -l /isodevice`，否则会造成安装卡死。如果遇到卡死的情况，多数同学可能会选择重启并重新安装。老高在此教大家不需要重启的办法。

当界面卡在安装界面的时候，点击右上角注销系统，然后重新登录即可！使用用户名ubuntu，密码空登录。卸载isodevice后再执行安装即可。

> ps.安装完成后可以进win移除Neogrub。

### 配置GRUB2

最新的ubuntu采用了grub2作为引导工具。下面给出老高的配置：

```bash
sudo vi /etc/default/grub
GRUB_DEFAULT=saved # 让grub记住上次选中的系统
GRUB_TIMEOUT=1 # 减少等待时间
# save

# 下面改变启动菜单的顺序，将win移至第一
sudo mv /etc/grub.d/30_os-prober /etc/grub.d/08_os-prober

# 不要忘了更新grub
sudo update-grub
```

### 连接WIFI

系统安装好后，连接无线wifi时可能会遇到以下问题：

>  Connection activation failed: (1) Creating object for path '/org/freedesktop/NetworkManager/ActiveConnection/34' failed in libnm-glib.

其实解决的办法很简单，点击右上角wifi图标，点击配置网络（Edit Connections...），找到刚才连接过的wifi名称，双击启用编辑。

在无线安全（wifi security）的tab中输入密码即可！

Reference:

http://www.ibm.com/developerworks/cn/linux/l-linuxboot/index.html
http://askubuntu.com/questions/469783/ubuntu-14-04-cant-connect-to-new-password-protected-wifi-network
http://blog.chinaunix.net/uid-9078996-id-2010311.html