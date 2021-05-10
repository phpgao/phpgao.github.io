---
title: "使用vsftp需要用到的东西"
categories: [ "服务器技术" ]
tags: [ "vsftp" ]
draft: false
slug: "vsftp_conf"
date: "2014-10-08 08:32:00"
url: "vsftp_conf.html"
---

## 安装

    yum install -y vsftpd

## 安全配置

    vi /etc/vsftpd/vsftpd.conf
    #add
    listen_port= XXXX
    anonymous_enable=NO
    local_enable=NO
    chroot_local_user=YES
    chroot_list_enable=NO

## 日志配置

    xferlog_enable=YES
    xferlog_std_format=YES 
    xferlog_file=/var/log/xferlog   
    dual_log_enable=YES 
    vsftpd_log_file=/var/log/vsftpd.log  

## 添加虚拟用户

    vi /etc/vsftpd/user_list.txt
    #add
    roooooooter
    random_passwd
    #生成认证文件
    db_load -T -t hash -f /etc/vsftpd/user_list.txt /etc/vsftpd/user.db
    #删除明文
    rm -f vi /etc/vsftpd/user_list.txt
    #编辑vsftpd的PAM认证文件
    #删除其他行，加入下面两行
    #pam_userdb.so 为认证库文件 db即上文user.db
    vi /etc/pam.d/vsftpd
    auth required pam_userdb.so db=/etc/vsftpd/user
    account required pam_userdb.so db=/etc/vsftpd/user
    #设定FTP用户映射(注意权限)
    useradd –d /home/xxx –s /sbin/nologin xxx
    chmod 700 /home/xxx
    #开启虚拟用户
    vi /etc/vsftpd/vsftpd.conf
    #开启虚拟用户
    guest_enable=YES
    #FTP虚拟用户对应的系统用户
    guest_username=xxx
    #PAM认证文件
    pam_service_name=user
    #虚拟用户配置文件
    user_config_dir=/etc/vsftpd/vsftpd_user_conf
    
## 配置虚拟用户

    mkdir -p /etc/vsftpd/vsftpd_user_conf
    #xxx为虚拟用户名
    vi /etc/vsftpd/vsftpd_user_conf/xxx
    #设置权限
    local_root=/home/xxx
    write_enable=YES #开放markwang的写权限
    anon_world_readable_only=NO #开放markwang的下载权限
    anon_upload_enable=YES #开放markwang的上传权限
    anon_mkdir_write_enable=YES #开放markwang创建目录的权限
    anon_other_write_enable=YES #开放markwang删除和重命名的权限

## 部分配置说明

### virtual_use_local_privs

    当virtual_use_local_privs=YES时，虚拟用户和本地用户有相同的权限；
    当virtual_use_local_privs=NO时，虚拟用户和匿名用户有相同的权限，默认是NO。
     
    当virtual_use_local_privs=YES，write_enable=YES时，虚拟用户具有写权限（上传、下载、删除、重命名）。
     
    当virtual_use_local_privs=NO，write_enable=YES，anon_world_readable_only=YES，
    anon_upload_enable=YES时，虚拟用户不能浏览目录，只能上传文件，无其他权限。
     
    当virtual_use_local_privs=NO，write_enable=YES，anon_world_readable_only=NO，
    anon_upload_enable=NO时，虚拟用户只能下载文件，无其他权限。
     
    当virtual_use_local_privs=NO，write_enable=YES，anon_world_readable_only=NO，
    anon_upload_enable=YES时，虚拟用户只能上传和下载文件，无其他权限。
     
    当virtual_use_local_privs=NO，write_enable=YES，anon_world_readable_only=NO，
    anon_mkdir_write_enable=YES时，虚拟用户只能下载文件和创建文件夹，无其他权限。
     
    当virtual_use_local_privs=NO，write_enable=YES，anon_world_readable_only=NO，
    anon_other_write_enable=YES时，虚拟用户只能下载、删除和重命名文件，无其他权限。

### 其他

    anonymous_enable=YES            允许匿名登录local_enable=YES                  允许本地用户登录 
    write_enable=YES                      开放本地用户写权限 
    local_umask=022                        设置本地用户生成文件的掩码为022 
    #anon_upload_enable=YES          此项设置允许匿名用户上传文件 
    #anon_mkdir_write_enable=YES  开启匿名用户的写和创建目录的权限 
    dirmessage_enable=YES            当切换到目录时，显示该目录下的.message隐藏文件的内容 
    xferlog_enable=YES                    激活上传和下载日志 
    connect_from_port_20=YES        启用FTP数据端口的连接请求 
    #chown_uploads=YES                是否具有上传权限.  用户由chown_username参数指定。 
    #chown_username=whoever        指定拥有上传文件权限的用户。此参数与chown_uploads联用。 
    #xferlog_file=/var/log/vsftpd.log 
    xferlog_std_format=YES              使用标准的ftpd xferlog日志格式 
    #idle_session_timeout=600          此设置将在用户会话空闲10分钟后被中断 
    #data_connection_timeout=120    将在数据连接空闲2分钟后被中断 
    #ascii_upload_enable=YES        启用上传的ASCII传输方式 
    #ascii_download_enable=YES    启用下载的ASCII传输方式 
    #ftpd_banner=Welcome to blah FTP service 设置用户连接服务器后显示消息 
    #deny_email_enable=NO  此参数默认值为NO。当值为YES时，拒绝使用banned_email_file参数指定文件中所列出的e-mail地址用户登录。 
    #banned_email_file=/etc/vsftpd.banned_emails 指定包含拒绝的e-mail地址的文件. 
    #chroot_list_enable=YES    设置本地用户登录后不能切换到自家目录以外的别的目录 
    #chroot_list_file=/etc/vsftpd.chroot_list 
    #ls_recurse_enable=YES 
    pam_service_name=vsftpd 设置PAM认证服务的配置文件名称，该文件存放在/etc/pam.d/ 
    userlist_enable=YES    此项配置/etc/vsftpd.user_list中指定的用户也不能访问服务器，若添加userlist_deny=No,则仅仅/etc /vsftpd.user_list文件中的用户可以访问，其他用户都不可以访问服务器。如过 userlist_enable=NO,userlist_deny=YES,则指定使文件/etc/vsftpd.user_list中指定的用户不可以访问服务器，其他本地用户可以访问服务器。 
    listen=YES              指明VSFTPD以独立运行方式启动 
    tcp_wrappers=YES        在VSFTPD中使用TCP_Wrappers远程访问控制机制，默认值为YES

参考

[http://www.cnblogs.com/welkinwalker/archive/2010/04/14/1711880.html][1]
[http://yuanbin.blog.51cto.com/363003/129071][2]


  [1]: http://www.cnblogs.com/welkinwalker/archive/2010/04/14/1711880.html
  [2]: http://yuanbin.blog.51cto.com/363003/129071