---
title: "typecho源代码解析1---系统初始化"
categories: [ "代码人生" ]
tags: [ "typecho","source code","xdebug" ]
draft: false
slug: "typecho_source_code_init"
date: "2014-10-11 12:20:00"
url: "typecho_source_code_init.html"
---

首先恭喜typecho v1.0的上线！

其次，这篇文章同[thinkphp的源代码解析][1]一样都是老高谋划了很久的文章，国庆节由于单位加班没有时间写，今天终于等来了轮休，果断放开了写。希望大家多多支持！

最后老高想说的是，如果大家有兴趣研究源码，那么问题来了，如何高效的学习研究源代码？

老高的建议是：

 1. 一定要熟悉MVC模式(针对WEB开发)
 2. 先看看文档再动手
 3. 分辨什么是好的坏的代码，不要搞盲目崇拜
 4. 做笔记

以下：

## 文档

如果有什么不明白的，文档里也许会找到答案。

[typecho开发文档][2]

## 版本

再研究源代码前，如果知道自己使用的typecho的版本呢？

答案写在`var/Typecho/Common.php`里，Typecho_Common类中的常量`VERSION`。

例子：

    class Typecho_Common
    {
        /** 程序版本 */
        const VERSION = '1.0/14.10.9';
        ...
    }

## 开启DEBUG模式

有些同学可能已经发现，typecho默认对外隐藏了PHP的错误信息，如果我们想要看到真正的报错信息，需要开启typecho的DEBUG模式，也可以叫做开发模式。当我们开启了这个模式后，在开发插件或者了解系统原理的时候就能够得到可视化的错误信息了。

开启方法：

```php
# 修改/config.inc.php
# 在代码的第一行加入
/**开启debug模式*/
define('__TYPECHO_DEBUG__',1);

```

ps.调试的时候如果你的服务器没有安装[xdebug][3]，那么你的`var_dump()`信息会没有格式。

在此老高推荐使用TP框架内的方法`dump()`，以后我们就可以使用dump()打印变量信息了。

```php
# 修改/config.inc.php
# 在debug后加入

/**
 * 浏览器友好的变量输出
 * @param mixed $var 变量
 * @param boolean $echo 是否输出 默认为True 如果为false 则返回输出字符串
 * @param string $label 标签 默认为空
 * @param boolean $strict 是否严谨 默认为true
 * @return void|string
 */
function dump($var, $echo=true, $label=null, $strict=true) {
    $label = ($label === null) ? '' : rtrim($label) . ' ';
    if (!$strict) {
        if (ini_get('html_errors')) {
            $output = print_r($var, true);
            $output = '<pre>' . $label . htmlspecialchars($output, ENT_QUOTES) . '</pre>';
        } else {
            $output = $label . print_r($var, true);
        }
    } else {
        ob_start();
        var_dump($var);
        $output = ob_get_clean();
        if (!extension_loaded('xdebug')) {
            $output = preg_replace('/\]\=\>\n(\s+)/m', '] => ', $output);
            $output = '<pre>' . $label . htmlspecialchars($output, ENT_QUOTES) . '</pre>';
        }
    }
    if ($echo) {
        echo($output);
        return null;
    }else
        return $output;
}

```

## 入口

入口文件为index.php，国际惯例贴代码。

    if (!@include_once 'config.inc.php') {
        file_exists('./install.php') ? header('Location: install.php') : print('Missing Config File');
        exit;
    }
    
    /** 初始化组件 */
    Typecho_Widget::widget('Widget_Init');
    
    /** 注册一个初始化插件 */
    Typecho_Plugin::factory('index.php')->begin();
    
    /** 开始路由分发 */
    Typecho_Router::dispatch();
    
    /** 注册一个结束插件 */
    Typecho_Plugin::factory('index.php')->end();

typecho也采用了流行的单一入口，在此会引出一个伪静态的问题，此处先留个坑，以后补上。

index.php文件流程：

`config.inc.php`文件如果存在，就引入；如果不存在，就跳转到`install.php`进行程序安装，请原谅老高在此跳过`install.php`流程，如果读者有问题，请留言交流。

`config.inc.php`在安装程序的时候会自动生成，其主要工作是：引入程序常量，设定包含路径，引入各个核心文件（注意此时没有使用`__autoLoad`，所以只能require），执行`Typecho_Common::init()`初始化工作，保存数据库连接信息并初始化数据库对象。

`Typecho_Common::init()`位于`var/Typecho/Common.php`，方法代码：

    public static function init()
        {
            ini_set( 'display_errors', 'On' );
            /** 设置自动载入函数 */
            if (function_exists('spl_autoload_register')) {
                spl_autoload_register(array('Typecho_Common', '__autoLoad'));
            } else {
                function __autoLoad($className) {
                    Typecho_Common::__autoLoad($className);
                }
            }
    
            /** 兼容php6 */
            if (function_exists('get_magic_quotes_gpc') && get_magic_quotes_gpc()) {
                $_GET = self::stripslashesDeep($_GET);
                $_POST = self::stripslashesDeep($_POST);
                $_COOKIE = self::stripslashesDeep($_COOKIE);
    
                reset($_GET);
                reset($_POST);
                reset($_COOKIE);
            }
    
            /** 设置异常截获函数 */
            set_exception_handler(array('Typecho_Common', 'exceptionHandle'));
        }

代码中`ini_set( 'display_errors', 'On' );`是老高自己加的，便于debug，请无视。此方法的主要工作是注册autoload，如果系统打开了magic_quotes_gpc，就使用stripslashesDeep处理输入数据，去除系统自动加入的`/`，最后接管了系统的异常截获。

当`config.inc.php`完成使命后，控制权又回到了`index.php`文件。

紧接着执行了`Typecho_Widget::widget('Widget_Init')`，Typecho_Widget哪里来的？请研究一下init阶段的autoload方法，把类名中的`_`或`\`替换为`/`，最后加上`.php`，这样类名就被映射到了一个确定的文件了。以Typecho_Widget为例，系统将得到`Typecho/Widget.php`，由于系统核心文件都在`var`下，那么最终的路径即`var/Typecho/Widget.php`。所以我们就得到了这样的规律，类名被`_`分开，类一般会以Typecho或Widget开头，分别对应着文件夹Typecho或Widget，之后就根据剩下的片段去找最终的文件名即可！

    @include_once str_replace(array('\\', '_'), '/', $className) . '.php';

Widget类是整个系统的核心类（抽象类），他抽象出了几个很不错的功能，这一点[Widget内部方法与接口][4]有介绍，需要注意的是魔术方法`__get`会在保护属性`$row`里查找变量，`$row`也让Widget类增色不少，`__call`和`push`方法也需要留意。

下面介绍的是`Typecho_Widget::widget`方法，`widget`方法其实是一个工厂方法，他接受一个类名，初始化输入输出对象，一并传入目标类名，执行了目标类的构造方法和`execute`方法，并将实例化的类保存在`self::$_widgetPool`数组中。execute十分重要，在thinkphp中有一个异曲同工的方法，叫`_initalize`，位于controller父类中。

以index.php文件中`Typecho_Widget::widget('Widget_Init')`为例，Typecho_Widget的widget接收到'Widget_Init'，初始化`var/Widget/Init.php`中的Widget_Init类后，执行了其execute方法。该方法又调用了`$this->widget('Widget_Options')`，即自己又初始化了`Widget_Options`类，Widget_Options类读出了`user = 0`的所有系统配置，并将`Widget_Options`保存在局部变量$options里，进行了一系列的初始化工作。值得留意的地方：

    # $options包含了路由信息
    # 系统在这一步才执行了解析PathInfo动作
    $this->request->getPathInfo();
    # 路由器在此初始化
    Typecho_Router::setRoutes
    # 所有插件在此初始化
    Typecho_Plugin::init($options->plugins);
    # 下面还有初始化回执，设定了返回的编码和类型
    $this->response->setCharset($options->charset);
    $this->response->setContentType($options->contentType);
    # 时区设置还有session初始化
    # 最后打开缓冲

至此`Typecho_Widget::widget('Widget_Init')`执行完毕！这时系统初始化正式完毕，开始真正的'工作'！

2015年01月12日更新：

开启开发者模式

[下一节][5]

**码字不易，转载请注明出处。**


  [1]: https://blog.phpgao.com/thinkphp_init.html
  [2]: http://docs.typecho.org/develop
  [3]: https://blog.phpgao.com/phpstorm_using_xdebug.html
  [4]: http://docs.typecho.org/develop/widget#widget%E5%86%85%E9%83%A8%E6%96%B9%E6%B3%95%E4%B8%8E%E6%8E%A5%E5%8F%A3
  [5]: https://blog.phpgao.com/typecho_source_code_plugin.html