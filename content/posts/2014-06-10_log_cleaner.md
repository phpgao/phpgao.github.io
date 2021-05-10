---
title: "用Python写了个日志清理的脚本"
categories: [ "代码人生" ]
tags: [ "python","script" ]
draft: false
slug: "log_cleaner"
date: "2014-06-10 03:56:00"
url: "log_cleaner.html"
---

欢迎吐槽！

    #! /usr/bin/env python
    # -*- coding: utf-8 -*-
    import os,time,shutil
    path = os.getcwd()
    target_dir = []
    cal = []
    target_dir.append('/var/lib/mysql/log/')
    target_dir.append('/newlogs/')
    target_dir.append('/oldlogs/')
    #初始化删除文件名
    del_ext = ['frm','MYD','MYI']
    #你要保留几天
    days_before = 5
    #初始化删除日期
    for x in xrange(days_before+1,days_before+30):
    	cal.append(time.strftime('%Y_%m_%d',time.localtime(time.time() - x*24*3600)))
    #初始化删除文件名，使用笛卡尔积！
    fl = map('.'.join, [[x,y] for x in cal for y in del_ext])
    
    #以日期命名的文件夹
    cal = []
    for x in xrange(days_before+1,days_before+30):
    	cal.append(time.strftime('%Y-%m-%d',time.localtime(time.time() - x*24*3600)))
    fl += cal
    #获取文件扩展名
    def get_ext(filename):
    	return filename.split('.')[-1]
    
    
    
    for dirs in target_dir:
    	#files = [i for i in box_list if(get_ext(i) in del_ext)]  #按扩展名筛选文件
    	#修改当前工作目录
    	i = 0
    	print 'opening -> ' + dirs
    	#判断文件夹存在
    	if os.path.exists(dirs):
    		#切换目录
    		os.chdir(dirs)
    		for files in fl:
    			#判断文件夹或文件
    			if(os.path.isfile(str(files))):
    				i += 1
    				try:
    					os.remove(files)
    					print files + '  deleted'
    				except:
    					print 'error occurs while deleting file'
    			elif(os.path.isdir(str(files))):
    				i += 1
    				try:
    				#shutil.rmtree删除非空文件夹
    					shutil.rmtree(files)
    					print files + '  deleted'
    				except:
    					print 'error occurs while deleting file'
    	else:
    		print 'Error on opening ' + dirs
    	if i:
    		print str(i) + ' files deleted'
    	else:
    		print 'nothing to remove'
    	print 'leaving -> ' + dirs
    	print '\n'