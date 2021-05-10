---
title: "新装虚拟机记录一下"
categories: [ "服务器技术" ]
tags: [ "virual","server" ]
draft: false
slug: "virtual_machine_record"
date: "2014-10-11 04:50:00"
url: "virtual_machine_record.html"
---

## 镜像下载

[centos官方][1]
[Aliyun Open Source Mirror Site][2]
[网易开源镜像站][3]


## 虚拟化工具

免费的virtualbox，[下载地址][4]
KVM
Xen

## 安装

[记一次centos最小安装][5]
[centos7.0 的最小化安装][6]

## 优化

[分享一个centos6.*优化脚本][7]
[CentOS精简命令][8]

## 后续

### 关闭防火墙和SELinux

    # 关闭防火墙
    service iptables stop 
    chkconfig iptables off
    chkconfig ip6tables off
    # SELinux
    vi /etc/selinux/config
    # change
    SELINUX=disabled
    #网卡
    ------
    vi /etc/sysconfig/network-scripts/ifcfg-eth0
    TYPE=Ethernet
    # change
    ONBOOT=yes
    NM_CONTROLLED=yes
    # change
    BOOTPROTO=dhcp
    or
    BOOTPROTO=static
    # 加上
    # 静态IP
    IPADDR=192.168.1.107
    # 设置子网
    NETMASK=255.255.255.0
    # 设置网关
    GATEWAY=192.168.1.1
    # 设置DNS
    DNS1=114.114.114.114
    IPV6INIT=no
    USERCTL=no
    ------
    service network restart
    chkconfig network on
    ------
    # 全局关闭IPv6
    vi /etc/sysctl.conf
    #加上
    net.ipv6.conf.all.disable_ipv6 = 1
    net.ipv6.conf.default.disable_ipv6 = 1
    ------
    sysctl -p
    reboot

### crontab

    yum install -y vixie-cron
    yum install -y crontabs
    service crond start

### MySQL

    yum install -y mysql-server
    mysql_db_install
    mysql_secure_installation
    service mysqld start

### ntpdate

    yum install -y ntpdate rdate
    ntpdate 210.72.145.44

### nginx

[nginx配置详解][9]

### PHP

[编译PHP5.6][10]

    # 慎用
    yum install -y zabbix zabbix-get zabbix-server zabbix-web-mysql zabbix-web zabbix-agent 

打开错误：

    ini_set( 'display_errors', 'On' );

  [1]: http://www.centos.org/download/mirrors/
  [2]: http://mirrors.aliyun.com
  [3]: http://mirrors.163.com
  [4]: https://www.virtualbox.org
  [5]: https://blog.phpgao.com/centos_minimal.html
  [6]: https://blog.phpgao.com/centos7.html
  [7]: https://blog.phpgao.com/centos_optimize.html
  [8]: https://blog.phpgao.com/centos_lite.html
  [9]: https://blog.phpgao.com/nginx_conf.html
  [10]: https://blog.phpgao.com/compile_php.html