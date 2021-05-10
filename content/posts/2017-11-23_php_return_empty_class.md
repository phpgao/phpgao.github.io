---
title: "php返回json格式的{}花括号"
categories: [ "代码人生" ]
tags: [ "PHP" ]
draft: false
slug: "php_return_empty_class"
date: "2017-11-23 06:48:00"
url: "php_return_empty_class.html"
---

方括号的[]在json中调试是一个数组，而花括号{}在json中表示一个对象，不过二者在PHP中基本上一个empty就可以判断为i空了，但在js里好像就有一些麻烦了，可能需要下面的判断方式。

```javascript
p = []
if((Array.isArray(p) && p.length === 0)){
    alert('[]');
}

p = {}
if(Object.prototype.isPrototypeOf(p) && Object.keys(p).length === 0){
    alert('{}');
}
```

WAY1:

```php

$a = json_decode('{}');

echo json_encode($a);
```

WAY2:

```php

$a = new stdclass;
// $a = new ArrayObject();
echo json_encode($a);
```


虽然花括号{}和方括号的[]二者在PHP和JSON中都表示不同的东西，但是突然要返回空对象还是冷不丁的想一下，我上哪儿去找一个空对象啊? ?