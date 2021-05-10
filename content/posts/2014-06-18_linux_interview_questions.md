---
title: "Linux经典面试题"
categories: [ "服务器技术" ]
tags: [ "Linux","interview" ]
draft: false
slug: "linux_interview_questions"
date: "2014-06-18 02:41:00"
url: "linux_interview_questions.html"
---

1. Linux挂载Winodws共享文件夹

2. 查看http的并发请求数及其TCP连接状态：

3. 用tcpdump嗅探80端口的访问看看谁最高

4. 统计/var/log/下文件个数

5. 查看当前系统每IP连接数

6. shell下32位随机密码生成

7. 统计出apache的access.log中访问量最多的5个IP

8. 如何查看二进制文件的内容

9. ps aux 中VSZ代表什么意思 RSS代表什么

10. 检测并修复/dev/hda5

11. Linux开机启动顺序

12. 符号链接和硬链接的区别

13. 保存当前磁盘分区的分区表

14. 手动安装grub

15. 改内核参数

16. 在1-39内取随机数

17. 限定apache每秒钟连接数为1,峰值为3

18. FTP主动与被动模式

19. 显示/etc/inittab中以#开头,且后面跟一个或多个空白字符,而后又跟了任意非空白字符的行;

20. 显示/etc/inittab中包含了:一个数字:(即两个冒号中间一个数字)的行;

21. 怎么把自己写的脚本添加到服务里面，即可以使用service命令来调用

22. 写一个脚本，实现批量添加20个用户，用户名为user1-20，密码为user后面跟5个随机字符

23. 写一个脚本，实现判断192.168.1.0/24网络里，当前在线的IP有哪些，能ping通则认为在线

24. 写一个脚本，判断一个指定的脚本是否是语法错误；如果有错误，则提醒用户键入Q或者q无视错误并退出其它任
何键可以通过vim打开这个指定的脚本；

25. 写一个脚本：创建一个函数，能接受两个参数：
        1. 第一个参数为URL，即可下载的文件；第二个参数为目录，即下载后保存的位置；
        2. 如果用户给的目录不存在，则提示用户是否创建；如果创建就继续执行，否则，函数返回一个51的错误值给调用脚
        本；
        3. 如果给的目录存在，则下载文件；下载命令执行结束后测试文件下载成功与否；如果成功，则返回0给调用脚本，
则，返回52给调用脚本；

27. 写一个脚本：
    1. 创建一个函数，可以接受一个磁盘设备路径（如/dev/sdb）作为参数;在真正开始后面步骤之前提醒用户有危险，
并让用户选择是否继续；
而后将此磁盘设备上的所有分区清空（提示，使用命令dd if=/dev/zero of=/dev/sdb bs=512 count=1实现，注意
其中的设备路径不要写错了；
如果此步骤失败，返回67给主程序；
接着在此磁盘设备上创建两个主分区，一个大小为100M，一个大小为1G；如果此步骤失败，返回68给主程序；
格式化此两分区，文件系统类型为ext3；如果此步骤失败，返回69给主程序；
如果上述过程都正常，返回0给主程序；

    2. 调用此函数；并通过接收函数执行的返回值来判断其执行情况，并将信息显示出来；
    
    
答案
    
    1、Linux挂载Winodws共享文件夹
    
    # mount -t cifs //1.1.1.254/ok /var/tmp/share/ -o username=administrator,password=yourpass
    
    2、查看http的并发请求数及其TCP连接状态：
    
    netstat -n | awk ‘/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}’
    
    3.用tcpdump嗅探80端口的访问看看谁最高
    
    tcpdump -i eth0 -tnn dst port 80 -c 1000 | awk -F”.” ‘{print $1″.”$2″.”$3″.”$4}’ | sort | uniq -c | sort-nr |head -20
    
    4、统计指定目录的文件个数
    
    find / -type f | wc -l
    
    5、查看IP连接数
    
    netstat -n | awk ‘/^tcp/ {print $5}’| awk -F: ‘{print $1}’ | sort | uniq -c | sort -rn
    
    6、【shell下32位随机密码生成】
    
    tr -dc \~
    \!@#\$\%\^\&\*\(\)\-\_\+\=\|\\\?\/\.\>\,\<a-za-z0-9_ <=”" dev=”" urandom|head=”" -c=”" 32|xargs
    
    7、统计出apache的access.log中访问量最多的5个IP
    
    cat access_log_2011_06_26.log |awk ‘{print $1}’|uniq -c|sort -n ｜head 5
    
    8、如何查看二进制文件的内容
    
    [root@new55 ~]# echo /etc/passwd | hexdump -C <== 规范的十六进制和ASCII码显示（Canonical hex+ASCII display ）
    
    9、ps aux 中VSZ代表什么意思 RSS代表什么
    
    aux
    
    a 显示所有与终端相关的进程,由终端发起的.
    
    x 显示所有与终端无关的进程.
    
    u 显示用户导向的用户列表.
    
    VSZ 虚拟内存集,进程占用的虚拟内存空间
    
    RSS 物理内存集,进程战用实际物理内存空间.
    
    S 可中断的睡眠态
    
    R 运行态
    
    D 不可中断的睡眠态
    
    T 停止态
    
    Z 僵死态
    
    10、检测并修复/dev/hda5
    
    e2fsck -p /dev/hda5
    
    11、Linux开机启动顺序
    
    启动顺序:
    
    POST(加电自检)–>由BIOS决定启动设备次序–>MBR(bootloader)–>解压kernel,加载Kernel到内存–>加载init进程(/etc/inittab)
    
    12、符号链接和硬链接的区别
    
    硬链接:不同路径的文件指定的是同一个inode
    
    硬链接不能跨分区存在
    
    硬链接不能链接到目录(要避免循环引用)
    
    删除硬链接只会减少文件被硬链接的次数,源文件不会动.
    
    软链接:是一个独立的文件,有自己独立的inode
    
    引用的是路径本身,不是inode
    
    删除软链接,跟源文件没任何关系
    
    软链接没有存储任何数据,只是存储了访问文件的另一种路径.
    
    软链接可以对目录创建,也可以跨分区
    
    源文件被删除,软连接将不可用.
    
    13、保存当前磁盘分区的分区表
    
    # sfdisk -d /dev/sdb >/etc/sdbpar.bak 保存分区表
    
    # sfdisk /dev/sdb
    
    14、检测并自动修复文件系统
    
    e2fsck – check a Linux ext2/ext3 file system
    
    -B 指定块大小
    
    -f 强制检测.
    
    -p 自动修复文件系统
    
    15、手动安装grub
    
    手动安装grub
    
    # grub-install –root-directory=(指定boot目录) DEVICE
    
    或者
    
    grub
    
    grub>root (hd0,0)
    
    grub>setup (hd0)
    
    16、改内核参数
    
    # sysctl
    
    -p 不用重启系统,让内核重读/etc/sysctl.conf文件
    
    -a 显示所有内核参数及其正在使用的值.
    
    -w 临时设置/etc/sysctl.conf参数 sysctl -w net.ipv4.ip_froward=0
    
    17、在指定范围内取随机数
    
    在指定范围内取随机
    
    echo $[$RANDOM%39] 对39进行取余,余数肯定是39以内的数字.
    
    18、限定apache每秒钟连接数为1,峰值为3
    
    # iptables -A INPUT -d 172.16.100.1 -p tcp –dport 80 -m limit –limit 1/second –limit-burst 3 -j ACCEPT
    
    19、FTP主动与被动模式
    
    主动模式
    
    1. 任何大于1024的端口到FTP服务器的21端口。（客户端初始化的连接）
    
    2. FTP服务器的21端口到大于1024的端口。 （服务器响应客户端的控制端口）
    
    3. FTP服务器的20端口到大于1024的端口。（服务器端初始化数据连接到客户端的数据端口）
    
    4. 大于1024端口到FTP服务器的20端口（客户端发送ACK响应到服务器的数据端口）
    
    被动模式
    
    1. 从任何大于1024的端口到服务器的21端口（客户端初始化的连接）
    
    2. 服务器的21端口到任何大于1024的端口（服务器响应到客户端的控制端口的连接）
    
    3. 从任何大于1024端口到服务器的大于1024端口（客户端初始化数据连接到服务器指定的任意端口）
    
    4. 服务器的大于1024端口到远程的大于1024的端口（服务器发送ACK响应和数据到客户端的数据端口）
    客户端与服务端建立连接，经过TCP三次握手，连接建立完成之后，客户端就可以通过建立的连接通道向服务器发送命令了，服务器根据客户端的命令，向客户端发送数据，发送数据的时候，服务端会再跟客户端建立一个TCP连接，用于专门的传输数据，这个连接就是数据连接。
    
    数据连接有两种工作模式：主动模式和被动模式
    
    主动模式指的是服务器是主动的
    
    被动模式指的是服务器是被动的
    
    主动模式：
    
    服务器端自动打开20号端口，即数据传输端口。主动的去连接客户端的命令端口+1的端口。当数据传输完成后，数据连接自动断开。
    
    被动模式：
    
    服务器端向客户端通知自己打开的端口，通知客户端来连接自己的数据端口，客户端使用命令端口+1的端口，去连接服务器端的数据端口。被动模式下，服务器的数据连接端口使用的是一个随机端口。主动模式服务端使用的是20端口。
    
    20、显示/etc/inittab中以#开头,且后面跟一个或多个空白字符,而后又跟了任意非空白字符的行;
    
    # grep “^\#[[:space:]]\{1,\}.\{1,\}” /etc/inittab
    
    21、显示/etc/inittab中包含了:一个数字:(即两个冒号中间一个数字)的行;
    
    # grep “\:[0-9]\{1\}\:” /etc/inittab
    
    22、怎么把自己写的脚本添加到服务里面，即可以使用service命令来调用
    
    #!/bin/bash
    
    # chkconfig: – 90 10
    
    # description: just a test
    
    echo “Hello,$1″
    
    # mv test /etc/init.d/
    
    # chmod +x /etc/init.d/test
    
    # chkconfig –add test
    
    # service test start
    
    //可以看到Hello,start表示成功
    
    23、写一个脚本，实现批量添加20个用户，用户名为user1-20，密码为user后面跟5个随机字符
    
    [root@localhost tmp]# cat Useradd.sh
    
    #!/bin/bash
    
    #description: useradd
    
    for i in
    seq 1 20
    
    do
    
    pwd=$(cat /dev/urandom | head -1 | md5sum | head -c 5)
    
    useradd user$i
    
    echo “user$i$pwd” | passwd –stdin user$i
    
    echo user$i user$i$pwd” >> userinfo.txt
    
    done
    
    24、写一个脚本，实现判断192.168.1.0/24网络里，当前在线的IP有哪些，能ping通则认为在线
    
    #!/bin/bash
    
    for ip in
    seq 1 255
    
    do
    
    {
    
    ping -c 1 192.168.1.$ip > /dev/null 2>&1
    
    if [ $? -eq 0 ]; then
    
    echo 192.168.1.$ip UP
    
    else
    
    echo 192.168.1.$ip DOWN
    
    fi
    
    }&
    
    done
    
    wait
    
    25、写一个脚本，判断一个指定的脚本是否是语法错误；如果有错误，则提醒用户键入Q或者q无视错误并退出其它任何键可以通过vim打开这个指定的脚本；
    
    [root@localhost tmp]# cat checksh.sh
    
    #!/bin/bash
    
    read -p “please input check script-> ” file
    
    if [ -f $file ]; then
    
    sh -n $file > /dev/null 2>&1
    
    if [ $? -ne 0 ]; then
    
    read -p “You input $file syntax error,[Type q to exit or Type vim to edit]” answer
    
    case $answer in
    
    q | Q)
    
    exit 0
    
    ;;
    
    vim)
    
    vim $file
    
    ;;
    
    *）
    
    3. exit 0
    
    4.
    
    ;;
    
    esac
    
    fi
    
    else
    
    echo “$file not exist”
    
    exit 1
    
    fi
    
    26、写一个脚本：
    
    1、创建一个函数，能接受两个参数：
    
    1)第一个参数为URL，即可下载的文件；第二个参数为目录，即下载后保存的位置；
    
    2)如果用户给的目录不存在，则提示用户是否创建；如果创建就继续执行，否则，函数返回一个51的错误值给调用脚本；
    
    3)如果给的目录存在，则下载文件；下载命令执行结束后测试文件下载成功与否；如果成功，则返回0给调用脚本，否则，返回52给调用脚本；
    
    [root@localhost tmp]# cat downfile.sh
    
    #!/bin/bash
    
    url=$1
    
    dir=$2
    
    download()
    
    {
    
    cd $dir >> /dev/null 2>&1
    
    if [ $? -ne 0 ];then
    
    read -p “$dir No such file or directory,create?(y/n)” answer
    
    if [ "$answer" == "y" ];then
    
    mkdir -p $dir
    
    cd $dir
    
    wget $url 1> /dev/null 2>&1
    
    if [ $? -ne 0 ]; then
    
    return “52″
    
    fi
    
    else
    
    return “51″
    
    fi
    
    fi
    
    }
    
    download $url $dir
    
    echo $?
    
    27、代码如下：
    
    #!/bin/bash
    
    #Author: MOS
    
    #Script name: funPart.sh
    
    #Date & Time: 2012-10-10/12:20:53
    
    #Version: 1.0.1
    
    #Description:
    
    # Disk_Mod(){
    
    #使用if匹配模式，使用for循环判断是否存在，如果有，则取其绝对路径，跳出循环，如果不匹配或文件不存在，跳出函数，返回66
    
    if [[ -n $1 && $1 =~ "/dev/sd[a-z]” ]];
    
    then local Darray=(
    ls /dev/sd[a-z]
    )
    
    for i in ${Darray[*]};do
    
    [[ "$i" == "$1" ]] && Sd=$i && break
    
    done
    
    else
    
    return 66
    
    fi
    
    #当匹配成功，进入选择，告诉用户，是否继续，输错的话进入无限循环，当用户选择Y,则清空目标分区，且跳出while循环
    
    while :;do
    
    read -p “Warning!!!This operation will clean $Sd data. Next=y,Quit=n [y|n]: ” Choice
    
    case $Choice in
    
    y)
    
    dd if=/dev/zero of=$Sd bs=512 count=1 &> /dev/null && break || return 67 ;; n)
    
    exit 88 ;;
    
    *)
    
    echo “Invalid choice,please choice again.” ;;
    
    esac
    
    done
    
    #使用echo传递给fdisk进行分区，如果此命令失败，则跳转出去，错误值68，需要注意的是，有时候这个返回值很诡异，笔者之前成功与否都是返回的1，后来重启之后，就好了，如果慎重的话，可以对创建的分区，进行判断，不过就需要使用其他工具截取相关字段了，虽有些小麻烦，但无大碍 echo -e “n\np\n1\n\n+100M\nn\np\n2\n\n+1024M\nw\n”|fdisk /dev/sdb &> /dev/null || return 68 #格式化之前，让内核重新读取磁盘分区表，值得注意的是，有的系统版本，使用partprobe无效，譬如笔者的环境是rhel5.8，而rhel6.0以后，这个命令就很危险了，而使用partx -a /dev/sdb则效果更好…此项需慎重，如果格式化失败，则告知把失败的分区定义成变量，且跳出函数，并带出错误值69
    
    partprobe
    Part=
    fdisk -l /dev/$Sd|tail -2|cut -d” ” -f1`
    
    for M in ${Part[*]};do
    
    ! mke2fs -j $M &> /dev/null && ErrorPart=$M && return 69
    
    done
    
    return 0
    
    }
    
    #下面代码，调用函数，接收函数返回值，根据返回值进行判断哪里出错。
    
    Disk_Mod $1
    
    Res=$?
    
    [ $Res -eq 0 ] && exit 0
    
    [ $Res -eq 66 ] && echo “Error! Invalid input.”
    
    [ $Res -eq 67 ] && echo “Error! Command -> dd <- Faild.”
    
    [ $Res -eq 68 ] && echo “Error! Command -> fdisk <- Faild.”
    
    [ $Res -eq 69 ] && echo “Error! Command -> mke2fs <- Faild.”