---
title: "小米路由器mini折腾之远程下载篇"
categories: [ "路由器" ]
tags: [ "router","remote","xiaomi","pandorabox","openwrt","download" ]
draft: false
slug: "xiaomi_router_mini_remote_download"
date: "2015-02-12 14:15:00"
url: "xiaomi_router_mini_remote_download.html"
---

小米路由器mini刷了Pandorabox后还是可以使用迅雷的远程下载的，步骤如下：


<!--more-->


 1. 移步至[论坛下载安装程序][1]，贴子里编译出的版本很多，对于mini应该下载 xware_mipsel_32_uclibc。
    
 1. ssh进入硬盘或U盘，创建xunlei文件夹，将数据解压至该目录。
    
 1. 执行`chmod 777 * -R`添加执行权限。
    
 1. 最后运行`./portal`，等待迅雷获取绑定码。

 1. 进入[远程官网][2]添加下载器，输入绑定码！
    
 1. 绑定成功后就可以开始离线下载了。


2015年02月16日更：

这样设置后路由器重启迅雷不会自动启动，所以我们需要将其加入开机脚本

假设我们把Xware解压至`/mnt/sda1/xunlei`，那么我们的portal文件的路径就是：`/mnt/sda1/xunlei/portal`。

```
# 使用vi命令打开启动文件
vi /etc/rc.local


# 在exit0前写入
/mnt/sda1/xunlei/portal
```

2015年03月25日更：

昨天貌似迅雷的服务器把我们免费用户的远程下载功能关了，导致我们无法获取到绑定码，但是细心的网友发现其实1.0.16的版本还是可以使用的，老高在此分享给大家！

下载地址： http://yunpan.cn/cZeHegqZ49INi （提取码：455c）


  [1]: http://g.xunlei.com/thread-12545-1-1.html
  [2]: http://yuancheng.xunlei.com/