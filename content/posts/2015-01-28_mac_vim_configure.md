---
title: "mac下vim的美化配置"
categories: [ "代码人生" ]
tags: [ "vim","configure","mac","osx" ]
draft: false
slug: "mac_vim_configure"
date: "2015-01-28 13:51:00"
url: "mac_vim_configure.html"
---

osx系统有些配置文件地方和Linux系统不太一样真是让人捉急

以VIM为例


<!--more-->


默认配置文件在`/usr/share/vim/vimrc`

```
sudo vim /usr/share/vim/vimrc

# add

syn on "语法支持
set laststatus=2 "始终显示状态栏
set tabstop=2 "一个制表符的长度
set softtabstop=2 "一个制表符的长度（可以大于tabstop）
set shiftwidth=2 "一个缩进的长度
set expandtab "使用空格替代制表符
set smarttab "智能制表符
set autoindent "自动缩进
set smartindent "只能缩进
set number "显示行号
set ruler "显示位置指示器
set backupdir=/tmp "设置备份文件目录
set directory=/tmp "设置临时文件目录
set ignorecase "检索时忽略大小写
set hls "检索时高亮显示匹配项
set helplang=cn "帮助系统设置为中文
set foldmethod=syntax "代码折叠
```

效果图：

![vim_fetion.png][1]


Reference:

 - http://hessian.cn/p/1026.html
 - http://blog.sina.com.cn/s/blog_5a6efa330101cumx.html

  [1]: https://blog.phpgao.com/usr/uploads/2015/01/2761251961.png