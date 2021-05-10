---
title: "thinkphp钩子的实现"
categories: [ "代码人生" ]
tags: [ "thinkphp","hook" ]
draft: false
slug: "thinkphp_hook"
date: "2014-09-29 02:22:00"
url: "thinkphp_hook.html"
---

钩子概念对初学者来说可能比较抽象难懂，但是只要掌握了他的工作方式，那么自己动手写一个钩子机制也不难。

Hook这个词很有意思，以下引用自某网络词典：

> Hook用作名词时意思是“钩”,转化为动词时可表示把某物弯成钩形,也可表示用弯曲的东西把某物体钩住,引申可表示为“吊”“挂”等。

作为一个程序猿，老高对钩子的解释是，他就是一个触发机制，把你的软件功能想象成一个陷阱，放到##系统流程##可能经过的路上，如果陷阱被系统踩到，就会执行你的程序，当你挂载的钩子执行完后，系统会根据你的程序的结果继续运行。

老高最早接触Hook的编程思想是源于windows，当时打dota很入迷，突然想研究一下改键的原理，于是发现了系统钩子这一说法。

改键的原理，简单地说来就是拦截系统按下键盘时的默认动作，如果需要把小键盘的7映射到Q上，就在拦截时做一个判断，如果的键码是小键盘7，就改为Q的键码，最后发送给系统修改后的键码，即完成了改键操作。

钩子机制的使用在很多系统上都有体现，如windows、wordpress、thinkphp等，由钩子实现的功能在wordpress中叫做插件，在TP中叫做行为。


<!--more-->


> 老高认为，钩子在MVC模式下十分重要，他实现了在不改变源代码的前提下提升系统的灵活性，如，在文章输出前打印版权信息，在文章输出后生成二维码信息，app运行前检查用户权限，还有更多产品经理提出的变态要求，都可以

掌握了钩子的原理后，那么实现起来就很简单了，TP只花了不到100行代码就搞定了，下面我们分析一下：

首先，我们要明确一些说法。在TP中，设置陷阱的过程称为##绑定事件##，而某个事件触发的功能函数称为##行为##。

钩子应该具有的基本方法应该有：

- 设置钩子（导入钩子）
- 触发事件
- 执行行为

首先我们看看TP是怎么写的，源代码位于`ThinkPHP/Library/Think/Hook.class.php`，Hook类中全是静态方法，其中有唯一静态属性`$tags`，他是一个数组，键为绑定的事件，值为绑定的行为。


其中有两个方法可以用于绑定，前者是单个，后者是是批量。

```php
    static public function add($tag,$name) {
        echo $tag;
        echo "\n";
        if(!isset(self::$tags[$tag])){
            self::$tags[$tag]   =   array();
        }
        if(is_array($name)){
            self::$tags[$tag]   =   array_merge(self::$tags[$tag],$name);
        }else{
            self::$tags[$tag][] =   $name;
        }
    }
    
    static public function import($data,$recursive=true) {
        if(!$recursive){ // 覆盖导入
            self::$tags   =   array_merge(self::$tags,$data);
        }else{ // 合并导入
            foreach ($data as $tag=>$val){
                if(!isset(self::$tags[$tag]))
                    self::$tags[$tag]   =   array();            
                if(!empty($val['_overlay'])){
                    // 可以针对某个标签指定覆盖模式
                    unset($val['_overlay']);
                    self::$tags[$tag]   =   $val;
                }else{
                    // 合并模式
                    self::$tags[$tag]   =   array_merge(self::$tags[$tag],$val);
                }
            }            
        }
    }
```

当系统触发了某个事件，比如app_start事件，TP会找到`Hook::listen`方法，该方法会查找$tags中有没有绑定app_start事件的方法，然后用foreach遍历$tags属性，并执行`Hook:exec`方法。

```php
    static public function listen($tag, &$params=NULL) {
        if(isset(self::$tags[$tag])) {
            if(APP_DEBUG) {
                G($tag.'Start');
                trace('[ '.$tag.' ] --START--','','INFO');
            }
            foreach (self::$tags[$tag] as $name) {
                APP_DEBUG && G($name.'_start');
                $result =   self::exec($name, $tag,$params);
                if(APP_DEBUG){
                    G($name.'_end');
                    trace('Run '.$name.' [ RunTime:'.G($name.'_start',$name.'_end',6).'s ]','','INFO');
                }
                if(false === $result) {
                    // 如果返回false 则中断插件执行
                    return ;
                }
            }
            if(APP_DEBUG) { // 记录行为的执行日志
                trace('[ '.$tag.' ] --END-- [ RunTime:'.G($tag.'Start',$tag.'End',6).'s ]','','INFO');
            }
        }
        return;
    }
```

Hook:exec方法会检查行为名称，如果包含Behavior关键字，那么入口方法必须为run方法，而执行run方法的参数在调用`Hook::listen`时指定。但如果不用##Behavior##关键字做配置，即将系统默认的`ReadHtmlCacheBehavior`改为`ReadHtml`，系统会报错吗？答案是会的！

如果去掉**Behavior**，系统就会找该类中绑定事件名称的方法，即`app_begin`。这样的好处是，不会强制使用run方法，一个行为可以复用了。

```php
    static public function exec($name, $tag,&$params=NULL) {
        if('Behavior' == substr($name,-8) ){
            // 行为扩展必须用run入口方法
            $tag    =   'run';
        }
        $addon   = new $name();
        return $addon->$tag($params);
    }
```