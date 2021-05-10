---
title: "离线安装setuptools"
categories: [ "代码人生" ]
tags: [ "python","setuptools" ]
draft: false
slug: "install_setuptools_offline"
date: "2016-10-12 11:00:00"
url: "install_setuptools_offline.html"
---

![setuptools.png][1]

由于某些原因，没法在线安装setuptools，研究了一下官方提供的安装脚本，发现原来想要离线安装setuptools很简单。


<!--more-->


首先下载[这个脚本][2]

然后在`url = download_base + egg_name`这一句后面加入

```
url = download_base + egg_name
print url
exit()
```

然后执行一下脚本 `python ez_setup.py`，就会得到一个URL，类似：

> https://pypi.python.org/packages/2.7/s/setuptools/setuptools-0.6c11-py2.7.egg

赶紧wget或者curl下来

```
wget https://pypi.python.org/packages/2.7/s/setuptools/setuptools-0.6c11-py2.7.egg
```

然后把egg文件放到和脚本同目录的地方，并且恢复之前的脚本(或者删掉旧的ez_setup.py，直接下载新的)。

然后就可以打包放到其他地方工离线安装使用了。

原理其实很简单，核心是这句 `sys.path.insert(0, egg)`，把当前egg导入系统路径，然后就可以各种import了。

PS.安装完脚本会把egg文件删除，你知道是在哪儿删除的吗？

----

如果嫌麻烦，可以直接用下面的脚本，但还是需要之前提到的egg文件

```
#! /usr/bin/env python
# -*- coding: utf-8 -*-

import sys

egg = "./setuptools-0.6c11-py2.7.egg"

sys.path.insert(0, egg)

from setuptools.command.easy_install import main

main([egg])

```


  [1]: https://blog.phpgao.com/usr/uploads/2016/10/1048756566.png
  [2]: http://peak.telecommunity.com/dist/ez_setup.py