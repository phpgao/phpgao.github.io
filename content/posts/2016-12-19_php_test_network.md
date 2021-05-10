---
title: "PHP判断网络连通"
categories: [ "代码人生" ]
tags: [ "PHP","proxy" ]
draft: false
slug: "php_test_network"
date: "2016-12-19 06:38:00"
url: "php_test_network.html"
---

PHP代理其实挺麻烦的。。。


<!--more-->


## 仅测试连通性

```php
$connected = @fsockopen("blog.phpgao.com", 80);
if ($connected){
    $is_conn = true;
    @fclose($connected);
}else{
    $is_conn = false;
}
return $is_conn;
```

## 并测试可用性

```php
//默认三秒超时
@ini_set('default_socket_timeout', 3);
$fp = @fsockopen($proxy_server, $proxy_port, $errno, $errstr, 30);
if (!$fp) {
    BizResult::ensureNotFalse(false, Constants::WEB_ERROR_SRV_UPDATE_PROXY);
} else {
    //$result['reason'] = '已通过测试，代理工作正常。';
    fclose($fp);
}

$url = 'http://blog.phpgao.com/';
$ch = curl_init();

curl_setopt($ch, CURLOPT_URL, $url);
curl_setopt($ch, CURLOPT_POST, 0);
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($ch, CURLOPT_PROXYTYPE, CURLPROXY_HTTP);
curl_setopt($ch, CURLOPT_PROXY, $proxy_server);
curl_setopt($ch, CURLOPT_PROXYPORT, $proxy_port);
curl_setopt($ch, CURLOPT_PROXYUSERPWD, $proxy_user . ':' . $proxy_password);
curl_setopt($ch, CURLOPT_TIMEOUT, 3);

$rtn = curl_exec($ch);
$httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);

// if(curl_errno($ch)){
//     echo curl_error($ch);exit();
// }

if (200 !== $httpCode) {
    BizResult::ensureNotFalse(false, Constants::WEB_ERROR_SRV_UPDATE_PROXY);
}
curl_close($ch);
```