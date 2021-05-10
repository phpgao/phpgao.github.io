---
title: "K2P刷padavan教程"
categories: [ "福利" ]
tags: [ "k2p","padavan" ]
draft: false
slug: "padavan_for_k2p"
date: "2018-01-23 05:17:00"
url: "padavan_for_k2p.html"
---

快上车吧！

> padavan原生支持802.1X认证，虽然K2也行，但是百兆的性能确实跟不上时代了，所以入了K2P愉快的刷起！

<!--more-->


## 确定硬件版本

路由器背面有硬件版本，老高的是A2版本，A1套路相同。


## 准备文件

 - winscp+putty
 - breed for k2p
 - A大的新固件
 - padavan

## 刷官改

请先移步[A大下载][1]页面，按照页面方法刷机！建议用BL刷机大法。


## 下载Breed

[Boot and Recovery Environment for Embedded Devices (BREED)][2]，找到`breed-mt7621-phicomm-k2p.bin`并下载，使用winscp上传到路由器的/tmp/下，使用用户名 root，密码 admin，刷breed命令`mtd -r write /tmp/breed-mt7621-phicomm-k2p.bin Bootloader`，刷机完成后提示重启，直接断电捅菊花10s后网线链接LAN口，设置IP手动为192.168.1.1/255.255.255.0/192.168.1.1，chrome或者火狐打开网址192.168.1.1，打开固件更新，选择并刷入padavan固件(见下小节)。

## 下载padavan

请移步[灯大的K2P][3]，下载K2P公测版，然后在Breed刷机

## 开启彩蛋

大家懂得，开启后获得某些天赋技能，很完美！

```bash
nvram set google_fu_mode=0xDEADBEEF
nvram set ext_show_lse=1
nvram commit
```


  [1]: http://www.right.com.cn/forum/thread-221578-1-1.html
  [2]: https://breed.hackpascal.net/
  [3]: http://www.right.com.cn/forum/forum.php?mod=viewthread&tid=218704