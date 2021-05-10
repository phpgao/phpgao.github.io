---
title: "近距离接触HTTP协议"
categories: [ "代码人生" ]
tags: [ "http","python","socket" ]
draft: false
slug: "socket-http"
date: "2015-06-24 00:16:00"
url: "socket-http.html"
---

![HTTP_logo][1]

(图片来自WIKI)

超文本传输协议（HTTP，HyperText Transfer Protocol)是互联网上应用最为广泛的一种网络协议。我们打开浏览器输入网址`www.google.com`，不对！刚才那个是一个不存在的网址，我们还是用`www.phpgao.com`为例吧。我们(客户端)将老高的域名输入浏览器，浏览器就会为我们呈现老高的网页，首先我们能确定作为客户端，在此期间必定与老高的服务器发生了某种关系！但是具体发送了什么呢？作为一名WEB开发人员，这是我们必须知道的。


<!--more-->


下面我们简单的模拟一下刚才的过程。

> 如何使用以下代码？ 在python的交互式环境中逐行输入命令。即在命令行中输入`python`

## 模拟

### DNS解析

```python
import socket
host = 'www.phpgao.com'
# 查询域名的IP
server_ip = socket.gethostbyname(host)
print 'Get server\'s IP --> %s' % server_ip
# Get server's IP --> xxx.62.107.xx

```

### 建立TCP链接

```python
# socket.SOCK_STREAM代表TCP
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
port = 80
s.connect((server_ip, port))
print '%s:%d Connected' % (server_ip, port)
```

### 发送http请求

```python
print 'Send %d bytes' % s.send('GET / HTTP/1.1\r\n')
print 'Send %d bytes' % s.send('Host: ' + host + '\r\n')
# 发送结束标志
print 'Send %d bytes' % s.send('\r\n')
```

### 接收http响应

```python
data = s.recv(200)
print repr(data)
print data
```

### 关闭链接

```python
s.close
```

### 组合

让我们把刚才的请求组合起来，并加一点注释

```python
# coding=UTF-8

import socket
host = 'www.phpgao.com'
# 查询域名的IP
server_ip = socket.gethostbyname(host)
print 'Get server\'s IP --> %s' % server_ip

# socket.SOCK_STREAM代表TCP
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
port = 80
s.connect((server_ip, port))
print '%s:%d Connected' % (server_ip, port)

print 'Send %d bytes' % s.send('GET / HTTP/1.1\r\n')
print 'Send %d bytes' % s.send('Host: ' + host + '\r\n')
# 结束标志
print 'Send %d bytes' % s.send('\r\n')

data = s.recv(200)

print 'We have received %d bytes' % len(data)

print '-----raw-----\n%s\n\n-------' % repr(data)
print '----formated----\n%s\n-------' % data

s.close
```

## 分析

以上代码的结果如下

```
Get server's IP --> xxx.62.107.xx
xxx.62.107.xx:80 Connected
Send header

-------raw---
'GET / HTTP/1.1\r\nHost: www.phpgao.com\r\n\r\n'
-----formated----
GET / HTTP/1.1
Host: www.phpgao.com


Total 40 bytes
We have received 200 bytes
-----raw-----
'HTTP/1.1 200 OK\r\nServer: nginx\r\nDate: Wed, 24 Jun 2015 00:23:37 GMT\r\nContent-Type: text/html; charset=UTF-8\r\nTransfer-Encoding: chunked\r\nConnection: keep-alive\r\nX-Cache-CFC:  - 1435105417.911\r\n\r\n8cfc\r'

-------
----formated----
HTTP/1.1 200 OK
Server: nginx
Date: Wed, 24 Jun 2015 00:23:37 GMT
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: keep-alive
X-Cache-CFC:  - 1435105417.911

8cfc
-------
```


可以看到：

我们发送出去的http协议很言简意赅，即规定了动作`GET`，路径`/`，协议版本`HTTP 1.1`和主机信息`www.phpgao.com`。

我们接收到http协议就相对复杂一点，有响应状态码、服务器软件信息、响应发送时间GMT格式、内容的元信息、Connection表示链接的持续性，最后的X-Cache-CFC是老高自定义的响应头，表明缓存是否命中。


> 你知道最后的8cfc是什么吗？ 答案：十六进制数，表示发送文件的大小，typecho程序特有。

## 一些问题

### 关于recv阻塞

为了接收全部HTML响应，我们使用以下代码。

```python
total_data=''

while 1:
	print 'Receiving data!'
	data = s.recv(1024)
	if not data: break
	total_data += data
	print 'We have received %d bytes' % len(data)
```

因为recv阻塞，程序会卡在`data = s.recv(1024)`，过很久(连接超时)才能让程序走到`if not data:`。

如何解决呢？

**在请求头部中设置链接为立刻关闭**
```python
header = 'GET /favicon.ico HTTP/1.1\r\n' + 'Host: ' + host + '\r\nConnection: close\r\n' + '\r\n'
```

**设置recv为非阻塞**

```python
s.connect((server_ip, port))

def recv_timeout(the_socket, timeout=2):
    #make socket non blocking
    the_socket.setblocking(0)
     
    #total data partwise in an array
    total_data=[];
    data='';
     
    #beginning time
    begin=time.time()
    while 1:
        #if you got some data, then break after timeout
        if total_data and time.time()-begin > timeout:
            break
         
        #if you got no data at all, wait a little longer, twice the timeout
        elif time.time()-begin > timeout*2:
            break
         
        #recv something
        try:
            data = the_socket.recv(8192)
            if data:
                total_data.append(data)
                #change the beginning time for measurement
                begin=time.time()
            else:
                #sleep for sometime to indicate a gap
                time.sleep(0.1)
        except:
            pass
     
    #join all parts to make final string
    return ''.join(total_data)
 
#get reply and print
print recv_timeout(s)
```


Reference:

 - [17.2. socket — Low-level networking interface¶][2]
 - [Receive full data with the recv socket function in python][3]
 - [PYTHON SOCKET之阻塞模式和非阻塞模式详解][4]


  [1]: https://blog.phpgao.com/usr/uploads/2015/06/4066750042.png
  [2]: https://docs.python.org/2/library/socket.html
  [3]: http://www.binarytides.com/receive-full-data-with-the-recv-socket-function-in-python/
  [4]: http://www.yhsafe.net/python_stack_block_unblock.html