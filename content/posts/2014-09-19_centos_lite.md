---
title: "CentOS精简命令"
categories: [ "性能优化","服务器技术" ]
tags: [ "centos","optimize" ]
draft: false
slug: "centos_lite"
date: "2014-09-19 05:22:00"
url: "centos_lite.html"
---

删除自带web、数据库、php

```bash
yum remove httpd* php* mysql-server mysql* php-mysql -y
```
删除不需要的程序

    yum remove Deployment_Guide-en-US finger cups-libs cups ypbind bluez-libs desktop-file-utils ppp rp-pppoe wireless-tools irda-utils sendmail* samba* talk-server finger-server bind* xinetd nfs-utils nfs-utils-lib rdate fetchmail eject ksh mkbootdisk mtools syslinux tcsh startup-notification talk apmd rmt dump setserial portmap yp-tools -y
    yum groupremove "Mail Server" "Games and Entertainment" "X Window System" "X Software Development" "Development Libraries" "Dialup Networking Support" "Games and Entertainment" "Sound and Video" "Graphics" "Editors" "Text-based Internet" "GNOME Desktop Environment" "GNOME Software Development" -y
    yum -y groupremove "FTP Server" "PostgreSQL Database client" "PostgreSQL Database server" "MySQL Database server" "MySQL Database client" "Web Server" "Office Suite and Productivity" "E-mail server" "Ruby Support" "Printing client"

关闭selinux

    setenforce 0
    sed -i 's/^SELINUX=.*$/SELINUX=disabled/' /etc/selinux/config

对系统进行一些优化

    sed -i 's/^id:.*$/id:3:initdefault:/' /etc/inittab
    init q
    [ -z "`cat ~/.bashrc | grep ^PS1`" ] && echo 'PS1="[e[37;40m][[e[32;40m]u[e[37;40m]@h [e[35;40m]W[e[0m]]\$ "' >> ~/.bashrc
    sed -i 's/^HISTSIZE=.*$/HISTSIZE=10/' /etc/profile
    [ -z "`cat ~/.bashrc | grep history-timestamp`" ] && echo "export PROMPT_COMMAND='{ msg=$(history 1 | { read x y; echo $y; });user=$(whoami); echo $(date "+%Y-%m-%d %H:%M:%S"):$user:`pwd`/:$msg ---- $(who am i); } >> /tmp/`hostname`.`whoami`.history-timestamp'" >> ~/.bashrc
    [ -z "`cat /etc/security/limits.conf | grep 'nproc 65535'`" ] && cat >> /etc/security/limits.conf <<EOF
    * soft nproc 65535
    * hard nproc 65535
    * soft nofile 65535
    * hard nofile 65535
    EOF
    [ -z "`cat /etc/rc.local | grep 'ulimit -SH 65535'`" ] && echo "ulimit -SH 65535" >> /etc/rc.local
    [ "$(hostname -i | awk '{print $1}')" != "127.0.0.1" ] && sed -i "s@^127.0.0.1(.*)@127.0.0.1   `hostname` 1@" /etc/hosts
    [ -z "`cat /etc/pam.d/system-auth | grep 'pam_tally2.so'`" ] && sed -i '4a auth        required      pam_tally2.so deny=5 unlock_time=180' /etc/pam.d/system-auth

输入vi自动打开vim

    [ -z "`cat ~/.bashrc | grep 'alias vi='`" ] && sed -i "s@alias mv=(.*)@alias mv=1nalias vi=vim@" ~/.bashrc && echo 'syntax on' >> /etc/vimrc

升级系统

    yum -y update
    yum clean all

美化命令行，其实在之前优化那里就美化了，这里只是让它生效而已

    . /etc/profile
    . ~/.bashrc

删除不需要的服务，这里自行考虑，反正我是只保留几个的~

    service modules_dep stop
    chkconfig modules_dep off
    chkconfig --del modules_dep
    service netconsole stop
    chkconfig netconsole off
    chkconfig --del netconsole
    service netfs stop
    chkconfig netfs off
    chkconfig --del netfs
    service nscd stop
    chkconfig nscd off
    chkconfig --del nscd
    service quota_nld stop
    chkconfig quota_nld off
    chkconfig --del quota_nld
    service rdisc stop
    chkconfig rdisc off
    chkconfig --del rdisc
    service restorecon stopd
    chkconfig restorecond off
    chkconfig --del restorecond
    service saslauthd stop
    chkconfig saslauthd off
    chkconfig --del saslauthd
    service snmpd stop
    chkconfig snmpd off
    chkconfig --del snmpd
    service snmptrapd stop
    chkconfig snmptrapd off
    chkconfig --del snmptrapd
    service gpm stop
    chkconfig gpm off
    chkconfig --del gpm
    service iscsi stop
    chkconfig iscsi off
    chkconfig --del iscsi
    service iscsid stop
    chkconfig iscsid off
    chkconfig --del iscsid
    service lm_sensors stop
    chkconfig lm_sensors off
    chkconfig --del lm_sensors
    service lvm2-monitor stop
    chkconfig lvm2-monitor off
    chkconfig --del lvm2-monitor
    service mcstrans stop
    chkconfig mcstrans off
    chkconfig --del mcstrans
    service messagebus stop
    chkconfig messagebus off
    chkconfig --del messagebus
    service multipathd stop
    chkconfig multipathd off
    chkconfig --del multipathd
    service netconsole stop
    chkconfig netconsole off
    chkconfig --del netconsole
    service netfs stop
    chkconfig netfs off
    chkconfig --del netfs
    service netplugd stop
    chkconfig netplugd off
    chkconfig --del netplugd
    service nscd stop
    chkconfig nscd off
    chkconfig --del nscd
    service rawdevices stop
    chkconfig rawdevices off
    chkconfig --del rawdevices
    service rdisc stop
    chkconfig rdisc off
    chkconfig --del rdisc
    service restorecond stop
    chkconfig restorecond off
    chkconfig --del restorecond
    service ntpd stop
    chkconfig ntpd off
    chkconfig --del ntpd
    service ip6tables stop
    chkconfig ip6tables off
    chkconfig --del ip6tables
    service ntpdate stop
    chkconfig ntpdate off
    chkconfig --del ntpdate
    service portreserve stop
    chkconfig portreserve off
    chkconfig --del portreserve
    service udev-post stop
    chkconfig udev-post off
    chkconfig --del udev-post
    service exim stop
    chkconfig exim off
    chkconfig --del exim

转自[http://shuang.ca/centos-optimizer/][1]


  [1]: http://shuang.ca/centos-optimizer/