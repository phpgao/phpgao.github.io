---
title: "Python-代码片段总结"
categories: [ "代码人生" ]
tags: [ "python" ]
draft: false
slug: "python_function"
date: "2016-06-16 07:31:00"
url: "python_function.html"
---

慢慢积累吧！

![python][1]


<!--more-->


## 升级PIP

```bash
python -m pip install --upgrade pip
```


## 代理

```bash
easy_install -i https://pypi.douban.com/simple/ requests
pip install -i https://pypi.douban.com/simple/ requests
```

## 文件头

```python
#! /usr/bin/env python
# -*- coding: utf-8 -*-

# or

#! /usr/bin/env python
# coding=utf-8
```

## 遍历文件夹

 - [benhoyt/scandir][2]
 - [python3 os.walk && os.fwalk][3]
 - [python2 os.walk][4]

```python
import os
for root, dirs, files in os.walk(top, topdown=False):
    for name in files:
        os.remove(os.path.join(root, name))
    for name in dirs:
        os.rmdir(os.path.join(root, name))
```

## 运行时相关

```python
# 当前文件夹
os.getcwd()
# 当前脚本路径
file_path  = os.path.realpath(__file__)
# 当前脚本文件夹
os.path.dirname(file_path)
```

## 生成pyc文件

用py_compile模块

```python
import py_compile
py_compile.compile(file_path)
```

也可以直接执行命令转换当前所有py文件，使用到compileall模块

```bash
python -m compileall xxx.py
python -m compileall .
```

生成的pyc可以跨平台使用，但是只能这样用`python xxx.pyc`，而不能使用`./xxx.pyc`执行，因为缺少了shebang的支持，不过貌似[Binfmt_misc][5]可以解决这个问题，ubuntu下`apt-get install binfmt-support`可以直接安装。

## 文件操作高级封装

[10.10. shutil — High-level file operations][6]

## 环境变量

[15.1.1. Process Parameters¶][7]

```python
import os

for k, v in os.environ.items():
    print "%s => %s" % (k, v)

# Note Calling putenv() directly does not change os.environ, so it’s better to modify os.environ.
os.getenv('PATH')
os.putenv('GO15VENDOREXPERIMENT', '0')
```

## main的写法

```python
if __name__ == "__main__":
```

## 子进程

Python 产生子进程的方法有：

1. os.popen
1. subprocess.popen
1. os.system
1. os.fork
1. os.spawnv
1. os.fork

## sys

```python
import sys

# 获取参数
print sys.argv

# 获取 path
print sys.path

# 获取已载入模块
print sys.modules

# 执行信息，发生异常时可以获取到
print sys.exec_info()
```


## 标准流

1. sys.stdin
1. sys.stdout
1. sys.stderr

提供文件方法的对象都可以作为标准流：

带 read() 方法的对象可以赋值给 sys.stdin； read() 为输入流提供数据
带 write() 方法的对象可以赋值给 sys.stdout； 所有标准输出都发给 write() 方法。

```python
class Input(object):
    def read(self):
        return 'hello, world\n'
class Output(object):
    def write(self, s):
        f = open('1.txt', 'w')
        f.write(s)
        f.close()

sys.stdin = Input()
sys.stdin.read() # 'hello, world'
sys.stderr = Input()
sys.stderr.read() # 'hello, world'
sys.stdout = Output()
sys.stdout.write('hello, world\n') #生成文件 1.txt，输入文本 'hello, world\n'
```

## 版本比较

Python的版本比较有很多种方法，StrictVersion、LooseVersion还有NormalizedVersion。不过在 PEP 386已经不推荐使用StrictVersion和LooseVersion了。

> Note that LooseVersion and StrictVersion have been deprecated under PEP 386 and will at some point be replaced by NormalizedVersion.

安装verlib

```bash
pip install -i https://pypi.douban.com/simple/ verlib
```

使用版本比较的功能。

```python
from verlib import NormalizedVersion
NormalizedVersion("1.2b1") < NormalizedVersion("1.2") #True
```

## 检查pid是否在运行

[How to check if there exists a process with a given pid?][8]

```python
import errno
import os
import sys

def pid_exists(pid):
    """Check whether pid exists in the current process table.
    UNIX only.
    """
    if pid < 0:
        return False
    if pid == 0:
        # According to "man 2 kill" PID 0 refers to every process
        # in the process group of the calling process.
        # On certain systems 0 is a valid PID but we have no way
        # to know that in a portable fashion.
        raise ValueError('invalid PID 0')
    try:
        os.kill(pid, 0)
    except OSError as err:
        if err.errno == errno.ESRCH:
            # ESRCH == No such process
            return False
        elif err.errno == errno.EPERM:
            # EPERM clearly means there's a process to deny access to
            return True
        else:
            # According to "man 2 kill" possible error values are
            # (EINVAL, EPERM, ESRCH)
            raise
    else:
        return True
```


Renference:

[学习Python][9]
[Compare version strings][10]
[Working with versions][11]
[PEP 386 -- Changing the version comparison module in Distutils][12]


  [1]: https://blog.phpgao.com/usr/uploads/2016/06/2183667823.jpeg
  [2]: https://github.com/benhoyt/scandir
  [3]: https://docs.python.org/3.5/library/os.html#os.walk
  [4]: https://docs.python.org/2.7/library/os.html#os.walk
  [5]: https://en.wikipedia.org/wiki/Binfmt_misc
  [6]: https://docs.python.org/2.7/library/shutil.html#module-shutil
  [7]: https://docs.python.org/2/library/os.html#process-parameters
  [8]: http://stackoverflow.com/questions/568271/how-to-check-if-there-exists-a-process-with-a-given-pid
  [9]: http://to-learn-python.readthedocs.io/index.html
  [10]: http://stackoverflow.com/questions/11887762/compare-version-strings
  [11]: http://alexis.notmyidea.org/distutils2/library/distutils2.version.html
  [12]: https://www.python.org/dev/peps/pep-0386/