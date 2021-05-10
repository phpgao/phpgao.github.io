---
title: "转到VScode"
categories: [ "代码人生" ]
tags: [ "vs" ]
draft: false
slug: "visual_studio_code"
date: "2016-08-19 06:17:00"
url: "visual_studio_code.html"
---

![integrated-terminal_integrated-terminal.png][1]

放弃atom，投入巨硬的怀抱。


<!--more-->

## 整合命令行

Command Palette (⇧⌘P)

```
shell command : install
```

## 常用插件

[vscode-nginx][2]
[Dockerfile and Docker Compose File (yml) Support][3]
[Python][4]
[Go][5]
[Laravel Blade Snippets][6]
[Vim][7]
[Visual Studio Code Settings Sync ][8]
[beautify][9]
[Markdown PDF][10]

## 主题

[Hacker Style Themes ][11]
[Material-theme][12]
[vscode-icons][13]

## 快捷键

文件-首选项-键盘快捷方式

```json
[
    {
        "key": "ctrl+d",
        "command": "editor.action.copyLinesDownAction",
        "when": "editorTextFocus"
    },
    {
        "key": "ctrl+shift+up",
        "command": "editor.action.moveLinesUpAction",
        "when": "editorTextFocus"
    },
    {
        "key": "ctrl+shift+down",
        "command": "editor.action.moveLinesDownAction",
        "when": "editorTextFocus"
    }
]
```


  [1]: https://blog.phpgao.com/usr/uploads/2016/08/4023163725.png
  [2]: https://marketplace.visualstudio.com/items?itemName=shanoor.vscode-nginx
  [3]: https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker
  [4]: https://marketplace.visualstudio.com/items?itemName=donjayamanne.python
  [5]: https://marketplace.visualstudio.com/items?itemName=lukehoban.Go
  [6]: https://marketplace.visualstudio.com/items?itemName=onecentlin.laravel-blade
  [7]: https://marketplace.visualstudio.com/items?itemName=vscodevim.vim
  [8]: https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync
  [9]: https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify
  [10]: https://marketplace.visualstudio.com/items?itemName=yzane.markdown-pdf
  [11]: https://marketplace.visualstudio.com/items?itemName=wsds.theme-hacker
  [12]: https://marketplace.visualstudio.com/items?itemName=zhuangtongfa.Material-theme
  [13]: https://marketplace.visualstudio.com/items?itemName=robertohuertasm.vscode-icons