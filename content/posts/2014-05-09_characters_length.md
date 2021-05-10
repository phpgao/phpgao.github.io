---
title: "各种编码字符长度"
categories: [ "代码人生" ]
tags: [ "utf8","gbk","gb2312" ]
draft: false
slug: "characters_length"
date: "2014-05-09 15:49:00"
url: "characters_length.html"
---

测试源码如下 

```php
header("Content-type: text/html; charset=utf-8");
echo '<br/>';
echo "ascii ", strlen('hello'); //ascii
echo '<br/>';
echo '<br/>';

function strlentest($str)
{
echo '<br/>';
echo $str, ":";
echo '<br/>';
echo "utf8 ", strlen($str); //utf8
echo '<br/>';
echo "gbk ", strlen(iconv('utf-8', 'GBK', $str)); //gbk
echo '<br/>';
echo "gb2312 ", strlen(iconv('utf-8', 'gb2312', $str)); //gb2312
echo '<br/>';
}
$str = "中国人";
strlentest($str);
$str = "A人1";
strlentest($str);
$str = "AAA";
strlentest($str);
$str = "111";
strlentest($str);
```

结论： 自己总结吧