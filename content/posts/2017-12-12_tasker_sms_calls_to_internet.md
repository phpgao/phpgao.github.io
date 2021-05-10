---
title: "使用tasker把新短信和未接电话转发到email或server酱"
categories: [ "福利" ]
tags: [  ]
draft: false
slug: "tasker_sms_calls_to_internet"
date: "2017-12-12 09:46:00"
url: "tasker_sms_calls_to_internet.html"
---

有两个手机号又同时用着IP的同学是不是每天都要带着两部手机闯天下呢？(老高更是有三个手机卡。。)每天在多个手机上操作简直快要精分了，于是想起了之前在PLAY上购买了tasker，貌似可以把多个手机收到的信息转发到网上，比如邮件或者push服务，老高能想到的几个：

 - [Pushbullet][1]
 - [Pushover][2]
 - [Instapush][3]
 - [server酱][4]

各家服务都差不多，都可以实现用一个API推送你的消息，区别就是UI和价格了，这次就以server酱(免费)为例看看如何设置吧。

> 有闲置的手机(最好是小米)也是可以搞一搞的，毕竟那么多神卡可以申请。

<!--more-->

## GOOGLE PLAY

如果可以，请给你的手机下载Google Play并购买正版。如果是小米手机，那就很方便了，MIUI9下直接下载GO安装器就行，如果是其他机器请自行Google方法。

## 配置APP

### 新短信

tasker的配置很简单，首先我们需要定义一个任务，该任务就是给服务器发送数据，比如提醒新短信。

在任务界面点击右下角的加号，然后输入名称sms，在详细页面点击加号，选择网络>HTTP POST，最后按照下图配置即可。

配置如下图

![配置task的动作][5]

文字版：

```
https://pushbear.ftqq.com

/sub

sendkey=ooxx
text=新短信来自%SMSRF
desp=姓名:%SMSRN;时间:%SMSRD %SMSRT;内容:%SMSRB;时间戳:%TIMES

application/x-www-form-urlencoded

Android/log.txt
```

然后新建一个配置文件，依次选择 事件>电话>收到短信，确定后返回，选择刚才创建的任务sm，用找回密码功能发送一个验证码试验一下。

### 未接来电

未接来电的配置和短信类似，需要修改的点：

 - 事件>电话>未接来电
 - 将sms复制，重命名为missed_call，修改HTTP POST的内容一栏：


```
sendkey=xxoo
text=未接来电
desp=时间:%CDATE %CTIME;电话号码:%CNUM;联系人:%CNAME;时间戳:%TIMES
```

这样配置后，未接来电和收到新短信都可以发送到微信，是不是很方便。

 > 这里老高遇到一个坑，因为老高的6太子刷的是魔趣系统，默认开启了隐私保护，所以tasker总是获取不到信息，需要在设置里取消对其的隐私设置即可。


  [1]: https://www.pushbullet.com/
  [2]: https://pushover.net/
  [3]: https://instapush.im/
  [4]: https://sc.ftqq.com
  [5]: https://blog.phpgao.com/usr/uploads/2017/12/2929543879.png