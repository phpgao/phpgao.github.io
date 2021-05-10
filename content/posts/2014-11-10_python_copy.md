---
title: "python中浅拷贝和深拷贝"
categories: [ "代码人生" ]
tags: [ "PHP","python","deepcopy","shallowcopy","clone" ]
draft: false
slug: "python_copy"
date: "2014-11-10 09:33:00"
url: "python_copy.html"
---

# python中浅拷贝和深拷贝

今天写python脚本，遇到了一个问题。先贴代码：

```python
#coding=utf-8
new_list = []               # 声明一个list
tmp = {'a':123,'b':'ccc'}   # 新建一个dict
new_list.append(tmp)        # 追加
print tmp
print new_list
tmp['a'] = 456              # 修改tmp
tmp['b'] = 'ddd'
new_list.append(tmp)        # 追加
print tmp
print new_list

# 执行结果：

{'a': 123, 'b': 'ccc'}
[{'a': 123, 'b': 'ccc'}]    # 当改变了tmp，list中的值也会变化
{'a': 456, 'b': 'ddd'}
[{'a': 456, 'b': 'ddd'}, {'a': 456, 'b': 'ddd'}]
```

如果是PHP会发生什么？

```php
$b = array();
$a = array('b'=>123);
array_push($b , $a);
$a = array('b'=>456);
array_push($b , $a);
var_dump($b);

$a = new ArrayObject(array('b'=>123));
$arr = new ArrayObject();
$arr->append($a);
$a['b'] = 456;
$arr->append($a);
var_dump($arr);

# 执行结果

array(2) {
  [0]=>
  array(1) {
    ["b"]=>
    int(123)
  }
  [1]=>
  array(1) {
    ["b"]=>
    int(456)
  }
}
object(ArrayObject)#2 (1) {
  ["storage":"ArrayObject":private]=>
  array(2) {
    [0]=>
    object(ArrayObject)#1 (1) {
      ["storage":"ArrayObject":private]=>
      array(1) {
        ["b"]=>
        int(456)
      }
    }
    [1]=>
    object(ArrayObject)#1 (1) {
      ["storage":"ArrayObject":private]=>
      array(1) {
        ["b"]=>
        int(456)
      }
    }
  }
}
```
由结果看，PHP中array_push方法和array_object的结果也不同。

为什么会这样呢？

这里就要讲讲浅拷贝和深拷贝了。

以python为例，当执行`new_list.append(tmp)`方法时，append方法仅仅把tmp变量的内存地址交给了new_list，而不是新建了一个与tmp一模一样的变量（内存中的地址不同，属性几乎一样），即**浅拷贝**。这就解释了，当改变了tmp的值后new_list中**保存**的tmp也会随之改变，因为他们都是一样一样的。

需要注意的是，python中非容器类没有拷贝这一说，还有一些坑在[这里][1]可以看到。

> 引用的能省下不少内存空间，但是会给新手造成迷惑。

PHP中的`array_push`方法，就会做一次**深拷贝**，创建了新的变量，所以不论怎么修改传入的$a，都不会影响最终得到两个不同的数组。而使用了数组对象做同样的事，就会出现和python一样的浅拷贝现象。

那么如何解决之前的问题呢？

python的做法是引入标准库中的copy模块。

```python
import copy

copy.copy(x)
# 返回一个x的浅复制。

copy.deepcopy(x)
# 返回一个x的深复制。

exception copy.error
# copy异常
```

将之前的python代码改为：

```python
#coding=utf-8
import copy
new_list = []               # 声明一个list
tmp = {'a':123,'b':'ccc'}   # 新建一个dict
new_list.append(tmp)        # 追加
print tmp
print new_list
tmp = copy.deepcopy(tmp)
tmp['a'] = 456              # 修改tmp
tmp['b'] = 'ddd'
new_list.append(tmp)        # 追加
print tmp
print new_list
```

PHP5中对象的赋值和传值都是以“引用”的方式，解决对象深拷贝采用的`clone`[语言结构][2]，有兴趣的TX可以参考[php深复制和浅复制][3]。

扩展阅读：

http://blog.csdn.net/dizzthxl/article/details/7688744
http://bbs.chinaunix.net/thread-1787290-1-1.html
http://www.jb51.net/article/54266.htm
http://blog.csdn.net/dizzthxl/article/details/7688744
http://www.cnblogs.com/windlaughing/archive/2013/05/26/3100362.html


  [1]: https://docs.python.org/2/library/copy.html?highlight=copy#module-copy
  [2]: http://blog.sina.com.cn/s/blog_475429950100hk75.html
  [3]: http://blog.csdn.net/ohmygirl/article/details/7778125