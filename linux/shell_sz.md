<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2017-06-04
title: Shell 数组
tags: shell,数组
category: Shell
status: publist
summary: 
-->

```
arr=(1 2 3 4 4)   #定义数组

echo ${#arr[*]}   #打印数组长度
echo ${arr[0]}    #打印数组的第一个成员
echo ${arr[1]}    #打印数组的二个成员
echo ${arr[*]}    #打印全部内容

echo ${arr[*]:1:3} #打印下标从1到3的内容

for i in ${arr[*]} #遍历数组
do
    echo $i
done 

arr[1]=10          #将下标为1的内容设置为100
echo ${arr[1]}

echo ${arr[*]}    #打印全部内容
unset arr[0]      #删除下标为0的内容
echo ${arr[*]}    #打印全部内容

arr[0]=100        #重新设置下标为0的内容为100
echo ${arr[*]}    #打印全部内容

unset arr         #删除数组的全部内容
echo ${arr[*]}    #打印全部内容
```
