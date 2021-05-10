---
title: "解决Ubuntu下ssh无法启动"
categories: [ "服务器技术" ]
tags: [ "ubuntu","ssh" ]
draft: false
slug: "ubuntu_ssh"
date: "2014-06-21 15:25:00"
url: "ubuntu_ssh.html"
---

安装完Ubuntu后自然需要使用ssh远程管理，但是sudo apt-get install openssh-server后虽然安装成功，但是总是提示

> Unknown job: ssh 百度之，未解决 google之，

运行

    sudo /usr/sbin/sshd
    
提示 

> Missing privilege separation directory: /var/run/sshd (缺少权限分离目录) 

然后运行 

    sudo mkdir /var/run/sshd
    sudo /usr/sbin/sshd
    netstat -nlt

应该能看到22端口已经启用！