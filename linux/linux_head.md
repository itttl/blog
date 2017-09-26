<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2014-12-17
title: head命令取出某文件中除去最后两行的所有行内容#超级发现
tags: head
category: Linux
status: publist
summary: head
-->

![gitblog-logo](./img/logo_64x64.png)


今天写binlog恢复数据脚本的时候，需要取出binlog目录下的所有完整   
的binlog文件，就是除去当前正在使用的binlog和binlog的索引日志的所有binlog文件。  

所以需要取出ls查看出的binlog目录下除去最后两行的所有行，用作循环库。  
开始打算用ls /data/mysql/log/binlog | grep -sed ……  
sed取出最后两行，用grep -v 拿掉。研究之后发现不合理  
小伙伴有知道可以告诉我一下，sed怎么取出最后一行内容  

然后决定用head  
```
ls /data/mysql/log/binlog | wc -l 可以算出binlog下的文件个数
$((`ls /data/mysql/log/binlog | wc -l` -2)) 就是去掉两个文件的个数
ls /data/mysql/log/binlog | head -n $((`ls /data/mysql/log/binlog | wc -l` -2))  这样就是除去最后两行的所有行的内容。
```
脑袋一热发出大胆猜测：
```
ls /data/mysql/log/binlog |head -n -2
```
