---
title: "Dropbox新姿势备份服务器数据"
categories: [ "服务器技术" ]
tags: [ "dropbox","backup" ]
draft: false
slug: "dropbox_backup"
date: "2017-10-13 08:00:00"
url: "dropbox_backup.html"
---

老高以前写过一篇[使用dropbox同步备份网站和数据库][1]的文章，里面的内容虽说能用，但已经很过时了，今天老高给大家介绍一中更好的方式，那就是用过API备份。


<!--more-->


> 新申请的Dropbox账号，默认有2G的可用空间，可以通过邀请等方式最高扩容到18G。老高直接18元入了马云家的18G账号回来改密码和邮箱?

## 准备

打开[App Console - Dropbox][2]，点击 **Create app**

 1\. Choose an API

选 **Dropbox API**

 2\. Choose the type of access you need

选 **App folder** ，也就是把权限控制在某一个文件夹里，比较安全，就算KEY泄露了也不会伤及无辜。

 3\. Name your app

输入app名称，全英文，下划线可以有，不要有空格，这里创建的名称会进入到 /应用/app名称的文件夹里

最后点击创建即可！

随后进入APP设置中，在OAuth 2中找到**Generated access token**，点一下，复制生成的token


## 工具

[andreafabrizi/Dropbox-Uploader][3]

```bash

# 初始化
git clone https://github.com/andreafabrizi/Dropbox-Uploader.git

cd Dropbox-Uploader
chmod a+x ./*.sh

# 第一次执行dropbox_uploader.sh时会检查主目录下面有没有 .dropbox_uploader，access token就记录在此文件里，不要忘了输入后还有一步确认

./dropbox_uploader.sh

 This is the first time you run this script, please follow the instructions:

 1) Open the following URL in your Browser, and log in using your account: https://www.dropbox.com/developers/apps
 2) Click on "Create App", then select "Dropbox API app"
 3) Now go on with the configuration, choosing the app permissions and access restrictions to your DropBox folder
 4) Enter the "App Name" that you prefer (e.g. MyUploader23201463624452)

 Now, click on the "Create App" button.

 When your new App is successfully created, please click on the Generate button
 under the 'Generated access token' section, then copy and paste the new access token here:
```


第一次运行成功后我们就可以使用他备份或者下载文件了

```bash
./dropbox_uploader.sh -h

Dropbox Uploader v1.0
Andrea Fabrizi - andrea.fabrizi@gmail.com

Usage: ./dropbox_uploader.sh [PARAMETERS] COMMAND...

Commands:
	 upload   <LOCAL_FILE/DIR ...>  <REMOTE_FILE/DIR>
	 download <REMOTE_FILE/DIR> [LOCAL_FILE/DIR]
	 delete   <REMOTE_FILE/DIR>
	 move     <REMOTE_FILE/DIR> <REMOTE_FILE/DIR>
	 copy     <REMOTE_FILE/DIR> <REMOTE_FILE/DIR>
	 mkdir    <REMOTE_DIR>
	 list     [REMOTE_DIR]
	 monitor  [REMOTE_DIR] [TIMEOUT]
	 share    <REMOTE_FILE>
	 saveurl  <URL> <REMOTE_DIR>
	 search   <QUERY>
	 info
	 space
	 unlink

Optional parameters:
	-f <FILENAME> Load the configuration file from a specific file
	-s            Skip already existing files when download/upload. Default: Overwrite
	-d            Enable DEBUG mode
	-q            Quiet mode. Don't show messages
	-h            Show file sizes in human readable format
	-p            Show cURL progress meter
	-k            Doesn't check for SSL certificates (insecure)

For more info and examples, please see the README file.
```

把本地的/tmp/123文件上传到dropbox上

```
./dropbox_uploader.sh upload /tmp/123 /tmp/123

```





  [1]: https://blog.phpgao.com/dropbox_sync_cli.html
  [2]: https://www.dropbox.com/developers/apps
  [3]: https://github.com/andreafabrizi/Dropbox-Uploader