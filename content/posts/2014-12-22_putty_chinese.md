---
title: "putty中文乱码"
categories: [ "服务器技术" ]
tags: [ "centos","yum","putty","chinese","i18n","sysconfig" ]
draft: false
slug: "putty_chinese"
date: "2014-12-22 03:42:00"
url: "putty_chinese.html"
---

每次重装centos总会碰到关于乱码的种种问题

一般中文乱码有以下两个原因

1.中文未安装

一句话安装

```bash
yum groupinstall "chinese support"
# 重启
reboot
```

2.设置问题

如果安装了中文支持还不行，那么就要考虑配置是否合适了。

linux的语言配置文件位于`/etc/sysconfig/i18n`，使用vim打开

```bash
cp /etc/sysconfig/i18n /etc/sysconfig/i18n.bak
vim /etc/sysconfig/i18n
```

替换原来的内容为

中文UTF-8（推荐）:

```bash
LANG="zh_CN.UTF-8"
LANGUAGE="zh_CN.UTF-8:zh_CN"
SUPPORTED="zh_CN.UTF-8:zh_CN:zh:en_US.UTF-8:en_US:en"
SYSFONT="latarcyrheb-sun16"
```

或者

中文GBK:

```bash
LANG="zh_CN.GB18030"
LANGUAGE="zh_CN.GB18030:zh_CN.GB2312:zh_CN"
SUPPORTED="zh_CN.GB18030:zh_CN:zh:en_US.UTF-8:en_US:en"
SYSFONT="lat0-sun16"
```

然后在putty等软件中设置对应字符集即可

vim乱码请参考老高的另一篇博文  [vim中文乱码的解决办法][1]


  [1]: https://blog.phpgao.com/vim_garbled.html