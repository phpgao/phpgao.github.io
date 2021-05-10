---
title: "小米路由器mini折腾之刷不死uboot篇"
categories: [ "路由器" ]
tags: [ "router","xiaomi","uboot" ]
draft: false
slug: "xiaomi_router_uboot"
date: "2015-03-08 01:48:00"
url: "xiaomi_router_uboot.html"
---

u-boot是一种普遍用于嵌入式系统中的Bootloader,Bootloader是在操作系统运行之前执行的一小段程序。他可以用来恢复小米路由器的固件，可以说只要刷了uboot，你的路由器基本上刷不死了。

**以下操作都是在你获取了root信息后才可以实现。**


<!--more-->


2015年11月10日更：

**刷uboot属于高危操作，如果你觉得刷了以后出现问题自己不一定Hold的住，请止步，谢谢合作！**

如果造成不可挽回的损失，老高也没辙。。。

2015年9月17日更：

新版固件不要刷uboot了，会造成很多问题。

2015年10月3日更：

感谢ABC的回复，新版固件可以刷这个[AR/QCA/MT7620 Breed，功能强大的多线程 Bootloader][1]！

## 备份硬件信息

> 刷机前做备份是一个好习惯。

已经有的案例，有同学在刷机时由于操作不当，导致刷掉了sn，虽然还是有方法可以曲线救国，但是还是很麻烦就是了。

```
# 查看MTD分区
cat /proc/mtd

# 备份
# /extdisks/sda1 就是外部存储设备的路径

dd if=/dev/mtd0 of=/extdisks/sda1/rom/ALL.bin
dd if=/dev/mtd1 of=/extdisks/sda1/rom/Bootloader.bin
dd if=/dev/mtd2 of=/extdisks/sda1/rom/Config.bin
dd if=/dev/mtd3 of=/extdisks/sda1/rom/Factory.bin
dd if=/dev/mtd4 of=/extdisks/sda1/rom/OS1.bin
dd if=/dev/mtd5 of=/extdisks/sda1/rom/rootfs.bin
dd if=/dev/mtd6 of=/extdisks/sda1/rom/OS2.bin
dd if=/dev/mtd7 of=/extdisks/sda1/rom/overlay.bin
dd if=/dev/mtd8 of=/extdisks/sda1/rom/crash.bin
dd if=/dev/mtd9 of=/extdisks/sda1/rom/reserved.bin
dd if=/dev/mtd10 of=/extdisks/sda1/rom/Bdata.bin
dd if=/dev/mtd11 of=/extdisks/sda1/rom/firmware.bin

# 恢复备份

mtd -r write /extdisks/sda1/rom/Bootloader.bin Bootloader
mtd -r write /extdisks/sda1/rom/Config.bin Config
mtd -r write /extdisks/sda1/rom/Factory.bin Factory
mtd -r write /extdisks/sda1/rom/OS1.bin OS1
mtd -r write /extdisks/sda1/rom/rootfs.bin rootfs
mtd -r write /extdisks/sda1/rom/OS2.bin OS2
mtd -r write /extdisks/sda1/rom/overlay.bin overlay
mtd -r write /extdisks/sda1/rom/crash.bin crash
mtd -r write /extdisks/sda1/rom/reserved.bin reserved
mtd -r write /extdisks/sda1/rom/Bdata.bin Bdata
mtd -r write /extdisks/sda1/rom/firmware.bin firmware
```

## 开始刷uboot

```
cd /tmp
wget http://downloads.openwrt.org.cn/PandoraBox/Xiaomi-Mini-R1CM/u-boot/uboot-xiaomi-mini-115200.bin -O uboot.bin

mtd -r write /tmp/uboot.bin Bootloader

# 等待重启
```

# 使用方法

1. reset键开机，黄灯闪烁后松开
1. 电脑使用网线连接lan口，将有线网卡的IP设为静态(192.168.1.2,255.255.255.0,192.168.1.1)
1. 在浏览器中打开192.168.1.1
1. 随意刷机


  [1]: http://www.right.com.cn/forum/thread-161906-1-1.html