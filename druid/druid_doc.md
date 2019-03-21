

# Druid

Druid拥有一个多进程，分布式架构，旨在实现云友好且易于操作。每个Druid流程类型都可以独立配置和扩展，为您的群集提供最大的灵活性。此设计还提供增强的容错能力：一个组件的中断不会立即影响其他组件。

## Historical
Historical是处理存储和查询“历史”数据（包括系统中已经存在足够长时间以提交的任何流数据）的主要工具。历史进程从深层存储中下载段并响应有关这些段的查询。他们不接受写作。

## MiddleManager
MiddleManager进程处理将新数据提取到集群中。他们负责从外部数据源阅读并发布新的Druid片段。

## Broker
Broker进程从外部客户端接收查询并将这些查询转发给Historicals和MiddleManagers。当Brokers从这些子查询中收到结果时，它们会合并这些结果并将它们返回给调用者。最终用户通常会查询Broker，而不是直接查询Historicals或MiddleManagers。

## Coordinator
 Coordinator(协调员)进程监视历史进程。他们负责将段分配给特定服务器，并确保段在历史记录之间保持平衡。
## Overlord
 Overlord进程监视MiddleManager进程，并且是数据摄入Druid的控制器。他们负责将提取任务分配给MiddleManagers并协调段发布。

## Router
 Router进程是可选进程，在Druid Brokers，Overlords和Coordinator之前提供统一的API网关。它们是可选的，因为您也可以直接联系Druid经纪人，宿主和协调员。



Druid进程可以单独部署（每个物理服务器，虚拟服务器或容器一个），也可以在共享服务器上共存。

## 一个常见的托管计划是三类计划：

- “数据”服务器运行历史和中间管理程序。
- “查询”服务器运行Broker和（可选）路由器进程。
- “Master”服务器运行Coordinator和Overlord进程。他们也可以运行ZooKeeper。

除了这些进程类型，Druid还有三个外部依赖。这些旨在能够利用现有的现有基础设施。

- Deep storage(深度存储)，每个Druid服务器都可以访问共享文件存储。这通常是像S3或HDFS这样的分布式对象存储，或者是网络安装的文件系统。Druid使用它来存储已被摄入系统的任何数据。
- Metadata store(元数据存储)，共享元数据存储。这通常是传统的RDBMS，如PostgreSQL或MySQL。
- ZooKeeper用于内部服务发现，协调和领导者选举。
-
这种架构背后的想法是使Druid集群在生产中大规模运作变得简单。
例如，Deep storage和Metadata store与集群其余部分的分离意味着Druid进程具有极大的容错能力：即使每个Druid服务器都出现故障，您仍然可以从存储在Deep storage和Metadata store重新启动集群。

## 查询和数据如何流经此体系结构：
![druid-architecture](./druid-architecture.png)
