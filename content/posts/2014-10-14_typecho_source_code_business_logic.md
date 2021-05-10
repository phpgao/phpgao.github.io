---
title: "typecho源代码解析4---业务逻辑"
categories: [ "代码人生" ]
tags: [ "typecho","source code" ]
draft: false
slug: "typecho_source_code_business_logic"
date: "2014-10-14 13:54:00"
url: "typecho_source_code_business_logic.html"
---

[上一节][1]

这篇我们就讲讲首页的业务逻辑，首先我们看看路由。

```php
[index] => Array
        (
            [url] => /
            [widget] => Widget_Archive
            [action] => render
            [regx] => |^[/]?$|
            [format] => /
            [params] => Array
                (
                )

        )
```
由路由表中的正则表达式我们能看出首页的URL为`https://blog.phpgao.com/`或`http://www.phpgao.com`，并且不接受任何参数。

下面我们找到了Widget_Archive类，让我们先看看这个类的继承关系（其中`Typecho_Widget`和`Widget_Abstract`为抽象类）：

![inherit.png][2]

由上一节我们可以得到结论，当路由表匹配到`/`，会实例化Widget_Archive类，他位于`var/Widget/Archive.php`，其方法为render。

下面我们一步步跟进，看看到底发生了些什么？

再深入之前，我们需要有一些基本的概念，在此需要讲清楚，便于理解typecho的设计模式：

 - 基类Typecho_Widget

该类位于`var/Typecho/Widget.php`，是`var/Widget`文件夹下所有类的基类，也就是说，几乎所有与业务有关的功能，就是继承此类。

纵观此类，方法不是很多，老高在此挑几个重要的讲解：

```php

    /**
     * 获取对象插件句柄
     * 此方法为插件的实现提供了快捷的语法
     */
    public function pluginHandle($handle = NULL)
    {
        return Typecho_Plugin::factory(empty($handle) ? get_class($this) : $handle);
    }
    
    /**
     * 将类本身赋值
     * 模板中很常见，将自己复制给某一个变量，在调用next()方法循环输出
     */
    public function to(&$variable)
    {
        return $variable = $this;
    }
    
    /**
     * 将每一行的值压入堆栈
     * 把数据放到自己的'肚子'里
     */
    public function push(array $value)
    {
        //将行数据按顺序置位
        $this->row = $value;
        $this->length ++;

        $this->stack[] = $value;
        return $value;
    }
    
    
    /**
     * 返回堆栈每一行的值
     * 有点像从数据库中循环读取结果集
     * 与push相对应，值得大家参考
     */
    public function next()
    {
        if ($this->stack) {
            $this->row = @$this->stack[key($this->stack)];
            next($this->stack);
            $this->sequence ++;
        }

        if (!$this->row) {
            reset($this->stack);
            if ($this->stack) {
                $this->row = $this->stack[key($this->stack)];
            }
            
            $this->sequence = 0;
            return false;
        }

        return $this->row;
    }
    
    # 魔术方法，当调用一个不存在的方法时触发，输出当前$this->name的属性值。
    public function __call($name, $args)
    
    # 魔术方法，当获取不存在的属性时，首先会在$this->row中寻找，如果未找到，则调用___name()方法，并将结果返回，如果还是不存在，就以name为挂载点，触发插件事件，并返回结果。
    
    public function __get($name)
    public function __set($name, $value)

```



**码字不易，转载请注明出处。**


  [1]: https://blog.phpgao.com/typecho_source_code_dispatch.html
  [2]: https://blog.phpgao.com/usr/uploads/2014/10/1453226543.png
  [3]: https://blog.phpgao.com/thinkphp_hook.html