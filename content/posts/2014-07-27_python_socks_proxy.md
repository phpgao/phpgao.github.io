---
title: "在python的urllib2中使用socks代理"
categories: [ "代码人生" ]
tags: [ "python","代理" ]
draft: false
slug: "python_socks_proxy"
date: "2014-07-27 10:08:00"
url: "python_socks_proxy.html"
---

转自[潘魏增][1]的博客！

python2.5中的urllib2支持http代理，不支持socks代理。假如代码库或者编码时使用了urllib2，同时又要使用socks，就需要第三方的库来实现。幸运的是，已经有人造好了轮子。

SocksiPy是一个socks module。它提供了一个类socket的接口，支持socks4、socks5和http proxy。下载后在代码中引用socks.py即可，也可以写个setup.py文件安装到python的site-packages目录中。 使用时，将下面的代码放在调用urllib2的代码之前即可：

    import socks
    import socket
    socks.setdefaultproxy(socks.PROXY_TYPE_SOCKS5, "127.0.0.1", 8080)
    socket.socket = socks.socksocket
    import urllib2
    print urllib2.urlopen('http://baidu.com').read()

如果是自己写的程序，也可以尝试使用pycurl这个库。ubuntu使用sudo apt-get install python-pycurl python-pycurl-dbg安装。 示例代码

    import pycurl
    c = pycurl.Curl()
    c.setopt(pycurl.URL, 'http://baidu.com')
    c.setopt(pycurl.PROXY, '127.0.0.1')
    c.setopt(pycurl.PROXYPORT, 8080)
    c.setopt(pycurl.PROXYTYPE, pycurl.PROXYTYPE_SOCKS5)
    c.perform()

如果在程序外部使用socks，推荐使用tsocks。ubuntu中直接使用sudo apt-get install tsocks安装。安装完毕以后需要修改/etc/tsocks.conf。配置样例如下

    # 默认代理服务器
    server = 127.0.0.1 
    # 代理服务器类型  
    server_type = 5 
    # 代理服务器端口
    server_port = 8080

使用时，在命令行输入tsocks 程序名即可。例如ubuntu下的epiphany浏览器没有代理设置，如果想使用socks代理，输入tsocks epiphany-browser即可（注意先关闭其他的epiphany实例）。


  [1]: http://www.panweizeng.com/python-urllib2-socks-proxy.html