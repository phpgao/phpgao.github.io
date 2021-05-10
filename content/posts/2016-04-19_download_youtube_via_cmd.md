---
title: "使用命令行下载youtube的视频和字幕"
categories: [ "代码人生" ]
tags: [ "youtube","download" ]
draft: false
slug: "download_youtube_via_cmd"
date: "2016-04-19 14:24:00"
url: "download_youtube_via_cmd.html"
---

老高买使用搬瓦工有好久了，但是直到今天才想起来用命令行下载youtube的视频，实在是惭愧。赶紧加班写篇文章赎罪！

![youtube.png][1]

今天的主角在这儿[youtube-dl][2]

源代码点这儿[rg3/youtube-dl][3]

<!--more-->


## 安装

```
pip install --upgrade youtube_dl
```

or

```
sudo curl https://yt-dl.org/downloads/2016.04.19/youtube-dl -o /usr/local/bin/youtube-dl

sudo chmod a+rx /usr/local/bin/youtube-dl
```

or

```
sudo wget https://yt-dl.org/downloads/2016.04.19/youtube-dl -O /usr/local/bin/youtube-dl

sudo chmod a+rx /usr/local/bin/youtube-dl
```

mac下也有

```
brew install youtube-dl
```


安装好了以后，在命令行输入`youtube-dl`，按回车，就能够看到其使用说明了！

![youtube-dl][4]

## 使用

如上图所示，最简单的使用方法就是

```
# https://www.youtube.com/watch?v=nNGmruHS3r8 就是要下载的视屏的URL
youtube-dl https://www.youtube.com/watch?v=nNGmruHS3r8
```


```
➜  ~ youtube-dl https://www.youtube.com/watch\?v\=UECh7X07m6E
[youtube] UECh7X07m6E: Downloading webpage
[youtube] UECh7X07m6E: Downloading video info webpage
[youtube] UECh7X07m6E: Extracting video information
[youtube] UECh7X07m6E: Downloading MPD manifest
[download] Destination: Golang UK Conference 2015 - Andrew Gerrand - Stupid Gopher Tricks-UECh7X07m6E.mp4
[download]  91.8% of 352.87MiB at 20.07MiB/s ETA 00:01
```

仔细观察可以看到，老高的VPS可以达到每秒20M的下载速度，下载速度还算不错。

ps.下载好的文件名中可能包含空格，我们可以使用命令`for f in *\ *; do mv "$f" "${f// /-}"; done`把文件名中的空格替换为`-`号，以方便传输！

等下载完成以后我们可以使用bypy上传到百度网盘，或者用命令`python -m SimpleHTTPServer 8080`搭建一个临时http服务器把文件离线回来！

## 下载方法

以运行python为例，进入下载好文件的目录中，然后执行之前提到的命令。

```
cd youtube/

python -m SimpleHTTPServer 8080
```

如果没有错误发生，现在访问你的`IP:8080`就能看到文件列表了！

![文件列表][5]

接下来的事情就太简单了，复制下载链接，然后添加到迅雷的下载列表里就可以把文件取回来了，不过因为是从美国那边下载，所以速度可能会慢一点。老高一般能达到500K/s.


动手能强的同学可以使用bypy，直接上传文件到百度网盘，不过百度网盘最近的限速也是让人很蛋疼，所以老高还是推荐http下载！

## 一些参数选项

应@Levi Zhang的需求，老高介绍一些比较常用的参数吧！ 还有@Levi Zhang推荐的另一款命令行工具[you-get][6]也很厉害，他也是使用python语言写的，看来py真是写爬虫的利器！

```
youtube-dl -h

-r, 限制最大下载速度 (e.g. 50K or 4.2M)
--yes-playlist  当你的下载链接是一个列表时，下载他.
--playlist-reverse   倒序下载列表
--write-sub   下载自带字幕
--write-auto-sub   下载自动生成的字幕(YouTube only)
-x, --extract-audio   转换为音频，依赖 ffmpeg or avconv and ffprobe or avprobe
--restrict-filenames     墙裂建议带上，避免文件名里出现 "&" 和空格
```


  [1]: https://blog.phpgao.com/usr/uploads/2016/04/2588463203.png
  [2]: https://rg3.github.io/youtube-dl/download.html
  [3]: https://github.com/rg3/youtube-dl
  [4]: https://blog.phpgao.com/usr/uploads/2016/04/653681467.png
  [5]: https://blog.phpgao.com/usr/uploads/2016/04/2301664888.jpg
  [6]: https://you-get.org/