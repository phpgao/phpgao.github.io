---
title: "centos6-优化脚本与安全脚本"
categories: [ "服务器技术" ]
tags: [ "centos","shell","optimize" ]
draft: false
slug: "centos_optimize"
date: "2014-05-13 01:54:00"
url: "centos_optimize.html"
---

新装的机子执行一下，请酌情使用，出问题后果自负。

## 优化设置

This script is only for 64bit Operating System !
64位系统专用，老高有修改

```shell
#!/bin/bash
#author suzezhi
#this script is only for CentOS 6
#check the OS

platform=`uname -i`
if [ $platform != "x86_64" ];then
echo "this script is only for 64bit Operating System !"
exit 1
fi
echo "the platform is ok"
version=`lsb_release -r |awk '{print substr($2,1,1)}'`
if [ $version != 6 ];then
echo "this script is only for CentOS 6 !"
exit 1
fi
cat << EOF
+---------------------------------------+
|   your system is CentOS 6 x86_64      |
|      start optimizing.......          |
+---------------------------------------
EOF

#make the 163.com as the default yum repo

#rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-7.noarch.rpm
#rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm

yum install iotop lsof wget ntpdate crontabs -y

#mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
#wget http://mirrors.163.com/.help/CentOS6-Base-163.repo -O /etc/yum.repos.d/CentOS-Base.repo

#add the third-party repo
#add the epel
rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6

#add the rpmforge
rpm -Uvh http://packages.sw.be/rpmforge-release/rpmforge-release-0.5.2-2.el6.rf.x86_64.rpm
rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-rpmforge-dag

rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm

#update the system and set the ntp
yum clean all
yum -y update glibc\*
yum -y update yum\* rpm\* python\*
yum -y update
yum -y install ntp
echo "* 4 * * * /usr/sbin/ntpdate 210.72.145.44 > /dev/null 2>&1" >> /var/spool/cron/root
service crond restart

#set the file limit
echo "ulimit -SHn 102400" >> /etc/rc.local
cat >> /etc/security/limits.conf << EOF
*           soft   nofile       65535
*           hard   nofile       65535
EOF

#set the control-alt-delete to guard against the miSUSE
sed -i 's#exec /sbin/shutdown -r now#\#exec /sbin/shutdown -r now#' /etc/init/control-alt-delete.conf

#disable selinux
sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config

#set ssh
sed -i 's/^GSSAPIAuthentication yes$/GSSAPIAuthentication no/' /etc/ssh/sshd_config
sed -i 's/#UseDNS yes/UseDNS no/' /etc/ssh/sshd_config
service sshd restart

#tune kernel parametres
cat >> /etc/sysctl.conf << EOF
net.ipv4.tcp_fin_timeout = 1
net.ipv4.tcp_keepalive_time = 1200
net.ipv4.tcp_mem = 94500000 915000000 927000000
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_timestamps = 0
net.ipv4.tcp_synack_retries = 1
net.ipv4.tcp_syn_retries = 1
net.ipv4.tcp_tw_recycle = 1
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216
net.core.netdev_max_backlog = 262144
net.core.somaxconn = 262144
net.ipv4.tcp_max_orphans = 3276800
net.ipv4.tcp_max_syn_backlog = 262144
net.core.wmem_default = 8388608
net.core.rmem_default = 8388608
EOF
/sbin/sysctl -p

#define the backspace button can erase the last character typed
#echo 'stty erase ^H' >> /etc/profile
#echo "syntax on" >> /root/.vimrc

#stop some crontab
mkdir /etc/cron.daily.bak
mv /etc/cron.daily/makewhatis.cron /etc/cron.daily.bak
mv /etc/cron.daily/mlocate.cron /etc/cron.daily.bak
chkconfig bluetooth off
chkconfig cups off
chkconfig ip6tables off
chkconfig iptables off
chkconfig fcoe off
chkconfig iscsi off
chkconfig iscsid off
chkconfig lldpad off
chkconfig nfslock off
chkconfig nfs off
chkconfig rpcbind off
chkconfig rpcgssd off
chkconfig rpcidmapd off
#disable the ipv6
cat > /etc/modprobe.d/ipv6.conf << EOFI
alias net-pf-10 off
options ipv6 disable=1
EOFI
echo "NETWORKING_IPV6=off" >> /etc/sysconfig/network
cat << EOF
+-------------------------------------------------+
|               optimizer is done                 |
|   it's recommond to restart this server !       |
+-------------------------------------------------+
EOF

cp -f /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

对脚本的内容做一下说明： 

1.  先对系统进行判断，如果是Cent OS 64位，就继续运行。
2.  先将系统的安装源设置为网易的（网易的安装源算是国内比较稳定的）
3.  安装epel的源和rpmforge的源，利用第三方的源来让yum安装起来更方便
4.  更新软件
5.  设置为每天凌晨四点进行时间同步（跟国家授时中心的服务器进行时间同步）
6.  将系统同时打开的文件个数增大
7.  将ctrl alt delete键进行屏蔽，防止误操作的时候服务器重启
8.  关闭selinux
9.  禁用GSSAPI来认证，也禁用DNS反向解析，加快SSH登陆速度
10. 优化一些内核参数
11. 调整删除字符的按键为backspace（某些系统默认是delete）
12. 打开vim的语法高亮
13. 取消生成whatis数据库和locate数据库
14. 关闭没用的服务
15. 关闭IPv6   


## 安全设置

```
#!/bin/bash

echo "#####################################"
echo "#####Centos Sytem Security Shell#####"
echo "#####################################"

time=`date  "+%Y-%m-%d"`

#1#######Lock Useless Users#########
for i in adm lp sync shutdown halt news uucp operator games gopher ftp
do
usermod -L $i
done

#2######Close Useless Services########
for i in nfs postfix ypbind portmap smb netfs lpd snmpd named squid xinetd apmd autofs cups isdn nfslock pcmcia sendmail xinetd
do
chkconfig --level 2345 $i off
done

#3#####Directory And File Limit#######
chattr +i /etc/passwd /etc/shadow /etc/group /etc/gshadow
# chattr -i /etc/passwd /etc/shadow /etc/group /etc/gshadow

chmod -R 700 /etc/rc.d/init.d/*
chmod 644 /var/log/wtmp /var/run/utmp

#4#####Clean System Banner###########
cp /etc/issue /etc/issue.$time
cp /etc/issue.net /etc/issue.net.$time
echo "" > /etc/issue
echo "" > /etc/issue.net

#5####Alter login.defs###############
cp /etc/login.defs /etc/login.defs.$time
egrep -v "^#|^$" /etc/login.defs|grep PASS_MIN_LEN
if [ $? = 0 ] ;then
egrep -v "^#|^$" /etc/login.defs|awk '/PASS_MIN_LEN/{print "PASS_MIN_LEN 12";next};{print}' >/etc/login.defs.tmp
mv -f /etc/login.defs.tmp /etc/login.defs
else
echo "PASS_MIN_LEN 12" >>/etc/login.defs
fi
egrep -v "^#|^$" /etc/login.defs|grep PASS_MAX_DAYS
if [ $? = 0 ] ;then
egrep -v "^#|^$" /etc/login.defs|awk '/PASS_MAX_DAYS/{print "PASS_MAX_DAYS 90";next};{print}' >/etc/login.defs.tmp
mv -f /etc/login.defs.tmp /etc/login.defs
else
echo "PASS_MAX_DAYS 90" >>/etc/login.defs
fi

#6####Alter profile###############
cp /etc/profile /etc/profile.$time
egrep -v "^#|^$" /etc/profile|grep TMOUT
if [ $? = 0 ] ;then
egrep -v "^#|^$" /etc/profile|awk '/TMOUT/{print "TMOUT=300;export TMOUT";next};{print}' >/etc/profile.tmp
mv -f /etc/profile.tmp /etc/profile
else
echo "TMOUT=300;export TMOUT" >>/etc/profile
fi

#7####Alter inittab###############
cp /etc/inittab /etc/inittab.bak$time
egrep -v "^#|^$" /etc/inittab|grep ca::ctrlaltdel
if [ $? = 0 ] ;then
egrep -v "^#|^$" /etc/inittab|awk '/^ca::ctrlaltdel/ {sub($1,"#"$1,$0);next};{print}' >/etc/inittab.tmp
mv -f /etc/inittab.tmp /etc/inittab
else
echo -e "#ca::ctrlaltdel:/sbin/shutdown\t-t3\t-r\tnow" >>/etc/inittab
fi

#8####Alter syslog###############
cp /etc/syslog.conf /etc/syslog.conf.$time
egrep -v "^#|^$" /etc/syslog.conf|egrep "\*\.err"
if [ $? != 0 ] ;then
echo "*.err        /var/adm/messages" >>/etc/syslog.conf
fi
egrep -v "^#|^$" /etc/syslog.conf|egrep "\*\.info"
if [ $? != 0 ] ;then
echo "*.info        /var/adm/messages" >>/etc/syslog.conf
fi
egrep -v "^#|^$" /etc/syslog.conf|egrep "\*\.emerg"
if [ $? != 0 ] ;then
echo "*.emerg        /var/adm/messages" >>/etc/syslog.conf
fi
egrep -v "^#|^$" /etc/syslog.conf|egrep "local7\.\*"
if [ $? != 0 ] ;then
echo "local7.*        /var/adm/messages" >>/etc/syslog.conf
fi
egrep -v "^#|^$" /etc/syslog.conf|egrep "kern\.debug"
if [ $? != 0 ] ;then
echo "kern.debug       /var/adm/messages" >>/etc/syslog.conf
fi
egrep -v "^#|^$" /etc/syslog.conf|egrep "kern\.warning"
if [ $? != 0 ] ;then
echo "kern.warning       /var/adm/messages" >>/etc/syslog.conf
fi
egrep -v "^#|^$" /etc/syslog.conf|egrep "authpriv\.none"
if [ $? != 0 ] ;then
echo "authpriv.none       /var/adm/messages" >>/etc/syslog.conf
fi
egrep -v "^#|^$" /etc/syslog.conf|egrep "mail\.none"
if [ $? != 0 ] ;then
echo "mail.none       /var/adm/messages" >>/etc/syslog.conf
fi
egrep -v "^#|^$" /etc/syslog.conf|egrep "daemon\.notice"
if [ $? != 0 ] ;then
echo "daemon.notice        /var/adm/messages" >>/etc/syslog.conf
fi
egrep -v "^#|^$" /etc/syslog.conf|egrep "cron\.\*"
if [ $? != 0 ] ;then
echo "cron.*      /var/log/cron" >>/etc/syslog.conf
fi
/etc/rc.d/init.d/syslog restart

#9####Alter host.conf###############
cp /etc/host.conf /etc/host.conf.$time
echo -e "order\tbind,hosts\nmulti\ton\nnospoof\ton" >/etc/host.conf

#10####Alter limits.conf###############
cp /etc/security/limits.conf /etc/security/limits.conf.$time
egrep -v "^#|^$" /etc/security/limits.conf|egrep "\*[[:space:]]soft[[:space:]]core[[:space:]]0"
if [ $? != 0 ] ;then
echo -e "*\tsoft\tcore\t0" >> /etc/security/limits.conf
fi
egrep -v "^#|^$" /etc/security/limits.conf|egrep "\*[[:space:]]hard[[:space:]]core[[:space:]]0"
if [ $? != 0 ] ;then
echo -e "*\thard\tcore\t0" >> /etc/security/limits.conf
fi
egrep -v "^#|^$" /etc/security/limits.conf|egrep "\*[[:space:]]hard[[:space:]]rss[[:space:]]5000"
if [ $? != 0 ] ;then
echo -e "*\thard\trss\t5000" >> /etc/security/limits.conf
fi
egrep -v "^#|^$" /etc/security/limits.conf|egrep "\*[[:space:]]hard[[:space:]]nproc[[:space:]]20"
if [ $? != 0 ] ;then
echo -e "*\thard\tnproc\t20" >> /etc/security/limits.conf
fi

#11####Alter pam_login###############
cp /etc/pam.d/login /etc/pam.d/login.$time
egrep -v "^#|^$" /etc/pam.d/login|egrep "^session[[:space:]]required[[:space:]]/lib/security/pam_limits.so"
if [ $? != 0 ] ;then
echo -e "session\trequired\t/lib/security/pam_limits.so" >> /etc/pam.d/login
fi

#12####Alter sshd_config###############
cp /etc/ssh/sshd_config /etc/ssh/sshd_config.$time
egrep -v "^#|^$" /etc/ssh/sshd_config|egrep "^Protocol[[:space:]]2"
if [ $? != 0 ] ;then
echo -e "Protocol 2" >>/etc/ssh/sshd_config
fi

#13####Alter sysctl.conf###############
cp /etc/sysctl.conf /etc/sysctl.conf.$time
egrep -v "^#|^$" /etc/sysctl.conf|egrep "net\.ipv4\.tcp_max_syn_backlog"
if [ $? != 0 ] ;then
echo "net.ipv4.tcp_max_syn_backlog=4096" >>/etc/sysctl.conf
fi
egrep -v "^#|^$" /etc/sysctl.conf|egrep "net\.ipv4\.conf_all.rp_filter"
if [ $? != 0 ] ;then
echo "net.ipv4.conf_all.rp_filter=1" >>/etc/sysctl.conf
fi
egrep -v "^#|^$" /etc/sysctl.conf|egrep "net\.ipv4\.tcp_syncookies"
if [ $? != 0 ] ;then
echo "net.ipv4.tcp_syncookies=1" >>/etc/sysctl.conf
fi
egrep -v "^#|^$" /etc/sysctl.conf|egrep "net\.ipv4\.conf\.all\.send_redirects"
if [ $? != 0 ] ;then
echo "net.ipv4.conf.all.send_redirects=0" >>/etc/sysctl.conf
fi
egrep -v "^#|^$" /etc/sysctl.conf|egrep "net\.ipv4\.conf\.all\.accept_redirects"
if [ $? != 0 ] ;then
echo "net.ipv4.conf.all.accept_redirects=0" >>/etc/sysctl.conf
fi
egrep -v "^#|^$" /etc/sysctl.conf|egrep "net\.ipv4\.ip_forward"
if [ $? != 0 ] ;then
echo "net.ipv4.ip_forward=0" >>/etc/sysctl.conf
fi
egrep -v "^#|^$" /etc/sysctl.conf|egrep "net\.ipv4\.conf\.all\.accept_source_route"
if [ $? != 0 ] ;then
echo "net.ipv4.conf.all.accept_source_route=0" >>/etc/sysctl.conf
fi
egrep -v "^#|^$" /etc/sysctl.conf|egrep "net\.ipv4\.conf\.default\.accept_redirects"
if [ $? != 0 ] ;then
echo "net.ipv4.conf.default.accept_redirects=0" >>/etc/sysctl.conf
fi
egrep -v "^#|^$" /etc/sysctl.conf|egrep "net\.ipv4\.conf\.default\.send_redirects"
if [ $? != 0 ] ;then
echo "net.ipv4.conf.default.send_redirects=0" >>/etc/sysctl.conf
fi
egrep -v "^#|^$" /etc/sysctl.conf|egrep "net\.ipv4\.icmp_echo_ignore_broadcasts"
if [ $? != 0 ] ;then
echo "net.ipv4.icmp_echo_ignore_broadcasts=1" >>/etc/sysctl.conf
fi
sysctl -p /etc/sysctl.conf
chown root:root /etc/sysctl.conf
chmod 600 /etc/sysctl.conf
```

脚本转自：

http://www.linuxde.net/2011/12/5756.html
http://www.linuxpad.cn/linux/centos-sytem-security-shell.html