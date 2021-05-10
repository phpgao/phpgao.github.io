---
title: "在github写博客"
categories: [ "代码人生" ]
tags: [ "github","jekyll" ]
draft: false
slug: "github_blog"
date: "2014-07-26 04:19:00"
url: "github_blog.html"
---

没想到jekyll这么火啊！

今天闲来无事，也想试试把博客搬到github玩玩。


<!--more-->


## 开启github主页

 - 以我的主页来说，先在[github主页][1]新建一个名为 `phpgao.github.com` 的仓库

 - 请将`phpgao`换为你的名字(旧仓库按理来说也可以直接使用，本人未验证)。

 - 进入[仓库][2]，点击Settings

![进入设置][3]

 - 在设置里找到`Automatic page generator`按钮

 - 一直下一步，完成后过10min后，就可以看到[你的github主页][4]

 - 这个仓库就可以作为你的博客啦！

## 绑定域名

如果没有做任何设置，那么新博客的地址就是类似 `https://yourname.github.com/` 的地址，`yourname`是你的帐户名。

如果你需要将你自有的域名和这个地址做关联，那么就需要做域名解析，分两种情况：

### 顶级域名

如果你需要将顶级域名解析到github，就新建一条A记录，将@和www解析到204.232.175.78就可以了。

### 二级域名

二级域名的设置方法也很简单

首先，将你需要为二级域名新建一个CNAME解析，连接到上面提到的 `yourname.github.com` 的地址，`yourname`是你的帐户名，注意不要带`http://`

然后在根目录下新建一个CNAME文件，不要后缀名，然后把刚才解析到的`yourname.github.com`写入到这个CNAME文件里，e.g.博主的CNAME文件里写的就是`github.phpgao.com`，然后add，commit，push到远程仓库中去。

> 做完解析后可能要等一段时间才能看到效果

# 接下来写写如何使用jekyll和jekyll-bootstrap

.....待续

最后欢迎[fork me on github][5]

  [1]: https://github.com/phpgao/
  [2]: https://github.com/phpgao/phpgao.github.com
  [3]: https://blog.phpgao.com/usr/uploads/2014/07/440938489.jpg
  [4]: https://phpgao.github.com/
  [5]: http://github.phpgao.com/