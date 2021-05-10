---
title: "Nexus6刷机笔记"
categories: [ "生活" ]
tags: [ "ROM","nexus6" ]
draft: false
slug: "nexus_flash_rom"
date: "2016-05-12 04:07:00"
url: "nexus_flash_rom.html"
---

记录一下刷机的命令


<!--more-->


## 双清

下载[http://opengapps.org][1]，要记得N6是32位处理器！
刷GAPP记得要双清

## 72小时锁定

修改密码后72小时会被锁定，没法安装原版系统

## 安装adb，fastboot

[nexus-tools][2]

or

```
brew install android-platform-tools
```

## 解锁BL

```
# 注意！！会清数据
fastboot oem unlock
```

## 刷原版系统

下载[Factory Images for Nexus Devices][3]

```
# 解压
tar zxvf shamu-mob30i-factory-21357b09.tgz

# 修改shamu-mob30i中的某一句，效果不同

# 清数据
fastboot -w update image-shamu-mob30i.zip
# 不清数据
fastboot update image-shamu-mob30i.zip
```

## 刷RE

下载[TWRP for Motorola Nexus 6][4]

```
fastboot flash recovery twrp-3.0.2-0-shamu.img
```

## 刷ROM

不用预先把zip放进手机，可以使用sideload功能，先进入RE的advanced

```
adb sideload xxx.zip
```

[CM][5]

[魔趣][6]


  [1]: http://opengapps.org
  [2]: https://github.com/corbindavenport/nexus-tools
  [3]: https://developers.google.com/android/nexus/images#shamu
  [4]: https://twrp.me/devices/motorolanexus6.html
  [5]: https://download.cyanogenmod.org/?device=shamu
  [6]: http://download.mokeedev.com/?device=shamu