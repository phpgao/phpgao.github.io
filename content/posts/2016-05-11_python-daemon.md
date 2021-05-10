---
title: "python守护进程---python-daemon"
categories: [ "代码人生" ]
tags: [ "python","daemon" ]
draft: false
slug: "python-daemon"
date: "2016-05-11 10:16:00"
url: "python-daemon.html"
---

python的守护进程模块，遵循[PEP 3143][1]，很好很强大！


<!--more-->


## install

```
# https://pypi.python.org/pypi/python-daemon/
sudo pip install python-daemon
```

## Usage

```
import logging
import time

# third party libs
from daemon import runner


class Updater():
    def __init__(self):
        self.stdin_path = '/dev/null'
        self.stdout_path = '/dev/tty'
        self.stderr_path = '/dev/tty'
        self.pidfile_path = '/tmp/testdaemon.pid'
        self.pidfile_timeout = 5

    def run(self):
        while True:
            # Main code goes here ...
            # Note that logger level needs to be set to logging.DEBUG before this shows up in the logs
            logger.debug("Debug message")
            logger.info("Info message")
            logger.warn("Warning message")
            logger.error("Error message")
            time.sleep(10)


app = Updater()
logger = logging.getLogger("DaemonLog")
logger.setLevel(logging.INFO)
formatter = logging.Formatter("%(asctime)s - %(name)s - %(levelname)s - %(message)s")
handler = logging.FileHandler("/tmp/testdaemon.log")
handler.setFormatter(formatter)
logger.addHandler(handler)

daemon_runner = runner.DaemonRunner(app)

# This ensures that the logger file handle does not get closed during daemonization
daemon_runner.daemon_context.files_preserve = [handler.stream]
daemon_runner.do_action()
```



Refer:

[Create a daemon on Ubuntu][2]
[Building a python daemon process][3]
[How do you use python-daemon the way that it's documentation dictates?][4]


  [1]: https://www.python.org/dev/peps/pep-3143
  [2]: http://arduinotips.blogspot.tw/2013/09/create-daemon-on-raspberry-pi.html
  [3]: http://www.gavinj.net/2012/06/building-python-daemon-process.html
  [4]: http://stackoverflow.com/questions/30408589/how-do-you-use-python-daemon-the-way-that-its-documentation-dictates