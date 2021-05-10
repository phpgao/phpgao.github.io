---
title: "-etc-profile,-etc-bashrc等文件的区别与作用"
categories: [ "服务器技术" ]
tags: [ "shell","Linux","bash" ]
draft: false
slug: "profile_bashrc_bash_profile_bashrc_definition"
date: "2015-05-28 17:14:00"
url: "profile_bashrc_bash_profile_bashrc_definition.html"
---

当我们在做一些与bash相关的操作时，比如设置别名、登录启动项等，多多少少都会与下面几个文件打交道，用的时候一查，然后又忘了。好记性不如烂笔头，下面老高就帮你理一理这些文件到底是干啥的。

- /etc/profile
- /etc/bashrc
- ~/.bash_profile
- ~/.bashrc 


<!--more-->


----------

`man bash`   ----- INVOCATION

以CENTOS为例，其他系统可能略有区别。

**登录shell**

> A login shell is one whose first character of argument zero is a -, or one started with the --login option.

老高注解：

判断一个shell是不是登录shell，只要你是`su -`, `bash --login`这样进入的就行，如果你进入一个terminal而不需要登录，即输入用户名密码，那么他就是一个 non-login shell。比如你在MAC下打开终端，是不需要登录过程的所以是non-login shell。

还有一个方法，`echo $0`，如果是登录shell，则会返回`-[bash name]`，否则返回[bash name]。

**交互式Shell**

> An interactive shell is one started without non-option arguments and without the -c option whose standard input and error are both connected to terminals (as determined by isatty(3)), or one started with the -i option.  PS1 is set and $- includes i if bash is interactive,allowing a shell script or a startup file to test this state.

老高注解：

判断一个交互式shell也很简单`echo $-`的结果中包含小写字母i就是了。

> 当你运行一个脚本时，那就是一个非交互式shell。

> When bash is started non-interactively, to run a shell script, for example, it looks for the variable  BASH_ENV  in  the environment, expands its value if it appears there, and uses the expanded value as the name of a file to read and execute.  Bash behaves as if the  following command were executed:
    if [ -n "$BASH_ENV" ]; then . "$BASH_ENV"; fi
but the value of the PATH variable is not used to search for the file name.

老高翻译：

当一个非交互的shell运行一个shell脚本时，他会在环境变量中 `$BASH_ENV` 并执行。代码为`if [ -n "$BASH_ENV" ]; then . "$BASH_ENV"; fi`。

> The  following  paragraphs  describe  how  bash executes its startup files.  If any of the files exist but cannot be read, bash reports an error.  Tildes are expanded in file names as described below under Tilde Expansion in the EXPANSION section.

> When bash is invoked as an interactive login shell, or as a non-interactive shell with the --login option, it  first  reads  and  executes commands  from  the  file  /etc/profile,  if  that file exists.  After reading that file, it looks for ~/.bash_profile, ~/.bash_login, and ~/.profile, in that order, and reads and executes commands from the first one that exists and is readable.  The --noprofile option may be used when the shell is started to inhibit this behavior.

老高翻译：

当你登录一个交互式的shell后，如果**/etc/profile**存在，bash首先会读取**/etc/profile**的配置，然后再依次读取对应用户的`~/.bash_profile`、`~/.bash_login`和`~/.profile`。

> When a login shell exits, bash reads and executes commands from the file ~/.bash_logout, if it exists.

老高翻译：

当一个登录shell退出的时候，如果当前用户的`~/.bash_logout`存在，bash会读取他。

> When  an  interactive  shell  that  is not a login shell is started, bash reads and executes commands from ~/.bashrc, if that file exists. This may be inhibited by using the --norc option.  The --rcfile file option will force bash to read and execute commands from file instead of ~/.bashrc.

老高翻译：

当一个非登陆shell运行，如果对应用户的`~/.bashrc`存在，bash首先会读取`~/.bashrc`，有个`--norc option`能阻止读取，还有个`--rcfile`的选项可以强制读取指定文件。


综上：

~/.bashrc与/etc/bashrc、~/.bash_profile与/etc/profile的关系一样，都是一个针对某个用户，一个针对全局设置。读取的时候也是全局优先。


----------


交互式shell登录时读取`profile`，初始化时读取`bashrc`。

非交互式只会读取`bashrc`。

一般把`alias`和`function`一类的放到`bashrc`或`~/.bashrc`中。

而把`export`更多的放在profile文件中。

推荐大家参考[My bashrc, bash aliases, profile and other files][1]的配置；

同时安装Linux下最好的shell ：[zsh + oh-my-zsh 默认shell的最佳替代品][2]；

如果还是钟情与bash，那么老高推荐你安装`bash-completion`，自动补全的功能很好用！


  [1]: http://stefaanlippens.net/my_bashrc_aliases_profile_and_other_stuff
  [2]: https://blog.phpgao.com/oh-my-zsh.html