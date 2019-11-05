<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2019-03-16
title: Druid快速入门
tags: Druid
category: Druid
status: publist
summary: Druid快速入门
-->


# Druid快速入门
---
在本快速入门中，我们将下载Druid并在一台机器上进行设置。完成此初始设置后，群集将准备好加载数据。

在开始快速入门之前，阅读一般的Druid概述和 摄取概述是有帮助的，因为教程将参考这些页面上讨论的概念。

### 先决条件你会需要：
```
Java 8
Linux，Mac OS X或其他类Unix操作系统（不支持Windows）
8G的RAM
2个vCPU
在Mac OS X上，您可以使用Oracle的JDK 8来安装Java。
```

在Linux上，您的OS包管理器应该能够帮助Java。如果基于Ubuntu的操作系统没有最新版本的Java，则WebUpd8会为这些操作系统提供软件包。

## 入门
### 下载 0.13.0孵化版本。
通过在终端中运行以下命令来解压Druid：
```
tar -xzf apache-druid-0.13.0-incubating-bin.tar.gz
cd apache-druid-0.13.0-incubating
```
在包中，您应该找到：
```
DISCLAIMER，LICENSE和NOTICE文件
bin/* - 对此快速入门有用的脚本
conf/* - 群集设置的模板配置
extensions/* - 核心Druid扩展
hadoop-dependencies/* - DruidHadoop依赖
lib/* - 核心Druid的图书馆和依赖项
quickstart/* - 快速入门教程的配置文件，示例数据和其他文件
```
### 下载Zookeeper

Druid依赖Apache ZooKeeper进行分布式协调。您需要下载并运行Zookeeper。

在程序包根目录中，运行以下命令：
```
curl https://archive.apache.org/dist/zookeeper/zookeeper-3.4.11/zookeeper-3.4.11.tar.gz -o zookeeper-3.4.11.tar.gz
tar -xzf zookeeper-3.4.11.tar.gz
mv zookeeper-3.4.11 zk
```
本教程的启动脚本将期望Zookeeper tar ball的内容位于zkapache-druid-0.13.0-incubating包根目录下。

### 启动Druid服务
从apache-druid-0.13.0-incubating package root，运行以下命令：
```
bin/supervise -c quickstart/tutorial/conf/tutorial-cluster.conf
```
这将启动Zookeeper和Druid服务的实例，所有这些都在本地机器上运行，例如：
```
bin/supervise -c quickstart/tutorial/conf/tutorial-cluster.conf
```
输出内容
```
[Thu Jul 26 12:16:23 2018] Running command[zk], logging to[/stage/apache-druid-0.13.0-incubating/var/sv/zk.log]: bin/run-zk quickstart/tutorial/conf
[Thu Jul 26 12:16:23 2018] Running command[coordinator], logging to[/stage/apache-druid-0.13.0-incubating/var/sv/coordinator.log]: bin/run-druid coordinator quickstart/tutorial/conf
[Thu Jul 26 12:16:23 2018] Running command[broker], logging to[//stage/apache-druid-0.13.0-incubating/var/sv/broker.log]: bin/run-druid broker quickstart/tutorial/conf
[Thu Jul 26 12:16:23 2018] Running command[historical], logging to[/stage/apache-druid-0.13.0-incubating/var/sv/historical.log]: bin/run-druid historical quickstart/tutorial/conf
[Thu Jul 26 12:16:23 2018] Running command[overlord], logging to[/stage/apache-druid-0.13.0-incubating/var/sv/overlord.log]: bin/run-druid overlord quickstart/tutorial/conf
[Thu Jul 26 12:16:23 2018] Running command[middleManager], logging to[/stage/apache-druid-0.13.0-incubating/var/sv/middleManager.log]: bin/run-druid middleManager quickstart/tutorial/conf
```
所有持久状态（如集群元数据存储和服务段）都将保存在varapache-druid-0.13.0-incubating package root下的目录中。服务日志位于var/sv。

### 停止服务
如果你想停止服务，CTRL-C退出bin/supervise脚本，这将终止Druid进程。

### 重置群集状态
如果要在停止服务后进行干净启动，请删除该var目录并bin/supervise再次运行该脚本。

每个服务启动后，您就可以加载数据了。

### 重置卡夫卡
如果您完成了教程：从Kafka加载流数据并希望重置群集状态，则还应清除任何Kafka状态。

在停止Zookeeper和Druid服务之前，使用CTRL-C关闭Kafka代理，然后删除Kafka日志目录/tmp/kafka-logs：

rm -rf /tmp/kafka-logs

## 集群安装配置

https://blog.csdn.net/wangshuminjava/article/details/80870157
https://blog.csdn.net/a794922102/article/details/88237610
