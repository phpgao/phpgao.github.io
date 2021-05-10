---
title: "centos下安装使用composer"
categories: [ "代码人生" ]
tags: [ "PHP","centos","composer" ]
draft: false
slug: "composer-centos"
date: "2015-06-09 02:20:00"
url: "composer-centos.html"
---

composer的中文意思为作曲家，是php最新的包管理器。

![php composer][1]


<!--more-->


## 安装

```bash
cd path-to-your-project
curl -sS https://getcomposer.org/installer | php

# Composer successfully installed to: /tmp/composer.phar
# Use it: php composer.phar
```

如果在此提示，那是因为php没有在系统的环境变量$PATH中。

> command not found: php

解决方法：

为当前php的路径创建一个软连接

```bash
ln -s /usr/local/php/bin/php /usr/local/bin/php
```

仔细察看composer.phar的权限，应该是755，即`-rwxr-xr-x`，说明这个文件其他人都有执行权限，所以如果我们将其移动至`/usr/local/bin/`中，即可全局可用！

```
mv composer.phar /usr/local/bin/composer
```

## 使用

我们先使用`-h`查看帮助

```
composer -h
Usage:
 help [--xml] [--format="..."] [--raw] [command_name]

Arguments:
 command               The command to execute
 command_name          The command name (default: "help")

Options:
 --xml                 To output help as XML
 --format              To output help in other formats (default: "txt")
 --raw                 To output raw command help
 --help (-h)           Display this help message
 --quiet (-q)          Do not output any message
 --verbose (-v|vv|vvv) Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug
 --version (-V)        Display this application version
 --ansi                Force ANSI output
 --no-ansi             Disable ANSI output
 --no-interaction (-n) Do not ask any interactive question
 --profile             Display timing and memory usage information
 --working-dir (-d)    If specified, use the given directory as working directory.

Help:
 The help command displays help for a given command:

   php /usr/local/bin/composer help list

 You can also output the help in other formats by using the --format option:

   php /usr/local/bin/composer help --format=xml list

 To display the list of available commands, please use the list command.
```

看来想要找到所有的命令需要运行`php /usr/local/bin/composer help list`

我们试试吧

```
php /usr/local/bin/composer list
   ______
  / ____/___  ____ ___  ____  ____  ________  _____
 / /   / __ \/ __ `__ \/ __ \/ __ \/ ___/ _ \/ ___/
/ /___/ /_/ / / / / / / /_/ / /_/ (__  )  __/ /
\____/\____/_/ /_/ /_/ .___/\____/____/\___/_/
                    /_/
Composer version 1.0-dev (4f80e7ff68466cab970c22b425725b8058c32970) 2015-06-09 00:03:48

Usage:
 command [options] [arguments]

Options:
 --help (-h)           Display this help message
 --quiet (-q)          Do not output any message
 --verbose (-v|vv|vvv) Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug
 --version (-V)        Display this application version
 --ansi                Force ANSI output
 --no-ansi             Disable ANSI output
 --no-interaction (-n) Do not ask any interactive question
 --profile             Display timing and memory usage information
 --working-dir (-d)    If specified, use the given directory as working directory.

Available commands:
 about            Short information about Composer
 archive          Create an archive of this composer package
 browse           Opens the package's repository URL or homepage in your browser.
 clear-cache      Clears composer's internal package cache.
 clearcache       Clears composer's internal package cache.
 config           Set config options
 create-project   Create new project from a package into given directory.
 depends          Shows which packages depend on the given package
 diagnose         Diagnoses the system to identify common errors.
 dump-autoload    Dumps the autoloader
 dumpautoload     Dumps the autoloader
 global           Allows running commands in the global composer dir ($COMPOSER_HOME).
 help             Displays help for a command
 home             Opens the package's repository URL or homepage in your browser.
 info             Show information about packages
 init             Creates a basic composer.json file in current directory.
 install          Installs the project dependencies from the composer.lock file if present, or falls back on the composer.json.
 licenses         Show information about licenses of dependencies
 list             Lists commands
 remove           Removes a package from the require or require-dev
 require          Adds required packages to your composer.json and installs them
 run-script       Run the scripts defined in composer.json.
 search           Search for packages
 self-update      Updates composer.phar to the latest version.
 selfupdate       Updates composer.phar to the latest version.
 show             Show information about packages
 status           Show a list of locally modified packages
 update           Updates your dependencies to the latest version according to composer.json, and updates the composer.lock file.
 validate         Validates a composer.json
```

## 国内镜像

为了更好的使用composer，我们使用[phpcomposer国内镜像][2]加速，具体使用方法很简单，你可以参考官网的使用说明！

## 在项目中使用composer

比如我们的项目需要使用laravel与monolog，怎么办呢？

首先，在[packagist.org][3]中搜索需要的包名，这样我们得到了包名`monolog/monolog`，laravel同理。

![搜索 monolog][4]

其次，我们需要确定版本，我们看到官网的提示：

```json
{
    "require": {
        "vendor/package": "1.3.2",   # 明确指定版本
        "vendor/package2": "1.*",    # 使用通配符，方便以后执行更新
        "vendor/package2": "~1.3",   # 是指 1.3<= X <2.0的版本
        "vendor/package3": ">=2.0.3", # 指定一个范围
        "vendor/package3": ">=2.0.3-dev" # 默认composer会取稳定版，但是如果指明-dev，则会下载dev版
    }
}
```

更多使用方法请移步[基本用法 | Composer 中文文档 | Composer 中文网][5]

我们在项目根目录下建立文件 composer.json

```json
{
    "require": {
        "monolog/monolog": "1.0.*"
    }
}
```

### 自动生成composer.json

为了自动生成composer.json，你可以通过命令

```bash
composer init --require "monolog/monolog:1.0.*" -n
```

### 更新

如果对composer配置做了修改，需要更新库，使用以下命令

```
composer update
```

## 更多

[composer中文帮助文档][6]


  [1]: https://blog.phpgao.com/usr/uploads/2015/06/3590997597.png
  [2]: http://pkg.phpcomposer.com
  [3]: https://packagist.org/
  [4]: https://blog.phpgao.com/usr/uploads/2015/06/3445716285.png
  [5]: http://docs.phpcomposer.com/01-basic-usage.html#Package-Versions
  [6]: http://docs.phpcomposer.com