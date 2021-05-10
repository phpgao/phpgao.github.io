---
title: "使用Launchrocket管理MacOS的服务"
categories: [ "福利" ]
tags: [ "mac","launchrocket","brew" ]
draft: false
slug: "launchrocket"
date: "2018-04-03 14:13:00"
url: "launchrocket.html"
---

经常使用MacOS做web开发的同学们，当我们使用`brew`安装nginx、php后，管理其守护进程的生命周期就基本只能靠类似`brew services start nginx`管理了，要是有一个GUI程序为我们管理这些服务进程就好了。


<!--more-->


此处应该有Windows下的WAMP的截图，稍后补！


项目地址[jimbojsb/launchrocket][1]



```bash
# 执行命令前需要安装brew
brew cask install launchrocket
```


安装之后就可以移步系统设置页面，找到launchrocket

![macsetting-min][2]

老高目前只安装了nginx，通过界面我们可以看到我们可以选择启动，结束，自动启动和移除。

![launchrocket setting][3]

> 如果没有显示出你的服务，点击一下`Scan Homebrew`即可。

从此可以告别命令行管理服务了，是不是爽歪歪！

补充卸载命令：

```bash
brew cask uninstall launchrocket
```


  [1]: https://github.com/jimbojsb/launchrocket
  [2]: https://blog.phpgao.com/usr/uploads/2018/04/2390127098.png
  [3]: https://blog.phpgao.com/usr/uploads/2018/04/3193128308.png