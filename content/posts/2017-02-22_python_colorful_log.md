---
title: "为python的logging加点颜色吧"
categories: [ "代码人生" ]
tags: [ "python","logging" ]
draft: false
slug: "python_colorful_log"
date: "2017-02-22 15:02:00"
url: "python_colorful_log.html"
---

代码原理很简单，在需要加颜色的地方加上控制颜色显示的语义即可。


<!--more-->


## 第三方模块

[coloredlogs][1]

```
# Create a logger object.
import logging
logger = logging.getLogger('your-module')

# Initialize coloredlogs.
import coloredlogs
coloredlogs.install(level='DEBUG')

# Some examples.
logger.debug("this is a debugging message")
logger.info("this is an informational message")
logger.warn("this is a warning message")
logger.error("this is an error message")
logger.critical("this is a critical message")
```

## 效果图

![coloredlogs.png][2]

## 纯代码实现

```
import logging



BLACK, RED, GREEN, YELLOW, BLUE, MAGENTA, CYAN, WHITE = range(8)

#The background is set with 40 plus the number of the color, and the foreground with 30

#These are the sequences need to get colored ouput
RESET_SEQ = "\033[0m"
COLOR_SEQ = "\033[1;%dm"
BOLD_SEQ = "\033[1m"

def formatter_message(message, use_color = True):
    if use_color:
        message = message.replace("$RESET", RESET_SEQ).replace("$BOLD", BOLD_SEQ)
    else:
        message = message.replace("$RESET", "").replace("$BOLD", "")
    return message

COLORS = {
    'WARNING': YELLOW,
    'INFO': WHITE,
    'DEBUG': BLUE,
    'CRITICAL': YELLOW,
    'ERROR': RED
}

class ColoredFormatter(logging.Formatter):
    def __init__(self, msg, use_color = True):
        logging.Formatter.__init__(self, msg)
        self.use_color = use_color

    def format(self, record):
        levelname = record.levelname
        if self.use_color and levelname in COLORS:
            levelname_color = COLOR_SEQ % (30 + COLORS[levelname]) + levelname + RESET_SEQ
            record.levelname = levelname_color
        return logging.Formatter.format(self, record)



# Custom logger class with multiple destinations
class ColoredLogger(logging.Logger):
    FORMAT = "[$BOLD%(name)-20s$RESET][%(levelname)-18s]  %(message)s ($BOLD%(filename)s$RESET:%(lineno)d)"
    COLOR_FORMAT = formatter_message(FORMAT, True)
    def __init__(self, name):
        logging.Logger.__init__(self, name, logging.DEBUG)                

        color_formatter = ColoredFormatter(self.COLOR_FORMAT)

        console = logging.StreamHandler()
        console.setFormatter(color_formatter)

        self.addHandler(console)
        return

# 

logging.setLoggerClass(ColoredLogger)
color_log = logging.getLogger(__name__)
color_log.setLevel(logging.DEBUG)

color_log.debug("test")
color_log.info("test")
color_log.warning("test")
color_log.error("test")
color_log.critical("test")

```

## 效果图

![color_logging.png][3]

## 日志等级怎么用

<div class="tg-wrap"><table>
  <tr>
    <th>日志等级</th>
    <th>何时使用</th>
  </tr>
  <tr>
    <td>DEBUG</td>
    <td>详细信息，调试问题用。</td>
  </tr>
  <tr>
    <td>INFO</td>
    <td>确认程序的工作符合预期。</td>
  </tr>
  <tr>
    <td>WARNING</td>
    <td>指出一个无法预料的事件发生，或者一个即将发生的错误，如空间不足，软件能继续工作。</td>
  </tr>
  <tr>
    <td>ERROR</td>
    <td>因为一些严重的问题，导致软件的有些功能无法正常工作</td>
  </tr>
  <tr>
    <td>CRITICAL</td>
    <td>一个严重的错误，指明程序可能无法继续运行</td>
  </tr>
</table></div>


原帖：

[How can I color Python logging output?][4]

参考：

 - https://docs.python.org/2/library/logging.html


  [1]: https://pypi.python.org/pypi/coloredlogs
  [2]: https://blog.phpgao.com/usr/uploads/2017/03/531025398.png
  [3]: https://blog.phpgao.com/usr/uploads/2017/03/2857553976.png
  [4]: http://stackoverflow.com/questions/384076/how-can-i-color-python-logging-output