---
title: "老高推荐的VPS---Bandwagon-Host"
categories: [ "福利" ]
tags: [ "vps","shadowsocks","bandwagonhost" ]
draft: false
slug: "bandwagon"
date: "2016-12-09 06:22:00"
url: "bandwagon.html"
---

![bandwagon_logo][1]


作为老高博客的常客来说，搬瓦工一定再熟悉不过了。这篇文章是从[30元一年，打造自己的翻墙VPS][2]独立出来的。主要是介绍搬瓦工(Bandwagon Host)的购买方式。


> ps.老高的博客目前就稳定运行在bandwagon每年$9.9的服务器上！

<!--more-->


## 写在前面

老高在接触VPS之前，搬瓦工已经很有名了，身边懂技术的同事们基本人手一个。所以如果遇到了便宜($20以下年付)的套餐，还是很推荐购买的。

下面是老高总结的一些优缺点，希望对你有帮助：

 - 搬瓦工(Bandwagon)最近推出了一个直通中国电信和联通的线路，主机内存512M，目测看1080P的油管无压力。  ✅
 - 搬瓦工(Bandwagon)服务器后台可以一键搭建shadowsocks服务端，很适合新手使用。 ✅
 - 虽然提供了比较新的系统，但是由于架构的原因，还是无法使用如Docker、锐速等工具。 ❌
 - 后台提供了一键快照工具，重做系统前记得做一次快照。   ✅
 - 后天提供一系列API可供使用，github上有人做过包装，可以用做自动化监控和管理的工具。 ✅
 - 有些服务器速度一般，通病了。   ❌
 - 进2年没有什么运维上的失误导致服务不可用，不过倒是被GFW盯上了，不翻墙没法浏览?。 ✅❌
 - 30天内随便退款还是很爽 ✅
 - 可以在线shell，临时维护一下还是很方便的 ✅
 - 自带性能监控(Detailed statistics)，可以查看性能瓶颈。 ✅
 - 2015年开始支持支付宝，然后就开始抢购了。  ✅❌
 - 没有提供中文页面，差评。   ❌
 - 可以一直续期 ✅

(不断补充中。。。)



以下内容基本就是原帖了：
----------


## 测速

测速这玩意和跑分一样，看看就行了?，话说速度还是很快的。

下图为老高更新SDK时的速度，如果你也有无法更新Android SDK的烦恼，请参考[使用搬瓦工代理解决无法更新Android SDK的问题][3]

![解决Android SDK更新][4]

服务器下载测速：**50MB/s**

![ADT的下载][5]

服务器下载国内文件测速：**31.9MB/s**

![0140824091404.png][6]

网友[@何时不做中国的牲口][7]的服务器，位于凤凰城

![网友测速][8]

今天老高给大家推荐一个国外的VPS服务商，名字叫**Bandwagon host**，又名搬瓦工。开始觉得VPS这么便宜是不是坑？用了才发现，果然是坑，因为老高第一次买了比较便宜的套餐，内存只有坑爹的128MB，centos还算跑的流畅，可是再想弄点别的就显得有点力不从心了。所以一咬牙一跺脚，小心翼翼的升级到$9.99的，内存512MB，还算不太坑，搭个小站足够用了。

## 支持一键安装shadowsocks

这是要被墙的节奏吗？搬瓦工支持centos**一键安装shadowsocks**（仅支持centos）。

![ss快捷安装][9]

## 服务器说明

> 
>     VPS technology: OpenVZ/KiwiVM               先进的管理后台
>     Linux OS: Centos, Debian, Ubuntu, Fedora    这么多的系统选择
>     Instant OS reload                           瞬间系统切换
>     1 Dedicated IPv4 address                    独占一个IP
>     Full root access                            完整的root权限
>     PPP and VPN support (tun/tap)               支持PPP和VPN！
>     Instant RDNS update from control panel      管理后台瞬间实现可逆DNS
>     No contract, anytime cancellation           无需客服，不满意直接退款！
>     99% uptime guarantee                        保证99%的无故障时间
>     30-day money back guarantee                 30天内可以随意退款

总结一下就是独立IP，1G带宽，随便整，直接翻墙，不爽退款

## 购买攻略

最简单的方式是先去[bandwagonhost.com][10]注册一个帐号

![注册bandwagonhost账号][11]

> 由于验证码经常被Q，下单时直接注册不需要填写验证码了。

**好消息，搬瓦工支持支付宝了！！结账的时候选择 Credit Card and AliPay (Stripe) 即可**，不要忘了使用老高的**优惠码**！。

![搬瓦工支持支付宝了][12]

## 优惠码

> 6/29更：目测500G流量以下已经没有库存了！

老高给大家申请的3%优惠码在这里！  --->  **phpgao**

> 如何使用搬瓦工优惠码？

添加到购物车后，在结帐页面输入搬瓦工优惠码即可！

![搬瓦工优惠码][13]

## 套餐列表

<table border="1" cellpadding="10%">
  <tr>
    <th>方案</th>
    <th>换机房</th>
    <th>内存</th>
    <th>CPU</th>
    <th>硬盘</th>
    <th>共享带宽</th>
    <th>每月流量</th>
    <th>年付</th>
    <th>购买链接</th>
  </tr>
  <tr>
    <td>10G PROMO V3(中国直连线路)</td>
    <td>N</td>
    <td>512MB</td>
    <td>1</td>
    <td>10 GB SSD</td>
    <td>1GB PS</td>
    <td>1000 GB</td>
    <td>$19.99</td>
    <td><a target="_blank" href="http://bandwagonhost.com/aff.php?aff=922&pid=34">点我购买</a></td>
  </tr>
  <tr>
    <td>20G PROMO V3(中国直连线路)</td>
    <td>N</td>
    <td>1GB</td>
    <td>1</td>
    <td>20 GB SSD</td>
    <td>1GB PS</td>
    <td>2000 GB</td>
    <td>$39.99</td>
    <td><a target="_blank" href="http://bandwagonhost.com/aff.php?aff=922&pid=35">点我购买</a></td>
  </tr>
  <tr>
    <td>10G PROMO V3 - PHOENIX AZ</td>
    <td>N</td>
    <td>512MB</td>
    <td>1</td>
    <td>10 GB SSD</td>
    <td>1GB PS</td>
    <td>1000 GB</td>
    <td>$11.99</td>
    <td><a target="_blank" href="http://bandwagonhost.com/aff.php?aff=922&pid=27">点我购买</a></td>
  </tr>
  <tr>
    <td>20G PROMO V3 - PHOENIX AZ</td>
    <td>N</td>
    <td>1GB</td>
    <td>1</td>
    <td>20 GB SSD</td>
    <td>1GB PS</td>
    <td>2000 GB</td>
    <td>$18.99</td>
    <td><a target="_blank" href="http://bandwagonhost.com/aff.php?aff=922&pid=28">点我购买</a></td>
  </tr>
  <tr>
    <td>Bandwagon 10G - PROMO</td>
    <td>Y</td>
    <td>256 MB</td>
    <td>1</td>
    <td>10 GB SSD</td>
    <td>1GB PS</td>
    <td>500 GB</td>
    <td>$19.99</td>
    <td><a target="_blank" href="http://bandwagonhost.com/aff.php?aff=922&pid=21">点我购买</a></td>
  </tr>
  <tr>
    <td>5G PROMO V2</td>
    <td>Y</td>
    <td>512M</td>
    <td>1</td>
    <td>5 GB SSD</td>
    <td>1GB PS</td>
    <td>500 GB</td>
    <td>$9.99</td>
    <td><a target="_blank" href="http://bandwagonhost.com/aff.php?aff=922&pid=22">点我购买</a></td>
  </tr>
  <tr>
    <td>10G PROMO V3</td>
    <td>Y</td>
    <td>512M</td>
    <td>1</td>
    <td>5 GB SSD</td>
    <td>1GB PS</td>
    <td>1000G</td>
    <td>$11.99</td>
    <td><a target="_blank" href="http://bandwagonhost.com/aff.php?aff=922&pid=27">点我购买</a></td>
  </tr>
  <tr>
    <td>20G PROMO V3</td>
    <td>Y</td>
    <td>1GB</td>
    <td>1</td>
    <td>5 GB SSD</td>
    <td>1GB PS</td>
    <td>2000G</td>
    <td>$18.99</td>
    <td><a target="_blank" href="http://bandwagonhost.com/aff.php?aff=922&pid=28">点我购买</a></td>
  </tr>
  <tr>
    <td>64M套餐</td>
    <td>Y</td>
    <td>64M</td>
    <td>1</td>
    <td>1.5GB 机械硬盘</td>
    <td>1GB PS</td>
    <td>100G</td>
    <td>$3.99</td>
    <td><a target="_blank" href="http://bandwagonhost.com/aff.php?aff=922&pid=19">点我购买</a></td>
  </tr>
  <tr>
    <td>96套餐</td>
    <td>Y</td>
    <td>96M</td>
    <td>1</td>
    <td>2GB 机械硬盘</td>
    <td>1GB PS</td>
    <td>200G</td>
    <td>$4.99</td>
    <td><a target="_blank" href="http://bandwagonhost.com/aff.php?aff=922&pid=20">点我购买</a></td>
  </tr>
  <tr>
    <td>128套餐</td>
    <td>Y</td>
    <td>128M</td>
    <td>1</td>
    <td>3GB 机械硬盘</td>
    <td>1GB PS</td>
    <td>300G</td>
    <td>$5.99</td>
    <td><a target="_blank" href="http://bandwagonhost.com/aff.php?aff=922&pid=21">点我购买</a></td>
  </tr>
</table>

点击对应的链接后如下图所示。

![选择套餐][14]

点击checkout就可以下单了。

![点击checkout][15]


----------


### 下单注意

> 下单前请注意：**monthly**是每月，**quarterly**是每季，**semiannually**是半年，**annually**是一年，请按需购买

> **长城宽带**就不要买了

> 可以先体验最便宜的那一款，测试后觉得靠谱再**升级**即可！($9.9以下可能有限速)。

> [如何使用搬瓦工(bandwagonhost)后台管理VPS][16]

> [老高提供shadowsocks免费试用][17]

> [搬瓦工购买前必读][18]

> [搬瓦工购买后必读][19]

> [使用搬瓦工的VPS搭建博客网站][20]

> 更多的折腾方式[翻越功夫网][21]

## 如何升级套餐

    1. [点此登陆帐户][22]
    2. 在页面最下面找到 -> My Services menu
    3. 点击 Manage
    4. 找到 Management Actions -> Upgrade/Downgrade
    5. 选择合适的套餐升级，补差价
    6. 瞬间升级完毕

老高的博客现在就运行在[5G PROMO V2][23]，由于内存还算充裕，速度和稳定性也是不错，机子已经一个多月没有重启了！

这么好的资源不用shadowsocks真是浪费，赶紧参考老高写的[安装shadowsocks教程][24]，同时老高还是提供[免费shadowsocks服务器的试用][25]，方便大家使用google查资料。


[更多VPS][26]


  [1]: https://blog.phpgao.com/usr/uploads/2016/12/1997432409.png
  [2]: https://blog.phpgao.com/vps.html
  [3]: https://blog.phpgao.com/privoxy-shadowsocks.html
  [4]: https://blog.phpgao.com/usr/uploads/2015/06/3730396386.png
  [5]: https://blog.phpgao.com/usr/uploads/2014/08/1113281084.png "飞快的下载速度"
  [6]: https://blog.phpgao.com/usr/uploads/2014/08/547157528.png "QQ下载速度"
  [7]: http://weibo.com/guhunyizhong
  [8]: https://blog.phpgao.com/usr/uploads/2015/05/2344751404.jpeg
  [9]: https://blog.phpgao.com/usr/uploads/2015/12/3686652929.jpg
  [10]: https://bandwagonhost.com/register.php
  [11]: https://blog.phpgao.com/usr/uploads/2015/05/2826423339.png
  [12]: https://blog.phpgao.com/usr/uploads/2015/06/1369919154.png
  [13]: https://blog.phpgao.com/usr/uploads/2015/06/1446800089.png
  [14]: https://blog.phpgao.com/usr/uploads/2014/12/3276652896.png
  [15]: https://blog.phpgao.com/usr/uploads/2014/12/2339820678.png
  [16]: https://blog.phpgao.com/bandwagonhost_vps_panel.html
  [17]: https://blog.phpgao.com/try_shadowsocks_via_qrcode.html
  [18]: https://blog.phpgao.com/pre_buy_faq.html
  [19]: https://blog.phpgao.com/post_buy_faq.html
  [20]: https://blog.phpgao.com/website_on_bandwagonhost.html
  [21]: https://blog.phpgao.com/slip_across_gfw.html
  [22]: https://bandwagonhost.com/clientarea.php?action=products
  [23]: http://bandwagonhost.com/aff.php?aff=922&pid=22
  [24]: https://blog.phpgao.com/shadowsocks_on_linux.html
  [25]: https://blog.phpgao.com/try_shadowsocks_via_qrcode.html
  [26]: https://blog.phpgao.com/vpses.html