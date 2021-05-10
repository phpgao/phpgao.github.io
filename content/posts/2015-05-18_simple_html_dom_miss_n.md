---
title: "simple_html_dom换行符丢失的问题"
categories: [ "代码人生" ]
tags: [ "PHP","simple_html_dom" ]
draft: false
slug: "simple_html_dom_miss_n"
date: "2015-05-18 12:10:37"
url: "simple_html_dom_miss_n.html"
---

今天老高在更新**[Table Of Contents for typecho][1]**的时候，遇到了一个问题。

因为解析文档的时候，换行符`\n`被无故删除掉了，导致`pre`中code的解析一片混乱，阅读源代码后发现了这个选项stripRN，默认值是`true`，即默认删除变量中的换行符，将其关闭即可！

核心代码位于约1147行左右。

```php
if ($stripRN) {
    $str = str_replace("\r", " ", $str);
    $str = str_replace("\n", " ", $str);
...
```

  [1]: https://blog.phpgao.com/table_of_contents_for_typecho.html