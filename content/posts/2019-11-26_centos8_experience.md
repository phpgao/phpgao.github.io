---
title: "Centos8-初体验"
categories: [ "浏览器" ]
tags: [ "centos" ]
draft: false
slug: "centos8_experience"
date: "2019-11-26 06:34:00"
url: "centos8_experience.html"
---

centos8已经发布有一段时间了，老高近期下载并安装了`CentOS-8-x86_64-1905-boot.iso`版本，给大家当个小白鼠！

![begin.png][1]
<!--more-->

整体来说，和centos7相比，差别不大！基本centos7的教程可以照搬到centos8中来！

其中最明显的改变一定是包管理系统从`yum`变为了`dnf`，`yum`虽然得以保留，但其实早已被换成dnf了！

```
tail /usr/bin/yum

from dnf.cli import main
main.user_main(sys.argv[1:], exit_code=True)
```

> 小提示，在安装过程中遇到错误 `error setting up base repository`，可以在地址选择http，然后填写`mirrorlist.centos.org/?release=8&arch=x86_64&repo=BaseOS`，然后点击**Done**即可解决！

![install][2]

![mirror][3]

![select][4]

![soft][5]


## 老高常用的软件以及对应的版本


| 软件名称       | 版本                                             |
|---------------|------------------------------------------------|
| gcc           | 8.2.1-3.5.el8                                  |
| git           | 2.18.1-3.el8                                   |
| golang        | 1.11.6-1.module_el8.0.0+192+8b12aa21           |
| httpd         | 2.4.37-12.module_el8.0.0+185+5908b0db          |
| mysql         | 8.0.17-3.module_el8.0.0+181+899d6349           |
| nginx         | 1:1.14.1-9.module_el8.0.0+184+e34fea82         |
| nodejs        | 1:10.16.3-2.module_el8.0.0+186+542b25fc        |
| php           | 7.2.11-1.module_el8.0.0+56+d1ca79aa            |
| podman-docker | 1.0.5-1.gitf604175.module_el8.0.0+194+ac560166 |
| postgresql    | 10.6-1.module_el8.0.0+15+f57f353b              |
| python36      | 3.6.8-2.module_el8.0.0+33+0a10c0e1             |
| redis         | 5.0.3-1.module_el8.0.0+6+ab019c03              |
| wget          | 1.19.5-7.el8_0.1                               |
| zsh           | 5.5.1-6.el8                                    |

## 补充

`development tools`这个命令还是可以用的！

```bash
dnf groupinstall "development tools"
```

  [1]: https://blog.phpgao.com/usr/uploads/2019/11/3214508915.png
  [2]: https://blog.phpgao.com/usr/uploads/2019/11/1947563906.png
  [3]: https://blog.phpgao.com/usr/uploads/2019/11/2428648117.png
  [4]: https://blog.phpgao.com/usr/uploads/2019/11/160445418.png
  [5]: https://blog.phpgao.com/usr/uploads/2019/11/3756931719.png