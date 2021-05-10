---
title: "让使用Apache的网站速度更快"
categories: [ "性能优化" ]
tags: [ "apache" ]
draft: false
slug: "faster_apache"
date: "2014-04-20 15:45:00"
url: "faster_apache.html"
---

Apache 2.0在性能上的改善最吸引人.在支持POSIX线程的Unix系统上,Apache可以通过不同的MPM运行在一种多进程与多线程相混合的模式下,增强部分配置的可扩充性能.相比于Apache 1.3,2.0版本做了大量的优化来提升处理能力和可伸缩性,并且大多数改进在默认状态下即可生效.但是在编译和运行时刻,2.0也有许多可以显著提高性能的选择. 

## MPM（Multi -Processing Modules,多道处理模块）

MPM是Apache2.0中影响性能的最核心特性. 

毫不夸张地说,MPM的引入是Apache 2.0最重要的变化.大家知道,Apache是基于模块化的设计,而Apache 2.0更扩展了模块化设计到Web服务器的最基本功能.服务器装载了一种多道处理模块,负责绑定本机网络端口、接受请求,并调度子进程来处理请求.扩展模块化设计有两个重要好处: 

 - apache可以更简洁、有效地支持多种操作系统;  
 - 务器可以按站点的特殊需要进行自定制.

在用户级,MPM看起来和其它Apache模块非常类似.主要区别是在任意时刻只能有一种MPM被装载到服务器中. 下面以Linux RedHat AS3为平台,演示一下在Apache 2.0中如何指定MPM. 

    # wget http://archive.apache.org/dist/httpd/httpd-2.0.52.tar.bz2 
    # tar jxvf httpd-2.0.52.tar.bz2 
    # cd httpd-2.0.52 
    # ./configure ——help|grep mpm 

显示如下: 

    ——with-mpm=MPM Choose the process model for Apache to use. MPM={beos|worker|prefork|mpmt\_os2| perchild|leader|threadpool} 

上述操作用来选择要使用的进程模型,即哪种MPM模块.Beos、mpmt\_os2分别是BeOS和OS/2上缺省的MPM, perchild主要设计目的是以不同的用户和组的身份来运行不同的子进程.这在运行多个需要CGI的虚拟主机时特别有用,会比1.3版中的SuExec 机制做得更好.leader和threadpool都是基于worker的变体,还处于实验性阶段,某些情况下并不会按照预期设想的那样工作,所以 Apache官方也并不推荐使用.因此,我们主要阐述prefork和worker这两种和性能关系最大的产品级MPM.

### prefork的工作原理 

如果不用“——with-mpm”显式指定某种MPM,prefork就是Unix平台上缺省的MPM.它所采用的预派生子进程方式也是 Apache 1.3中采用的模式.prefork本身并没有使用到线程,2.0版使用它是为了与1.3版保持兼容性;另一方面,prefork用单独的子进程来处理不同的请求,进程之间是彼此独立的,这也使其成为最稳定的MPM之一.

prefork的工作原理是,控制进程在最初建立“StartServers”个子进程后,为了满足MinSpareServers设置的需要创建一个进程,等待一秒钟,继续创建两个,再等待一秒钟,继续创建四个……如此按指数级增加创建的进程数,最多达到每秒32个,直到满足 MinSpareServers设置的值为止.这就是预派生（prefork）的由来.这种模式可以不必在请求到来时再产生新的进程,从而减小了系统开销以增加性能. 

### worker的工作原理

相对于prefork,worker是2.0 版中全新的支持多线程和多进程混合模型的MPM.由于使用线程来处理,所以可以处理相对海量的请求,而系统资源的开销要小于基于进程的服务器.但是, worker也使用了多进程,每个进程又生成多个线程,以获得基于进程服务器的稳定性.这种MPM的工作方式将是Apache 2.0的发展趋势.
worker的工作原理是,由主控制进程生成“StartServers”个子进程,每个子进程中包含固定的ThreadsPerChild 线程数,各个线程独立地处理请求.同样,为了不在请求到来时再生成线程,MinSpareThreads和MaxSpareThreads设置了最少和最多的空闲线程数;而MaxClients设置了所有子进程中的线程总数.如果现有子进程中的线程总数不能满足负载,控制进程将派生新的子进程.

下面我以worker模式进行编译安装

    # ./configure --prefix=/usr/local/apache --with-mpm=worker --enable-so
    # #注释（让它支持DSO功能,这样以后可以动态加载模块）
    # make
    # make install
    # cd /usr/local/apache/conf
    # vi httpd.conf
    StartServers 2 MaxClients
    150 ServerLimit 25 MinSpareThreads
    25 MaxSpareThreads 75 ThreadLimit
    25 ThreadsPerChild 25 MaxRequestsPerChild 0
    
Worker模式下所能同时处理的请求总数是由子进程总数乘以ThreadsPerChild值决定的,应该大于等于MaxClients.如果负载很大,现有的子进程数不能满足时,控制进程会派生新的子进程.默认最大的子进程总数是16,加大时也需要显式声明ServerLimit（最大值是20000） 

需要注意的是,如果显式声明了ServerLimit,那么它乘以ThreadsPerChild的值必须大于等于MaxClients,而且MaxClients必须是ThreadsPerChild的整数倍,否则Apache将会自动调节到一个相应值（可能是个非期望值）.下面是笔者的 worker配置段:   

    <IfModule worker.c> 
    StartServers 3 MaxClients 2000 ServerLimit 
    25 MinSpareThreads 50 MaxSpareThreads 
    200 ThreadLimit 200 ThreadsPerChild 
    100 MaxRequestsPerChild 0 </IfModule></pre>
    # 保存退出. 
    # /usr/local/apache/bin/apachectl start 
    # 可根据实际情况来配置Apache相关的核心参数,以获得最大的性能和稳定性. 

## 限制Apache并发连接数 

我们知道当网站以http方式提供软件下载时,若是每个用户都开启多个线程并没有带宽的限制,将很快达到http的最大连接数或者造成网络阻塞,使得网站的许多正常服务都无法运行.下面我们添加mod_limitipconn模块,来控制http的并发连接数. 

    # wget http://dominia.org/djao/limit/mod_limitipconn-0.22.tar.gz
    # tar zxvf mod_limitipconn-0.22.tar.gz
    # cd mod_limitipconn-0.22
    # /usr/local/apache/bin/ apxs -c -i -a mod_limitipconn.c
    # 编译好后会自动把mod_rewrite.so拷贝到/usr/local/apache/modules下,并修改你的httpd.conf文件.
    # vi /usr/local/apache/conf/httpd.conf
    # 在最后一行加入
    #所限制的目录所在,此处表示主机的根目录MaxConnPerIP 2
    #所限制的每个IP并发连接数为2个

    # 保存退出.
    # /usr/local/apache/bin/apachectl start

我们刚才已经限制了IP并发数,但如果对方把链接盗链到别的页面,我们刚才做的就毫无意义了,因为他完全可以通过蚂蚁或快车进行下载.所以就这种情况,我们要引用mod\_rewrite.so模块.这样,当他盗链了文件,通过mod\_rewrite.so模块把页面引到了一个事先我们制定好的错误页面里,这样就防止了盗链。 

    # /usr/local/apache/bin/apxs -c -i -a /opt/httpd-2.0.52/modules/mappers/mod_rewrite.c
    # 编译好后会自动把mod_rewrite.so拷贝到/usr/local/apache/modules下,并修改你的httpd.conf文件.
    
    # vi /usr/local/apache/conf/httpd.conf
    
    RewriteEngine onRewriteCond %{HTTP_REFERER} !
    ^http://www.squall.cn/.*$ [NC]RewriteCond %{HTTP_REFERER} !
    ^http://www.squall.cn$ [NC]RewriteCond %{HTTP_REFERER} !
    ^http://squall.cn/.*$ [NC]RewriteCond %{HTTP_REFERER} !
    ^http://squall.cn$ [NC]RewriteRule .*\\.
    (jpg|gif|png|bmp|tar|gz|rar|zip|exe)$
    http://www.squall.cn/error.htm [R,NC]
    
到此,我们就对Apache做了一次全面优化,性能比原来明显地有了很大的提高.这次实施过程到此也就圆满的结束了.相信大家通过读完我的这篇文章后,对Apache优化也有了一些心得,相信你在工作中也会处理好突发事件。