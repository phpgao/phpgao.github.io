---
title: "单循环链表"
categories: [ "代码人生" ]
tags: [ "algorithm","算法" ]
draft: false
slug: "single_cycle_linked_list"
date: "2018-08-02 14:34:35"
url: "single_cycle_linked_list.html"
---

```python
#!/usr/bin/env python3
# coding=utf-8


class Node:
    def __init__(self, data):
        self._data = data
        self._next = None

    def set_next(self, node):
        self._next = node

    def set_data(self, data):
        self._data = data

    def get_data(self):
        return self._data

    def get_next(self):
        return self._next


class SingleCycleLinkedList:
    def __init__(self):
        self.head = Node(None)
        self.head.set_next(self.head)

    # 清空/初始化
    def clear(self):
        self.head.set_next(self.head)

    # 是否为空
    def is_empty(self):
        return self.head.get_next() == self.head

    # 获取大小
    def size(self):
        count = 0
        cur = self.head.get_next()
        while cur != self.head:
            count = count + 1
            cur = cur.get_next()
        return count

    # 搜索是否存在
    def search(self, data):
        cur = self.head.get_next()
        while cur != self.head:
            if cur.get_data() == data:
                return True
            cur = cur.get_next()
        return False

    # 移除指定元素
    def remove(self, data):
        prev = self.head
        while prev.get_next() != self.head:
            cur = prev.get_next()
            if cur.get_data() == data:
                prev.set_next(cur.get_next())
            prev = prev.get_next()

    # 头插
    def insert(self, data):
        temp = Node(data)
        temp.set_next(self.head.get_next())
        self.head.set_next(temp)

    # 尾插
    def append(self, data):
        # 建立node
        temp = Node(data)
        cur = self.head
        # 拿到最后一个node
        while cur.get_next() != self.head:
            cur = cur.get_next()
        # 插入
        temp.set_next(self.head)
        cur.set_next(temp)

    def print_me(self):
        cur = self.head.get_next()
        while cur != self.head:
            print cur.get_data()
            cur = cur.get_next()


if __name__ == '__main__':
    a = SingleCycleLinkedList()
    a.append('aa')
    a.remove('aa')
    a.print_me()
```