---
title: "在Finder当前目录打开终端"
categories: [ "代码人生" ]
tags: [ "mac","osx","terminal","finder" ]
draft: false
slug: "open_terminal_in_finder"
date: "2014-12-27 11:38:00"
url: "open_terminal_in_finder.html"
---

在终端里打开Finder很简单，使用`open .`即可

## 系统设置

在Finder打开terminal这个功能其实是有的，但是系统默认没有打开，我们可以通过如下方法将其打开

进入系统偏好设置->键盘->快捷键->服务。

在右边`新建位于文件夹位置的终端窗口`上打勾。

如此设置后，在Finder中右击某文件，在出现的菜单中找到服务，然后点击`新建位于文件夹位置的终端窗口`即可！


<!--more-->


## 第三方

在Finder中打开其他终端就要费脑子了。不过有大神已经把相关工具写出来了，下面老高就介绍一下如何使用。

ps.此方法暂时不适用Yosemite。

首先，请移步至[https://github.com/jbtule/cdto/releases][1]。

然后下载最新的release，目前v2.6支持到Mavericks。

解压文件后得到terminal、x11_xterm、iterm文件夹，文件夹里有cd to.app，他们名字一样，但分别对用不同的终端软件，选择你需要的cd to.app，将他拷贝至应用程序文件夹中。

如果你的系统是10.9 Mavericks，那么最后一步需要快捷键的配合。

在Finder中按住 ⌘ + ⌥，即command+option。

然后拖到Finder的工具栏中，如下图

![cd to.app][2]

ps.如何让Finder标题显示当前路径？

在终端里输入以下命令

```bash
defaults write com.apple.finder _FXShowPosixPathInTitle -bool YES 
killall Finder
```

完

  [1]: https://github.com/jbtule/cdto/releases
  [2]: https://www.phpgao.com/usr/uploads/2014/12/3103464104.png