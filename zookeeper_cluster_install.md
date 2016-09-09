<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2016-01-04
title: zookeeper集群安装配置 
tags: zookeeper
category: zookeeper
status: publist
summary: zookeeper集群安装配置
-->

![gitblog-logo](./img/logo_64x64.png)

/etc/hosts
```
10.0.0.52 n12
10.0.0.53 n13
10.0.0.54 n14
```

## 下载地址
[http://apache.dataguru.cn/zookeeper/zookeeper-3.4.7/zookeeper-3.4.7.tar.gz][101]

如果慢或者打不开，可以到[apache-zookeeper-download][102]找不同的资源下载想要的版本  

## 一、安装
```
tar zxvf /data/package/zookeeper-3.4.7.tar.gz -C /usr/local/
```
## 二、配置
```
cd /usr/local/zookeeper-3.4.7
```
### 1.配置文件
默认读取的是zoo.cfg
```
$ cat conf/zoo.cfg 

dataDir=/data/zookeeper
dataLogDir=/data/logs/zookeeper
clientPort=2181
maxClientCnxns=0

initLimit=5
syncLimit=2
tickTime=2000

server.1=n12:2888:3888
server.2=n13:2888:3888
server.3=n14:2888:3888
```
### 2.创建目录&id file
```
mkdir -p /data/zookeeper
echo "1" > /data/zookeeper/myid #三个节点唯一不同的就是这个id文件,对应server-id即可
```
### 3.启动zookeeper
```
bin/zkServer.sh start
```
## 三、测试
### 1.连接
```
bin/zkCli.sh -server 127.0.0.1:2181
```
### 2.简单操作
```
[zkshell: 0] help
ZooKeeper host:port cmd args
        get path [watch]
        ls path [watch]
        set path data [version]
        delquota [-n|-b] path
        quit
        printwatches on|off
        createpath data acl
        stat path [watch]
        listquota path
        history
        setAcl path acl
        getAcl path
        sync path
        redo cmdno
        addauth scheme auth
        delete path [version]
        setquota -n|-b val path

[zkshell: 8] ls /
[zookeeper]

[zkshell: 9] create /zk_test my_data
Created /zk_test
      
[zkshell: 11] ls /
[zookeeper, zk_test]

[zkshell: 12] get /zk_test
my_data
cZxid = 5
ctime = Fri Jun 05 13:57:06 PDT 2009
mZxid = 5
mtime = Fri Jun 05 13:57:06 PDT 2009
pZxid = 5
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0
dataLength = 7
numChildren = 0
        
[zkshell: 14] set /zk_test junk
cZxid = 5
ctime = Fri Jun 05 13:57:06 PDT 2009
mZxid = 6
mtime = Fri Jun 05 14:01:52 PDT 2009
pZxid = 5
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0
dataLength = 4
numChildren = 0
[zkshell: 15] get /zk_test
junk
cZxid = 5
ctime = Fri Jun 05 13:57:06 PDT 2009
mZxid = 6
mtime = Fri Jun 05 14:01:52 PDT 2009
pZxid = 5
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0
dataLength = 4
numChildren = 0
      
[zkshell: 16] delete /zk_test
[zkshell: 17] ls /
[zookeeper]
[zkshell: 18]
```


[101]:http://apache.dataguru.cn/zookeeper/zookeeper-3.4.7/zookeeper-3.4.7.tar.gz
[102]:http://www.apache.org/dyn/closer.cgi/zookeeper/
