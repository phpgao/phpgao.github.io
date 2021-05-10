---
title: "linux无界面(headless)使用selenium抓取数据"
categories: [ "代码人生" ]
tags: [ "centos","python","Linux","headless","selenium" ]
draft: false
slug: "headless_selenium"
date: "2014-12-09 09:47:00"
url: "headless_selenium.html"
---

## 问题

老高最近遇到一个需求，linux\centos下，使用selenium技术抓取数据。本来很简单的问题，但是由于内存限制，安装X window不现实，所以一个BT的想法诞生了，是否可以在centos命令行界面运行一个虚拟的桌面，然后使用selenium控制Firefox浏览器完成一些操作，Firefox运行在虚拟的桌面中，一切操作都在命令行中完成。

Google之，发现了Xvfb，他可以新建一个虚拟的X窗口，再配合python的pyvirtualdisplay，简直就是神器！

## 安装

centos下：

```bash
# 安装Xvfb和pyvirtualdisplay
yum install xorg-x11-server-Xvfb
pip install pyvirtualdisplay
```

安装firefox和selenium

```bash
yum install firefox
pip install selenium
```

## 代码

```python
from pyvirtualdisplay import Display
from selenium import webdriver

display = Display(visible=0, size=(800, 600))
display.start()

browser = webdriver.Firefox()
browser.get('http://www.google.com')
print browser.title
browser.quit()

display.stop()
```


参考网站：

http://selenium-python.readthedocs.org/en/latest/getting-started.html
http://nullege.com/codes/search/selenium.webdriver.Remote.find_elements_by_class_name
http://www.opsview.com/forum/opsview-core/how-do-i/how-do-i-install-selenium-centos-server
https://gist.github.com/textarcana/5855427
http://scraping.pro/use-headless-firefox-scraping-linux/
http://serverfault.com/questions/363827/how-can-i-run-firefox-on-centos-with-no-display
https://realpython.com/blog/python/headless-selenium-testing-with-python-and-phantomjs/
https://pypi.python.org/pypi/selenium

http://selenium.googlecode.com/git/docs/api/py/selenium/selenium.selenium.html#module-selenium.selenium

http://www.ibm.com/developerworks/cn/opensource/os-php-designptrns/
http://www.cnblogs.com/fnng/p/3230768.html
http://www.cnblogs.com/fnng/p/3157639.html
http://www.cnblogs.com/fnng/p/3157639.html