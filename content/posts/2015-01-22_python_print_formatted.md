---
title: "python格式化输出dict等集合对象"
categories: [ "代码人生" ]
tags: [ "python","format","print","dump" ]
draft: false
slug: "python_print_formatted"
date: "2015-01-22 02:36:04"
url: "python_print_formatted.html"
---

调试程序的时候，如果需要打印出变量的信息，在python中很容易，一句`print`即可，他几乎可以打印任何类型的对象，不像PHP中，有一堆`echo(),print(),print_r(),var_dump()`，让人头疼！

但是PHP的打印函数有个好处，就是打印格式良好，而Python的打印信息就不是很友好了，如

```
# 模拟一个很大的键值对
dic = {}
for i in xrange(201):
	dic[i] = "value for %d" % i
print dic
```

其结果我就不打印了，总之很难看！

如何让python那冗长而且没有格式的打印变得更直观，方法有两种。


## 自定义dump

此方法来自stackoverflow

```python

# 以后需要有格式的打印一个集合对象，直接使用dump(xxx)即可！
# 不要忘了import sys
import sys

def dump(obj, nested_level=0, output=sys.stdout):
    spacing = '   '
    if type(obj) == dict:
        print >> output, '%s{' % ((nested_level) * spacing)
        for k, v in obj.items():
            if hasattr(v, '__iter__'):
                print >> output, '%s%s:' % ((nested_level + 1) * spacing, k)
                dump(v, nested_level + 1, output)
            else:
                print >> output, '%s%s: %s' % ((nested_level + 1) * spacing, k, v)
        print >> output, '%s}' % (nested_level * spacing)
    elif type(obj) == list:
        print >> output, '%s[' % ((nested_level) * spacing)
        for v in obj:
            if hasattr(v, '__iter__'):
                dump(v, nested_level + 1, output)
            else:
                print >> output, '%s%s' % ((nested_level + 1) * spacing, v)
        print >> output, '%s]' % ((nested_level) * spacing)
    else:
        print >> output, '%s%s' % (nested_level * spacing, obj)

```

## pprint

此方法来自官方，可以自定义缩进，宽度等信息。

```python
import pprint

dic = {}
for i in xrange(201):
    dic[i] = "value for %d" % i

# 自定义缩进为4空格
pp = pprint.PrettyPrinter(indent=4)
pp.pprint(dic)
```

老高推荐官方的pprint，自定义格式很不错！

完


Reference:

http://stackoverflow.com/questions/15785719/how-to-print-a-dictionary-line-by-line-in-python
https://docs.python.org/2/library/pprint.html