---
title: "充分利用oh-my-zsh"
categories: [ "福利" ]
tags: [ "zsh" ]
draft: false
slug: "oh_my_zsh_advanced"
date: "2015-11-26 12:10:00"
url: "oh_my_zsh_advanced.html"
---

![sss2.png][1]

都听说[oh-my-zsh][2]很NB，但是具体NB在哪里呢？

- 代码补全
- 切换目录增强
- 各种主题
- 命令历史增强


<!--more-->



> 老高在[打造你的超级终端][3]一文中就使用了**zsh** + **oh-my-zsh**的黄金组合！


## 折腾前的准备

首先，我们从别名入手，进入`~/.oh-my-zsh`目录后，看看该目录的结构，猜测下他们的功能！

![oh-my-zsh][4]

其中：

- lib 应该是提供了核心功能的脚本库
- tools 应该是提供安装、升级等功能的快捷工具
- plugins 就不用说了，一定是各种插件的HOME了
- templates 模板的存放处
- themes 就更不用说了，主题文件
- custom 应该是个性化配置的地方

> ps.不要忘了使用`upgrade_oh_my_zsh`升级你的oh_my_zsh！

## 各种alias

### 寻找alias

首先老高对别名很感兴趣，能够让你从各种复杂的命令中解放出来，我们先到插件(plugins)的目录下看看

```bash
cd ~/.oh-my-zsh/plugins
ls -la
# 你应该会发现一个名叫common-aliases的文件夹
cd common-aliases
```

嗯，应该是他了！现在我们看看他都给我们提供了那些alias！

```bash
grep alias common-aliases.plugin.zsh

# 感觉命令太多了怎么办？
# 这样看怎么样？

# 过滤单字母别名
grep "alias\ \(\-g\ \)\?\w\{1\}='" . -rh

# 过滤双字母别名
grep "alias\ \(\-g\ \)\?\w\{2\}='" . -rh

# 看出规律了吗？替换字母N就可以筛出N-M个字母的别名了！
grep "alias\ \(\-g\ \)\?\w\{N,M\}='" . -rh
```

![别名截图][5]

### 怎么用？

```
# 之前ls -l，现在只需要：
l
# 之前history，现在只需要：
h
# 之前find . -type d -name，现在只需要：
fd
```


## 下一节待续


  [1]: https://blog.phpgao.com/usr/uploads/2015/11/3511197024.png
  [2]: http://ohmyz.sh
  [3]: https://blog.phpgao.com/ultimate_terminal.html
  [4]: https://blog.phpgao.com/usr/uploads/2015/11/2793892524.png
  [5]: https://blog.phpgao.com/usr/uploads/2015/11/2243469067.png