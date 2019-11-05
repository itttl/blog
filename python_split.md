<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2016-06-03
title: Python split 字符串数组话
tags: python,split
category: Python
status: publist
summary: Python split 字符串数组话
-->

![gitblog-logo](./img/logo_64x64.png)

```
#!/usr/bin/env python
#coding:utf-8


names="亮哥 李白 杜甫"
names1="nginx:php:mysql"
print names
print names1

## 默认空格拆分
namesList=names.split()
## 指定：拆分
names1List=names1.split(':')
print namesList
print names1List

print namesList[1]
print names1List[2]

```
