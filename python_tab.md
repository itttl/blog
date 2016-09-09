<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2016-04-19
title: Python tab 自动补齐
tags: python,tab
category: Python
status: publist
summary: Python tab 自动补齐 
-->

![gitblog-logo](./img/logo_64x64.png)


```
#!/usr/bin/env python
# python startup file
import sys
import readline
import rlcompleter
import atexit
import os
# tab completion
readline.parse_and_bind('tab: complete')
# history file
histfile = os.path.join(os.environ['HOME'], '.pythonhistory')
try:
    readline.read_history_file(histfile)
except IOError:
    pass
atexit.register(readline.write_history_file, histfile)


del os, histfile, readline, rlcompleter
```
