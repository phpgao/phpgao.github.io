---
title: "typecho插件编写教程6---调用接口"
categories: [ "typecho插件" ]
tags: [ "typecho","plugin","tutorial" ]
draft: false
slug: "typecho_plugin_tutorial-6"
date: "2015-05-20 12:39:00"
url: "typecho_plugin_tutorial-6.html"
---

此篇我们开始调用接口，我们在插件类中新定义一个方法，起名为`send_post`，在方法中我们通过系统配置获取接口调用地址。


<!--more-->


百度给的例子中使用了php的CURL，更高级的使用方法可以学习 [PHP_cURL初始化和执行方法][1]

下面我们结合一下百度站长提供的代码。

```php
    /**
     * 发送数据
     * @param $url 准备发送的url
     * @param $options 系统配置
     */
    public static function send_post($url, $options){
        //获取API
        $api = $options->plugin('BaiduSubmitTest')->api;

        //准备数据
        if( is_array($url) ){
            $urls = $url;
        }else{
            $urls = array($url);
        }
        
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

        //记录日志
        file_put_contents('/tmp/send_log', date('H:i:s') . $result . "\n");
    }
```

由于我们还没有建立日志系统，所以我们将日志先写入文件，先看效果吧！

返回值：

```json
{"remain":48,"success":1}
```

Good!看来没有什么问题！不过为了保险起见，我还是用typecho自带的http类重写了此方法。

```php
    public static function send_post($url, $options){
        //获取API
        $api = $options->plugin('BaiduSubmitTest')->api;

        //准备数据
        if( is_array($url) ){
            $urls = $url;
        }else{
            $urls = array($url);
        }

        //为了保证成功调用，老高先做了判断
        if (false == Typecho_Http_Client::get()) {
            throw new Typecho_Plugin_Exception(_t('对不起, 您的主机不支持 php-curl 扩展而且没有打开 allow_url_fopen 功能, 无法正常使用此功能'));
        }

        //发送请求
        $http = Typecho_Http_Client::get();
        $http->setData(implode("\n", $urls));
        $http->setHeader('Content-Type','text/plain');
        $result = $http->send($api);

        //记录日志
        file_put_contents('/tmp/send_log', date('H:i:s') . $result . "\n");
    }
}
```

现在我们的插件基本能够运行了，但是在结构上还可以进一步优化！


  [1]: https://blog.phpgao.com/curl_init.html