---
title: "使用Android-Studio可能会遇到的问题"
categories: [ "代码人生" ]
tags: [ "android" ]
draft: false
slug: "android_studio_faq"
date: "2014-10-26 13:23:00"
url: "android_studio_faq.html"
---

> Error:Execution failed for task ':app:processDebugManifest'. > Manifest merger failed : uses-sdk:minSdkVersion 8 cannot be smaller than version L declared in library com.android.support:support-v4:21.0.0-rc1

解决方式：
1、Change compile 'com.android.support:support-v4:+' to compile 'com.android.support:support-v4:20.+' in build.gradle. This will prevent gradle from using v4:21.0.0 that requires version L. 
2、Remove/Comment 21.0.0-rc1 in your file <android-sdk>/extras/android/m2repository/com/android/support-v4/maven-metadata.xml

> Failed to import Gradle project: Could not fetch model of type 'IdeaProject' using Gradle distribution 'http://services.gradle.org/distributions/gradle-1.6-bin.zip'.

解决方法：先从网页上将其下载下来，然后将其解压到任何位置，在环境变量path下配置路径（到gradle的bin目录），可通过cmd命令 gradle -v验证是否成功。

> "Could not find any version that matches com.android.support:support-v4:13.0.+"

解决方法：通过SDK Manage 安装Android Support Repository

> Connection to https://dl-ssl.google.com refused

解决方法：找到C:\WINDOWS\system32\drivers\etc中的hosts文件，添加`74.125.237.1 dl-ssl.google.com`保存退出

> 通过菜单help->install update ，无法更新 Android Studio

解决办法：找到其目录下bin-studio.exe.vmoptions,添加： 

```
-Didea.updates.url=https://dl.google.com/android/studio/patches/updates.xml
-Didea.patches.url=https://dl.google.com/android/studio/patches/
```

> 错误：svn: E175002: handshake alert: unrecognized_name

解决方法：在Studio程序bin文件夹下的studio.vmoptions添加

```
-Dsvnkit.http.sslProtocols=SSLv3
-Djsse.enableSNIExtension=false
```

> Manifest merging failed

解决方法：在build.gradle文件中 将AndroidMainfest.xml对应的版本号，版本名补充完整

转自：http://waychel.com/shi-yong-android-studioke-neng-hui-yu-dao-de-wen-ti/