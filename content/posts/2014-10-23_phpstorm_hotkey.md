---
title: "PHPSTORM快捷键"
categories: [ "代码人生" ]
tags: [ "phpstorm","hotkey" ]
draft: false
slug: "phpstorm_hotkey"
date: "2014-10-23 06:46:00"
url: "phpstorm_hotkey.html"
---

点击菜单：File>Settings>(IDE Settings) Keymap
修改工具栏的字体File->Setting->Appearance
如何设置代码自动换行 File->Setting->editor->use soft wrap in editor选项
显示行号 File->Setting->editor->Appearance-> show line numbers选项
phpStorm设置大括号和方法名在同一行显示 ; 大括号和方法名在同一行警告 File -> Settings -> code style -> PHP -> Wrapping and Braces -> Braces placement ->End of line.
在settings->Plugins里，Browse repositories按钮，搜索vim就会找到 IdeaVim插件该插件是控制phpstorm的keymap(也就是快捷键)的
phpstorm Settings->Editor->Highlight全部勾选
Editor tabs -> tab limit 16
Settings->Editor->colors&fonts ->font 设置字体大小
navigate/bookmark  F11,  设置书签直接切换next bookmark

`ctrl+j`            插入活动代码提示
`ctrl+alt+t`        当前位置插入环绕代码
`alt+insert`        生成代码菜单
`Shift + Enter`     新一行
`ctrl+q`            查看代码注释
`ctrl+d`            复制当前行
`ctrl+y`            删除当前行
`ctrl+alt+y`        刷新项目缓存Synchronize 或文件夹右键Synchronize 
`shift+F6`          重命名
`ctrl+shift+u`      字母大小写转换 
`ctrl+f`            查找
`ctrl+r`            替换
`ctrl+shift+I`      查看变量或方法定义源
`ctrl+g`            跳转行
`ctrl+alt+F12`      跳转至当前文件在磁盘上的位置
`alt+down`          查看下一个方法
`alt+up`            查看上一个方法
`ctrl+alt+l`        重新格式化代码 
`ctrl+shift+down`   statement向下移动
`ctrl+shift+up`     statement向上移动
`alt+shift+down`    line向下移动
`alt+shift+up`      line向上移动
`ctrl+/`            行注释
`ctrl+shift+/`      块注释
`Ctrl + Shift + Z`  redo
`Ctrl + Shift + F`  区域查找 
`ctrl+shift+n`      打开工程中的文件
`ctrl+b`            跳到变量申明处
`ctrl+[]`           匹配 {}[]
`ctrl+shift+]/[`    选中块代码
`ctrl+x`            剪切行
`ctrl+shift+v`      复制多个文本 
`alt+left/right`    标签切换
`ctrl+p`            显示默认参数
`ctrl + -/+`        可以折叠项目中的任何代码块

2014-11-5更：

单击右键，选择Column Selection Mode，或者按住ALT键选择块，有惊喜！

没错，这就是列选择功能，就好像把代码旋转了90度。

比如我同时选择每一行的最后，插入一个`;`，可以这样做：

1. 想象目标区域是一个矩形，将鼠标放到其右上角
2. 按住ALT键
3. 垂直向下拖鼠标
4. 松开ALT键


转自:http://hudeyong926.iteye.com/blog/1734768