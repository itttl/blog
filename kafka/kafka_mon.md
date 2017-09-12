<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2017-02-2
title: 【企业级应用】Kafka 监控脚本(配合Zabbix使用)
tags: Kafka，监控
category: Kafka
status: publist
summary: 
-->


## 直接上脚本
```
#!/bin/bash


# Group           Topic                          Pid Offset          logSize         Lag             Owner
#motor-receiver-3 Motor-Receiver-GpsQueue        0   10554889114     10554889218     104             motor-receiver-3_motor02centos7p82-1487224754215-9b5a391f-2
#motor-receiver-3 Motor-Receiver-GpsQueue        1   10554849454     10554849580     126             motor-receiver-3_motor02centos7p82-1487224754215-9b5a391f-1
#motor-receiver-3 Motor-Receiver-GpsQueue        2   98394           98394           0               motor-receiver-3_motor02centos7p82-1487224754215-9b5a391f-0

#cat /tmp/kafka-tp.info | grep -v Offset | awk '{print $4}'

kafka_ip="kafka01.itttl.com"
kafka_port=2181
topic_name=${1:-Motor-Receiver-GpsQueue}
group_id=${2:-motor-receiver-3}
pn=${3:-Offset}
/usr/local/kafka/bin/kafka-run-class.sh kafka.tools.ConsumerOffsetChecker --topic=$topic_name --group=$group_id --zookeeper=$kafka_ip:$kafka_port | grep -v Offset > /tmp/kafka-tp-${topic_name}-${group_id}.info

Offset=0
logSize=0
Lag=0
while read line
do
    Offset=$((${Offset}+`echo $line |awk '{print $4}'`))
    logSize=$((${logSize}+`echo $line |awk '{print $5}'`))
    Lag=$(($Lag+`echo $line |awk '{print $6}'`))
done < /tmp/kafka-tp-${topic_name}-${group_id}.info

#echo Offset :$Offset
#echo logSize :$logSize
#echo Lag : $Lag
case $pn in
    offset|Offset)
    echo $Offset
    ;;
    logsize|logSize)
    echo $logSize
    ;;
    lag|Lag)
    echo $Lag
    ;;
    *)
    echo Error
    ;;
esac
```
## 使用说明
```
./kafka_mon.sh $1 $2 $3

$1 : topic name
$2 : group id
$3 : 需要的数据 (Offset、logSize、Lag)

配合Zabbix可以提取数据并自动绘图
```

## Zabbix Agent配置
```
UserParameter=kafka_mon[*],sh /usr/local/zabbix/bin/kafka_mon.sh $1 $2 $3
```
