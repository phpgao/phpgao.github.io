---
title: "Nexus6刷机教程"
categories: [ "生活" ]
tags: [ "nexus6" ]
draft: false
slug: "nexus6_unlock"
date: "2016-01-19 07:13:00"
url: "nexus6_unlock.html"
---

![nexus6][1]

老高的Nexus5去年由于进水，导致触摸屏失灵，淘宝一搜屏幕的价格，我去。。。算了还是在家吃灰吧！

转眼到了11月黑五，色魔张大妈推了一条信息，奶6促销$199，果断入手。

在经历了清关被扣，又要交税等等事情后，终于于今天（2016年1月19日）顺利收到，在此十分感谢向同事从上海人肉快递到北京！

总部的网可以直接Google，但是OTA升级的速度简直不敢恭维，索性直接解锁线刷系统了！


<!--more-->


[下载镜像][2]

> 下载版本有原生不加密版，推荐使用。目前6.0.1 (MMB29K)为最新的原生不加密版！

[安装驱动 google usb driver][3]

[安装SDK][4]

使用SDK安装**platform-tools**，就可以得到我们最终需要的四个文件了

> AdbWinApi.dll
AdbWinUsbApi.dll
adb.exe
fastboot.exe

当以上都准备好了以后，就可以开始刷机了！



开启调试，并开启OEM解锁，然后连接手机到电脑！

```
# 重启至bootloader 或者 关机后 锁屏+音量-
adb reboot bootloader

# 解锁，选择Yes
fastboot oem unlock

# 解锁后重启，再次进入系统后关机，锁屏+音量-再次进入bootloader，开始刷系统

# 刷机命令
# 首先解压镜像
# 然后把之前提到的platform-tools下四个文件复制到镜像文件夹中，和flash-all.bat保证在一起！
# 下面是刷机命令
flash-all.bat

# 执行完毕后完成刷机！
```
  [1]: https://blog.phpgao.com/usr/uploads/2016/01/3352563752.jpg
  [2]: https://developers.google.com/android/nexus/images?hl=en
  [3]: http://developer.android.com/sdk/win-usb.html#top
  [4]: http://developer.android.com/sdk/