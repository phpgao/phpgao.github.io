---
title: "Shell中各种奇怪的变量"
categories: [ "代码人生" ]
tags: [ "Linux","command" ]
draft: false
slug: "linux_puzzle"
date: "2014-06-18 02:34:00"
url: "linux_puzzle.html"
---

这些变量是随系统运行而变化的，灵活使这些变量可简化我们的操作和更好地控制bash shell的行为。

```
$# 是传给脚本的参数个数
$0 是脚本本身的名字
$1 是传递给该shell脚本的第一个参数
$2 是传递给该shell脚本的第二个参数
$@ 是传给脚本的所有参数的列表
$* 是以一个单字符串显示所有向脚本传递的参数，与位置变量不同，参数可超过9个
$$ 是脚本运行的当前进程ID号
$? 是显示最后命令的退出状态，0表示没有错误，其他表示有错误
```