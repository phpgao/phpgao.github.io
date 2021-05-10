---
title: "Windows激活工具的研究学习"
categories: [ "服务器技术" ]
tags: [ "windows" ]
draft: false
slug: "windows_activation"
date: "2018-06-21 07:38:00"
url: "windows_activation.html"
---

老高的K3路由器刷了官改，果然已经集成了最新的vlmcsd，路径为`/usr/root/kms/vlmcsd`，命令行为`/usr/root/kms/vlmcsd -i /usr/root/kms/vlmcsd.ini -p /tmp/vlmcsd.pid -l /tmp/kms.log`，既然有了这个，我们就学习一下怎么使用KMS激活Windows系统吧！

> 严正声明：此篇文章只是研究学习，学习后请勿使用于任何场合！


<!--more-->


![K3_KMS][1]

根据工具的输出来看，kms运行在端口1688，假设我们的路由器地址为**192.168.1.1**，有了这二者，我们就可以开始干活了！

```
2018-06-14 12:56:32: Read ini file /usr/root/kms/vlmcsd.ini
2018-06-14 12:56:32: Listening on [::]:1688
2018-06-14 12:56:32: Listening on 0.0.0.0:1688
2018-06-14 12:56:32: vlmcsd 1108, built 2017-01-19 21:27:37 UTC started successfully
```

## 准备工作

首选准备一个win10的测试用的安装密钥**XXXXX-XXXXX-XXXXX-XXXXX-3V66T**，然后在系统上以管理员权限运行cmd。

## 卸载当前密钥

```
slmgr /upk
slmgr /cpky
```

## 安装新密钥

```
slmgr /ipk XXXXX-XXXXX-XXXXX-XXXXX-3V66T
```

## 设定激活服务器IP

```
slmgr /skms 192.168.1.1:1688
```

## 进行自动激活

```
slmgr /ato
```

## 查询激活信息

```
slmgr /dlv
```

## 查看过期时间

```
slmgr /xpr
```


  [1]: https://blog.phpgao.com/usr/uploads/2018/06/1850493935.png