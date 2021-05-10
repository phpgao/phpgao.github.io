---
title: "thinkphp框架解析0----起源"
categories: [ "代码人生" ]
tags: [ "thinkphp","框架","framework" ]
draft: false
slug: "thinkphp_init"
date: "2014-09-25 14:13:00"
url: "thinkphp_init.html"
---

THINKPHP是国人开发的PHP框架，如今终于深入内部打探了一番，下面给大家分享老高的心得。


<!--more-->


@THINKPHP_VERSION = 3.2.2 CORE

# 入口

THINKPHP采用了现在普遍使用的单一入口。即程序的所有入口只有一个，即根目录下的index.php文件，所有请求都会先到这里。

好处当然是统一管理，集中操作，使程序的结构更加稳固，更加容易实现MVC，但是由于需要dispatcher去解析(路由)，在一定程度上会增加程序的复杂性。

先来看看index.php的源代码

    // 应用入口文件
    // 检测PHP环境
    if(version_compare(PHP_VERSION,'5.3.0','<'))  die('require PHP > 5.3.0 !');
    // 开启调试模式 建议开发阶段开启 部署阶段注释或者设为false
    define('APP_DEBUG',True);
    // 定义应用目录
    define('APP_PATH','./Application/');
    // 引入ThinkPHP入口文件
    require './ThinkPHP/ThinkPHP.php';

此文件一共做了四件事情：

1. 确保运行在PHP5.3环境下，因为3.2+使用了命名空间。
2. 检测DEBUG模式是否开启。
3. 定义了程序主目录Application，以后的解说都基于Application。
4. 引入框架的入口。

还有一些参数可以在此定义，比如入口绑定等(以后会讲)。

# 第一次正式接触

瞬间，我们的WEBAPP读取到了`/ThinkPHP/ThinkPHP.php`这个文件，不要小看这不到100行的代码，这里的常量几乎贯穿了以后所有的系统流程里，需要注意的是，常量一旦定义就无法修改，所以这里不会加载配置文件，更多的是和系统的运行环境打交道。

这里还没有载入类似`/ThinkPHP/Common/functions.php`的文件，所以在这里我们还不能用自定义的函数和类。在系统常量定义的时候，我们还能看到一种`or`的特殊用法:

    defined('APP_DEBUG') or define('APP_DEBUG',false); // 是否调试模式

其实给这个表达式带个括号就更容易读懂了。即，左边的表达式如果为假，那么执行右边的表达式。上面这句话的意思是，如果没有定义APP_DEBUG这个常量，那么把它设置为false。逻辑简洁明了，希望大家能够把这个`表达式`用在以后的开发中。

这个文件需要注意几个常量：

    MEMORY_LIMIT_ON   //是否支持内存开销查询，为以后的DEBUG做准备
    CORE_PATH  //核心目录
    VENDOR_PATH  //第三方类库目录
    IS_CGI IS_WIN IS_CLI  是否运行在CGI模式下，是否win系统，是否命令行运行
    _PHP_FILE_  //当前文件名
    EXT  //类文件后缀
    CONF_EXT  //配置文件后缀

`think.php`的最后两行引入了核心类文件`thinkphp.class.php`，系统开始加载核心类，并调用其静态方法::start。

    require CORE_PATH.'Think'.EXT;
    Think\Think::start();

# 核心类

终于可以勇敢的面向对象了，赶紧注册一个spl_autoload_register方法。

    spl_autoload_register('Think\Think::autoload');

以后当实例化某个类的时候，如果该类不存在，系统不会立即报错，而是抱着试一试的想法去执行`Think\Think::autoload`方法，如果找到了，就在加载一次，否则报错。所以这个方法再框架中举足轻重！怪不得第一句就加载。下面贴出此方法，做一些解析。

    /**
     * 类库自动加载
     * @param string $class 对象类名
     * @return void
     */
    public static function autoload($class) {
        // 检查是否存在映射
        if(isset(self::$_map[$class])) {
            include self::$_map[$class];
        }elseif(false !== strpos($class,'\\')){
          $name           =   strstr($class, '\\', true);
          if(in_array($name,array('Think','Org','Behavior','Com','Vendor')) || is_dir(LIB_PATH.$name)){ 
              // Library目录下面的命名空间自动定位
              $path       =   LIB_PATH;
          }else{
              // 检测自定义命名空间 否则就以模块为命名空间
              $namespace  =   C('AUTOLOAD_NAMESPACE');
              $path       =   isset($namespace[$name])? dirname($namespace[$name]).'/' : APP_PATH;
          }
          $filename       =   $path . str_replace('\\', '/', $class) . EXT;
          if(is_file($filename)) {
              // Win环境下面严格区分大小写
              if (IS_WIN && false === strpos(str_replace('/', '\\', realpath($filename)), $class . EXT)){
                  return ;
              }
              include $filename;
          }
        }elseif (!C('APP_USE_NAMESPACE')) {
            // 自动加载的类库层
            foreach(explode(',',C('APP_AUTOLOAD_LAYER')) as $layer){
                if(substr($class,-strlen($layer))==$layer){
                    if(require_cache(MODULE_PATH.$layer.'/'.$class.EXT)) {
                        return ;
                    }
                }            
            }
            // 根据自动加载路径设置进行尝试搜索
            foreach (explode(',',C('APP_AUTOLOAD_PATH')) as $path){
                if(import($path.'.'.$class))
                    // 如果加载类成功则返回
                    return ;
            }
        }
    }

核心类中存在一个静态属性$_map，以类名为键，类的路径为值，保存了类映射，以后二次加载就不需要再次分析，提升了性能。

因为引入了命名空间，所以参数中会带`\\`表示命名空间，所以用if做了判断，strstr的第三个参数如果为TRUE，那么就返回NEEDLE之前的字符串。

在注册了类自动加载方法后，TP又接管了系统的错误处理句柄，所以TP里的报错不只是简单的一句话，而是可以更好的输出错误信息。

`Storage::connect(STORAGE_TYPE)`设置了全局的文件读写方式，[在线手册-sae][1]一节对SAE有较详细的解释。Storage驱动文件位于ThinkPHP/Library/Think/Storage/Driver/下，可以自行扩展。

`$runtimefile`就是名为`~runtime.php`，它保存了系统的核心设置，他经常出现在项目的缓存文件夹下，真实路径为`RUNTIME_PATH.APP_MODE`，可见其路径由APP_MODE常量决定，接下来的代码解释了什么情况下会使用缓存：

    if(!APP_DEBUG && Storage::has($runtimefile)){
              Storage::load($runtimefile);
    }else{

即非APP_DEBUG模式下会启用缓存，所以修改配置后若不生效，可能是因为开启了APP_DEBUG。下面TP开始读取各种配置文件。

          $mode   =   include is_file(CONF_PATH.'core.php')?CONF_PATH.'core.php':MODE_PATH.APP_MODE.'.php';

一般情况下，这行代码会去找`ThinkPHP/Mode/common.php`，他包含了系统的定义核心设置的文件路径，如配置文件、别名定义、函数和类文件还有行为扩展，这些文件散布在框架的各个地方。

    foreach ($mode['core'] as $file){
        if(is_file($file)) {
            include $file;
            if(!APP_DEBUG) $content   .= compile($file);
        }
    }

导入文件之后TP首先用foreach循环遍历`$mode['core']`中的函数和类文件，并导入`$content`中，最后写入缓存文件。需要注意的是，`ThinkPHP/Common/functions.php`在此会被加载。

    foreach ($mode['config'] as $key=>$file){
        is_numeric($key)?C(load_config($file)):C($key,load_config($file));
    }

之后循环加载`$mode['config']`里的文件，在这里`ThinkPHP/Conf/convention.php`（TP的惯例配置文件）、`CONF_PATH.'config'.CONF_EXT`（项目公共配置）会被加载。

    if('common' != APP_MODE && is_file(CONF_PATH.'config_'.APP_MODE.CONF_EXT))
      C(load_config(CONF_PATH.'config_'.APP_MODE.CONF_EXT));  
    
    // 加载模式别名定义
    if(isset($mode['alias'])){
      self::addMap(is_array($mode['alias'])?$mode['alias']:include $mode['alias']);
    }
    
    // 加载应用别名定义文件
    if(is_file(CONF_PATH.'alias.php'))
      self::addMap(include CONF_PATH.'alias.php');
    
    // 加载模式行为定义
    if(isset($mode['tags'])) {
      Hook::import(is_array($mode['tags'])?$mode['tags']:include $mode['tags']);
    }

读取当前应用模式对应的配置文件，如果项目的应用模式为sae，那么应用的配置文件应该为`Application/Common/Conf/config_sae.php`。

加载应用别名，需要注意的是，3.2.2引入了命名空间，所以需要在之前的键名前加上命名空间。

    // 加载模式行为定义
    if(isset($mode['tags'])) {
      Hook::import(is_array($mode['tags'])?$mode['tags']:include $mode['tags']);
    }
    
    // 加载应用行为定义
    if(is_file(CONF_PATH.'tags.php'))
      // 允许应用增加开发模式配置定义
      Hook::import(include CONF_PATH.'tags.php');   

加载模式行为定义，或者叫钩子，都是在这里引入的。系统自定义的行为保存在`$mode['tags']`中，用户自定义的行为应该保存在`Application/Common/tags.php`。`tags.php`文件写法有坑，后面会讲到。

下面给出系统为大家预留的插件位：

    app_init    应用初始化标签位
    path_info   PATH_INFO检测标签位
    app_begin   应用开始标签位
    action_name 操作方法名标签位
    action_begin    控制器开始标签位
    view_begin  视图输出开始标签位
    view_parse  视图解析标签位
    template_filter 模板内容解析标签位
    view_filter 视图输出过滤标签位
    view_end    视图输出结束标签位
    action_end  控制器结束标签位
    app_end         应用结束标签位

    L(include THINK_PATH.'Lang/'.strtolower(C('DEFAULT_LANG')).'.php');

接着TP载入了底层语言包，这才对嘛。如果考虑到程序不仅仅面向中文使用者，那么一定要注意国际化的问题。`DEFAULT_LANG`的默认值记录在`ThinkPHP/Conf/convention.php`中。

以上提到这些信息组成了TP运行的基本运行环境，在非DEBUG模式下，TP会把以上信息带上命名空间都写入缓存文件里以提高性能。

而如果程序运行在DEBUG模式下，则会多读取一个文件，当然是DEBUG的配置文件了，他位于`ThinkPHP/Conf/debug.php`，如果想在自己的项目中修改某些DEBUG配置，使用者可以在`Application/Common/`下面建立相同格式的配置，注意：项目的配置优先级更高。

    if(APP_STATUS && is_file(CONF_PATH.APP_STATUS.CONF_EXT))
        C(include CONF_PATH.APP_STATUS.CONF_EXT);

下面读取不同应用状态下对应的不同配置，`APP_STATUS`默认为''，如果需要配置，请在`Application/Common/`下面建立相同对应名称的配置文件`APP_STATUS.php`即可。

    date_default_timezone_set(C('DEFAULT_TIMEZONE'));

时区对程序的影响很大，也需要配置。TP默认的时区可以在惯例配置中找到，记得好像是'PRC'，一般不会变动。再次提一下，服务器的系统时间最好设置一个crontab定时同步，否则会带来很多意想不到的问题。

    if(C('CHECK_APP_DIR')) {
        $module     =   defined('BIND_MODULE') ? BIND_MODULE : C('DEFAULT_MODULE');
        if(!is_dir(APP_PATH.$module) || !is_dir(LOG_PATH)){
            // 检测应用目录结构
            Build::checkDir($module);
        }
    }

下面终于进入模块文件夹了，如果TP发现对应模块里还是空空一片，就会自动创建目录结构和默认控制器文件。这里可以用到`BIND_MODULE`来绑定默认模块。

    G('loadTime');

倒数第二步：G函数记录加载文件的时间。

最后一步：调用`App::run()`方法，进入APP初始化。



  [1]: http://document.thinkphp.cn/manual_3_2.html#sae