---
title: "获取QQ头像"
categories: [ "代码人生" ]
tags: [ "qq" ]
draft: false
slug: "qq_face_url"
date: "2020-10-26 08:04:00"
url: "qq_face_url.html"
---

最近有一个需求，某个需求的某个功能是获取QQ号码的头像，虽然QQ内部有方法能够获取到这个头像的URL，但是多一次请求总是会对性能有影响，所以最后搜索到了一种方法，只要只到对方的QQ号码，就可以拿到Ta的头像。


<!--more-->


其实很简单，比如某人的QQ号是123456，那么Ta的头像地址就是

> https://q.qlogo.cn/g?b=qq&nk=123456&s=100
https://q.qlogo.cn/g?b=qq&nk=123456&s=160
https://q.qlogo.cn/g?b=qq&nk=123456&s=640

> Use it if you're quoting a person, a song or whatever.

> You can use *italic* or lists inside them also.
And just like with other paragraphs,
all of these lines are still
part of the blockquote, even without the > character in front.

To end the blockquote, just put a blank line before the following
paragraph.


> Docker匹配镜像决定是否使用缓存的规则如下:
> 1. 从缓存中存在的基础镜像开始，比较所有子镜像，检查它们构建的指令是否和当前的是否完全一致。如果不一致则缓存不匹配。
> 1. 多数情况中，使用其中一个子镜像来比较Dockerfile中的指令是足够的。然而，特定的指令需要做更多的判断。
> 1. ADD COPY 指令中，将要添加到镜像中的文件也要被检查。通常是检查文件的校验和(checksum)。
> 1. 缓存匹配检查并不检查容器中的文件。例如，当使用 RUN apt-get -y update 命令更新了容器中的文件，并不会被缓存检查策略作为缓存匹配的依据。

把其中123456替换为你的QQ号，然后在浏览器中打开，是不是就是你的头像啦！细心的同学能够看出这个URL还带一个s的参数，其实就是size的意思，目前测试100，160，640都可以。

聪明的你肯定能够发现，这样URL地址就会暴露qq号码了，那么有没有办法能够在不暴露QQ号码的情况下展示QQ头像呢？当然是可以的，只是多了一个步骤而已。

```
http://ptlogin2.qq.com/getface?&imgtype=1&uin=123456
```

这个接口返回了一个json，里面是一个url，其实他也是一个头像地址，只是这个地址的参数中k的值为一串字符串`xyOnRe5ML3Aw96iaaQ1hh6w`，并非之前的QQ号，但是如果把这个字符串带入到之前的参数nk中，然后把nk改为k，我们就得到了下面的URL，虽然效果一样，但是这一次就没有QQ号码在里面了，是不是很神奇？

> https://q.qlogo.cn/g?b=qq&k=xyOnRe5ML3Aw96iaaQ1hh6w&s=100
https://q.qlogo.cn/g?b=qq&k=xyOnRe5ML3Aw96iaaQ1hh6w&s=160
https://q.qlogo.cn/g?b=qq&k=xyOnRe5ML3Aw96iaaQ1hh6w&s=640



同时，为了提高性能，在返回时我们可以请求不同的域名以达到加速展示的效果


> https://q.qlogo.cn/g?b=qq&k=xyOnRe5ML3Aw96iaaQ1hh6w&s=640
https://q1.qlogo.cn/g?b=qq&k=xyOnRe5ML3Aw96iaaQ1hh6w&s=640
https://q2.qlogo.cn/g?b=qq&k=xyOnRe5ML3Aw96iaaQ1hh6w&s=640
https://q3.qlogo.cn/g?b=qq&k=xyOnRe5ML3Aw96iaaQ1hh6w&s=640
https://q4.qlogo.cn/g?b=qq&k=xyOnRe5ML3Aw96iaaQ1hh6w&s=640

---
完