<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2015-10-29
title: 【企业级应用】Kafka & zookeeper安装配置
tags: Kafka
category: Kafka
status: publish
summary: Kafka & zookeeper 集群的安装配置，以及简单的测试
-->

### 配置hosts
kafka01/02/03  
```bash
vim /etc/hosts
#-----------------------------
10.0.1.11 kafka01
10.0.1.12 kafka02
10.0.1.13 kafka03
#-----------------------------
```
### 安装kafka
kafka01/02/03  
```bash
tar zxvf /opt/kafka_2.11-0.8.2.0.tgz -C /data/
mv /data/kafka_2.11-0.8.2.0 /data/kafka
```
### 配置zookeeper
kafka01/02/03  
```bash
cd /data/kafka/config
vim zookeeper.properties 
#-----------------------------
dataDir=/data/zookeeper
clientPort=2181
maxClientCnxns=0

initLimit=5
syncLimit=2
server.1=kafka01:2888:3888
server.2=kafka02:2888:3888
server.3=kafka03:2888:3888
#-----------------------------

mkdir /data/zookeeper
echo "1" > /data/zookeeper/myid
```
### 配置kafka
kafka01/02/03  
```bash
vim config/server.properties
#-----------------------------
#port log.dir broker.id
broker.id=1
host.name=kafka01
log.dirs=/data/logs/kafka/kafka-logs-1
port=9092

#Replication configurations
num.replica.fetchers=4
replica.fetch.max.bytes=1048576
replica.fetch.wait.max.ms=500
replica.high.watermark.checkpoint.interval.ms=5000
replica.socket.timeout.ms=30000
replica.socket.receive.buffer.bytes=65536
replica.lag.time.max.ms=10000
replica.lag.max.messages=4000

controller.socket.timeout.ms=30000
controller.message.queue.size=10

#Log configuration
num.partitions=3
message.max.bytes=1000000
auto.create.topics.enable=true
log.index.interval.bytes=4096
log.index.size.max.bytes=10485760
log.retention.hours=168
log.flush.interval.ms=10000
log.flush.interval.messages=20000
log.flush.scheduler.interval.ms=2000
log.roll.hours=168
log.retention.check.interval.ms=300000
log.cleanup.policy=delete
log.segment.bytes=1073741824

#ZK configuration
zookeeper.connect=kafka01:2181,kafka02:2181,kafka03:2181
zookeeper.connection.timeout.ms=6000
zookeeper.sync.time.ms=2000

#Socket server configuration
num.io.threads=8
num.network.threads=8
socket.request.max.bytes=104857600
socket.receive.buffer.bytes=1048576
socket.send.buffer.bytes=1048576
queued.max.requests=16
fetch.purgatory.purge.interval.requests=100
producer.purgatory.purge.interval.requests=100
#-----------------------------
```

### 配置防火墙
kafka01/02/03  
```bash
vim /etc/sysconfig/iptables
#-----------------------------
-A INPUT -p tcp -m tcp -i eth0 --dport 2888:3888 -j ACCEPT
-A INPUT -p tcp -m tcp -i eth0 --dport 2181 -j ACCEPT
-A INPUT -p tcp -m tcp -i eth0 --dport 9092 -j ACCEPT
#-----------------------------
systemctl restart iptables.service
or
service iptables restart
```


### 启动zookeeper/kafka
kafka01/02/03  
```bash
nohup ./bin/zookeeper-server-start.sh ./config/zookeeper.properties &
and
nohup ./bin/kafka-server-start.sh ./config/server.properties &
```

### 创建topic
```bash
bin/kafka-topics.sh --create --zookeeper kafka01:2181,kafka02:2181,kafka03:2181 --replication-factor 2 --partitions 3 --topic niu_text_topic01
```


### 查看已有topic
```bash
bin/kafka-topics.sh --list --zookeeper=kafka01:2181
bin/kafka-topics.sh --list --zookeeper=kafka02:2181
bin/kafka-topics.sh --list --zookeeper=kafka03:2181

bin/kafka-topics.sh --describe --zookeeper=kafka01:2181
bin/kafka-topics.sh --describe --zookeeper=kafka02:2181
bin/kafka-topics.sh --describe --zookeeper=kafka03:2181
```

### Test　Demo

#### 发送消息
```bash
bin/kafka-console-producer.sh --broker-list kafka01:9092,kafka02:9092,kafka03:9092 --topic niu_text_topic01
```
#### 接收消息
```bash
bin/kafka-console-consumer.sh --zookeeper kafka01:2181 --topic niu_text_topic01 --from-beginning
```





