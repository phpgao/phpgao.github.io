---
title: "mac快速移动窗口至另一个桌面"
categories: [ "代码人生" ]
tags: [ "mac","osx","apple" ]
draft: false
slug: "mac_move_window_to_other_desktop"
date: "2014-10-11 12:28:00"
url: "mac_move_window_to_other_desktop.html"
---

Mac系统有一大优势，就是在一台电脑上面，可以设定出很多桌面空间使用，这样我们的桌面就不会很凌乱，像MacGG一般都会用到4个桌面空间。在使用桌面空间的时候，大家可能都会用到一个功能，就是我要移动一个窗口到另一个桌面空间，都会把窗口拖到屏幕右边停留几秒，就回自动的切换到下一个桌面空间。今天MacGG给大家介绍一个方法，可以秒切哈。

什么是秒切，就是你拖动窗口到屏幕边缘，无需停留，就自动切到下一桌面空间。

首先打开“终端”（Finder->应用程序->实用工具->终端），并且输入以下命令：

     defaults write com.apple.dock workspaces-edge-delay -float 0;killall Dock

现在大家试一下秒切吧。哈哈。。很爽不。是不是太快了，如果觉得太快，我们还可以在改改设定，我就设定在屏幕边缘停留半秒切换吧。
再次打开“终端”（Finder->应用程序->实用工具->终端），并且输入以下命令：

    defaults write com.apple.dock workspaces-edge-delay -float 0.15;killall Dock

如果你想还原成原样，也很简单，再次打开“终端”（Finder->应用程序->实用工具->终端），并且输入以下命令：

    defaults delete com.apple.dock workspaces-edge-delay;killall Dock

转自：[http://www.macgg.com/archives/6429.html][1]


  [1]: http://www.macgg.com/archives/6429.html