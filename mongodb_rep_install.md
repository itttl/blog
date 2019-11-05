<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2015-11-26
title: MongoDB副本集群安装配置 
tags: MongoDB,副本集
category: MongoDB
status: publist
summary: MongoDB单点&MongoDB副本集安装配置.
-->

![mongodb-logo](./img/mongodb-logo.png)
MonogDB
## MongoDB副本集安装配置

### MongoDB下载
```
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-2.6.2.tgz
or
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-${VERSION}.tgz
例如：
wget https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-2.6.11.tgz
```

### 配置hosts
```
10.0.0.1 mongodb1
10.0.0.2 mongodb2
10.0.0.3 mongodb3
```

### MongoDB安装
#### mongodb1/mongodb2/mongodb3
```bash
tar zxvf /opt/mongodb-linux-x86_64-2.6.2.tgz -C /usr/local/
mv /usr/local/mongodb-linux-x86_64-2.6.2 /usr/local/mongodb
echo 'export MONGODB_HOME=/usr/local/mongodb' >> /etc/bashrc 
echo 'export PATH=$MONGODB_HOME/bin:$PATH' >> /etc/bashrc

mkdir -p /data/mongodb/journal
mkdir -p /data/mongodb/log
touch /data/mongodb/log/mongodb.log
useradd mongodb -M -s /sbin/nologin
chown -R mongodb:mongodb /data/mongodb

#一个节点生成keyfile拷贝到其他节点即可
openssl rand -base64 753 > /usr/local/mongodb/keyfile512

```
### MongoDB副本集形式启动
#### mongodb1
```
/usr/local/mongodb/bin/mongod  --bind_ip 10.0.0.1,127.0.0.1 --oplogSize=4096 --dbpath=/data/mongodb/data -logpath=/data/mongodb/log/mongodb.log --port 27017 --logappend --fork --replSet xiaoniu -keyFile=/usr/local/mongodb/keyfile512
```

#### mongodb2
```
/usr/local/mongodb/bin/mongod  --bind_ip 10.0.0.2,127.0.0.1 --oplogSize=4096 --dbpath=/data/mongodb/data -logpath=/data/mongodb/log/mongodb.log -port=27017 --logappend --fork --replSet xiaoniu -keyFile=/usr/local/mongodb/keyfile512
```

#### mongodb3
```
#从节点　　
/usr/local/mongodb/bin/mongod  --bind_ip 10.0.0.3,127.0.0.1 --oplogSize=4096 --dbpath=/data/mongodb/data -logpath=/data/mongodb/log/mongodb.log -port=27017 --logappend --fork --replSet xiaoniu -keyFile=/usr/local/mongodb/keyfile512

#仲裁节点
/usr/local/mongodb/bin/mongod --bind_ip 10.0.0.3,127.0.0.1 --dbpath=/data/mongodb/ez -logpath=/data/mongodb/log/mongodbez.log -port=27018 --logappend --fork --replSet xiaoniu -keyFile=/usr/local/mongodb/keyfile512
```

## MongoDB副本集初始化
###
```
#接入任意Mongo 节点
rs.initiate({ 
    _id : "xiaoniu", 
    members : [ 
    {_id : 1, host : "mongodb1:27017"}, 
    {_id : 2, host : "mongodb2:27017"}, 
    {_id : 3, host : "mongodb3:27017"}, 
    {_id : 4, host : "mongodb3:27018", "arbiterOnly": true}, 
    ] 
    }); 
```

### 查看集群信息；
```
#连入连入任意数据节点
xiaoniu:PRIMARY> rs.status()
```

