---
title: "树莓派samba的配置和自动挂载USB储存设备"
categories: [ "服务器技术" ]
tags: [ "raspberry","samba","mount" ]
draft: false
slug: "raspberry_automount"
date: "2016-01-09 07:16:00"
url: "raspberry_automount.html"
---



## 安装依赖包

```
apt-get install ntfs-3g
apt-get install samba
```

## 编辑samba

```
vi /etc/samba/smb.conf

#add

[media]
    comment = Temporary file space
    path = /media
    read only = no
    public = yes
```

重启samba服务

```
/etc/init.d/samba restart
```

## USB自动挂载

挂载好麻烦，自动脚本来帮忙，使用的技术叫做udev。

```
vi /etc/udev/rules.d/10-usbstorage.rules

KERNEL!="sd*", GOTO="media_by_label_auto_mount_end" 
SUBSYSTEM!="block",GOTO="media_by_label_auto_mount_end"
IMPORT{program}="/sbin/blkid -o udev -p %N" 
ENV{ID_FS_TYPE}=="", GOTO="media_by_label_auto_mount_end"
ENV{ID_FS_LABEL}!="", ENV{dir_name}="%E{ID_FS_LABEL}" 
ENV{ID_FS_LABEL}=="", ENV{dir_name}="Untitled-%k" 
ACTION=="add", ENV{mount_options}="relatime,sync" 
ACTION=="add", ENV{ID_FS_TYPE}=="vfat", ENV{mount_options}="iocharset=utf8,umask=000" 
ACTION=="add", ENV{ID_FS_TYPE}=="ntfs", ENV{mount_options}="iocharset=utf8,umask=000" 
ACTION=="add", RUN+="/bin/mkdir -p /media/%E{dir_name}", RUN+="/bin/mount -o $env{mount_options} /dev/%k /media/%E{dir_name}" 

ACTION=="remove", ENV{dir_name}!="", RUN+="/bin/umount -l /media/%E{dir_name}", RUN+="/bin/rmdir /media/%E{dir_name}" 
LABEL="media_by_label_auto_mount_end"
```

参考 [树莓派自动挂载usb移动存储设备][1]


  [1]: http://rpi.linux48.com/usbstorage.html