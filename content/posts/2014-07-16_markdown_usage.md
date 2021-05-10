---
title: "markdown语法简介"
categories: [ "代码人生" ]
tags: [ "markdown" ]
draft: false
slug: "markdown_usage"
date: "2014-07-16 03:08:00"
url: "markdown_usage.html"
---

最近把之前的文章都用markdown重写了一遍

之前寄希望于md自动转换，可惜转换后的格式惨不忍睹，同时发现我写得的文章也是惨不忍睹 ::>_<:: 

下面简单写一下md的语法


<!--more-->


# 标题

`#`代表html里面的h标签，个数与权重相关

格式：

	# Title1
	## Title2
	### Title3
	#### Title4
	##### Title5
	###### Title6

html效果：

# H1_Title1
## H2_Title2
### H3_Title3
#### H4_Title4
##### H5_Title5
###### H6_Title6

# 引用

类似WP的blockquote，就是引用一段话用的，我的记忆里好像不用每行都放一个 \> 符号

**引用是可以嵌套的**

格式：

	> Reference1
	> > Reference2
	> > > Reference3

html效果：

> Reference1
> > Reference2
> > > Reference3

# 列表

## 无序列表

这个比较随意，+ - * 都可以表示无序列表，**不要忘了后面要加一个空格**

格式：

	+ Row1
	- Row2
	* Row3

html效果：

+ Row1
- Row2
* Row3
1. Row4
2. Row5

## 有序列表

可以注意到，当md检测到已经开启了有序列表，那么你就不用在意编号了，**全部写成1都可**以，md会自动转换

格式：

    1. Row1
    3. Row2
    1. row3

html效果：

1. Row1
3. Row2
1. row3

## 列表嵌套

列表中可以使用四个space或者一个tab缩进来开启嵌套，**注意层次最好不要超过3**

格式：

    1. Row1
    1. Row2
        - rowa
        - rowb
        - rowc
            1. rowe
            1. rowf
    1. row3
    1. row4
    
html效果：

1. Row1
1. Row2
    - rowa
    - rowb
    - rowc
        1. rowe
        1. rowf
1. row3
1. row4


# pre-formatted

分两种情况

一种使用\`\`\`将多行代码包裹，你还可以指定语言名称，这样解析后会加上`class="xxx"`，以方便定制与美化，代码如下

    ```python
    code1
    code2
    ```

这样会被解释为以下html：

```
<pre><code class="language-python">code1
code2
</code><pre>
```
    
另一种是使用 \` 包裹一句代码，这样会被解释为以下html：

```
<code>你的代码</code>
```
格式：

    `echo 'www.phpgao.com';`

html效果：

`echo 'www.phpgao.com';`


# 链接与图片

格式：

	[phpgao](http://www.phpgao.com "老高@PHPer")

	[phpgao][ref]
	[ref]: http://www.phpgao.com (老高@PHPer)

	![baidu](http://www.baidu.com/img/baidu_sylogo1.gif "百度一下")

	![baidu][pic]
	[pic]: http://www.baidu.com/img/baidu_sylogo1.gif "百度一下"


html效果：

[phpgao](http://www.phpgao.com "老高@PHPer")

[phpgao][ref]
[ref]: http://www.phpgao.com (老高@PHPer)

![baidu](http://www.baidu.com/img/baidu_sylogo1.gif "百度一下")

![baidu][pic]
[pic]: http://www.baidu.com/img/baidu_sylogo1.gif "百度一下"


# 文字特殊格式

格式：

	*MarkDown*         #斜体
	_MarkDown_         #斜体
	**MarkDown**       #加粗
	__MarkDown__       #加粗

html效果：

*MarkDown*
_MarkDown_
**MarkDown**
__MarkDown__

# 转义

\# \\ 等符号在md中是元字符，所以需要转义，和正则表达式一样，他们都用 \\ 反斜杠转义

格式：

	\\ \` \* \_ \{ \} \[ \] \( \) \# \+ \- \. \!

html效果：

\\ \` \* \_ \{ \} \[ \] \( \) \# \+ \- \. \!


基本上就是这么多啦！

如果你是程序员，那么我推荐你使用highlight来高亮代码。