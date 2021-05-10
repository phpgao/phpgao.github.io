---
title: "PHP缓冲研究"
categories: [ "代码人生" ]
tags: [ "PHP","buffer","flush","ob_start" ]
draft: false
slug: "php_buffering"
date: "2014-05-21 08:54:00"
url: "php_buffering.html"
---

输出缓冲是个让人头大的问题，因为它不仅牵扯到web语言本身，浏览器自身的原因也会导致无内容输出。

所以，在输出前保险的做法是先输出4096个空白字符，然后再使用缓冲输出内容，这样就可以实现程序随时输出内容啦！

以下代码chrome，FF，IE，Safari，手机chrome，UCWEB 都测试通过，需要注意的是：

在网页最开始一定要输出足够多的字符来骗过浏览器
ob_flush,flush需要一起使用
    
    <?php
    //以下几个函数作用于PHP及服务器的设置，一般不需要更改
    /*
    @apache_setenv('no-gzip', 1);
    @ini_set('zlib.output_compression', 0);
    @ini_set('implicit_flush', 1);  //==ob_implicit_flush(1);
    */
     
    //检测是否开启缓冲
    if (ob_get_level() == 0) ob_start();
    //这里的数字可变 FF:1024 CHROME:0 IE:4096
    echo str_repeat(' ' ,4096);
    //echo str_pad('',4096)."\n";
     
    for ($i = 1; $i<=10; $i++){
        echo "Line $i<br>";
        //ob_end_clean();  //如果再此打开，flush将会报错，因为清空了缓冲区
        //echo '   length: '.ob_get_length();
        ob_flush();
        flush();
        sleep(1);
    }
     
    echo "Done.";
     
    /*
    ob_end_flush();    //送出输出缓冲区内容并关闭缓冲
    ob_get_clean();    //得到当前缓冲区的内容并删除当前输出缓。
    ob_get_contents(); //只是得到输出缓冲区的内容，但不清除它。
    ob_implicit_flush(1); //将打开或关闭绝对（隐式）刷送。绝对（隐式）刷送将导致在每次输出调用后有一次刷送操作，以便不再需要对 flush() 的显式调用。
    */
     
     
    /*
    刷新PHP程序的缓冲，而不论PHP执行在何种情况下（CGI ，web服务器等等）。该函数将当前为止程序的所有输出发送到用户的浏览器。flush() 函数不会对服务器或客户端浏览器的缓存模式产生影响。因此，必须同时使用 ob_flush() 和flush() 函数来刷新输出缓冲。个别web服务器程序，特别是Win32下的web服务器程序，在发送结果到浏览器之前，仍然会缓存脚本的输出，直到程序结束为止。有些Apache的模块，比如mod_gzip，可能自己进行输出缓存，这将导致flush()函数产生的结果不会立即被发送到客户端浏览器。甚至浏览器也会在显示之前，缓存接收到的内容。例如 Netscape 浏览器会在接受到换行或 html 标记的开头之前缓存内容，并且在接受到 </table> 标记之前，不会显示出整个表格。一些版本的 Microsoft Internet Explorer 只有当接受到的256个字节以后才开始显示该页面，所以必须发送一些额外的空格来让这些浏览器显示页面内容。
    */


以下转自http://www.cnblogs.com/zemliu/archive/2012/05/11/2495293.html

 

首先明确一下PHP的输出顺序：

1.打开了php输出缓存: 

> echo,print -> php output_buffring -> server buffering -> browser buffering -> browser display

2.未打开php输出缓存: 

> echo,print -> server buffering -> browser buffering -> browser display

另外明确一下浏览器的输出缓存:

> IE为256Bytes, Chrome与FireFox为1000Bytes,只有输出数据达到了这个长度或者脚本结束浏览器才会将数据输出在页面上

再来说说用到的几个PHP设置和API:

1.php.ini中的output_buffering配置

Off: 表示关闭PHP输出缓存
On: 打开无限大的输出缓存
4096: 打开大小为4096Byte的输出缓存

2.php.ini中的implicit_flush配置

On: 表示每次输出(如echo,print)后自动调用flush()函数后,直接输出
Off: 与On相反,每次输出后不会调用flush(),需要等到server buffering满了才会输出,但是我们可以用flush()函数代替它,不开启也没关系,反而更加灵活

3.ob_flush()函数: 取出PHP buffering中的数据,放入server buffering

4.flush()函数: 取出Server buffering的数据,放入browser buffering

5.ob_start()函数:对于这个函数我现在了解的不是很清楚,因为开启后输出就会不受ob_flush()控制,即使使用ob_flush()和flush(),数据也不能立即输出在浏览器上.现在知道的是,如果output_buffering=Off,即使使用了ob_start(),也是无法将输出数据缓存的,而如果output_buffering=On的话,即使不用ob_start(),输出数据也可以被PHP缓存,所以觉得ob_start比较废,暂时不管他


最后引用一段Laruence的一段blog,希望对大家的理解有帮助


> ob_flush/flush在手册中的描述, 都是刷新输出缓冲区, 并且还需要配套使用, 所以会导致很多人迷惑…
> 
> 其实, 他们俩的操作对象不同, 有些情况下, flush根本不做什么事情..
> 
> ob_*系列函数, 是操作PHP本身的输出缓冲区.
> 
> 所以, ob_flush是刷新PHP自身的缓冲区.
> 
> 而flush, 严格来讲, 这个只有在PHP做为apache的Module(handler或者filter)安装的时候, 才有实际作用.
> 它是刷新WebServer(可以认为特指apache)的缓冲区.
> 
> 在apache module的sapi下, flush会通过调用sapi_module的flush成员函数指针,
> 间接的调用apache的api: ap_rflush刷新apache的输出缓冲区, 当然手册中也说了, 有一些apache的其他模块,
> 可能会改变这个动作的结果..
> 
> 有些Apache的模块，比如mod_gzip，可能自己进行输出缓存，这将导致flush()函数产生的结果不会立即被发送到客户端浏览器。
> 
> 甚至浏览器也会在显示之前，缓存接收到的内容。例如 Netscape浏览器会在接受到换行或 html标记的开头之前缓存内容，并且在接受到
> </table> 标记之前，不会显示出整个表格。
> 
> 一些版本的 Microsoft Internet Explorer
> 只有当接受到的256个字节以后才开始显示该页面，所以必须发送一些额外的空格来让这些浏览器显示页面内容。
> 
> 所以, 正确使用俩者的顺序是. 先ob_flush, 然后flush,
> 
> 当然, 在其他sapi下, 不调用flush也可以, 只不过为了保证你代码的可移植性, 建议配套使用.