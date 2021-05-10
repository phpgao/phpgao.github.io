---
title: "抖音的学习研究"
categories: [ "代码人生" ]
tags: [ "spider" ]
draft: false
slug: "douyin_spider"
date: "2019-06-25 07:18:00"
url: "douyin_spider.html"
---

老高由于一些目的，需要研究学习一下抖音视频。记录一下过程，以作备忘。

> 重要声明，本教程仅供参考，请勿直接通过本教程的代码或思路爬取抖音视频，否则后果自负！


<!--more-->


## 分析

抖音不像其他视频app，评论等数据都没有直接开放在H5端，所以就需要各种办法把抖音APP中的数据偷出来，然后与H5端能爬到的数据结合起来，数据就差不多了。


### H5端

H5端主要有2个比较有用的地址

#### 视频列表页

 - https://www.iesdouyin.com/share/user/96454853869
 - https://www.iesdouyin.com/share/user/111519505621

只要知道了抖音的用户id，就可以通过访问`https://www.iesdouyin.com/share/user/%id`得到用户的一些信息，以及所有的视频列表。

视频列表的接口：

```
https://www.iesdouyin.com/web/api/v2/aweme/post/?user_id=96454853869&count=21&max_cursor=0&aid=1128&_signature=ycRB9hAYlOA8ozERGA-RrMnEQO&dytk=461d02904aff073ed0f5c745f20345c5


user_id: 96454853869
count: 21
max_cursor: 0
aid: 1128
_signature: ycRB9hAYlOA8ozERGA-RrMnEQO
dytk: 461d02904aff073ed0f5c745f20345c5
```

max_cursor为游标，是上一次访问的接口返回给我们的值
aid=1128不变，但是我们可以从js中获取
_signature需要破解
user_id，dytk可以从源码中用正则提取


返回数据格式：

```
{
    "status_code":0,
    "aweme_list":[
        {
            "aweme_id":"6697580929038191883",
            "desc":"六一这天下雨了，给娃做了仙豆糕，一家人坐在家里吃着面条，总感觉幸福满满……",
            "cha_list":null,
            "video":{
                "play_addr":{
                    "uri":"v0300f8a0000bjpp0dd57hrqvjfjke1g",
                    "url_list":[
                        "https://aweme.snssdk.com/aweme/v1/play/?video_id=v0300f8a0000bjpp0dd57hrqvjfjke1g&line=0&ratio=540p&media_type=4&vr_type=0&improve_bitrate=0",
                        "https://api.amemv.com/aweme/v1/play/?video_id=v0300f8a0000bjpp0dd57hrqvjfjke1g&line=1&ratio=540p&media_type=4&vr_type=0&improve_bitrate=0"
                    ]
                },
                "cover":{
                    "uri":"17abf00187505e16fe845",
                    "url_list":[
                        "https://p9-dy.byteimg.com/aweme/300x400/17abf00187505e16fe845.jpeg",
                        "https://p1-dy.byteimg.com/aweme/300x400/17abf00187505e16fe845.jpeg",
                        "https://p3-dy.byteimg.com/aweme/300x400/17abf00187505e16fe845.jpeg"
                    ]
                },
                "height":960,
                "width":540,
                "dynamic_cover":{
                    "uri":"26bad000a23ba6f5a67d1",
                    "url_list":[
                        "https://p9-dy.byteimg.com/obj/26bad000a23ba6f5a67d1",
                        "https://p1-dy.byteimg.com/obj/26bad000a23ba6f5a67d1",
                        "https://p3-dy.byteimg.com/obj/26bad000a23ba6f5a67d1"
                    ]
                },
                "origin_cover":{
                    "uri":"large/26bb70005da2e69075bef",
                    "url_list":[
                        "http://p9-dy.byteimg.com/large/26bb70005da2e69075bef.jpeg",
                        "http://p1-dy.byteimg.com/large/26bb70005da2e69075bef.jpeg",
                        "http://p3-dy.byteimg.com/large/26bb70005da2e69075bef.jpeg"
                    ]
                },
                "ratio":"540p",
                "download_addr":{
                    "uri":"v0300f8a0000bjpp0dd57hrqvjfjke1g",
                    "url_list":[
                        "https://aweme.snssdk.com/aweme/v1/play/?video_id=v0300f8a0000bjpp0dd57hrqvjfjke1g&line=0&ratio=540p&watermark=1&media_type=4&vr_type=0&improve_bitrate=0&logo_name=aweme",
                        "https://api.amemv.com/aweme/v1/play/?video_id=v0300f8a0000bjpp0dd57hrqvjfjke1g&line=1&ratio=540p&watermark=1&media_type=4&vr_type=0&improve_bitrate=0&logo_name=aweme"
                    ]
                },
                "has_watermark":true,
                "bit_rate":null,
                "duration":32033
            },
            "statistics":{
                "aweme_id":"6697580929038191883",
                "comment_count":2014,
                "digg_count":112000,
                "play_count":0,
                "share_count":424,
                "forward_count":15
            },
            "text_extra":[

            ],
            "video_labels":null,
            "aweme_type":4,
            "image_infos":null,
            "position":null,
            "uniqid_position":null,
            "comment_list":null,
            "geofencing":null,
            "video_text":null,
            "label_top_text":null,
            "promotions":null,
            "long_video":null
        }
    ],
    "max_cursor":1557455743000,
    "min_cursor":1559466150000,
    "has_more":true
}
```


此处我们需要写两个爬虫，一种是通过接口，一种通过模拟浏览器(备用方案)。


##### 反爬点

字体反爬

#### 视频详情页

 - https://www.iesdouyin.com/share/video/6706027802904366339/?region=CN&mid=6681424173102795533&u_code=jheb4e7h&titleType=title
 - https://www.iesdouyin.com/share/video/6706027802904366339/?region=CN&mid=6681424173102795533

知道了视频的唯一id(aweme_id)后，还需要获取一个mid才能到达此页面，目前还在研究。


待更新。。。


  [1]: http://fan45.top/