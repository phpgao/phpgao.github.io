---
title: "typecho源代码解析2---插件机制"
categories: [ "代码人生" ]
tags: [ "typecho","source code","plugin" ]
draft: false
slug: "typecho_source_code_plugin"
date: "2014-10-12 01:24:00"
url: "typecho_source_code_plugin.html"
---

[上一节][1]

上次我们讲到了系统完成了初始化，这一篇我们会更加深入typecho，同时会介绍typecho的插件机制。

## 插件点

热心的读者肯定会问，什么是插件点？当然你会不知道，因为这个名词是老高想出来的ಠ౪ಠ，本次要讲的插件机制的基础知识可以参考我的另一片博文[thinkphp钩子的实现][2]。`钩子`这个词比较粗俗，其实高大上的叫法就是插件，而老高自创的插件点就是事件的意思。

index.php中`Typecho_Plugin::factory('index.php')->begin()`，其实就是通知挂载到'index.php'这个事件的插件可以执行了。

具体流程如下：

Typecho_Plugin::factory('index.php')返回了Typecho_Plugin的实例，构造函数中确定了唯一的句柄，即'index.php'，紧接着执行了该实例的`begin()`方法，由于该方法不存在，所以调用了魔术方法`__call`，最后由`__call`方法执行所有在这个插件点挂载的插件。

`__call`方法源代码：

```php
    public function __call($component, $args)
    {
        $component = $this->_handle . ':' . $component;
        $last = count($args);
        $args[$last] = $last > 0 ? $args[0] : false;
    
        if (isset(self::$_plugins['handles'][$component])) {
            $args[$last] = NULL;
            $this->_signal = true;
            foreach (self::$_plugins['handles'][$component] as $callback) {
                $args[$last] = call_user_func_array($callback, $args);
            }
        }
    
        return $args[$last];
    }
```
关键点：

 - __call是可以带参数的
 - `$callback`是二位数组，一个类名，一个方法名
 - 在该类`init`方法中，activated和handles是分开保存的，因为activated以插件名做键名，而handles以插件点为键，目的不同
 - 插件的关联信息保存在数据库中`typecho_options`表中的`plugins`
 - 需要禁用再启用才能使修改后的插件生效

当完成了第一个插件点的挂载后，程序开始路由分发。

[下一节][3]

**码字不易，转载请注明出处。**


  [1]: https://blog.phpgao.com/typecho_source_code_init.html
  [2]: https://blog.phpgao.com/thinkphp_hook.html
  [3]: https://blog.phpgao.com/typecho_source_code_dispatch.html