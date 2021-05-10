---
title: "PHP验证IP"
categories: [ "代码人生" ]
tags: [ "PHP","ip","regex" ]
draft: false
slug: "php_valid_ip"
date: "2018-01-23 06:19:00"
url: "php_valid_ip.html"
---

正则的效率是在比不上原生的，所以丢个[链接(过滤器函数)][1]走人。

二逼正则:

```php
$valid = preg_match('/^\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}\z/', $string);
```

正确正则:

```php
preg_match('/^(?:25[0-5]|2[0-4]\d|1\d\d|[1-9]\d|\d)(?:[.](?:25[0-5]|2[0-4]\d|1\d\d|[1-9]\d|\d)){3}$/', $ipAddress);
```


[过滤器的选项][2]，比如可以过滤私有IP地址等。

用法参考[Validating an IP address with PHP's filter_var function][3]


  [1]: http://php.net/manual/zh/function.filter-var.php
  [2]: http://php.net/manual/zh/filter.filters.validate.php
  [3]: https://www.electrictoolbox.com/php-validate-ip-address-filter-var/