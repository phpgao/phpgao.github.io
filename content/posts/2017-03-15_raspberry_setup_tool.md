---
title: "树莓派安装记录-usb-samba-aria2-xware-apt-get"
categories: [ "服务器技术" ]
tags: [ "raspberry","samba","ntfs","xware" ]
draft: false
slug: "raspberry_setup_tool"
date: "2017-03-15 15:49:00"
url: "raspberry_setup_tool.html"
---

老高的R6300v2自从刷了梅林后，一直在挂硬盘做迅雷离线下载。眼看着夏天到了，还是给路由器减减压，于是祭出老早买的树莓派2B，再次踏上折腾之路。


<!--more-->

## 系统下载

 - [Download Raspbian][1]
 - [Centos7][2]
 - 老高推荐[Raspbian - RASPBIAN JESSIE LITE][3]
 - [其他系统][4]

## 搜教程

教程还是官网靠谱

 - [INSTALLING OPERATING SYSTEM IMAGES][5]
 - [INSTALLING OPERATING SYSTEM IMAGES USING WINDOWS][6]
 - [INSTALLING OPERATING SYSTEM IMAGES ON MAC OS][7]
 - [INSTALLING OPERATING SYSTEM IMAGES ON LINUX][8]

Win系统下直接[Win32 Disk Imager][9]，然后选镜像，点Write即可！

## 启动系统

> 新版的系统默认是没有开启ssh的，所以准备远程ssh管理树莓派的话，给SD卡写完数据后一定要在BOOT分区建个名为`ssh`的文件，创建一个TXT，然后改名为ssh或者`touch ssh`即可！

```bash
ssh pi@192.168.1.111
passwd : raspberry
```


## apt加速

[aspbian镜像使用帮助][10]

`sudo vi /etc/apt/sources.list`文件。删除原文件所有内容，用以下内容取代：

```list
deb http://mirrors.ustc.edu.cn/raspbian/raspbian/ jessie main non-free contrib
deb-src http://mirrors.ustc.edu.cn/raspbian/raspbian/ jessie main non-free contrib
```

编辑此文件后，请使用`sudo apt-get update`命令，更新软件列表。

[更多源][11]

## 修复vim的方向键变为ABCD的方法

```
sudo vi /etc/vim/vimrc.tiny

# 最后添加
set nocompatible
```


## 实用工具

[raspi-config][12]树莓派自带工具

```bash
sudo raspi-config
```

↓ raspi-config运行界面

![raspi-config][13]

↓ 点击advanced-->expand，然后重启就可以找回丢失的SD卡空间

![expand][14]

还有4.Localisation可以设定时区等信息，在此就不再赘述了。

安装zsh和omz

```
sudo apt-get install -y zsh git
sudo curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh
```


## 硬盘挂载

```bash
sudo apt-get install -y ntfs-3g
sudo mkdir -p /mnt/data

# 直接挂载
mount /dev/sda1 /mnt/data

# 或使用配置挂载
sudo cp /etc/fstab /etc/fstab.backup
sudo vi /etc/fstab
# add
/dev/sda1       /mnt/data       auto    defaults,utf8,umask=000 0    0
# 第二块硬盘
/dev/sdb1       /mnt/hd         ext4    defaults,rw 0    0
# NTFS
/dev/sda3      /mnt/volume      ntfs    defaults 0 0
# 不用重启
sudo mount -a
ls -al /mnt/data
# 检查权限
touch /mnt/data/test
```

## samba

```bash
sudo apt-get install samba samba-common-bin

sudo vi /etc/samba/smb.conf

#add

[data]
    comment = No comments
    path = /mnt/data
    browseable = yes
    writable = yes
    public = yes
```

或者想要配合用户名使用

```bash
[data]
    comment = No comments
    valid users = pi
    path = /mnt/data
    browseable = yes
    writable = yes
    public = yes



sudo smbpasswd -a pi
New SMB password:
Retype new SMB password:
Added user pi.


sudo /etc/init.d/samba restart
```

在explorer中输入`\\192.168.1.111`，即Pi的IP，提示用户名密码，输入**anonymous**即可！

## 远程下载（已废弃）

```bash
# 通过ssh上传文件
scp Xware1.0.31_armel_v5te_glibc.zip pi@192.168.1.111:~

ssh pi@192.168.1.111

mkdir xunlei
mv Xware1.0.31_armel_v5te_glibc.zip xunlei/
cd xunlei && unzip Xware1.0.31_armel_v5te_glibc.zip
./portal

sudo chmod a+x /etc/rc.local
sudo vi /etc/rc.local

#add
/home/pi/xunlei/portal
sleep 5
```

## aria2

### 安装配置

conf例子在[phpgao/aria2.conf][15]

```bash
# 安装
sudo apt-get install aria2 -y

# 默认配置文件夹，如果不配置则使用 --conf-path=/home/pi/aria2.conf 指定
mkdir -p /home/pi/.aria2/
# 默认下载文件夹
mkdir -p /mnt/data/download

# 下载配置到本地默认位置
wget https://gist.githubusercontent.com/phpgao/3eb55c223c2bced278a4f5ca24fc5874/raw/2a0181b4f3381c5e6a324a9a654e4daa3b0518f0/aria2.conf -O /home/pi/.aria2/aria2.conf

```

### 运行和启动

```
# 后台运行
aria2c --enable-rpc --rpc-listen-all --enable-dht -D

# 开机启动
sudo vi /etc/rc.local
# add

# run command as pi
su pi -c "aria2c --enable-rpc --rpc-listen-all --enable-dht -D"

su pi -c "/home/pi/xunlei/portal"

exit 0
```

> 小贴士，如果需要更新trackerslist，可以到[ngosang/trackerslist][16]下载最新的列表，老高推荐使用**trackers_all_ip**，获取后使用[在线正则表达式测试][17]，将`\n\n`替换为`,`，然后加入配置文件里即可！

### 一些技巧

如果要远程管理aria2，那么可以把树莓派的6800端口映射出来，然后可以托管到在线服务，比如

 - [Yet Another Aria2 Web Frontend][18]
 - [Aria2 WebUI][19]



如果不放心，可以自己搭建nginx服务器，然后把[Aria2 Web Frontend项目][20]放到/var/www/html下，就可以在内网直接用IP访问控制台了。

[配置说明-en][21]
[配置说明-zh][22]

## 开机启动


```bash
# 配置locale
export LC_ALL=C

# 启用服务
systemctl enable samba rc.local nginx

# 检查服务
systemctl list-units --type=service|grep run
```


## FRP

先使用wget下载最新的linux_arm版本的frp  [github链接][23]

```
wget https://github.com/fatedier/frp/releases/download/v0.20.0/frp_0.20.0_linux_arm.tar.gz

tar zxf frp_0.20.0_linux_arm.tar.gz
cd frp_0.20.0_linux_arm
mv frpc /usr/local/bin/frpc

mkdir /etc/frp/
cp frpc.ini /etc/frp/
# 之后编辑/etc/frp/frpc.ini，直到符合要求
```

最后编辑`/etc/systemd/system/frpc.service`，写入以下内容

```
[Unit]
Description=frpc daemon
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/frpc -c /etc/frp/frpc.ini
ExecReload=/usr/local/bin/frpc -c /etc/frp/frpc.ini reload
Restart=always
RestartSec=5s

[Install]
WantedBy=multi-user.target
```


这样frpc就变为服务了！

```
systemctl enable frpc
systemctl restart frpc
systemctl status frpc

```

  [1]: https://www.raspberrypi.org/downloads/
  [2]: http://mirror.centos.org/altarch/7/isos/armhfp/
  [3]: https://www.raspberrypi.org/downloads/raspbian/
  [4]: http://www.raspbian.org/RaspbianImages#Unofficial_Raspbian_Images
  [5]: https://www.raspberrypi.org/documentation/installation/installing-images/README.md
  [6]: https://www.raspberrypi.org/documentation/installation/installing-images/windows.md
  [7]: https://www.raspberrypi.org/documentation/installation/installing-images/mac.md
  [8]: https://www.raspberrypi.org/documentation/installation/installing-images/linux.md
  [9]: https://sourceforge.net/projects/win32diskimager/
  [10]: https://lug.ustc.edu.cn/wiki/mirrors/help/raspbian
  [11]: http://shumeipai.nxez.com/2013/08/31/raspbian-chinese-software-source.html
  [12]: https://github.com/RPi-Distro/raspi-config
  [13]: https://blog.phpgao.com/usr/uploads/2017/03/2721557335.jpg
  [14]: https://blog.phpgao.com/usr/uploads/2017/03/2755792594.png
  [15]: https://gist.github.com/phpgao/3eb55c223c2bced278a4f5ca24fc5874
  [16]: https://github.com/ngosang/trackerslist
  [17]: http://tool.oschina.net/regex
  [18]: http://yaaw.ghostry.cn
  [19]: http://webui-aria2.ghostry.cn
  [20]: http://dy.ghostry.cn
  [21]: https://aria2.github.io/manual/en/html/aria2c.html
  [22]: http://aria2c.com/usage.html
  [23]: https://github.com/fatedier/frp/releases