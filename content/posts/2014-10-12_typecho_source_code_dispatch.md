---
title: "typecho源代码解析3---路由分发"
categories: [ "代码人生" ]
tags: [ "typecho","source code","dispatch","router" ]
draft: false
slug: "typecho_source_code_dispatch"
date: "2014-10-12 01:24:00"
url: "typecho_source_code_dispatch.html"
---

[上一节][1]

时光匆匆，我们终于来到路由工作的地方。几乎每个MVC框架都不会缺少路由功能，因为他实在太重要了。

路由的功能简单的说就是根据**请求**找到对应**业务**代码，请求来自外部，业务由系统提供，路由器为二者建立了可靠的链接。

typecho和thinkphp都有路由模块，虽然实现方式不同，但用法却惊人的相似！

thinkphp：

    Dispatcher::dispatch();

typecho：

    Typecho_Router::dispatch();

typecho的路由实现是保存在数据库中的路由表，使用正则匹配路径，这一点和Django框架有点像，thinkphp则是基于自己设定的一套规则，[详见官方文档][2]。

typecho的路由表如下数组组成，路由器类会使用子数组中的regx正则式逐个匹配pathinfo中的路径，如果匹配成功，立即初始化并执行该类对应的action。

以index为例，如果使用正则表达式匹配成功，系统就会新建Widget_Archive类并执行其render方法，看他的名字就知道执行的是渲染页面的方法，也就是之前说的**业务**。

    Array
    (
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
    
        [archive] => Array
            (
                [url] => /blog/
                [widget] => Widget_Archive
                [action] => render
                [regx] => |^/blog[/]?$|
                [format] => /blog/
                [params] => Array
                    (
                    )
    
            )
        # ....

有的同学可能会说，typecho的路由表写在数据库里，不够灵活。其实作者也考虑到了，大家可以自由扩展路由表。有兴趣的同学可以去看看`Helper`类中addRoute和removeRoute的用法。举个例子，如果你需要自定义一个URL,比如www.phpgao.com/robots.txt，就可以在路由表加入如下路由：

    [robots] => Array
            (
                [url] => /robots.txt
                [widget] => Robots_Action
                [action] => render
            )

需要注意的是：

 - 新建路由时只需要url widget action 三个参数就够了
 - 如果url中包含参数，需要在url体现，具体实现请看var/Typecho/Router/Parser.php中的parse方法
 - 不需要担心widget方法找不到你的类，因为插件的安装路径已经在config.inc.php文件中加入了包含路径
 - 路由的参数和正则表达式系统自动生成
 - 在初始化阶段，Widget_Options类做了一个优化，具体在`execute`方法的最后，动作是将解析后的路由表保存在键值为0的数组里，以后初始化的时候先看看有没有缓存，这个缓存在路由删除或新建的时候会被删除，可参考`var/Helper.php`中的addRoute和removeRoute。

URL中如果有参数，以page为例，使用规则如下：

    [page] => Array
            (
                [url] => /[slug].html
                [widget] => Widget_Archive
                [action] => render
            )

其中`[]`里的`slug`即参数名称，此处没有限定参数格式，默认为char，如果想要限定格式，就必须参照Typecho_Router_Parser类的_defaultRegx属性，即：

    $this->_defaultRegx = array(
                'string' => '(.%s)',
                'char'   => '([^/]%s)',
                'digital'=> '([0-9]%s)',
                'alpha'  => '([_0-9a-zA-Z-]%s)',
                'alphaslash'  => '([_0-9a-zA-Z-/]%s)',
                'split'  => '((?:[^/]+/)%s[^/]+)',
            );

如果想限定参数为数字，那么`[url] => /[slug].html`需要改为`[url] => /[slug:digital].html`；

如果需要精确限定次数，即4个数字，格式为`[url] => /[slug:digital:4].html`；

如果需要限定次数在某个范围，即1-4个数字，格式为`[url] => /[slug:digital:1:4].html`；

其他格式同理。

如果要某个插件要添加action而不需要绑定到某个URL怎么办？

如默认文章发布提交的地址为

/action/contents-post-edit?_=XXX

现在我想扩充这个地址，想要系统能够处理图片的发布怎么办？

/action/picture-post-edit?_=XXX

答：

我们可以注意到Helper::addAction方法，该方法接收两个参数，action名字和执行action使用的类名。

也就是说我们绑定某个action到插件的action.php文件的action()方法中去，当`Widget_Do`在处理从`Dispatcher::dispatch()`接收到的参数后，`Widget_Do`会在自己的MAP属性(系统默认)和数据库(插件添加)中找到合适的处理此请求的类。

找到这个类后，`Widget_Do`会使用PHP中[ReflectionClass反射技术][3]处理并调用目标类的action方法！

这样就完成了原先系统无法处理的请求，扩展性得到了很大的提高！



系统通过路由找到了真正的业务逻辑，比如首页或文章的展示，feed输出等具体业务，我们下回抓几个典型讲讲。


2015年01月17日更新：

增加action的小节。

[下一节][4]

**码字不易，转载请注明出处。**


  [1]: https://blog.phpgao.com/typecho_source_code_plugin.html
  [2]: http://document.thinkphp.cn/manual_3_2.html#route
  [3]: http://php.net/manual/zh/class.reflectionclass.php
  [4]: https://blog.phpgao.com/typecho_source_code_business_logic.html