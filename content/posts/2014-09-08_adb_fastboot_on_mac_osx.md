---
title: "在mac上使用adb与fastboot"
categories: [ "服务器技术" ]
tags: [ "adb","mac","osx","刷机","fastboot" ]
draft: false
slug: "adb_fastboot_on_mac_osx"
date: "2014-09-08 00:48:00"
url: "adb_fastboot_on_mac_osx.html"
---

今天给老娘的Mi2S刷机，研究了一下在mac上刷机的方法，在此记录一下。

PS.本方法适用于所有Android机器

## Android File Transfer

安装Android File Transfer

```bash
brew cask install android-file-transfer
```

要在OSX上管理Android手机上的文件，需要下载安装这个官方工具[http://www.android.com/filetransfer/][1]

由于某些特殊原因网站打不开不要紧，不会翻墙的TX可以到[我的网盘][2]下载到目前最新的Android File Transfer。

下载安装完成后运行，就可以轻松管理手机文件了，这个时候把下载好的ROM拷贝到手机目录下，再进入recovery刷机了。

![如何安装Android File Transfer][3]

## 命令行刷机

安装adb工具

```bash
brew cask install android-platform-tools
```

接下来再terminal中运行`adb`和`fastboot`即可开始刷机

![adb版本][4]

**小提示：**刷机用到的命令可以参考博主的这篇文章[adb,fastboot常用命令及刷机技巧][5]

部分参考来自：

[http://www.technobuzz.net/install-adb-fastboot-mac-linux-chrome-os-nexus-tool-script/?utm_source=tuicool][6]


  


  [1]: http://www.android.com/filetransfer/
  [2]: http://weibo.com/1731573754/Bm0oDhZbc
  [3]: https://blog.phpgao.com/usr/uploads/2014/09/3578216054.png
  [4]: https://blog.phpgao.com/usr/uploads/2014/09/548047147.png
  [5]: https://blog.phpgao.com/adb.html
  [6]: http://www.technobuzz.net/install-adb-fastboot-mac-linux-chrome-os-nexus-tool-script/?utm_source=tuicool