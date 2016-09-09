<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2016-06-03
title: Python中操作文件之seek()方法的使用教程
tags: python,seek
category: Python
status: publist
summary: Python中操作文件之seek()方法的使用教程
-->

![gitblog-logo](./img/logo_64x64.png)

### seek()方法的语法：
```
fileObject.seek(offset[, whence])

参数
    offset -- 这是在文件中，读/写指针的位置。
    whence -- 这是可选的，默认为0，这意味着绝对的文件定位，其它的值是1，这意味着寻求相对于当前位置，2表示相对于文件的末尾。
返回值
此方法不返回任何值。
```
### 例子
#### 文件
#cat 03/write.txt
```
123456
hahaha
oneoneone
```
####
```
#!/usr/bin/env python
#coding:utf-8
import os
f=open("03/write.txt","r+")

f.seek(0,0)
b=f.readline()
print b
f.seek(0,1)
print b
f.close
```
