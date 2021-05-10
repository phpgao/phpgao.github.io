---
title: "记一次主题更新"
categories: [ "生活" ]
tags: [ "wordpress","theme","update" ]
draft: false
slug: "theme_update"
date: "2014-05-13 01:40:00"
url: "theme_update.html"
---

国外的主题对中文支持不好很不和谐，需要修改的地方很多，用起来也很麻烦，但是古语有云： 
> Bitch is bitchy!Taylor is Swift! 

下面就记录一下更新主题前后的工作。动手能力强的可以参考[这里][1]自己写一个子主题，也可以完美解决！ 

*   预先备份原来主题文件夹，起个名字叫old_theme。留个后悔药      备份路径wp-content/themes/<span style="color: #ff0000;">sparkling</span> ，sparkling就是老高的主题
*   升级主题，原主题被覆盖
*   老高修改过的文件包括(old_theme文件夹里按时间排序就能找到) 
    1.  header.php   \---|\---|seo优化
    2.  functions.php \---|--常用函数
    3.  single.php      \---|\---| 解决手机底部链接打不开的样式问题
    4.  content.php   \---|\---|-主页显示文章字数限制，<a title="WordPress首页文章不显示全文的方法" href="https://blog.phpgao.com/wordpress_excerpt.html" target="_blank">参考这篇文章</a>
    5.  <del>footer.php     --------统计代码</del>已解决
*   在新的主题文件夹中找到对应文件并修改 子主题以后讲

 [1]: http://codex.wordpress.org/zh-cn:%E5%AD%90%E4%B8%BB%E9%A2%98