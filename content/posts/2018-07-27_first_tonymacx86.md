---
title: "记一次黑苹果的安装"
categories: [ "代码人生" ]
tags: [ "macos","tonymacx86","黑苹果" ]
draft: false
slug: "first_tonymacx86"
date: "2018-07-27 01:27:00"
url: "first_tonymacx86.html"
---

最近老高的MBP和MACmini卡的不行，想换硬件吧，硬件直接焊死，简直*良心苹果*，参考了很多黑苹果的安装贴后果断决定在我的台式机上安装最新版的macOS-High-Sierra-10.13.6，系统选择的是[【黑果小兵】][1]，确实好用，老高十分推荐！

![blackapple][2]

先来贴一波老高的渣渣台式机配置吧：

```
CPU: i56500
主板: GA b150m power2 小板
内存: 16G 海盗船8*2套装
硬盘: 850evo 750evo(MacOS) SM951(win10) 西数4T红盘你懂的
WIFI: BCM943602CS (黑苹果免驱，win10需要安装驱动)
显卡: 七彩虹 烈焰战神 1066 特供版(穷)
显示器: DELL UP2516D 2k分辨率(开启HiDPI后凑活能看)
```

总结一下：

安装黑苹果需要你对U盘装系统、BIOS、EFI有一个大致的了解，否则还是先补课再来吧，除非你不怕把数据弄丢，或者不管弄成啥样你都有办法重新装Windows系统，那就OK，请瞎折腾吧！！

还有一点，如果新装机的同学有安装黑苹果的打算，请务必挑选技嘉的板子，老高用这个板子安装黑苹果确实没有遇到什么神坑，过程还算比较顺利，目前老高的黑苹果系统运行流程稳定，睡眠唤醒都很正常，估计也是托了技嘉的福?。

<!--more-->


## 准备工作

 - U盘8G+，最好两个，一个装PE，一个MacOS安装盘
 - 黑果小兵的懒人版镜像(文件比较大，老高下的版本有问题，需要额外删除一些HD开头的config文件，一定要注意)
 - (可选)额外一台计算机，以防出问题查资料或者做系统用

## 制作启动盘

老高在Win下使用TransMac，很好用的工具，需要注意的是选择恢复盘的时候一定要确认好自己的U盘盘符，以免丢失数据！

具体方法请自行解决，英语好的人应该自己琢磨一下就知道怎么用了！

同时为了防止安装失败，老高会同时准备另一个PE盘，以防万一！老高推荐[微PE工具箱][3]

## BIOS

当硬件软件准备就绪了，我们就开始下一步BIOS的设置工作！

老高主要参考了几个帖子

[El Capitan 10.11.4, GA B150M D3H, i5 6500, GTX960][4]
[打造一台你的专属黑苹果][5]

总结一下：

```
BIOS Settings
1. Enter with DEL key
2. Load Optimized Defaults (F7)
3. Settings
Secure Boot Mode disabled
Fast boot disabled
XHCI Hand-Off enabled
Serial Port disabled
VT-d disabled
Set OS Type to Other OS
If your system has CFG-Lock, disable it
IOAPIC 24-119Entries —> Disable
```

老高的微调：

```
BIOS里直接关闭集显(后面会解释)
```

## 启动并安装

这可是最折腾，也是最有意思的环节了，在安装的过程中你可能会遇到各种各样的问题，并且你会发现不知一个人也会遇到这个问题。。。然后一起解决问题，是不是很有意思！

> 最好直接把U盘启动放在第一，因为安装过程中会重新启动2-3次，可能发生一不小进入原Windows系统的情况。

老高在安装的时候遇到几个问题，都不算很难解决：

### 无法启动Clover

现象：在Clover的界面选择系统进入系统直接卡死，此时还没有进入黑屏解密

解决：这个问题是因为兼容性问题，在老高提到的黑果小兵在页面有说明，只要根据他的说明操作即可！

### A kext releasing a(n) ApplePS2MouseDevice has corrupted the registry

现象：加载期间报错A kext releasing a(n) ApplePS2MouseDevice has corrupted the registry
解决：一看就是PS2设备驱动报错，直接在clover的驱动里删掉即可

### 卡在 IOConsoleUsers: gIOScreenLockState3, hs0, bs0 now0 sm0x0k

现象：在最后阶段直接卡在IOConsoleUsers: gIOScreenLockState3, hs0, bs0 now0 sm0x0k，一看就是卡在显示设备上
解决：直接禁用集显


## 进入系统

注意点：

 - 最好先不要登录你的APPLE ID，在界面选择跳过，先看看能不能进系统
 - (可以跳过)如果能进入，先配置好网络，下载`Clover Configurator`，加载U盘里的EFI分区，加载config，修改SMBIOS为你想要的机型，然后写入随机UUID，重启后就能发现有序列号，这个时候再登录就不容易封号！

## 驱动

### 声卡

首先查询你的声卡是什么型号，老高在技嘉官网查询到[GA-B150M-Power2 (rev. 1.0)][6]的声卡参数如下，Google之，搜索到这个工具[macOS Realtek ALC onboard audio with Clover][7]，很完美！一个命令行解决问题！

![alc887][8]

### 显卡

A卡理论上是原生支持的，老高的1066需要安装驱动，老高在这个论坛下载的[Nvidia Web Driver updates for macOS High Sierra (UPDATE 07/09/2018)][9]，找到对应系统下载，安装，重启！

## EFI覆盖

老高在MacOS下使用`Clover Configurator`挂载位于NVME下的EFI分区，同时挂载U盘里的EFI分区，将EFI分区里的CLOVER文件夹和BOOT覆盖NVME里，注意不要删除Microsoft文件夹，否则可能造成无法进入原Windows系统的情况！然后在BIOS里选择使用磁盘的EFI启动！

## 键位映射

Windows里F1-12都有不同的作用，但是在MacOS下不同，比如F3是预览桌面，F7-9是控制播放器的，F10-12是控制音量。

如果想在黑苹果下使用这些功能，需要使用[Karabiner工具][10]，老高的配置如下：

![Karabiner][11]

## 开启HiDPI

首先下载[RDM][12]

在按照这个页面[SCALED RESOLUTIONS FOR YOUR MACBOOKS EXTERNAL MONITOR][13]操作即可开启HiDPI。

> MBP 13'3 = 227 PPI && UP2516D 25' = 118 PPI，是不是差很多啊！


  [1]: https://blog.daliansky.net/macOS-High-Sierra-10.13.6-17G65-Release-Version-with-Clover-4596-original-mirror.html
  [2]: https://blog.phpgao.com/usr/uploads/2018/07/2089683343.png
  [3]: http://www.wepe.com.cn/
  [4]: https://www.tonymacx86.com/threads/skylake-guide-el-capitan-10-11-4-ga-b150m-d3h-i5-6500-gtx960.193156/
  [5]: http://skx926.com/2016/11/28/make-your-own-hackintosh/
  [6]: http://www.gigabyte.cn/Motherboard/GA-B150M-Power2-rev-10#sp
  [7]: https://github.com/toleda/audio_CloverALC
  [8]: https://blog.phpgao.com/usr/uploads/2018/07/2463611138.jpg
  [9]: https://www.insanelymac.com/forum/topic/324195-nvidia-web-driver-updates-for-macos-high-sierra-update-07092018/
  [10]: https://pqrs.org/osx/karabiner/
  [11]: https://blog.phpgao.com/usr/uploads/2018/07/3030260309.png
  [12]: http://avi.alkalay.net/software/RDM/
  [13]: https://comsysto.github.io/Display-Override-PropertyList-File-Parser-and-Generator-with-HiDPI-Support-For-Scaled-Resolutions/