---
title: "puppeteer的使用教程2---更多设置"
categories: [ "浏览器" ]
tags: [ "puppeteer" ]
draft: false
slug: "puppeteer_basic_settings"
date: "2019-08-19 11:47:00"
url: "puppeteer_basic_settings.html"
---

在第一篇，老高只是简单的介绍了puppeteer如何安装和简单的用法，在第二篇中，老高为大家带来chrome浏览器的一些基本设置，比如禁止gpu以提升性能等等！

<!--more-->

下面是老高总结出来的很常用的chrome浏览器的args

```
'--no-sandbox',  # 不使用沙箱
'--proxy-server=http://127.0.0.1:1080', # 代理
'--no-startup-window',  # 禁止开始界面
'--incognito',  # 隐身模式
'--disable-infobars',  # 禁止信息提示栏
'--disable-gpu',  # 不用gpu
'--disable-setuid-sandbox',  # 与--no-sandbox配合
'--no-default-browser-check',  # 不检查默认浏览器
'--disable-extensions',  # 禁止扩展
'--disable-default-apps',  # 禁止默认应用
'--disable-dev-shm-usage',  # 禁止使用/dev/shm，防止内存不够用
'--disable-hang-monitor',  # 禁止页面无响应提示
'--disable-popup-blocking',  # 禁止popup
'--disable-prompt-on-repost',  # 禁止重新发送post请求的提示
'--disable-sync',  # 禁止同步
'--disable-translate',  # 禁止翻译
'--disable-bundled-ppapi-flash',  # 禁止内置的flash
'--disable-component-update',  # 禁止组件升级
'--disable-background-networking',  # 禁止后台的网络连接
'--disable-background-timer-throttling',  # 禁止后台任务冻结
'--disable-client-side-phishing-detection',  # 禁止危险页面检测
'--disable-logging',
'--mute-audio',  # 静音
'--single-process',  # 单进程
'--no-zygote',  # 禁止zygote进程fork子进程
'--safebrowsing-disable-auto-update',
'--no-first-run',  # 禁止首次运行界面
'--hide-scrollbars',  # 隐藏滚动栏
'--use-mock-keychain',  # 使用mock-keychain防止提示权限提示
'--ignore-certificate-errors',  # 忽略证书错误
```


更多选项可以参考[chromedriver中的浏览器选项][1] && [Troubleshooting  |  Tools for Web Developers  |  Google Developers][2] &&  [Flags][3]

> 注意！这些选项是老高经常会用到的，但是其中某些选项只是针对个别系统的，比如`--use-mock-keychain`只有mac系统生效，而`--disable-dev-shm-usage`之对Linux系统生效！

那么如何使用这些flag呢？其实很简单，将下面的代码保存为`2.js`

```javascript
const puppeteer = require('puppeteer');

options = [
    // 这里就是flag应该添加的地方
    // 这是一个数组
    '--no-sandbox',
    '--hide-scrollbars',
]

args = {
    executablePath: '/Users/xxx/code/Chromium.app/Contents/MacOS/Chromium',
    // options 最终在这里被引用，最后会被以args的性质传给chrome浏览器
    args: options,
};
(async () => {
    const browser = await puppeteer.launch(args);
    const page = await browser.newPage();
    await page.setViewport({width:1920, height:1080});
    await page.goto('https://blog.phpgao.com');
    await page.screenshot({ path: 'phpgao.png' });
    await browser.close();
})();
```


运行以上代码后，会在当前文件夹看到一张老高博客的截图，并且没有滚动栏，是不是很神奇！

![phpgao的截图][4]

> 你注意到了吗？代码中有一句`page.setViewport({width:1920, height:1080});`，这句代码将浏览器的窗口设为1920\*1080的大小，所以我们的截图才是`16:9`的样子，还记得`1.js`中我们的截图吗？他的默认分辨率为800\*600。

下一节，老高会教你如何与页面互动！


  [1]: https://www.cnblogs.com/hushaojun/p/5981646.html
  [2]: https://developers.google.com/web/tools/puppeteer/troubleshooting
  [3]: https://github.com/GoogleChrome/chrome-launcher/blob/master/docs/chrome-flags-for-tools.md
  [4]: https://blog.phpgao.com/usr/uploads/2019/08/3222189880.png