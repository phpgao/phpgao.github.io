---
title: "使用DDoS-Deflate"
categories: [ "服务器技术" ]
tags: [ "deflate","ddos" ]
draft: false
slug: "dos_deflate"
date: "2014-10-16 02:39:00"
url: "dos_deflate.html"
---

昨天老高的博客受攻击了，本想着没啥流量就不用太在意，哎，防人之心不可无啊！

先装个DoS-Deflate试试看效果吧！

## 下载

    wget http://www.inetbase.com/scripts/ddos/install.sh
    sh install.sh

## 配置

    vi /usr/local/ddos/ddos.conf
    # show
    ##### Paths of the script and other files
    PROGDIR="/usr/local/ddos"
    PROG="/usr/local/ddos/ddos.sh"
    IGNORE_IP_LIST="/usr/local/ddos/ignore.ip.list"
    CRON="/etc/cron.d/ddos.cron"
    APF="/etc/apf/apf"
    IPT="/sbin/iptables"
    
    ##### frequency in minutes for running the script
    ##### Caution: Every time this setting is changed, run the script with --cron
    #####          option so that the new frequency takes effect
    FREQ=1
    
    ##### How many connections define a bad IP? Indicate that below.
    NO_OF_CONNECTIONS=150
    
    ##### APF_BAN=1 (Make sure your APF version is atleast 0.96)
    ##### APF_BAN=0 (Uses iptables for banning ips instead of APF)
    APF_BAN=0
    
    ##### KILL=0 (Bad IPs are'nt banned, good for interactive execution of script)
    ##### KILL=1 (Recommended setting)
    KILL=1
    
    ##### An email is sent to the following address when an IP is banned.
    ##### Blank would suppress sending of mails
    EMAIL_TO="root"
    
    ##### Number of seconds the banned ip should remain in blacklist.
    BAN_PERIOD=600

## 使用

白名单路径：/usr/local/ddos/ignore.ip.list
配置文件路径：/usr/local/ddos/ddos.conf

## 卸载

    wget http://www.inetbase.com/scripts/ddos/uninstall.ddos
    sh ./uninstall.ddos