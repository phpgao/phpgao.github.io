---
title: "python时间函数整理"
categories: [ "代码人生" ]
tags: [ "python","time" ]
draft: false
slug: "python_time"
date: "2014-06-09 02:44:00"
url: "python_time.html"
---

有一些是拷的，自己加了一点，大家凑活看吧

    #-*- coding:utf-8 -*-
    
    import time
    import datetime
    
    #in python is float while in php is int
    currenttime = 1402244512.48
    print time.time()
    print time.ctime()
    print time.ctime(currenttime)
    print time.localtime()
    print type(time.localtime())
    print time.strftime('%Y-%m-%d',time.localtime(time.time()-3600*24))
    print time.strftime('%Y-%m-%d %H:%I:%S',time.localtime(time.time()))
    print time.strftime('%Y-%m-%d %H:%I:%S',time.gmtime(time.time()))
    
    currentstr = '2013-08-21 17:05:01'
    print time.strptime(currentstr,'%Y-%m-%d %H:%I:%S')
    
    '''
    python中时间日期格式化符号：
    %y 两位数的年份表示（00-99）
    %Y 四位数的年份表示（000-9999）
    %m 月份（01-12）
    %d 月内中的一天（0-31）
    %H 24小时制小时数（0-23）
    %I 12小时制小时数（01-12）
    %M 分钟数（00=59）
    %S 秒（00-59）
    
    %a 本地简化星期名称
    %A 本地完整星期名称
    %b 本地简化的月份名称
    %B 本地完整的月份名称
    %c 本地相应的日期表示和时间表示
    %j 年内的一天（001-366）
    %p 本地A.M.或P.M.的等价符
    %U 一年中的星期数（00-53）星期天为星期的开始
    %w 星期（0-6），星期天为星期的开始
    %W 一年中的星期数（00-53）星期一为星期的开始
    %x 本地相应的日期表示
    %X 本地相应的时间表示
    %Z 当前时区的名称
    %% %号本身
    '''
    
    '''
    python time,datetime,string转换
    http://hi.baidu.com/_yuan0518/item/f5bb48c39fad35dcee183bbe
    '''
    #把datetime转成字符串  
    def datetime_toString(dt):  
        return dt.strftime("%Y-%m-%d-%H")  
    
    #把字符串转成datetime  
    def string_toDatetime(string):  
        return datetime.strptime(string, "%Y-%m-%d-%H")  
    
    #把字符串转成时间戳形式  
    def string_toTimestamp(strTime):  
        return time.mktime(string_toDatetime(strTime).timetuple())  
    
    #把时间戳转成字符串形式  
    def timestamp_toString(stamp):  
        return time.strftime("%Y-%m-%d-%H", time.localtime(stamp))  
    
    #把datetime类型转外时间戳形式  
    def datetime_toTimestamp(dateTim):  
        return time.mktime(dateTim.timetuple())