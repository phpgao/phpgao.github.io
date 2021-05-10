---
title: "centos安装EPEL"
categories: [ "服务器技术" ]
tags: [ "PHP","centos","epel" ]
draft: false
slug: "centos_install_epel"
date: "2015-05-27 14:25:00"
url: "centos_install_epel.html"
---

EPEL全称[Extra Packages for Enterprise Linux][1]，官方翻译为 企业版 Linux 附加软件包。顾名思义，他可以扩充你的软件库，安装很多软件时能省去很多时间。比如最近很火的PHP的DoS漏洞，Bug 61461，在EPEL下面只需要一条命令就能将PHP升级为5.9，避免重新编。

老高推荐新安装的centos系统都先安装此附加软件包！


安装命令：

```bash
yum install epel-release
```

没有报错的话，那么EPEL就安装完毕了。

如果上述命令还是解决不了问题，那就必须有针对性的安装了。

以下命令复制自[Install EPEL and additional repositories on CentOS and Red Hat][2]

```bash
#CentOS and Red Hat Enterprise Linux 5.x

wget http://dl.fedoraproject.org/pub/epel/5/x86_64/epel-release-5-4.noarch.rpm
rpm -Uvh epel-release-5*.rpm

#CentOS and Red Hat Enterprise Linux 6.x

wget http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
rpm -Uvh epel-release-6*.rpm

#CentOS and Red Hat Enterprise Linux 7.x

wget http://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-5.noarch.rpm
rpm -Uvh epel-release-7*.rpm
```

你还可以[在此查看每个系统对应的最新的EPEL版本][3]


  [1]: https://fedoraproject.org/wiki/EPEL/zh-cn
  [2]: http://www.rackspace.com/knowledge_center/article/install-epel-and-additional-repositories-on-centos-and-red-hat
  [3]: https://fedoraproject.org/wiki/EPEL#How_can_I_use_these_extra_packages.3F