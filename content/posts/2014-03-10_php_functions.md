---
title: "PHP经典函数收集"
categories: [ "代码人生" ]
tags: [ "PHP","function","glob" ]
draft: false
slug: "php_functions"
date: "2014-03-10 15:05:00"
url: "php_functions.html"
---

## PHP经典函数

### sys_getloadavg() 

这个函数返回当前系统的负载均值信息（当然 Windows 下不适用），详细文档可以翻阅 PHP的相关文档。文档中有段示例代码，基本上也就能看出它的用途了。 

    $load = sys_getloadavg();
    if ($load[0] > 80) {
        header('HTTP/1.1 503 Too busy, try again later');
        die('Server too busy. Please try again later.');
    }
    //PS，如果“很不幸”你的 PHP 环境中没有这个函数，可以考虑使用下面这段代码
    if (!function_exists('sys_getloadavg')) {
        function sys_getloadavg()
        {
            $loadavg_file = '/proc/loadavg';
            if (file_exists($loadavg_file)) {
                return explode(chr(32),file_get_contents($loadavg_file));
            }
            return array(0,0,0);
        }
    }


### debug_backtrace兄弟

使用请参考使用[debug_backtrace()做PHP调试][1]

### get_browser()

该方法参考一个名叫browscap.ini的文件，该文件收录了各种浏览器信息，通过比对实现查找客户浏览器的功能。
两个使用方法：

1.下载最新的browscap.ini,放入PHP的安装目录或者C:\Windows文件夹下

[下载地址][2]

2.在PHP.INI文件中加入以下配置

    [browscap]
    ;https://blog.phpgao.com/classic_code/
    browscap = php_browscap.ini

3.使用代码如下 

    http://www.phpgao.com</a>
    print_r($browser);

返回结果如下 

    Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.7) Gecko/20040803 Firefox/0.9.3
    
    Array
    (
        [browser_name_regex] => ^mozilla/5\.0 (windows; .; windows nt 5\.1; .*rv:.*) gecko/.* firefox/0\.9.*$
        [browser_name_pattern] => Mozilla/5.0 (Windows; ?; Windows NT 5.1; *rv:*) Gecko/* Firefox/0.9*
        [parent] => Firefox 0.9
        [platform] => WinXP
        [browser] => Firefox
        [version] => 0.9
        [majorver] => 0
        [minorver] => 9
        [cssversion] => 2
        [frames] => 1
        [iframes] => 1
        [tables] => 1
        [cookies] => 1
        [backgroundsounds] =>
        [vbscript] =>
        [javascript] => 1
        [javaapplets] => 1
        [activexcontrols] =>
        [cdf] =>
        [aol] =>
        [beta] => 1
        [win16] =>
        [crawler] =>
        [stripper] =>
        [wap] =>
        [netclr] =>
    )

另一种方法：

http://alexandre.alapetite.fr/doc-alex/php-local-browscap/index.en.html
    
### glob() 

eg1:

**方便地替代 [opendir()][3] 和相关函数** 

    <?php 
    $matches = glob("*.txt"); 
    if ( is_array ( $matches ) ) { 
       foreach ( $matches as $filename) { 
          echo "$filename size " . filesize($filename) . "\n"; 
       } 
    } 
    
    /*
    以上例程的输出类似于：
    funclist.txt size 44686
    funcsummary.txt size 267625
    quickref.txt size 137820
    */
    ?>
    
eg2:获取所有文件名，并输出为一个带链接的列表 
    
    <?php
    foreach (glob("*.*") as $filename) 
    {
       echo "<a href=\"".$filename."\">".$filename."</a><br/>";
    }
    ?>

eg3:非递归实现文件遍历

    <?php
    function scandir_through($dir)
    {
        $items = glob($dir . '/*');
     
        for ($i = 0; $i < count($items); $i++) {
            if (is_dir($items[$i])) {
                $add = glob($items[$i] . '/*');
                $items = array_merge($items, $add);
            }
        }
     
        return $items;
    }
    ?>
    
更多使用请参考：

[http://php.net/manual/zh/function.glob.php][4]

### PHP Filter 函数

让人很省心的函数，自动验证电子邮件或者url格式，使用方法请参考：

[http://www.w3school.com.cn/php/php_ref_filter.asp][5]

### 错误处理

set_error_handler — 设置一个用户定义的错误处理函数

占个坑，以后讲

  [1]: https://blog.phpgao.com/debug_with_backtrace.html
  [2]: http://browscap.org/
  [3]: http://www.php.net/manual/zh/function.opendir.php
  [4]: http://php.net/manual/zh/function.glob.php
  [5]: http://www.w3school.com.cn/php/php_ref_filter.asp