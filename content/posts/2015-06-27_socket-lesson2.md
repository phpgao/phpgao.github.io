---
title: "简单socket编程2"
categories: [ "代码人生" ]
tags: [ "socket","lesson" ]
draft: false
slug: "socket-lesson2"
date: "2015-06-27 14:09:00"
url: "socket-lesson2.html"
---

上一节我们提到了socket编程的基本形式，这一节我们加强服务端的性能！


<!--more-->


```python
#!/usr/bin/env python

import socket, threading

class ClientThread(threading.Thread):

    def __init__(self, ip, port, socket):
        threading.Thread.__init__(self)
        self.ip = ip
        self.port = port
        self.socket = socket
        print "[+] New thread started for "+ip+":"+str(port)

    def run(self):    
        print "Connection from : "+ip+":"+str(port)

        self.socket.send("\nWelcome to the server\n\n")

        data = "dummydata"

        while len(data):
            data = self.socket.recv(2048)
            print "Client sent : "+data
            self.socket.send("You sent me : "+data)

        print "Client disconnected..."

host = "0.0.0.0"
port = 9999

tcpsock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
tcpsock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)

tcpsock.bind((host,port))
threads = []


while True:
    tcpsock.listen(4)
    print "\nListening for incoming connections..."
    (clientsock, (ip, port)) = tcpsock.accept()
    newthread = ClientThread(ip, port, clientsock)
    newthread.start()
    threads.append(newthread)

for t in threads:
    t.join()
```