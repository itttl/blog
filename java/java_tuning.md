<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2017-09-07
title: Java进程CPU过高故障排查
tags: 调优,java
category: 调优
status: publist
summary: Java进程CPU过高故障排查,top、ps、grep、printf、jstack等命令
-->


## 问题描述：
排查java服务CPU负载异常过高。


## 解决过程：
### 1，根据top命令，发现PID为9914的Java进程占用CPU高达150%，出现故障。

### 2，找到该进程后，如何定位具体线程或代码呢，首先显示线程列表,并按照CPU占用高的线程排序：
```
[root@test01 logs]# ps -mp 9914 -o THREAD,tid,time | sort -rn
```
#### 显示结果如下：   
```
USER     %CPU PRI SCNT WCHAN  USER SYSTEM   TID     TIME
root     28.6  19    - futex_    -      - 10032 00:16:56
root     28.6  19    - -         -      -  9985 00:16:59
root     28.5  19    - futex_    -      -  9981 00:16:56
root     28.5  19    - -         -      - 10108 00:16:55
root      116   -    - -         -      -     - 01:08:50
root      0.5  19    - futex_    -      -  9983 00:00:18
root      0.3  19    - futex_    -      -  9917 00:00:11
root      0.3  19    - futex_    -      -  9916 00:00:11
root      0.2  19    - futex_    -      -  9918 00:00:08
root      0.1  19    - futex_    -      -  9922 00:00:04
root      0.0  19    - skb_re    -      - 21908 00:00:00
root      0.0  19    - futex_    -      -  9984 00:00:00
root      0.0  19    - futex_    -      -  9925 00:00:02
root      0.0  19    - futex_    -      -  9924 00:00:00
root      0.0  19    - futex_    -      -  9923 00:00:01
root      0.0  19    - futex_    -      -  9921 00:00:00
root      0.0  19    - futex_    -      -  9920 00:00:00
root      0.0  19    - futex_    -      -  9919 00:00:00
root      0.0  19    - futex_    -      -  9915 00:00:00
root      0.0  19    - futex_    -      -  9914 00:00:00
root      0.0  19    - ep_pol    -      -  9976 00:00:03
```
找到了耗时最高的线程10032，占用CPU时间有16分钟了！   

将需要的线程ID转换为16进制格式：
```
[root@test01 logs]# printf "%x\n" 10032
```
2730

最后打印线程的堆栈信息：
```
[root@test01 logs]# jstack 2633 |grep 2730 -A 30
```

将输出的信息发给开发部进行确认，这样就能找出有问题的代码。

