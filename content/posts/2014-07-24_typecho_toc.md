---
title: "让typecho支持table-of-contents"
categories: [ "性能优化" ]
tags: [ "markdown","toc","typecho" ]
draft: false
slug: "typecho_toc"
date: "2014-07-24 08:28:00"
url: "typecho_toc.html"
---

# 什么是toc?

[toc]


> table of contents 即文章目录

# toc有什么用

> 废话，你说目录有啥用，方便查找呗

使用typecho的TX，一定会一点markdown吧，在文章中如果出现`##this's h2 tag##`，会被程序转换为

    <h2>this's h2 tag</h2>

一个h标签就好比一本书的各个章节，如果我们能把他们**清点**一下，组成一个目录输出，岂不是妙哉！

# TOC如何使用

简单到不能再简单，在你想插入目录的地方放一个`[toc]`或`[TOC]`即可(推荐大写)！

> `[TOC]`必须处于顶格

# typecho中的markdown

typecho的源代码中已经使用了激进的MarkdownExtraExtended类来转化md文件，为什么说他很激进呢？因为他扩展了标准的markdown，添加了很多个性化的语法，如直接给元素添加id或者class，还有脚注、缩写词等，用起来确实很爽！但需要注意的是：**太多的非标准语法可能会带来移植性差的问题**

具体的语法参考请看这里[php Markdown Extra][1]

# 如何使typecho支持toc

要使typecho支持toc需要替换位于源程序中的`/wwwroot/var/`文件夹下的`MarkdownExtraExtended.php`  为 [MarkdownExtraExtended.php][2]

# 原理

这个文件的作用就是为typecho提供md2html的作用，我修改了MarkdownExtraExtended类的__construct方法，为block_gamut数组添加了 `doToc` 处理模块，并把优先级降到最低。

转换细节位于`doToc`与`_doToc_callback`中，在此不作赘述。

# 不完美的地方

 * 要是能加入一个锚点之间平滑滚动的效果就更完美了，改天写个插件弄一弄
 * 在首页也能看到目录，改天看看源代码研究一下，看能不能解决
 * 希望后台加一个选项，自动生成目录，这个比较麻烦，以后看情况解决


  [1]: https://michelf.ca/projects/php-markdown/extra/
  [2]: http://pan.baidu.com/s/18fp0M