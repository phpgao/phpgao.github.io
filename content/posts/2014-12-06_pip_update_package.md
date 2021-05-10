---
title: "使用pip升级所有安装的package"
categories: [ "服务器技术" ]
tags: [ "python","pip" ]
draft: false
slug: "pip_update_package"
date: "2014-12-06 14:29:00"
url: "pip_update_package.html"
---

升级的时候注意权限

```python
import pip
from subprocess import call

for dist in pip.get_installed_distributions():
    call("pip install --upgrade -i https://pypi.douban.com/simple/ " + dist.project_name, shell=True)
```