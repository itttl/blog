<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2016-01-05
title: Redis安装配置 
tags: Redis
category: Redis
status: publist
summary: Redis安装配置
-->

![redis-logo](./img/redis-white.png)

## 下载
```
wget http://download.redis.io/releases/redis-2.8.24.tar.gz 
```
## 安装
```
yum install gcc gcc-c++ make -y
tar zxvf /data/package/redis-2.8.24.tar.gz -C /usr/local/src/
cd /usr/local/src/redis-2.8.24/
make
make install 
```
## 配置
vim /etc/redis.conf
```
daemonize yes
pidfile /var/run/redis01.pid
port 6379
tcp-backlog 511
timeout 0
tcp-keepalive 0
loglevel notice
logfile /data/log/redis/redis01.log
databases 16
save 900 1
save 300 10
save 60 10000
stop-writes-on-bgsave-error yes
rdbcompression yes
rdbchecksum yes
dbfilename dump.rdb
dir /data/redis01
slave-serve-stale-data yes
slave-read-only yes
repl-diskless-sync no
repl-diskless-sync-delay 5
repl-disable-tcp-nodelay no
slave-priority 100
requirepass redis_pw123456
maxclients 250
maxmemory 536870912
appendonly no
appendfilename "appendonly.aof"
appendfsync everysec
no-appendfsync-on-rewrite no
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
aof-load-truncated yes
lua-time-limit 5000
slowlog-log-slower-than 10000
slowlog-max-len 128
latency-monitor-threshold 0
notify-keyspace-events ""
hash-max-ziplist-entries 512
hash-max-ziplist-value 64
list-max-ziplist-entries 512
list-max-ziplist-value 64
set-max-intset-entries 512
zset-max-ziplist-entries 128
zset-max-ziplist-value 64
hll-sparse-max-bytes 3000
activerehashing yes
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit slave 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60
hz 10
aof-rewrite-incremental-fsync yes
```
## 启动
```
redis-server /etc/redis.conf
```
## 测试
### 1.查看监听端口
```
netstat -antulp | grep :6379
```
### 2.连接redis
```
$ redis-cli -h 127.0.0.1 -p 6379

127.0.0.1:6379> auth redis_pw123456
OK
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> set v1 20150105
OK
127.0.0.1:6379> get v1
"20150105"
127.0.0.1:6379>
```

[Redis官网][101]
[101]:http://redis.io/
