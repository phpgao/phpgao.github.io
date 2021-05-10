---
title: "typecho插件编写教程2---新插件"
categories: [ "typecho插件" ]
tags: [ "typecho","plugin","tutorial" ]
draft: false
slug: "typecho_plugin_tutorial-2"
date: "2015-05-20 01:05:00"
url: "typecho_plugin_tutorial-2.html"
---

第一节我们了解了一个插件的基本构成，下面我们需要一个实例练习巩固。

真赶巧，老高最近正在改版[百度sitemap提交插件for typecho][1]，下面和老高一起改版吧！


<!--more-->


## 准备

不知道大家用过WP版的百度结构化插件没？老高就是研究了那个插件，观察其API，然后就写出了typecho版的。

> 为什么要改版呢？

百度站长最近推出新的接口，使用起来更简单，工作量不算大，索性就改改吧！

> 新版插件需要实现哪些功能？

 - 文章实时推送
 - 推送历史数据
 - 站点地图

> 接口调用地址(API)在哪儿？

[百度站长后台][2]，PHP接口实例：

```php
$urls = array(
    'http://www.example.com/1.html',
    'http://www.example.com/2.html',
);
$api = 'http://data.zz.baidu.com/urls?site=www.phpgao.com&token=你的准入密钥';
$ch = curl_init();
$options =  array(
    CURLOPT_URL => $api,
    CURLOPT_POST => true,
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_POSTFIELDS => implode("\n", $urls),
    CURLOPT_HTTPHEADER => array('Content-Type: text/plain'),
);
curl_setopt_array($ch, $options);
$result = curl_exec($ch);
echo $result;
```

## 开始动手

让老高先把上一节HELLO_WORLD插件的代码盗版一下，删除掉所有注释，添加自己的信息。

```
<?php
if (!defined('__TYPECHO_ROOT_DIR__')) exit;
/**
 * 百度结构化插件教程版
 *
 * @package BaiduSubmitTest
 * @author  老高
 * @version 0.4
 * @link https://blog.phpgao.com/typecho_plugin_baidusubmit.html
 */
class BaiduSubmitTest_Plugin implements Typecho_Plugin_Interface
{

    public static function activate(){}

    public static function deactivate(){}

    public static function config(Typecho_Widget_Helper_Form $form){}

    public static function personalConfig(Typecho_Widget_Helper_Form $form){}

    public static function render(){}
}
```

以上代码老高起了个名字叫**裸插件**，意思是啥都干不了的插件，每次写插件的时候老高都会准备一个裸插件。

我们将其放在`usr/plugins/BaiduSubmitTest/Plugin.php`

立即去后台插件处，如图

![新插件][3]

> 为什么是即插即用？

因为我们没有实现插件的方法，所以无法启用。

下一节我们让我们的插件变的丰满起来！

本节完。


  [1]: https://blog.phpgao.com/typecho_plugin_baidusubmit.html
  [2]: http://zhanzhang.baidu.com
  [3]: https://blog.phpgao.com/usr/uploads/2015/05/2482721947.png