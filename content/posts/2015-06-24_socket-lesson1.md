---
title: "简单socket编程1"
categories: [ "代码人生" ]
tags: [ "socket","lesson" ]
draft: false
slug: "socket-lesson1"
date: "2015-06-24 04:16:00"
url: "socket-lesson1.html"
---

![socket][1]

(图片来自互联网)

说到socket，学过网络基础的人都听过，他是TCP/IP的抽象，他是网络世界的入口，它无处不在。

学会了socket编程，就意味着能够更深层次的控制你的流量，之后再看python的urllib*或php的curl，简直就是小儿科！


<!--more-->


## 一些名词

### Address Family

地址类型，协议族，可能是以下

```
socket.AF_INET ---> IPv4 addresses.
socket.AF_INET6 ---> IPv6 addresses.
socket.AF_UNIX ---> Unix domain sockets (例如 /var/run/mysqld/mysqld.sock is an example).
socket.AF_IPX ---> IPX addresses.
```

### Socktype

sock类型

```
socket.SOCK_STREAM ---> 流式socket , for TCP
socket.SOCK_DGRAM ---> 数据报式socket , for UDP
```

### 其他

<div class="table-responsive"><table class="table table-bordered"><tbody><tr><td>参数</td><td>取值</td><td>值</td><td>说明</td></tr><tr><td rowspan="3">Address Family</td><td>AF_INET</td><td>2</td><td>IPv4</td></tr><tr><td>AF_INET6</td><td>23</td><td>IPv6</td></tr><tr><td>AF_UNSPEC</td><td>0</td><td>协议无关</td></tr><tr><td rowspan="5">Protocol Numbers 协议号</td><td>IPPROTO_IP</td><td>0</td><td>IP协议</td></tr><tr><td>IPPROTO_IPV4</td><td>4</td><td>IPv4</td></tr><tr><td>IPPROTO_IPV6</td><td>41</td><td>IPv6</td></tr><tr><td>IPPROTO_UDP</td><td>17</td><td>UDP</td></tr><tr><td>IPPROTO_TCP</td><td>6</td><td>TCP</td></tr><tr><td rowspan="2">Socktype sock类型</td><td>SOCK_STREAM</td><td>1</td><td>流</td></tr><tr><td>SOCK_DGRAM</td><td>2</td><td>数据报</td></tr><tr><td rowspan="3">ai_flags</td><td>AI_PASSIVE</td><td>1</td><td>被动的，用于bind，通常用于server socket</td></tr><tr><td>AI_CANONNAME</td><td>2</td><td>用于返回主机的规范名称<br>　　</td></tr><tr><td>AI_NUMERICHOST</td><td>4</td><td>地址为数字串</td></tr></tbody></table></div>
## 简单的例子

来自官方文档的例子，很基础。

### 服务端

```python
# Echo server program
import socket

HOST = '127.0.0.1'                 # Symbolic name meaning all available interfaces
PORT = 50007              # Arbitrary non-privileged port
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.bind((HOST, PORT))
s.listen(1)
conn, addr = s.accept()
print 'Connected by', addr
while 1:
    data = conn.recv(1024)
    if not data: break
    conn.sendall(data)
conn.close()
```

### 客户端

```
# Echo client program
import socket

HOST = 'localhost'    # The remote host
PORT = 50007              # The same port as used by the server
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((HOST, PORT))
s.sendall('Hello, world')
data = s.recv(1024)
s.close()
print 'Received', repr(data)
```

## 更复杂的例子

### 服务端

与之前不同的是，支持IPV6

```
# Echo server program
import socket
import sys

HOST = None               # Symbolic name meaning all available interfaces
PORT = 50007              # Arbitrary non-privileged port
s = None
for res in socket.getaddrinfo(HOST, PORT, socket.AF_UNSPEC,
                              socket.SOCK_STREAM, 0, socket.AI_PASSIVE):
    af, socktype, proto, canonname, sa = res
    try:
        s = socket.socket(af, socktype, proto)
    except socket.error as msg:
        s = None
        continue
    try:
        s.bind(sa)
        s.listen(1)
    except socket.error as msg:
        s.close()
        s = None
        continue
    break
if s is None:
    print 'could not open socket'
    sys.exit(1)
conn, addr = s.accept()
print 'Connected by', addr
while 1:
    data = conn.recv(1024)
    if not data: break
    conn.send(data)
conn.close()
```

### 客户端

```python
# Echo client program
import socket
import sys

HOST = 'localhost'    # The remote host
PORT = 50007              # The same port as used by the server
s = None
for res in socket.getaddrinfo(HOST, PORT, socket.AF_UNSPEC, socket.SOCK_STREAM):
    af, socktype, proto, canonname, sa = res
    try:
        s = socket.socket(af, socktype, proto)
    except socket.error as msg:
        s = None
        continue
    try:
        s.connect(sa)
    except socket.error as msg:
        s.close()
        s = None
        continue
    break
if s is None:
    print 'could not open socket'
    sys.exit(1)
s.sendall('Hello, world')
data = s.recv(1024)
s.close()
print 'Received', repr(data)
```

  [1]: https://blog.phpgao.com/usr/uploads/2015/06/2605814858.jpg