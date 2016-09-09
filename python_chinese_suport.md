<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2016-06-03
title: Python2.X 中文支持
tags: python,tab
category: Python
status: publist
summary: Python2.X 中文支持
-->

![gitblog-logo](./img/logo_64x64.png)

## 添加utf-8
python2.X默认不支持中文  
若代码中用到中文需要在最开头加上  
```
#-*- coding:utf-8 -*-
or
# coding:utf-8
or
＃ coding＝utf-8
```

## 例如
```
#!/usr/bin/env python
# coding:utf-8
print "哈哈哈"

```
