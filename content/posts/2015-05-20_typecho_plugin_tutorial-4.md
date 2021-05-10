---
title: "typecho插件编写教程4---插件点"
categories: [ "typecho插件" ]
tags: [ "typecho","plugin","tutorial" ]
draft: false
slug: "typecho_plugin_tutorial-4"
date: "2015-05-20 05:42:00"
url: "typecho_plugin_tutorial-4.html"
---

终于，在能够保存配置信息后，我们可以开始编写插件的挂载功能了。

首先我们需要知道系统为我们在各个关键的环节都预留了插件点给我们，系统运行到插件点时，会检测到是否有插件挂在这个点上，然后执行插件的逻辑！

插件的工作就是找到合适的插件点，挂上去，然后执行自己的逻辑。


<!--more-->


> 插件点，插件钩子，插件接口。。。在老高这儿是一个概念

[官方的插件接口及功能列表][1]

我们的插件需要执行的逻辑在这里，Widget_Contents_Post_Edit类的finishPublish方法

文件路径`var/Widget/Contents/Post/Edit.php:736`，可以看到，接口为我们传递了两个参数，一个是发布的内容，另一个是类本身。有了类本身，我们可以在插件中随意调用他的公共方法！

```php
    // 文章完成发布插件接口
    $this->pluginHandle()->finishPublish($contents, $this);
```

还有一个页面发布接口，下面的代码会提到。

## 如何挂载

代码如下，有省略。

```php
<?php

class BaiduSubmitTest_Plugin implements Typecho_Plugin_Interface
{

    public static function activate(){
        //挂载发布文章和页面的接口
        Typecho_Plugin::factory('Widget_Contents_Post_Edit')->finishPublish = array('BaiduSubmitTest_Plugin', 'send');
        Typecho_Plugin::factory('Widget_Contents_Page_Edit')->finishPublish = array('BaiduSubmitTest_Plugin', 'send');
        return '插件安装成功，请进入设置填写准入密钥';
    }
...
...
    public static function render($contents, $class){
        //do something
    }
}
```

有代码我们看到，在插件激活时，系统将插件类`BaiduSubmitTest_Plugin`的render方法绑定到finishPublish的接口上！由于finishPublish会传递两个参数，所以render方法也要接受两个参数。

此时，只要插件正常启用，当我们发布完某一篇文章后，系统就会自动调用`BaiduSubmitTest_Plugin:render()`方法了。

> 仔细观察源代码，你会发现此接口没有返回值，如果有返回值怎么办？

这个情况比较复杂，老高先留个坑。

> 为什么叫render方法？

老高从hello world中复制粘贴过来的，一直没有改名。

## 接下来做什么

挂载点搞定了，下一步就是执行我们的核心逻辑代码了。

老高就留到下一篇吧！

  [1]: http://docs.typecho.org/plugins/hooks