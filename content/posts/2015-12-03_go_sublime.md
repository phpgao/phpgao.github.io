---
title: "sublime-配置-插件-主题总结"
categories: [ "代码人生" ]
tags: [ "theme","plugin","config","sublime" ]
draft: false
slug: "go_sublime"
date: "2015-12-03 11:23:00"
url: "go_sublime.html"
---

![sublime][1]

可自用，可参考，欢迎大家补充！


<!--more-->


## 修改点

[伪・镜像服务器][2]

### PC

```pyton
import urllib.request,os,hashlib; h = '2915d1851351e5ee549c20394736b442' + '8bc59f460fa1548d1514676163dafc88'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```

### User配置

有屏蔽升级，设置代理的功能，不用的直接删除！

```json
{
    "color_scheme": "Packages/Material Theme/schemes/Material-Theme-OceanicNext.tmTheme",
    "font_face": "Ubuntu Mono derivative Powerline",
    "font_size": 16,
    "http_proxy": "http://10.16.13.18:8888",
    "https_proxy": "http://10.16.13.18:8888",
    "ignored_packages": [
        "Vintage"
    ],
    "show_encoding": true,
    "theme": "Material-Theme.sublime-theme",
    "update_check": false,
    "tab_size": 4,
    "translate_tabs_to_spaces": true,
}

```

### 插件

[Go​Sublime][3]

> Tools->Build System->New Build System

```json
{
    "shell_cmd": "go run $file",
    "encoding": "utf-8"
}
```


[Encoding​Helper][4]
[Pretty JSON][5]  `Ctrl+Alt+J`
[BracketHighlighter][6]
[Trail­ing­Spaces][7]
[Alignment][8]  `Ctrl+Alt+A`
[Side​Bar​Enhancements][9]
[Php​Doc][10] `/**`
[DocBlockr][11]
[Phpcs][12]
[sublime-php-namespace][13]
[Git][14]
[Git Gutter][15]
[VCS Gutter][16]
[HTML-CSS-JS Prettify][17]
[SqlBeautifier][18] `ctrl+k`
[Terminal][19]
[nginx][20]   nginx语法高亮
ApacheConf.tmLanguage
INI
Markdown Extended
[Dockerfile Syntax Highlighting][21]


### 主题

[Material Theme][22]
[Theme - Cobalt​2][23]
[Hero Theme][24]


  [1]: https://blog.phpgao.com/usr/uploads/2015/12/3173255088.png
  [2]: http://www.cnblogs.com/52cik/p/Package-Control.html
  [3]: https://packagecontrol.io/packages/GoSublime
  [4]: https://packagecontrol.io/packages/EncodingHelper
  [5]: https://packagecontrol.io/packages/Pretty%20JSON
  [6]: https://github.com/facelessuser/BracketHighlighter/
  [7]: https://github.com/SublimeText/TrailingSpaces
  [8]: https://packagecontrol.io/packages/Alignment
  [9]: https://packagecontrol.io/packages/SideBarEnhancements
  [10]: https://packagecontrol.io/packages/PhpDoc
  [11]: https://github.com/spadgos/sublime-jsdocs
  [12]: https://packagecontrol.io/packages/Phpcs
  [13]: https://github.com/gl3n/sublime-php-namespace
  [14]: https://packagecontrol.io/packages/Git
  [15]: https://packagecontrol.io/packages/GitGutter
  [16]: https://packagecontrol.io/packages/VCS%20Gutter
  [17]: https://packagecontrol.io/packages/HTML-CSS-JS%20Prettify
  [18]: https://packagecontrol.io/packages/SqlBeautifier
  [19]: http://wbond.net/sublime_packages/terminal
  [20]: https://github.com/brandonwamboldt/sublime-nginx
  [21]: https://packagecontrol.io/packages/Dockerfile%20Syntax%20Highlighting
  [22]: https://packagecontrol.io/packages/Material%20Theme
  [23]: https://packagecontrol.io/packages/Theme%20-%20Cobalt2
  [24]: https://packagecontrol.io/packages/Theme%20-%20Hero