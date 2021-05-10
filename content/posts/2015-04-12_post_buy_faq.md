---
title: "搬瓦工购买后必读"
categories: [ "服务器技术" ]
tags: [ "vps","bandwagonhost" ]
draft: false
slug: "post_buy_faq"
date: "2015-04-12 10:35:00"
url: "post_buy_faq.html"
---

本文介绍了搬瓦工购买后的常规操作。


<!--more-->


## 服务器后台的操作

请移步[如何使用搬瓦工后台管理VPS&安全设置][1]

## 我的VPS在哪儿？

![vps后台][2]

## 如何登陆我的vps

**windows**用户需要终端，请[下载cmder完整版][3]

运行后使用命令

```
# 11.11.11.11 是服务器IP xxxx是ssh端口号
ssh root@11.11.11.11 -p xxxx
```

之后根据提示，输入`yes`，然后再输入**root用户的密码**即可顺利登陆！

## 系统选择

由于搬瓦工默认给大家安装的系统为Centos 32位

老高建议大家换成**Centos 64位**。

具体操作请参考`如何使用搬瓦工后台管理VPS&安全设置`一文，将系统更换为老高推荐的**centos-6-x86_64-minimal**。

## shadowsocks管理

### 管理服务端

如下图所示，shadowsocks的主要功能都集中在这个菜单里，比如安装、修改密码等操作！

![shadowsocks][4]

### 下载shadowsocks的客户端

shadowsocks的客户端几乎支持**所有平台**。

客户端下载请移步：

http://shadowsocks.org/en/download/clients.html

## 配置客户端

ss的配置一般有两种，一种是**文件形式**，比较直观；另一种是**二维码形式**，适合分享。

文件形式：

```
# xx.x.x.x  --> 服务器IP
# 23456 --> SS服务端口
# xxxosihd --> 密码
# rc4-md5 --> 加密方式
# 其他保持不变

{
    "server": "xx.x.x.x",
    "server_port": 23456,
    "local": "0.0.0.0",
    "local_port": 1080,
    "password": "xxxosihd",
    "timeout": 60,
    "method": "rc4-md5"
}
```

二维码形式：

请善用这两个功能，**生成二维码后，在移动设备上扫描一下，很方便！**

![二维码][5]

## 安全设置

安全设置应该是**小白**最在意的问题了！

其实服务器只要做到下面几点就行了

 - root用户使用强密码，其实搬瓦工初始化的时候已经是强密码了
 - 搬瓦工用户使用强密码
 - 定期修改shadowsocks密码
 - 定期更新系统

刚刚拿到系统时，一定要**更新系统**，请在终端中执行，命令如下：

```
yum update -y
```

## 接下来干点啥

当你的ss搭建完毕，你可以继续建立你的博客，参考此教程：[使用搬瓦工的VPS搭建博客网站][6]

或者可以折腾你的路由器，让他能够让子网设备自动翻墙，Google、Android、1024什么的在家里随便上，下面是文章的索引：

大家可以参考 [小米路由器mini折腾系列][7] 文章！


  [1]: https://blog.phpgao.com/bandwagonhost_vps_panel.html
  [2]: https://blog.phpgao.com/usr/uploads/2015/04/2689784148.jpg
  [3]: https://blog.phpgao.com/cmder.html
  [4]: https://blog.phpgao.com/usr/uploads/2015/12/3686652929.jpg
  [5]: https://blog.phpgao.com/usr/uploads/2015/04/4226952229.png
  [6]: https://blog.phpgao.com/website_on_bandwagonhost.html
  [7]: https://blog.phpgao.com/search/%E5%B0%8F%E7%B1%B3%E8%B7%AF%E7%94%B1%E5%99%A8mini%E6%8A%98%E8%85%BE/
  [8]: 