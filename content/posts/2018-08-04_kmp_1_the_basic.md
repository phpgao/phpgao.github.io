---
title: "KMP算法笔记I-------先学会朴素算法"
categories: [ "代码人生" ]
tags: [ "algorithm","算法","kmp" ]
draft: false
slug: "kmp_1_the_basic"
date: "2018-08-04 13:48:00"
url: "kmp_1_the_basic.html"
---

第一次学习KMP算法走了不少弯路，下面老高按照自己的学习步骤，总结一下KMP算法的要点，如果有错误或者疑问，欢迎指正！

老高使用python语言实现算法，实现的语言不重要，重要的是他的思想！(其实老高的C语言早已年久失修?)

本文是系列的第一篇，学习KMP之前最好了解一下朴素算法的写法，为以后的学习最好铺垫，此为渐进式学习！

<!--more-->

## 一些约定

 - 函数查找不到返回-1，最好支持全局搜索
 - s(string) 代表 需要匹配的字符串
 - t(target) 代表 我们想要查找的字符串
 - i 代表查找string时的下标
 - j 代表匹配target时的下标
 - k 代表next数组时最大前缀后缀的长度
 - next(next) 代表 next数组

## 查找字符朴素算法

朴素算法的内容很简单，s和t用笨办法比较，计算时我们只需要搞清楚i和j的位置即可完成匹配

```python
def native(s, t):
    # 初始化长度
    s_len = len(s)
    t_len = len(t)
    # 初始化游标
    i = 0
    j = 0
    # 当遍历完s或者t完全匹配到时，停止循环
    while i < s_len and j < t_len:
        # 当准备考试匹配时检查剩下需要匹配的字符串长度是否足够比较
        # 如果长度不足时停止匹配
        if j == 0 and s_len - i < t_len:
            break

        if s[i] == t[j]:
            # 如果s的i和t的j相等，继续比较
            i = i + 1
            j = j + 1
        else:
            # 如果不相等，把i置为已经比较过的位置的下一个继续比较
            i = i - j + 1
            j = 0

        if j == t_len:
            return i - j

    return -1
```

我们还可以顺手把它改为查找全部：

```python
def native_all(s, t):
    # 初始化长度
    s_len = len(s)
    t_len = len(t)
    # 初始化游标
    i = 0
    j = 0
    index = []
    # 当遍历完s或者t完全匹配到时，停止循环
    while i < s_len and j < t_len:
        # 当准备考试匹配时检查剩下需要匹配的字符串长度是否足够比较
        # 如果长度不足时停止匹配
        if j == 0 and s_len - i < t_len:
            break

        if s[i] == t[j]:
            # 如果s的i和t的j相等，继续比较
            i = i + 1
            j = j + 1
        else:
            # 如果不相等，把i置为已经比较过的位置的下一个继续比较
            i = i - j + 1
            j = 0
        # 如果发现j自增后于t的长度相等，说明匹配成功
        if j == t_len:
            # 放到index中，然后继续匹配，此时i
            index.append(i - j)
            j = 0

    return index
```
