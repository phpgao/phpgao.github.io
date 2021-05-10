---
title: "搬瓦工(bandwagonhost)后台管理VPS&amp;安全设置"
categories: [ "服务器技术" ]
tags: [ "vps","security","bandwagonhost","panel","reboot","upgrade" ]
draft: false
slug: "bandwagonhost_vps_panel"
date: "2014-12-28 14:57:00"
url: "bandwagonhost_vps_panel.html"
---

购买了搬瓦工(bandwagonhost)的VPS，如何使用呢？

在此，老高以FAQ的形式给出介绍。

首先插几句使用建议，老高认为十分重要，为什么呢？搬瓦工如果监控到有大量的垃圾信息从我们的主机上发出，就会立即冻结VPS，冻结后的VPS相当于关机，我们可以进入panel后手动解冻即可恢复正常，但是解冻的次数每年只有三次，所以大家一定要注意自己VPS的安全。在此老高也给大家分享一些必要的操作，请务必参考。部分建议需要一定的linux基础，不会的TX请参考老高的[翻墙教程][1]后再google之。


<!--more-->


## 关于找不到SS面板

好多同学抱怨说搬瓦工的SS面板不见了，老高在此给大家讲解一下如何找回隐藏掉的SS入口

首先我们进入VPS的[管理界面][2]，点击进入`KiwiVM Control Panel`，就是丢失了SS面板的界面，然后点击[SS面板][3]，这个就是被隐藏掉的SS面板啦！当然，你说找不到ssr的面板，很简单！点击这个地址就OK [SSR面板][4]，什么？你说看不清楚，那老高写下来给你喽。

```
https://kiwivm.64clouds.com/main-exec.php?mode=extras_shadowsocks
https://kiwivm.64clouds.com/main-exec.php?mode=extras_shadowsocksr
```

> 搬瓦工SS一键安装需要系统为centos6，请大家不要忘了！

> 有伙伴说一键安装面板打不开，为啥不试试老高的[Centos7 系统一键安装 Shadowsocks 和 KCPTUN 加速][5]还有[Ubuntu 系统一键安装 Shadowsocks 和 KCPTUN 加速][6]

------

1. 请务必手动修改SSH的端口号。
1. 密码同上。
1. 禁止root远程密码登录，开启指纹登录，fail2ban是个好工具。
1. 用户登录后发送邮件想法不错，有兴趣的TX请参考文末reference。
1. 有PHP的话一定要开启open_basedir。
1. 不要轻信互联网上分享的什么一键安装脚本，自己编译才是王道。
1. 定期修改密码。
1. 数据库禁止远程登录。
1. shadowsocks不要使用root权限运行，mysql等服务器同理。
1. 不要给陌生人共享VPS。
1. 防火墙还是要学一下的，老高过一阵子会出教程。
1. 有补充的TX请务必留言，老高会第一时间共享给大家。

SSH安全配置请参考

[VPS安全之SSH][7]
[VPS安全之防火墙设置][8]

## 我的主机在哪儿？

点击右边黄色按钮Client Area

![Client Area][9]

登录之后，Services->My Services，即可进入VPS列表。

![service][10]

## 如何升级/降级我的系统

点击上图的`Manage`按钮，进入VPS管理后台，再点击`Upgrade/Downgrade`按钮，即可进入升级/降级界面。

![Upgrade/Downgrade][11]

在此选择5G PROMO V2套餐，点击升级即可！

![升级套餐][12]

## 如何管理我的VPS系统

回到VPS列表，点击`KiwiVM Control Panel`即可进入VPS系统管理界面，在这里，你可以对VPS系统执行各种管理操作，如系统的关机、重启、重新安装、重启、

![KiwiVM Control Panel][13]

## 如何一键安装shadowsocks

首先确保你的系统是centos，然后参考下图

![一键安装shadowsocks][14]

安装后返回，你可以在这里修改各种运行参数

![shadowsocks设置界面][15]

## 如何重做系统

点击`KiwiVM Control Panel`左侧的Install new OS，然后参考下图即可！

> ps. 重做系统成功后会有一个回馈页面，上面有更新后的SSH端口和root用户密码，请妥善保存。

> pss. 如果忘记保存，那么SSH口可以在VPS系统管理界面的信息中找到SSH端口信息，但是root密码是无法找回的，需要进入左侧进阶或者互动root shell中使用linux命令 passwd 直接修改root用户的密码即可。

> psss. 老高推荐的系统 centos-6-x86_64-minimal ， 重装完不要忘了yum update -y 升级系统！

![Install new OS][16]

## 重做系统时出现问题

有TX重做系统的时候会报以下错误

![ERROR][17]

该错误是因为您在VPS没有关机的情况下进行了此操作，所以需要先关机，进入主面板，点击ACTION后面的stop，等待关机后即可操作！

## 忘记ROOT密码

如果忘了密码，那就使用后台的shell功能直接进入系统修改root用户的密码。

首先点击左侧列表页，按下图操作

![运行shell][18]

图中的文字告诉我们，运行shell需要一个支持html5的浏览器，并且需要服务器开启一个没有出口限制的TCP连接，端口在32000-65000随机产生。

打开shell以后，我们就可以修改root密码了

```bash
# 修改root密码
passwd root
更改用户 root 的密码 。
新的 密码：
无效的密码： 它基于（颠倒的）字典单词
重新输入新的 密码：
passwd： 所有的身份验证令牌已经成功更新。
```

至此密码修改成功！

## 操作超时

如果在使用的过程中遇到下面的提示，不要慌张，只是你的本次会话超时了，重新点击`KiwiVM Control Panel`进入后台即可继续操作！

> Session timed out, please log in.

Reference:

http://0v.org/installing-ghost-on-ubuntu-nginx-and-mysql/
http://blog.tankywoo.com/ops/2013/09/14/common-security-of-vps.html
http://blog.geekli.cn/archives/70
https://www.linode.com/docs/security/securing-your-server/
https://www.google.com/#newwindow=1&q=site:v2ex.com%2Ft+VPS+安全


  [1]: https://blog.phpgao.com/shadowsocks_on_linux.html
  [2]: https://bandwagonhost.com/clientarea.php?action=products
  [3]: https://kiwivm.64clouds.com/main-exec.php?mode=extras_shadowsocks
  [4]: https://kiwivm.64clouds.com/main-exec.php?mode=extras_shadowsocksr
  [5]: https://blog.phpgao.com/auto_install_shadowsocks_and_kcptun.html
  [6]: https://blog.phpgao.com/auto_install_shadowsocks_and_kcptun-html.html
  [7]: https://blog.phpgao.com/vps_ssh.html
  [8]: https://blog.phpgao.com/vps_iptables.html
  [9]: https://blog.phpgao.com/usr/uploads/2015/01/3046723685.png
  [10]: https://blog.phpgao.com/usr/uploads/2015/04/2689784148.jpg
  [11]: http://ww2.sinaimg.cn/large/6735b7fatw1enpr0w34nnj20hs067gmf.jpg
  [12]: http://ww1.sinaimg.cn/large/6735b7fatw1enpr7idd52j20hs07p400.jpg
  [13]: http://ww2.sinaimg.cn/large/6735b7fatw1enprnzw4xzj20m80f5jwj.jpg
  [14]: https://blog.phpgao.com/usr/uploads/2015/05/394696712.jpg
  [15]: https://blog.phpgao.com/usr/uploads/2015/05/2722443899.png
  [16]: http://ww4.sinaimg.cn/large/6735b7fatw1enprvep2ubj20hs0a4gnc.jpg
  [17]: http://ww4.sinaimg.cn/large/6735b7fatw1enprwvzyjoj20f20340sw.jpg
  [18]: https://blog.phpgao.com/usr/uploads/2015/01/1800117711.png