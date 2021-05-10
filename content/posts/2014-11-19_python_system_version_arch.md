---
title: "python获取操作系统平台、版本及架构"
categories: [ "代码人生" ]
tags: [ "python" ]
draft: false
slug: "python_system_version_arch"
date: "2014-11-19 02:14:00"
url: "python_system_version_arch.html"
---

转自: http://openwares.net/linux/python_os_version_platform.html

platform模块提供了底层系统平台的相关信息

## 系统架构

32位还是64位

```bash
>>> import platform
>>> platform.architecture()
('64bit', 'ELF') # python 3.3.2+ 64 bits on debian jessie 64 bits
('32bit', 'WindowsPE') # python 3.3.2 32 bits on windows 8.1 64 bits
('64bit', 'WindowsPE') # python 3.3.2 64 bits on wndows 8.1 64 bits
('64bit', '') # python 3.4.1 64 bits on mac os x 10.9.4
ELF和WindowsPE是可执行文件格式
```

## 操作系统

linux,mac还是windows

```bash
>>> platform.system()
'Linux' # python 3.3.2+ 64 bits on debian jessie 64 bits
'Windows' # python 3.3.2 32 bits on windows 8.1 64 bits
'Windows' # python 3.3.2 64 bits on windows 8.1 64 bits
'Darwin' # python 3.4.1 64 bits on mac os x 10.9.4
```

## 系统版本

```bash
>>> platform.version()
'#1 SMP Debian 3.10.11-1 (2013-09-10)' # python 3.3.2+ 64 bits on debian jessie 64 bits
'6.2.9200' # python 3.3.2 32 bits on windows 8.1 64 bits
'6.2.9200' # python 3.3.2 64 bits on windows 8.1 64 bits
'Darwin Kernel Version 13.3.0: Tue Jun  3 21:27:35 PDT 2014; root:xnu-2422.110.17~1/RELEASE_X86_64' # python 3.4.1 64 bits on mac os x 10.9.4
```

## CPU平台

```bash
>>> platform.machine()
'x86_64' # python 3.3.2+ 64 bits on debian jessie 64 bits
'AMD64' # python 3.3.2 32 bits on windows 8.1 64 bits
'AMD64' # python 3.3.2 64 bits on windows 8.1 64 bits
'x86_64' # python 3.4.1 64 bits on mac os x 10.9.4
```

## linux发行版

```bash
>>> platform.dist()
('debian', 'jessie/sid', '') # python 3.3.2+ 64 bits on debian jessie 64 bits
```

## 节点名

也就是机器名

```bash
>>> platform.node()
'work' # python 3.3.2+ 64 bits on debian jessie 64 bits
'work-xxx' # python 3.3.2 32 bits on windows 8.1 64 bits
```

## 系统信息

```bash
>>> platform.uname()
uname_result(system='Linux', node='work', release='3.10-3-amd64', version='#1 SMP Debian 3.10.11-1 (2013-09-10)', machine='x86_64', processor='') # python 3.3.2+ 64 bits on debian jessie 64 bits
 
uname_result(system='Windows', node='work-xxx', release='8', version='6.2.9200', machine='AMD64', processor='Intel64 Family 6 Model 58 Stepping 9,
GenuineIntel') # python 3.3.2 32 bits on windows 8.1 64 bits
 
uname_result(system='Darwin', node='mba', release='13.3.0', version='Darwin Kernel Version 13.3.0: Tue Jun  3 21:27:35 PDT 2014; root:xnu-2422.110.17~1/RELEASE_X86_64', machine='x86_64', processor='i386') # python 3.4.1 64 bits on mac os x 10.9.4
```

## python版本

```bash
>>> platform.python_verison()
'3.3.2+' # python 3.3.2+ 64 bits on debian jessie 64 bits
'3.3.3' # python 3.3.2 32 bits on windows 8.1 64 bits
```