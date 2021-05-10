---
title: "美化Apache的文件列表"
categories: [ "性能优化" ]
tags: [ "apache","list" ]
draft: false
slug: "prettify_index"
date: "2014-02-28 07:45:00"
url: "prettify_index.html"
---

此篇针对apache服务器，其他服务器还在研究中 PC端： 

![PC端美化][1]

手机端： 

![手机端美化][2]

此为<a title="apache文件列表样式美化" href="http://hi.baidu.com/microji/item/87325263da54d793c4d24903" target="_blank">原帖地址</a>，讲得有点简单了，本人总结并修改了一下，发现只要遵循我的步骤，一定能开启！ (系统不限win,linux)

开启美化文件列表步骤： 

1. 打开Apache的autoindex模块 
2. 开启htaccess及includes 
```
<Directory "../htdocs">
    Options Indexes FollowSymLinks MultiViews IncludesNoExec 
    AllowOverride All
    Order allow,deny
    allow from all
```
3. 打开ssi(shtml)功能 去注释 AddType text/html .shtml AddOutputFilter INCLUDES .shtml 
4. 将压缩包解压至web根目录 enjoy

<a title="下载地址" href="http://pan.baidu.com/s/1sj8UvYt" target="_blank">下载地址</a> 提取码：rod5


  [1]: https://blog.phpgao.com/usr/uploads/2014/07/3737112752.png
  [2]: https://blog.phpgao.com/usr/uploads/2014/07/2446703017.png