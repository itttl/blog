<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2016-01-12
title: Elasticsearch-logstash-Kibana安装配置
tags: log采集，elk
category: log
status: publist
summary: Elasticsearch-logstash-Kibana安装配置
-->


## 一、Elasticsearch
![elasticsearch-logo](./img/elasticsearch.png)
#### 安装包下载地址
https://download.elasticsearch.org/elasticsearch/release/org/elasticsearch/distribution/rpm/elasticsearch/2.1.1/elasticsearch-2.1.1.rpm
#### How To Install and Configure Elasticsearch on CentOS 7
https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-elasticsearch-on-centos-7
### 1.安装
```
rpm -ivh /opt/elasticsearch-2.1.1.rpm
sudo systemctl daemon-reload
sudo systemctl enable elasticsearch.service
```
### 2.配置
```
vim /usr/share/elasticsearch/bin/elasticsearch
---
export JAVA_HOME=/data/jdk1.8.0_51
export PATH=$JAVA_HOME/bin:$PATH
---
#防火墙配置
sudo iptables -A INPUT -p tcp -m state --state NEW -m tcp -m multiport --dports 9200:9400 -j ACCEPT
#config_file
vim /etc/elasticsearch/elasticsearch.yml
---
cluster.name: niu_online
node.name: n12
path.data: /data/elasticsearch/data
path.work: /data/elasticsearch/work
path.logs: /data/elasticsearch/logs
node.data: true
network.host: 172.16.1.12
http.port: 9500

---
mkdir -p /data/elasticsearch/{data,logs,work}
chown -R elasticsearch:elasticsearch /data/elasticsearch/
```
### 3.启停
```
sudo systemctl start elasticsearch.service
sudo systemctl stop elasticsearch.service

```
---
## 二、Kibana
![kibana_00-logo](./img/kibana_00.jpg)
#### 安装包下载地址
https://download.elastic.co/kibana/kibana/kibana-4.3.1-linux-x64.tar.gz
### 1.安装
```
tar zxvf kibana-4.3.1-linux-x64.tar.gz
mv kibana-4.3.1-linux-x64/ /usr/share/kibana-4.3.1
#防火墙配置
sudo iptables -A INPUT -p tcp -m state --state NEW -m tcp -m multiport --dports 5601 -j ACCEPT  vim /usr/share/kibana-4.3.1/config/kibana.yml
```
### 2.配置
```
cd /usr/share/kibana-4.3.1
vim ./config/kibana.yml
---
server.host: "127.0.0.1"
elasticsearch.url: "http://127.0.0.1:9200"
---
```
### 3.启停
```
nohup ./bin/kibana &
```
### 4.配置Nginx代理
```
upstream lb_kibana {
   # ip_hash;
    server 127.0.0.1:5601 weight=10;
}

server {
    listen  80;
    server_name _;

    location = /404.html {
        root   html;
    }
    location = /50x.html {
        root   html;
    }

    location / {
        proxy_pass http://lb_kibana;

        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
    }
        access_log  /data/logs/nginx/access/kibana.log main;
}
```

---
## 三、Logstash
![logstash-logo](./img/logstash.jpg)
#### 安装包下载地址
https://download.elastic.co/logstash/logstash/logstash-all-plugins-2.1.0.tar.gz
### 1.安装
```
tar zxvf /opt/logstash-all-plugins-2.1.0.tar.gz -C /data/
```
### 2.配置
```
vim /data/logstash-2.1.0/config/logstash-all.conf
```
#### logstash-all.conf
```
input {
    file {
        path => ["/var/log/messages","/var/log/secure","/data/logs/*/*.log","/data/logs/*/*/*.log"]
    exclude => ["*.gz","*.tmp","*.zip","*.1","*.2"]
        start_position => beginning

    }
}
filter {
#    grok {
#        match => { "message" => "%{COMBINEDAPACHELOG}"}
#    }
#    geoip {
#        source => "clientip"
#    }
}
output {
    elasticsearch {
        hosts => "171.16.1.99:9500"
    }
    stdout {}
}
```
### 3.启停
```
cd /data/logstash-2.1.0/
#测试配置文件
./bin/logstash -f ./config/logstash-all.conf --configtest
#启动
./bin/logstash -f ./config/logstash-all.conf >> /dev/null &
```
---

