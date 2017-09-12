<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2015-02-12
title: Vim Tab设置为4个空格
tags: vim,tag
category: Linux
status: publist
summary: Vim Tab设置为4个空格
-->

![gitblog-logo](./img/logo_64x64.png)

## 在.vimrc中添加以下代码后，重启vim即可实现按TAB产生4个空格：
```
set ts=4  (注：ts是tabstop的缩写，设TAB宽4个空格)
set expandtab (设置tab为四个空格，而不是一个tab距离为四个空格)
```
对于已保存的文件，可以使用下面的方法进行空格和TAB的替换：
## TAB替换为空格：
```
:set ts=4
:set expandtab
:%retab!
```
## 空格替换为TAB：
```
:set ts=4
:set noexpandtab
:%retab!
```

- 加!是用于处理非空白字符之后的TAB，即所有的TAB，若不加!，则只处理行首的TAB。
