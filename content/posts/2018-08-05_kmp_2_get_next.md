---
title: "KMP算法笔记II--------学会计算next数组"
categories: [ "代码人生" ]
tags: [ "algorithm","算法","kmp" ]
draft: false
slug: "kmp_2_get_next"
date: "2018-08-05 16:31:00"
url: "kmp_2_get_next.html"
---

第一次学习KMP算法走了不少弯路，下面老高按照自己的学习步骤，总结一下KMP算法的要点，如果有错误或者疑问，欢迎指正！

老高使用python语言实现算法，实现的语言不重要，重要的是他的思想！(其实老高的C语言早已年久失修?)

本文是系列的第二篇，主要探讨一下KMP算法的思维方式并引出next数组概念。


<!--more-->


## 一些约定

 - 函数查找不到返回-1，最好支持全局搜索
 - s(string) 代表 需要匹配的字符串
 - t(target) 代表 我们想要查找的字符串
 - i 代表查找string时的下标
 - j 代表匹配target时的下标
 - k 代表next数组时最大前缀后缀的长度
 - next(next) 代表 next数组

## 理解KMP算法

通过第一篇，我们知道用笨办法查找字符串，现在我们通过一些特例来了解KMP算法。相比于朴素算法，KMP优化了j的移动策略，使得在发生失配的时候不需要再把i向左移动了，从而减少了匹配次数，大大提高了匹配效率。KMP算法的难点在于求解NEXT数组，NEXT数组就像是一本字典，它会指引你在发生失配的时候需要做什么。

现在我们考虑在某个字符串搜索字符`ABABC`，下面演示一下

```
A	B	A	B	Å	B	C
A	B	A	B	C
```

KMP算法的不同就在于箭头处出现失配的做法，下面看看朴素算法的下一步：

```
A	B	A	B	Å	B	C
A	B	A	B	C
```

同时对比KMP算法的下一步：

```
A	B	A	B	Å	B	C
 	 	A	B	A	B	C
```

快来找不同：

 - 朴素算法的游标发生倒退，而KMP算法没有
 - 重新对比的位置很奇特，为什么不是从头开始呢
 
我们先观察`ABABC`字符串，发现有`ABAB`这个模式，所以当我们匹配到第五个字母`A`发生失配时，就不用从头开始计算，而直接从`ABABC`的第三位开始对比，字面下的意思就是最近的`AB`我对比不成，但是我又发现后面的`AB`有一个远方亲戚，即最前方的`AB`可以直接用来匹配，而不用去管之前的匹配，确实省了不少事情。

接下来要确定的就是如何计算next数组了，其实就是求解当前字符处（或之前）的字符串的最大前缀后缀的共有最大元素的长度。

在此老高得到了很多算法，但是最后生成的数组不尽相同。

比如 [阮一峰的网络日志- 字符串匹配的KMP算法](http://www.ruanyifeng.com/blog/2013/05/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm.html)中，next[k]的计算包含当前k对应字符，即`ABABC`的next[3]的值的是`ABAB`的计算结果。
即 `ABABC`的next数组为`[0, 0, 1, 2, 0]`

而另一种结果为`[-1, 0, 0, 1, 2]`，此结果指的是k之前的字符串所计算的值，与前者差别不大，也就是`next[j-1]`和`next[j]`的问题。

下面我们笨办法试着写一个生成next数组(包含当前字符)的方法吧！

```python
# 此方法还可以优化，比如去掉t_temp
def get_next_slow(t):
    t_len = len(t)
    # 初始化next数组
    next_list = [0] * t_len
    # 除了i=0，其他都需要计算
    for i in range(1, t_len):
        # 初始化max_len
        k = 0
        # 截取需要计算的部分
        t_temp = t[:i + 1]
        # j表示当前最大匹配长度，如果对比成功，j就是k
        j = i
        while j > 0:
            # 一步一步截取前缀，直到j=0
            if t_temp[0:j] == t_temp[i - j + 1:]:
                # 如果相等，直接赋值
                k = j
                break
            j = j - 1
        # 将max写入next
        next_list[i] = k
    return next_list

```

或者使用递归思想，模式字符串记为`P`(下标从0开始)，`next[q] = k` 表示`P[q]`之前的子串中，存在长度为`k`的相同前缀和后缀，即`P[0]~P[k-1]`与`P[q-k]~P[q-1]`依次相同。如果`P[k] = P[q]`，那么`next[q+1] = k+1`，此时表示`P[q+1]`之前的子串中，存在长度为`k+1`的相同前后缀，这应该不成问题。（转自知乎>[怎么理解kmp算法中的next数组?](https://www.zhihu.com/question/21474082/answer/58767627)）

```python
def get_next(t):
    t_len = len(t)
    next = [0] * t_len
    k = 0
    for i in range(1, t_len):
        # 进入循环查找是否存在一个k，使得t[i] == t[k]，k会原来越短，直到k=0，退出循环
        # 注：i是不变的
        while k > 0 and t[i] != t[k]:
            k = next[k - 1]

        # 如果t[i] == t[k]，k长度加一
        if t[i] == t[k]:
            k = k + 1

        next[i] = k
    return next

```