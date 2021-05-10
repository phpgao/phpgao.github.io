---
title: "误删了phpwind下data-bbscache怎么办？"
categories: [ "代码人生" ]
tags: [ "bbscache" ]
draft: false
slug: "phpwin_bbscache"
date: "2014-06-22 05:57:00"
url: "phpwin_bbscache.html"
---

今天手残，把上述文件夹给删了，而缺没有备份，网页直接打不开了 网上的回答五花八门，有登陆后台重建缓存的，有分析install.php重建文件夹的。。。各种无语啊 还是自己想的办法好：

自己在本地重建建立一个一模一样的phpwind，然后把生成的bbscache文件夹覆盖回去即可！ 

难点在于如何搭建一模一样的phpwind，主要是数据库的用户名密码和域名，还有后台管理的用户名密码，讲一下步骤：
 
1.  先在host里绑定域名www.abc.com到127.0.0.2
2.  在Apache或其他服务器新建虚拟主机127.0.0.2，别名设为www.abc.com
3.  把俺炸U那个文件拷贝进去，安装吧~