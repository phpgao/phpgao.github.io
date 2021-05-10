---
title: "Pycharm创建文件模板"
categories: [ "代码人生" ]
tags: [ "python","pycharm","template","ide" ]
draft: false
slug: "pycharm_template"
date: "2014-12-23 09:34:00"
url: "pycharm_template.html"
---

进入File->settings->Editor->File and Code Templates->Python Script

添加以下内容：

```python
#!/usr/bin/env python
# encoding: utf-8

#set( $SITE = "http://www.phpgao.com" )

"""
@version: ??
@author: phpergao
@license: Apache Licence 
@contact: endoffight@gmail.com
@site: ${SITE}
@software: ${PRODUCT_NAME}
@file: ${NAME}.py
@time: ${DATE} ${TIME}
"""

def func():
    pass


class Main():
    def __init__(self):
        pass


if __name__ == '__main__':
    pass
```


脚本中还可以添加自定义变量！



参考链接：

http://wiki.woodpecker.org.cn/moin/CodeCommentingRule
http://www.cnblogs.com/ziyouchutuwenwu/archive/2013/12/18/3480035.html
https://github.com/phpgao/PyCharm-Python-Templates
https://www.jetbrains.com/pycharm/webhelp/creating-and-editing-file-templates.html
http://peter-hoffmann.com/2010/python-live-templates-for-pycharm.html
