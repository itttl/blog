<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2015-12-24
title: Linux服务器iptables限制tcp连接和频率
tags: iptables,防火墙，并发优化
category: iptables
status: publist
summary: Linux服务器iptables限制tcp连接和频率
-->

![gitblog-logo](./img/logo_64x64.png)

## Linux服务器iptables限制tcp连接和频率

cc攻击一到就有点兵临城下的感觉，正确的设置防护规则可以做到临危不乱，这里给出一个iptables对ip进行连接频率和并发限制，限制单ip连接和频率的设置规则的介绍


### 单个IP在60秒内只允许新建20个连接,这里假设web端口就是80,
```
iptables -I  INPUT -i eth1 -p tcp -m tcp --dport 80 -m state --state NEW -m recent --update --seconds 60 --hitcount 20 --name DEFAULT --rsource -j DROP

iptables -I  INPUT -i eth1 -p tcp -m tcp --dport 80 -m state --state NEW -m recent --set --name DEFAULT --rsource
```

### 控制单个IP的最大并发连接数为20
```
iptables  -I INPUT -p tcp --dport 80 -m connlimit  --connlimit-above 20 -j REJECT  
```

### 每个IP最多20个初始连接
```
iptables -I  INPUT -p tcp --syn -m connlimit --connlimit-above 20 -j DROP
```

### 参数解释：
```
-p协议  

-m module_name：

-m tcp 的意思是使用 tcp 扩展模块的功能 (tcp扩展模块提供了 --dport, --tcp-flags, --sync等功能）

recent模块：

--name #设定列表名称，默认DEFAULT。

--rsource #源地址，此为默认。

--rdest #目的地址

--seconds #指定时间内

--hitcount #命中次数

--set #将地址添加进列表，并更新信息，包含地址加入的时间戳。

--rcheck #检查地址是否在列表，以第一个匹配开始计算时间。

--update #和rcheck类似，以最后一个匹配计算时间。

--remove #在列表里删除相应地址，后跟列表名称及地址


connlimit功能：

connlimit模块允许你限制每个客户端IP的并发连接数，即每个IP同时连接到一个服务器个数。

connlimit模块主要可以限制内网用户的网络使用，对服务器而言则可以限制每个IP发起的连接数。

–connlimit-above n 　　　＃限制为多少个

–connlimit-mask n 　　　 ＃这组主机的掩码,默认是connlimit-mask 32 ,即每个IP.
```

转载自[阿里云help][101]  

[101]:https://help.aliyun.com/knowledge_detail/7603021.html?spm=5176.7618386.5.1.k1xPPI
