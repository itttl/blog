<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2016-04-26
title: Nginx+php中php-fpm参数配置优化
tags: PHP,php-fpm
category: PHP
status: publist
summary: Nginx+php中php-fpm参数配置优化,性能相关参数含义以及优化公式
-->

![gitblog-logo](./img/logo_64x64.png)
## Nginx+php中php-fpm参数配置优化



### 配置Nginx服务中，通常需要用php-fpm来解释执行php程序。

 - 注：php-fpm是随php安装而产生



php-fpm有两种不同的进程管理方式，通过pm这个参数进行设置，pm是来控制php-fpm的工作进程数到底是一次性产生固定不变(static)还是在运行过程中随着需要动态变化(dynamic)。
两种模式大致可以通过以下判断进行选择：  

- 可以用网站的访问情况进行分析，如果网站每天的访问量都很稳定，波动不大，而且一天的不同时间段内的访问量波动也不大，可以使用dynamic模式。  

- 如果波动很大，可以考虑使用static，避免进程的频繁创建带来的CPU负载的增加。  


### php-fpm.conf中与进程数相关的参数说明：
```
pm = static  
//pm决定进程管理器如何控制子进程数目。
//static表示pm.max_children固定子进程数；
//dynamic表示子进程数是动态变化的。



pm.max_children = 50      
//设置并发请求数，它与apache prefork模式下的Maxclients等效，与php-cgi中的PHP_FCGI_CHILDREN参数等效。
//在pm设置为static时，系统中php-fpm主进程可以创建的php-fpm子进程数目；
//在pm设置为dynamic时，系统中php-fpm主进程可以创建的最大php-fpm子进程数目。
//这个值的设置可以通过top观察每个php-fpm进程占用的内存数，然后结合服务器的内存进行判断。

```
#### 举例：

每个php-fpm子进程占用20M内存，服务器的内存是1G，除去系统中其他应用占用的内存，假如为300M，剩余700M，700/20=35，最多设置35，可以取值稍微低一些为30。  


```
pm = dynamic
pm.start_servers = 5       //php-fpm启动起始进程数
pm.min_spare_servers = 5   //php-fpm的最小空闲进程数
pm.max_spare_servers = 35  //php-fpm的最大空闲进程数
pm.max_requests = 5000   
 //每个子进程重生之前服务的请求数，主要目的是为了控制请求处理过程中的内存溢出，使得内存占用在一个可接受的范围内。如果设置为 '0' 则一直接受请求。它不能设置为非常小的值，当这个数字非常小的时候，由于PHP请求是平均地分配给各个工作进程的，如果这个值太小就会导致所有的工作进程几乎同时达到这个值并且进入该进程自身重启的状态，当所有的工作进程都在同一时刻重启就会发生在数秒内甚至更长的时间PHP将停止响应直到所有的进程均重启完为止，此时有新请求时则无法被正常处理，只能排队待进程重启完成，如果超过了超时时间，会提示502 bad gateway。同时因为进程频繁停止与创建，可以看到CPU利用率有明显的增加。所以这个值需要调整稍微大一些。这个值可以通过以下的方式进行估算：
pm.max_requests = 进程重启间隔时间*并发量/pm.max_children 
```
#### 举例：
所有子进程重启时间为300s一次，并发量为50，max_children设置为25，max_requests = 300*50/25=600，如果想让进程每隔一小时重生一次，则是3600*50/25=7200。  

 

#### 注：以下是两种模式下的php-fpm进程的运行情况
##### 1、
```
pm = static
pm.max_children = 5
[root@iZ25mkjrlboZ etc]# ps aux | grep php-fpm | egrep -v "master|grep"
www       4287  0.0  0.4 273296  4828 ?        S    16:27   0:00 php-fpm: pool www
www       4288  0.0  0.4 273296  4828 ?        S    16:27   0:00 php-fpm: pool www
www       4289  0.0  0.4 273296  4828 ?        S    16:27   0:00 php-fpm: pool www
www       4290  0.0  0.4 273296  4828 ?        S    16:27   0:00 php-fpm: pool www
www       4291  0.0  0.4 273296  4828 ?        S    16:27   0:00 php-fpm: pool www
```
可以看到有5个进程在运行中。  

##### 2、
```
pm = dynamic
pm.max_children = 50
pm.start_servers = 4
pm.min_spare_servers = 4
pm.max_spare_servers = 20
[root@iZ25mkjrlboZ etc]# ps aux | grep php-fpm | egrep -v "master|grep"
www       4306  0.0  0.4 273476  4828 ?        S    16:32   0:00 php-fpm: pool www  
www       4307  0.0  0.4 273476  4828 ?        S    16:32   0:00 php-fpm: pool www  
www       4308  0.0  0.4 273476  4828 ?        S    16:32   0:00 php-fpm: pool www  
www       4309  0.0  0.4 273476  4828 ?        S    16:32   0:00 php-fpm: pool www
```
可以看到初始启动了4个进程。  

[转载自阿里help](https://help.aliyun.com/knowledge_detail/5989822.html)
