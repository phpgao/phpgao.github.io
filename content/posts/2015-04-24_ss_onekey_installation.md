---
title: "shadowsocks一键安装脚本"
categories: [ "服务器技术" ]
tags: [ "install","shadowsocks","vim" ]
draft: false
slug: "ss_onekey_installation"
date: "2015-04-24 16:54:00"
url: "ss_onekey_installation.html"
---

摘自**搬瓦工**后台，脚本简洁明了，请自取。

一键安装需求：

centos6 + root用户。

<!--more-->


请将下面一段代码保存为 `ss.sh`，并上传至/tmp目录。

然后在终端执行`sh /tmp/ss.sh`即可！。

```bash
yum -y install python-setuptools
easy_install pip
pip install shadowsocks
echo 443 > /root/.kiwivm-shadowsocks-port
echo rc4-md5 > /root/.kiwivm-shadowsocks-encryption
date +%s%N | md5sum | base64 | head -c 10 > /root/.kiwivm-shadowsocks-password
echo '' >> /etc/rc.d/rc.local
echo '/usr/bin/ssserver -p `cat /root/.kiwivm-shadowsocks-port` -k `cat /root/.kiwivm-shadowsocks-password` -m `cat /root/.kiwivm-shadowsocks-encryption` --user nobody --workers 2 -d start' >> /etc/rc.d/rc.local
echo 'Installation complete!'
echo 'Running shadowsocks!'
/usr/bin/ssserver -p `cat /root/.kiwivm-shadowsocks-port` -k `cat /root/.kiwivm-shadowsocks-password` -m `cat /root/.kiwivm-shadowsocks-encryption` --user nobody --workers 2 -d start
```

```
cd ~ && wget -O - "https://bootstrap.pypa.io/get-pip.py" | python
pip install shadowsocks
echo 'ssserver -p xxx -k xxxxx -m rc4-md5 --workers 10 --pid-file /tmp/ss.pid --log-file /tmp/ss.log --user nobody  -v -d start' >> /etc/rc.d/rc.local

```


**下面是后台源码**

```bash
mkdir -p '/tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b'
if [ ! -d "/root/.kiwivm-task" ]; then
    mkdir "/root/.kiwivm-task"
fi
PIDFILE=/root/.kiwivm-task/pid
if [ -e ${PIDFILE} ] && kill -0 `cat ${PIDFILE}`; then
    echo "Another task is already running, PID: `cat ${PIDFILE}`"
    exit
fi
trap "rm -f ${PIDFILE}; rm -f /tmp/kiwivm_task_file_d1106540378a9f630a58cdab4678c5c4e1320097; exit" INT KILL TERM EXIT
echo $$ > ${PIDFILE}
rm -f /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt
touch /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt
echo '**************************************************************' >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt
echo 'KiwiVM Task File, executed: Tue, 21 Apr 2015 21:00:03 -0400'  >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt
echo '**************************************************************'  >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt
echo '' > /tmp/.kiwivm-task/index.html
UNAME_M=`uname -m` >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
if [ ${UNAME_M} == 'x86_64' ]; then >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
ARCH='x86_64' >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
else >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
ARCH='i686' >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
fi >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
OS=`cat /etc/redhat-release  | awk '{print $1}'` >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
if [ "$OS" != "CentOS" ]; then >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
echo "Detected OS: $OS. Sorry, this script requires CentOS 6."; >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
echo '*** End of transmission ***' >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
exit; >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
fi >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
OSVER=`cat /etc/redhat-release  | awk '{print $3}' | awk -F . '{print $1}'` >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
if [ "$OSVER" != "6" ]; then >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
echo "Detected OS: $OS $OSVER. Sorry, this script requires CentOS 6."; >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
echo '*** End of transmission ***' >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
exit; >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
fi >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
echo "OS: $OS $OSVER $ARCH" >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
yum -y install python-setuptools >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
easy_install pip >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
pip install shadowsocks >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
echo 443 > /root/.kiwivm-shadowsocks-port
echo rc4-md5 > /root/.kiwivm-shadowsocks-encryption
date +%s%N | md5sum | base64 | head -c 10 > /root/.kiwivm-shadowsocks-password
echo '' >> /etc/rc.d/rc.local
echo '/usr/bin/ssserver -p `cat /root/.kiwivm-shadowsocks-port` -k `cat /root/.kiwivm-shadowsocks-password` -m `cat /root/.kiwivm-shadowsocks-encryption` --user nobody --workers 2 -d start' >> /etc/rc.d/rc.local
/usr/bin/ssserver -p `cat /root/.kiwivm-shadowsocks-port` -k `cat /root/.kiwivm-shadowsocks-password` -m `cat /root/.kiwivm-shadowsocks-encryption` --user nobody --workers 2 -d start >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
echo '' >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
echo '******************************************************************' >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
echo '* Completed.                                                     *' >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
echo '******************************************************************' >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
rm -f ${PIDFILE}
echo '*** End of transmission ***' >> /tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt 2>&1
sleep 30
cp '/tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b/logfile.txt' '/root/.kiwivm-task/taskfile-result-1429664403-7740.log'
rm -f '/tmp/kiwivm_task_file_d1106540378a9f630a58cdab4678c5c4e1320097'
rm -rf '/tmp/.kiwivm-task/ede0bee507b593659fe522d89761488de0f1708b'
```