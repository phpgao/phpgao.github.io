---
title: "typecho插件编写教程5---核心代码"
categories: [ "typecho插件" ]
tags: [ "typecho","plugin","tutorial" ]
draft: false
slug: "typecho_plugin_tutorial-5"
date: "2015-05-20 10:47:00"
url: "typecho_plugin_tutorial-5.html"
---

之前啰嗦了很多，现在开始写核心代码。

分析一下，发布文章的时候，我们需要的信息就是当前文章的URL，我们需要想办法从`$contents`、 `$class`中拿到他。


<!--more-->


目前我们的插件类代码如下（请注意**render**被我改成了**send**）

```php
class BaiduSubmitTest_Plugin implements Typecho_Plugin_Interface
{

    public static function activate(){
        //挂载发布文章和页面的接口
        Typecho_Plugin::factory('Widget_Contents_Post_Edit')->finishPublish = array('BaiduSubmitTest_Plugin', 'send');
        Typecho_Plugin::factory('Widget_Contents_Page_Edit')->finishPublish = array('BaiduSubmitTest_Plugin', 'send');
        return '插件安装成功，请进入设置填写准入密钥';
    }

    public static function deactivate(){
        // do something
        return '插件卸载成功';
    }

    public static function config(Typecho_Widget_Helper_Form $form){
        $element = new Typecho_Widget_Helper_Form_Element_Text('api', null, null, _t('准入秘钥'), '请登录百度站长平台获取');
        $form->addInput($element);
    }

    public static function personalConfig(Typecho_Widget_Helper_Form $form){}

    public static function send($contents, $class){
        //do something
    }
}
```

## 获取URL

获取永久链接需要通过路由规则 + `Typecho_Common::url` 联合生成！

```
class BaiduSubmitTest_Plugin implements Typecho_Plugin_Interface
{

    public static function activate(){
        //挂载发布文章和页面的接口
        Typecho_Plugin::factory('Widget_Contents_Post_Edit')->finishPublish = array('BaiduSubmitTest_Plugin', 'send');
        Typecho_Plugin::factory('Widget_Contents_Page_Edit')->finishPublish = array('BaiduSubmitTest_Plugin', 'send');
        return '插件安装成功，请进入设置填写准入密钥';
    }

    public static function deactivate(){
        // do something
        return '插件卸载成功';
    }

    public static function config(Typecho_Widget_Helper_Form $form){
        //保存接口调用地址
        $element = new Typecho_Widget_Helper_Form_Element_Text('api', null, null, _t('接口调用地址'), '请登录百度站长平台获取');
        $form->addInput($element);
    }

    public static function personalConfig(Typecho_Widget_Helper_Form $form){}

    /**
     * 准备数据
     * @param $contents 文章内容
     * @param $class 调用接口的类
     * @throws Typecho_Plugin_Exception
     */
    public static function send($contents, $class){

        //如果文章属性为隐藏或滞后发布
        if( 'publish' != $contents['visibility'] || $contents['created'] > time()){
            return;
        }

        //获取系统配置
        $options = Helper::options();

        //判断是否配置好API
        if( is_null($options->plugin('BaiduSubmitTest')->api) ){
            return;
        }

        //获取文章类型
        $type = $contents['type'];

        //获取路由信息
        $routeExists = (NULL != Typecho_Router::get($type));

        //生成永久连接
        $path_info = $routeExists ? Typecho_Router::url($type, $contents) : '#';
        $permalink = Typecho_Common::url($path_info, $options->index);
    }
}
```

代码中有注释，老高就不在赘述了。

至此我们已经拿到了文章的永久链接，下一步就是给百度服务器发送数据了！

本节完！