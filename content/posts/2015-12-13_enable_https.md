---
title: "终于全站https了，看到小绿锁开心不？"
categories: [ "服务器技术" ]
tags: [ "disqus" ]
draft: false
slug: "enable_https"
date: "2015-12-13 12:40:00"
url: "enable_https.html"
---

![https][1]

今天花了点时间把全站改为https了，其实主要是把多说砍了！


<!--more-->


换成了Disqus，当然加载速度在国内慢一点，不过老高开启了访客模式，大家不用注册也可以留言！

刚才由于操作不当，丢了2天的留言。。。无语了，看来需要一套Wordpress程序做导出导入中转了。。。

目前的问题是：

换了评论系统后界面风格不搭了。

不过过年前后老高会抽时间更新一套新主题的~


ssl检测：

[blog.phpgao.com][2]


参考链接：

[Certificate Installation: NGINX][3]

再补充一句，微软和Google将在年底或近期将取消对 **基于sha1加密的凭证** 的信任，如果不确定你的加密是128还是256位的，可以在[SHAAAAAAAAAAAAA][4]查询。


  [1]: https://blog.phpgao.com/usr/uploads/2015/12/3818874258.png
  [2]: https://www.sslshopper.com/ssl-checker.html#hostname=blog.phpgao.com
  [3]: https://support.comodo.com/index.php?/Default/Knowledgebase/Article/View/789/0/certificate-installation-nginx
  [4]: https://shaaaaaaaaaaaaa.com/