---
title: "HostMyBytes-最低一年8刀的VPS"
categories: [ "福利" ]
tags: [ "vps" ]
draft: true
slug: "hostmybytes"
date: "2017-12-13 06:55:00"
url: "hostmybytes.html"
---

今年搬瓦工价格飞起，好在今天老高发现了HostMyBytes，目前双12促销**128M内存****一年`$5`，**768M内存****一年`$8`，**1G内存**一年`$9.9`，而且是`亚洲优化路线路`，上海电信实测PING值`160`，有没有动心？时间有限赶紧买买买！

--------


20190606更：

> **重要！**HostMyBytes已经被AlphaRacks收了，导致现在老高已经登录不了账户了，所以还是老老实实搬瓦工吧。针对之前使用HostMyBytes的筒子们，只能祭出工单大法了！

![发工单][1]

20180228更：

> 根据最近评论里反馈的问题推测，这个服务商还是没有搬瓦工靠谱，所以老高推荐到搬瓦工买19.9刀的那个套餐，最然配置差了一点，但是客服和控制面板还行相对好很多。但如果你是大牛或者有相对丰富的VPS使用经验，那么HostMyBytes还是可以一试的。

---------


<!--more-->

> 下单前请注意，特价产品好像在退款条件里写了促销活动销售的产品不保证退款，暂时还不理解这个是什么意思，我已经以身试法退了一个，然后发了工单，等回复中。(所以最好买$9.9的，以免之后后悔)

> 讨论结果是这个是促销套餐，不能退款，只能恢复之前的取消的service，于是老高现在跑了一个$18和$8的服务器，正在考虑要不要把$8的免费提供给大家使用?

![时间有限赶紧买买买][2]

> 下单时不要忘记选择加速服务

![下单时不要忘记选择加速服务][3]

## 实测

IP : `45.61.156.81` 和 `66.154.104.90` (选的洛杉矶)


![服务器ping][4]

```
time dd if=/dev/zero of=/test bs=8k count=300000
300000+0 records in
300000+0 records out
2457600000 bytes (2.5 GB) copied, 18.8002 s, 131 MB/s

real	0m22.286s
user	0m0.080s
sys	0m5.448s
```

## 购买

> 这个价格估计几天就卖光了，8刀你买不了吃亏

购买链接在此，推荐大家量力购买，不过由于HostMyBytes目前不能像搬瓦工那样补差价升级，所以还是推荐一次到位，入$9.9或者更高的。

[12.12 - 128MB VPS（亚洲优化）年付5刀][5]  ---> 只运行ss推荐
[12.12 - 768MB VPS（亚洲优化）年付8刀][6]  ---> 额外可以搭建一个博客，但是内存吃紧
[12.12 - 1GB VPS（亚洲优化）年付9.99刀][7] ---> Wordpress博客流畅运行
[12.12 - 2GB VPS（亚洲优化）年付18刀][8]   ---> 2CPU + 2G内存，你还要啥自行车啊！

可选中文，结账方式可以选择paypal，信用卡，比特币，另外可以选择中文。

![HostMyBytes 选择中文][9]

> 老高推荐系统选择`Centos7-minimal`，然后使用[Centos7 一键安装 SS][10]，快速安装shadow$ocks与kcptun！


## 退款

故事是这样的，活动一开始老高订购了$8的套餐，后来发现内存不是很够用就申请退款然后入了$18的套餐，退款当时没有受理我也没有注意，后来在评论中@allen提到了这个事情，我才想起来退款这件事儿，于是查看我的pp账单，果然没有收到，于是开始了和客服的撕逼，最后的结果就是没法退款，只能恢复之前退款的service。理由是[Billing & Refunds][11]里有提到，促销的套餐不支持退款，**所以大家下单前一定要注意**！！

吃瓜请看这儿：

![ticket][12]


  [1]: https://blog.phpgao.com/usr/uploads/2019/06/1905816438.png
  [2]: https://blog.phpgao.com/usr/uploads/2017/12/3403119263.jpg
  [3]: https://blog.phpgao.com/usr/uploads/2017/12/1175514436.jpg
  [4]: https://blog.phpgao.com/usr/uploads/2017/12/1359880438.jpeg
  [5]: https://clients.hostmybytes.com/aff.php?aff=193&pid=178
  [6]: https://clients.hostmybytes.com/aff.php?aff=193&pid=179
  [7]: https://clients.hostmybytes.com/aff.php?aff=193&pid=180
  [8]: https://clients.hostmybytes.com/aff.php?aff=193&pid=181
  [9]: https://blog.phpgao.com/usr/uploads/2017/12/2799880283.jpeg
  [10]: https://blog.phpgao.com/auto_install_shadowsocks_and_kcptun.html
  [11]: https://www.hostmybytes.com/terms-of-service.php
  [12]: https://blog.phpgao.com/usr/uploads/2018/01/3625301412.png