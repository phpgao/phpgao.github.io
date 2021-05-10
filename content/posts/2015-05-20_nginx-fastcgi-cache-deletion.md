---
title: "删除nginx-fastcgi-cache缓存"
categories: [ "服务器技术" ]
tags: [ "nginx","cache","grep" ]
draft: false
slug: "nginx-fastcgi-cache-deletion"
date: "2015-05-20 05:05:00"
url: "nginx-fastcgi-cache-deletion.html"
---

老高通过[nginx的优化配置][1]，配置了fastcgi_cache，服务器使用缓存固然很好，但是也会遇到很尴尬的时候。

比如突然发现某个错别字，在后台改好后发现文章之前已被缓存，不知道还要等多久才能刷新，解决办法是删除生成的文件

但想要找到已经可不容易，有两个办法：

- 一个一个找或者直接删除全部缓存
- 根据nginx的URL -- key的规则，再使用两层hash来定位文件

因为老高缓存的文件数不多，所以最后的解决办法是 -- 关键字过滤

```bash
# 找
grep -r 'your keywords' .
# 删
grep -rl 'your keywords' . |xargs rm
```

下面的也可以

```bash
find |xargs grep 'your keywords'
find |xargs grep -l 'your keywords'|xargs rm
```

顺便记个笔记

```bash
# 顺便记一下find限定查找文件和目录
find -type f
find -type d

# grep --help 中-R也很有用！
grep -r --include="*.php"
```


  [1]: https://blog.phpgao.com/nginx_optimization.html#fastcgi_cache