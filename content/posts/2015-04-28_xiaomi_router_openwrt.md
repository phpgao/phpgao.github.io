---
title: "编译小米路由mini的openwrt固件"
categories: [ "服务器技术" ]
tags: [ "router","xiaomi","openwrt" ]
draft: false
slug: "xiaomi_router_openwrt"
date: "2015-04-28 16:15:00"
url: "xiaomi_router_openwrt.html"
---

花了一天，终于编译好了小米路由器的openwrt，过程真是一波三折。。。值得一讲。


<!--more-->


## 硬件准备

老高之前很天真的想用搬瓦工的VPS编译，没想到真是的图样图森破。

当时想的是搬瓦工vps的下载速度很快，能够缩短编译时间，事实证明，确实下载很快。

## 第一个坑

但遇到的**第一个坑**当然是内存不足，无法编译。

无奈，于是打开virtualbox，创建了一个centos6.6系统。

**内存1G，硬盘8G，快速安装**。

## 软件准备

既然是centos，那么安装软件的工具当然是`yum`，如果对速度不满意，可以参考[Centos源设置][1]将源服务器设为国内的阿里或者163，这样速度能快一些。

其他系统可以参考[官方的文档][2]

```
# 国内网速你懂的
yum update -y
yum groupinstall "development tools" -y
yum install zlib zlib-devel bzip2-devel pcre-devel openssl openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel -y
```

## 下载源代码

```
git clone git://git.openwrt.org/openwrt.git
```

## 编译

```
cd openwrt/

./scripts/feeds update -a
./scripts/feeds install -a

make menuconfig

# 穿墙你懂的
pushd package
git clone https://github.com/aa65535/openwrt-dist-luci.git
popd

make menuconfig

# 由于是mac没敢开多线程，如果是台式机，推荐使用-j参数
make V=99

# 编译期间可能会遇到某些源代码无法下载，这个不算是坑，可以自己手动下载，并复制到`openwrt/dl`文件夹里，然后继续编译即可。
# 接下来就是等待编译过程完毕了！
```

教程貌似结束了？



----------




## 不过问题来了

当然不会完，期间老高遇到的问题无数，考验人的耐力和体力啊！

### 第二个坑

当程序运行到某处报错

> i686-linux-gnu-g++: internal compiler error: Killed (program cc1plus)

通过重现发现此时MEM占用99%，瞬间懂了，原来是内存给少了，1G+500SWAP都不够。。。

解决办法：

```
# 关机
init 0
#然后在虚拟机设置处将内存调整至2G
```

### 第三个坑

空间不足，8G也不够。。。真是小看openwrt了。

我的解决办法是在上一块虚拟硬盘，通过**扩展LVM容量**解决的。

盗一个图先

![LVM关系图][3]

```
# 第一步，关机，上一个新的同规格硬盘，容量8G，开机
# 查看是否安装成功
ll /dev/sd*
# 此时应该多一个/dev/sdb
fdisk /dev/sdb
# 现在进入交互模式，输入m可以打开帮助
# 输入n，然后一路回车，就把sdb创建了一整个分区sdb1
# 输入q退出

# 下面开始增加容量

# 在sdb1上创建新的物理卷（Physical Volume，PV）
pvcreate /dev/sdb1

# 如果需要把整个磁盘直接创建为物理卷可以使用 pvcreate /dev/sdb
# 查看物理卷
pvdisplay
```

>   --- Physical volume ---
  PV Name               /dev/sda2
  VG Name               VolGroup
  PV Size               7.51 GiB / not usable 3.00 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              1922
  Free PE               0
  Allocated PE          1922
  PV UUID               6QpO6a-Xg0D-bbnu-znPR-EyLf-J4AD-PFna1y

>  --- Physical volume ---
  PV Name               /dev/sdb1
  VG Name               VolGroup
  PV Size               8.00 GiB / not usable 4.38 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              2046
  Free PE               0
  Allocated PE          2046
  PV UUID               q0uMIR-Sqy6-0djq-wJ2g-V1zN-TdoV-Nk9wAY

```
# 查看卷组（Volume Group，VG）
vgdisplay
```
>  --- Volume group ---
  **VG Name               VolGroup**
  System ID
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  5
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                2
  Act PV                2
  **VG Size             7.4 GiB**
  PE Size               4.00 MiB
  Total PE              3968
  Alloc PE / Size       3968 / 15.50 GiB
  Free  PE / Size       0 / 0
  VG UUID               PlIpGQ-gBtF-RpQp-fzFh-9jao-JyDY-hfxVDl


```
# 现在将VG扩容，注意VG Name
vgextend VolGroup /dev/sdb1

# get
# Volume group "VolGroup" successfully extended

# 下面开始'分区'
查看逻辑卷（Logical Volume，LV）
lvdisplay
```

>  --- Logical volume ---
  LV Path                /dev/VolGroup/lv_root
  LV Name                lv_root
  VG Name                VolGroup
  LV UUID                FbWpkT-WAz3-bmXI-3j06-kdYh-1dvt-ZbwKmH
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2015-04-28 09:48:44 +0800
  LV Status              available
  open                 1
  **LV Size              7.4 GiB**
  Current LE             3764
  Segments               2
  Allocation             inherit
  Read ahead sectors     auto
  /- currently set to     256
  Block device           253:0

>  --- Logical volume ---
  LV Path                /dev/VolGroup/lv_swap
  LV Name                lv_swap
  VG Name                VolGroup
  LV UUID                0o4wmo-lj1r-xRTe-zSvd-Bpo7-wLI7-9kX1gf
  LV Write Access        read/write
  LV Creation host, time localhost.localdomain, 2015-04-28 09:48:45 +0800
  LV Status              available
  open                 1
  LV Size                816.00 MiB
  Current LE             204
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  /- currently set to     256
  Block device           253:1

```
# 继续查看挂载，可以发现 /dev/mapper/VolGroup-lv_root 被挂载到根目录
df -h

#/dev/mapper/VolGroup-lv_root 7.4G   7.4G  0G  100% /

# 开始扩充
lvextend /dev/VolGroup/lv_root /dev/sdb1

# Logical volume lv_root successfully resized

# 增加了逻辑卷的容量以后，就需要修改文件系统大小以实现利用扩充的空间
resize2fs /dev/VolGroup/lv_root

```

至此扩容完成，可以继续愉快的编译了。

![编译成果][4]

## 总结

硬件：虚拟机2G内存 >=16G硬盘(编译完还有3.4G剩余)
系统：centos6.6
其他：耐心

> 第一次运行会下载很多软件在dl目录，下次编译的时候就快了


  [1]: https://blog.phpgao.com/repo_for_centos.html
  [2]: http://wiki.openwrt.org/doc/howto/buildroot.exigence
  [3]: https://blog.phpgao.com/usr/uploads/2015/04/2747659839.png
  [4]: https://blog.phpgao.com/usr/uploads/2015/04/1849193754.png