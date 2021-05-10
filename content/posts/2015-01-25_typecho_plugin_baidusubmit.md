---
title: "百度sitemap提交插件-for-Typecho"
categories: [ "typecho插件" ]
tags: [ "typecho","plugin" ]
draft: false
slug: "typecho_plugin_baidusubmit"
date: "2015-01-25 03:31:00"
url: "typecho_plugin_baidusubmit.html"
---

几经开发，这款工具终于可以进入测试阶段。

其主要内容是向百度提交sitemap，参考了站长工具里wp的插件制作。

老高目测对SEO还是有一些用处的。

目前没有做分页sitemap，如果大家有这个需求，老高会考虑加上。


<!--more-->


<del>**过一阵子老高会出一篇typecho的插件简单教程，欢迎关注！**</del>

教程已出，[传送门][1]

## 功能


### 提交百度口味的sitemap

提交sitemap给百度服务器，优化收录。

新的百度xml地图为地址为 `http://yourweb/baidu_sitemap.xml` ---->  对应功能  百度站长--链接提交--自动提交--sitemap


### 实时推送

插件会在添加、修改文章时向百度接口POST信息，做这些操作的时候可能会比以前慢一点，考虑到网络情况的不同，默认发送超时3000ms。

### 主动推送新链接

如果你有采集来的文章或者之前没有推送过的文章，老高增加了**后台主动推送文章**功能，目前百度每天有**50条(不是次数，是条数)URL限额**！所以老高加上了分组功能，文章可以按组发送，默认一次发送15条，支持自定义条数，免得消耗过多额度，建议推送后检查日志`更多信息`里面的URL看是否包含你想要推送的文章，组的排序**从新到旧**，最老的文章必然在最后一组。

功能截图：

![主动推送功能截图][2]


### 日志

控制台->百度结构化日志

## FAQ

- **百度站长后台？找不到地方？**
- 答：

![百度站长后台][3]

- **提交地址在哪儿？找不到地方？**
- 答：

![API就静静的躺在那儿][4]

## 效果

效果还用说吗？

![插件提升了收录][5]

## 安装

将文件夹重命名为`BaiduSubmit`，然后拷贝至`usr/plugins/`下，最后在后台->插件处安装。

## 升级方法

**请先禁用插件后再升级**

## 使用

全新的日志记录功能！

![日志记录][6]

## 下载

[phpgao/BaiduSubmit][7]

## 感谢

感谢 @typecho [@disonlee][8] @培轩 [@Mike][9] [@xx][10] 反馈BUG！


  [1]: https://blog.phpgao.com/typecho_plugin_release_page.html
  [2]: https://blog.phpgao.com/usr/uploads/2015/05/496021076.jpeg
  [3]: https://blog.phpgao.com/usr/uploads/2015/05/528961753.jpeg
  [4]: https://blog.phpgao.com/usr/uploads/2015/05/989945993.jpeg
  [5]: https://blog.phpgao.com/usr/uploads/2015/05/1039791935.png
  [6]: https://blog.phpgao.com/usr/uploads/2015/05/879628597.png
  [7]: https://github.com/phpgao/BaiduSubmit
  [8]: http://weibo.com/nocheers
  [9]: http://weibo.com/74880511
  [10]: http://www.zeroblogs.net