<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2015-12-14
title: Shell中单引号引用$变量
tags: shell,单引号 
category: Shell
status: publist
summary: 应用场景:单引号里引用$变量
-->

![gitblog-logo](./img/logo_64x64.png)
## 看例子
```
a=123
echo $a

echo '$a'

echo ''"$a"''
#单引号内再嵌套一个双引号套单引号
#来个清晰点的
echo '$a is '"$a"',ok itttl'
```
#### 至于有没有这种需求，那就仁者见仁智者见智了。

