---
title: "使用debug_backtrace()做PHP调试"
categories: [ "代码人生" ]
tags: [ "PHP","debug" ]
draft: false
slug: "debug_with_backtrace"
date: "2014-03-11 15:09:00"
url: "debug_with_backtrace.html"
---

## 问题？ 今天迁站的时候PHP突然报错： 

> is\_readable() [function.is-readable]: open\_basedir restriction in effect. File(D:\phpnow\vhosts\wordpress.com/wp-content/plugins/D:\phpnow\vhosts\wordpress.com\wp-content\plugins\crayon-syntax-highlighter/trans/crayon-syntax-highlighter-zh_CN.mo) is not within the allowed path(s): (D:\phpnow\vhosts\wordpress.com;C:\Windows\Temp;) in 
**D:\phpnow\vhosts\wordpress.com\wp-includes\l10n.php** on line **339** 好吧，看来是crayon-syntax-highlighter插件出错了，下面我们就一起找找出错的地方吧。 

## 如何解决

### 确定出错地点 根据出错提示我们找到了

**D:\phpnow\vhosts\wordpress.com\wp-includes\l10n.php**的第339行，代码如下： 

    if ( !is_readable( $mofile ) ) return false;

这个$mofile哪里来的呢?把整个函数贴出来找找看！ 

    function load_textdomain( $domain, $mofile ) {
    	global $l10n;
    
    	$plugin_override = apply_filters( 'override_load_textdomain', false, $domain, $mofile );
    
    	if ( true == $plugin_override ) {
    		return true;
    	}
    
    	do_action( 'load_textdomain', $domain, $mofile );
    
    	$mofile = apply_filters( 'load_textdomain_mofile', $mofile, $domain );
    
    	if ( !is_readable( $mofile ) ) return false;
    
    	$mo = new MO();
    	if ( !$mo->import_from_file( $mofile ) ) return false;
    
    	if ( isset( $l10n[$domain] ) )
    		$mo->merge_with( $l10n[$domain] );
    
    	$l10n[$domain] = &$mo;
    
    	return true;
    }

看来是函数传进来的一个字符串，看来我们要继续寻找调用这个函数的地方了。不过wordpress的php文件少说也有1000多个，这样找下去是个什么效率？ 

### 栈 

等等！我好像想起来在学习数据结构的时候有一个概念叫栈，stack，什么是栈呢？其实站只是一个特殊的数据结构，它只允许在一串数据的一段进行操作，比如增加数据元素(入栈),删除数据元素(出栈)。举个栗子，堆栈就好比向水杯中放乒乓球，水杯的横截面只能通过一个乒乓球，如果想要把最先放进去的球取出来，必须把他上面的乒乓球按顺序一个一个取出来才行。 为什么突然想到栈这个概念呢？因为我们的函数调用就是使用的栈这个概念。简单地说，一个函数调用另一个函数，那么在调用期间需要保存现场，将自己的数据（比如调用指针、参数等）压入一个栈中，当调用完毕后再出栈恢复数据，然后继续调用。这个过程说起来简单，其实很复杂。以下摘自wikipedia: 

> 在计算机科学领域中，'***调用栈'***（*Call Stack*）是用于存储子程序信息的一类栈，别称执行栈（*execution stack*）、控制栈（*control stack*）、运行时栈（*run-time stack*）与机器栈（*machine stack*），在英语中亦经常简称为“栈”（“*the stack*”）。调用栈的维护对多数软件的正常运转有着重要意义，但一般高级语言都会隐藏其细节并自动进行维护。

既然细节被隐藏了，那么就不用深究了。下面就引出今天的主角函数。 

### 主角 

debug\_backtrace()和debug\_print\_backtrace()两兄弟。

主要功能：他们保存了函数的调用栈信息，一个不输出以数组形式保存，另一个直接输出调用栈，调试的时候一般使用debug\_print\_backtrace()。 知道了这两个函数，将函数加入要追溯的函数内的第一行，不要忘了加;。 debug\_backtrace()使用print_r显示如下： 

    Array
    (
        [0] => Array
            (
                [file] => D:\phpnow\vhosts\wordpress.com\wp-includes\l10n.php
                [line] => 428
                [function] => load_textdomain
                [args] => Array
                    (
                        [0] => crayon-syntax-highlighter
                        [1] => D:\phpnow\vhosts\wordpress.com/wp-content/plugins/D:\phpnow\vhosts\wordpress.com\wp-content\plugins\crayon-syntax-highlighter/trans/crayon-syntax-highlighter-zh_CN.mo
                    )
    
            )


定睛一看，原来是个多维数组。file是调用文件，line是函数出现的行数，function是函数名，args是参数。 直接使用debug\_print\_backtrace()看看:

    #0  load_textdomain(crayon-syntax-highlighter, D:\phpnow\vhosts\wordpress.com/wp-content/plugins/D:\phpnow\vhosts\wordpress.com\wp-content\plugins\crayon-syntax-highlighter/trans/crayon-syntax-highlighter-zh_CN.mo) called at [D:\phpnow\vhosts\wordpress.com\wp-includes\l10n.php:428]
    #1  load_plugin_textdomain(crayon-syntax-highlighter, , D:\phpnow\vhosts\wordpress.com\wp-content\plugins\crayon-syntax-highlighter/trans/) called at [D:\phpnow\vhosts\wordpress.com\wp-admin\includes\plugin.php:121]
    #2  _get_plugin_data_markup_translate(D:\phpnow\vhosts\wordpress.com\wp-content\plugins\crayon-syntax-highlighter\crayon_wp.class.php, Array ([Name] => Crayon Syntax Highlighter,[PluginURI] => https://github.com/aramkocharyan/crayon-syntax-highlighter,[Version] => 2.4.1,[Description] => Supports multiple languages, themes, highlighting from a URL, local file or post text.,[Author] => Aram Kocharyan,[AuthorURI] => http://aramk.com/,[TextDomain] => crayon-syntax-highlighter,[DomainPath] => /trans/,[Network] => ), 1, 1) called at [D:\phpnow\vhosts\wordpress.com\wp-admin\includes\plugin.php:99]
    #3  get_plugin_data(D:\phpnow\vhosts\wordpress.com\wp-content\plugins\crayon-syntax-highlighter\crayon_wp.class.php) called at [D:\phpnow\vhosts\wordpress.com\wp-content\plugins\crayon-syntax-highlighter\crayon_wp.class.php:39]
    #4  include_once(D:\phpnow\vhosts\wordpress.com\wp-content\plugins\crayon-syntax-highlighter\crayon_wp.class.php) called at [D:\phpnow\vhosts\wordpress.com\wp-settings.php:197]
    #5  require_once(D:\phpnow\vhosts\wordpress.com\wp-settings.php) called at [D:\phpnow\vhosts\wordpress.com\wp-config.php:90]
    #6  require_once(D:\phpnow\vhosts\wordpress.com\wp-config.php) called at [D:\phpnow\vhosts\wordpress.com\wp-load.php:29]
    #7  require_once(D:\phpnow\vhosts\wordpress.com\wp-load.php) called at [D:\phpnow\vhosts\wordpress.com\wp-blog-header.php:12]
    #8  require(D:\phpnow\vhosts\wordpress.com\wp-blog-header.php) called at [D:\phpnow\vhosts\wordpress.com\index.php:30]

所有的函数调用清晰的呈现！

BUG在哪里？

通过仔细查找，发现原来load_plugin_textdomain()的第三个参数出了问题，该参数没有返回相对路径导致最后的路径错误，导致了MO文件无法找到。修改即可！ 
    
### 高级应用

请参考此篇

[PHP debug_backtrace的胡思乱想][1]
    


  [1]: http://www.cnblogs.com/melonblog/archive/2013/05/09/3062303.html