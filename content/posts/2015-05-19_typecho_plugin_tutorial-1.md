---
title: "typecho插件编写教程1---从HelloWorld说起"
categories: [ "typecho插件" ]
tags: [ "typecho","plugin","tutorial" ]
draft: false
slug: "typecho_plugin_tutorial-1"
date: "2015-05-19 13:41:00"
url: "typecho_plugin_tutorial-1.html"
---

最近老高正在编写一个关于typecho的插件，由于typecho不像wordpress，有那么多的文档参考，写一个插件还是遇到了很多的坑，不过随着研究的不断深入，老高也慢慢上手了，于是总结出此篇编写教程分享给大家！

如果你对typecho的源码有兴趣，可以参考老高的系列文章

[typecho源代码解析1 - 系统初始化][1]
[typecho源代码解析2 - 插件机制][2]
[typecho源代码解析3 - 路由分发][3]
[typecho源代码解析4 - 业务逻辑][4]


<!--more-->


## 从HelloWorld说起

### 基本信息

想必想要开发typecho的你一定阅读过官方示例插件HelloWorld的源码吧？

我们先看看`usr/plugins/HelloWorld/Plugin.php`文件前几行

```php
if (!defined('__TYPECHO_ROOT_DIR__')) exit;
/**
 * Hello World
 * 
 * @package HelloWorld 
 * @author qining
 * @version 1.0.0
 * @link http://typecho.org
 */
 ...
 ...
```

这几行代码是一个插件的基本信息，我们由代码可以得出以下与插件相关的基本信息

插件说明     --->     Hello World 
插件包名     --->     HelloWorld
插件作者     --->     qining
插件版本     --->     1.0.0
插件链接     --->     http://typecho.org

同时这些信息都会显示在插件页中，如下图

![插件基本信息][5]

### 插件结构

我们继续向后面的代码看，一个最简单的插件结构如下（为了缩短篇幅，老高移除了具体方法的实现）

每个方法基本都有注释，老高不再赘述。

看起来很简单吧？其实里面还是有不少坑的。

```php
class HelloWorld_Plugin implements Typecho_Plugin_Interface
{
    /**
     * 激活插件方法,如果激活失败,直接抛出异常
     * 
     * @access public
     * @return void
     * @throws Typecho_Plugin_Exception
     */
    public static function activate(){}
    
    /**
     * 禁用插件方法,如果禁用失败,直接抛出异常
     * 
     * @static
     * @access public
     * @return void
     * @throws Typecho_Plugin_Exception
     */
    public static function deactivate(){}
    
    /**
     * 获取插件配置面板
     * 
     * @access public
     * @param Typecho_Widget_Helper_Form $form 配置面板
     * @return void
     */
    public static function config(Typecho_Widget_Helper_Form $form){}
    
    /**
     * 个人用户的配置面板
     * 
     * @access public
     * @param Typecho_Widget_Helper_Form $form
     * @return void
     */
    public static function personalConfig(Typecho_Widget_Helper_Form $form){}
    
    /**
     * 插件实现方法
     * 
     * @access public
     * @return void
     */
    public static function render(){}
}
```

### 插件流程

插件的基本流程是这样的。

1. 当我们的插件写好后会出现在后台
2. 点击启用按钮后，会执行对应插件类的activate方法
3. 插件与目标插件点关联，等待触发
4. 当点击停用的时候调用deactivate方法

本节完。

下一节老高会更详细的说明插件类的方法。



  [1]: https://blog.phpgao.com/typecho_source_code_init.html
  [2]: https://blog.phpgao.com/typecho_source_code_plugin.html
  [3]: https://blog.phpgao.com/typecho_source_code_dispatch.html
  [4]: https://blog.phpgao.com/typecho_source_code_business_logic.html
  [5]: https://blog.phpgao.com/usr/uploads/2015/05/1130015779.png