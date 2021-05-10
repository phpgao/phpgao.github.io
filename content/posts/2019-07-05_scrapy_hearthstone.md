---
title: "炉石传说有多少张卡牌"
categories: [ "代码人生" ]
tags: [ "scrapy" ]
draft: false
slug: "scrapy_hearthstone"
date: "2019-07-05 02:46:00"
url: "scrapy_hearthstone.html"
---

炉石传说从老高入坑倒退坑，已经过了好几个年头，玩了这么久，你知道炉石传说有多少张卡牌吗？

> 答案是标准目前914多张，至于狂野模式是多少，点进来我告诉你。


<!--more-->


## 仓库地址

目前炉石传说`奥丹姆守护者`还没有上线，所以下一个版本还会更多，还好老高早已弃坑 ?。废话不多说，赶紧上代码！

[hearthstone][1]

## 开发环境

```
python 3.7
scrapy 1.6.0
```

## 执行命令

> hs表示标准模式，hs_wild为狂野模式

```
scrapy crawl hs -o cards.json

scrapy crawl hs_wild -o cards_wild.json

处理一下格式，就更完美了！

python3 convert.py
```

## 演示

> 真实数字藏在链接里，你能找到吗？

[![asciicast](https://asciinema.org/a/255712.svg)](https://asciinema.org/a/255712)

## 答案

截止目前(20190705)，狂野模式，也就是全部卡牌，一共有`2015`张可以被收集的卡。


  [1]: https://git.cao6.app/spider/hearthstone