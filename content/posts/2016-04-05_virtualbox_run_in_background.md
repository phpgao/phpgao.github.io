---
title: "使用VirtualBox在后台运行镜像"
categories: [ "服务器技术" ]
tags: [ "headless","cmder","virtualbox" ]
draft: false
slug: "virtualbox_run_in_background"
date: "2016-04-05 01:58:00"
url: "virtualbox_run_in_background.html"
---

![VirtualBox][1]

老高的想做的很简单，就是开一个没有界面的虚拟机运行在后台，由于VirtualBox的GUI和虚拟机的运行管理是分离的，想要做到这一点很容易！

<!--more-->


如封面图所示，老高有一个ubuntu系统，没有安装X，所以每次启动后老高都用cmder工具，然后ssh远程管理。

但是每次运行系统的时候都会占用一个窗口，本来笔记本的任务栏就很小了，还要被占用一个，实在不爽！

其实解决办法很简单：

首先，我们在VirtualBox的安装目录打开CMD或者cmder(什么？你还没听过cmder！请移步[windows下cmd的替换工具cmder][2])。准备在命令行里输入新的命令。

然后运行`VBoxManage.exe -h`，查看帮助

```bash
VBoxManage.exe -h
# 看到有一条帮助信息是关于启动vm的
#  startvm                   <uuid|vmname>...
#                            [--type gui|sdl|headless|separate]
```

最后，结合自己的vm名称，即可拼出命令

```bash
# ubuntu是老高的虚拟机名称
VBoxManage.exe startvm ubuntu --type headless
```

\>end


  [1]: https://blog.phpgao.com/usr/uploads/2016/04/3686880991.png
  [2]: https://blog.phpgao.com/cmder.html