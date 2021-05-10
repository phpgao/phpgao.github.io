---
title: "(转)UBUNTU之SSH登陆认证慢的解决办法"
categories: [ "服务器技术" ]
tags: [ "ubuntu","ssh" ]
draft: false
slug: "ubuntu_slow_ssh_authorize"
date: "2014-06-26 14:56:00"
url: "ubuntu_slow_ssh_authorize.html"
---

步骤一 关闭ssh的gssapi认证 

    vi /etc/ssh/ssh_config

注释掉如下两行 

    GSSAPIAuthentication yes 
    GSSAPIDelegateCredentials no 

步骤二 关闭ssh的UseDNS 

    vi /etc/ssh/sshd_config 

注：ubuntu的sshd\_config文件没有UseDNS参数,而据我了解,其它类\*NIX、\*BSD操作系统的sshd\_config都有UseDNS参数，且它们缺省都是"UseDNS yes"，那估计ubuntu的sshd\_config虽然没有UseDNS，那它缺省也是"UseDNS yes"了 

于是，在sshd\_config末尾处插入如下行 

    UseDNS no 

然后执行指令"/etc/init.d/ssh restart"，重启ssh服务，配置生效

经测试，使用此方法后，ubuntu的认证时间大大缩短，原来其登录认证一般耗时在10秒以上15秒左右，有些时候甚至达到20多秒到30秒左右，而经过此修改，ubuntu的登录认证时间将会缩短在3秒以内。