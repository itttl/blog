<!--
author: yanliang.zhao
head: http://blog.itttl.com/logo_miao.png
date: 2016-01-04
title: MongoDB 用户的创建、删除、密码修改，角色分配 
tags: MongoDB role,MongoDB创建用户
category: MongoDB
status: publist
summary: MongoDB 用户的创建、删除、密码修改，角色分配
-->

![mongodb-logo](./img/mongodb-logo.png)

## 一、创建
```
db.createUser({user:'admin',pwd:'pw123456',roles:[{role:'root',db:'admin'}]})  
db.createUser({user:'user1',pwd:'pw123456',roles:[{role:'dbOwner',db:'db1'}]})
db.createUser({user:'user2',pwd:'pw123456',roles:[{role:'read',db:'db1'}]})
```
### 1.定义：
创建一个数据库新用户用db.createUser()方法，如果用户存在则返回一个用户重复错误。

### 2.语法：
```
db.createUser(user, writeConcern)
    user这个文档创建关于用户的身份认证和访问信息；
    writeConcern这个文档描述保证MongoDB提供写操作的成功报告。
```
### 3.user文档，定义了用户的以下形式：
```
{ user: "<name>",
  pwd: "<cleartext password>",
  customData: { <any information> },
  roles: [
    { role: "<role>", db: "<database>" } | "<role>",
    ...
  ]
}
```
### 4.user文档字段介绍：
    - user字段，为新用户的名字；
    - pwd字段，用户的密码；
    - cusomData字段，为任意内容，例如可以为用户全名介绍；
    - roles字段，指定用户的角色，可以用一个空数组给新用户设定空角色；
    - 在roles字段,可以指定内置角色和用户定义的角色。

### 5.Built-In Roles（内置角色）：
   - 1.数据库用户角色：read、readWrite;
   - 2.数据库管理角色：dbAdmin、dbOwner、userAdmin；
   - 3.集群管理角色：clusterAdmin、clusterManager、clusterMonitor、hostManager；
   - 4.备份恢复角色：backup、restore；
   - 5.所有数据库角色：readAnyDatabase、readWriteAnyDatabase、userAdminAnyDatabase、dbAdminAnyDatabase
   - 6.超级用户角色：root  
　 // 这里还有几个角色间接或直接提供了系统超级用户的访问（dbOwner 、userAdmin、userAdminAnyDatabase）
   - 7.内部角色：__system
   PS：关于每个角色所拥有的操作权限可以点击上面的内置角色链接查看详情。

### 6.writeConcern文档（官方说明）
   - w选项：允许的值分别是 1、0、大于1的值、"majority"、<tag set>；
   - j选项：确保mongod实例写数据到磁盘上的journal（日志），这可以确保mongd以外关闭不会丢失数据。设置true启用。
   - wtimeout：指定一个时间限制,以毫秒为单位。wtimeout只适用于w值大于1。

#### 例如：在products数据库创建用户accountAdmin01，并给该用户admin数据库上clusterAdmin和readAnyDatabase的角色，products数据库上readWrite角色。
```
use products
db.createUser( { "user" : "accountAdmin01",
                 "pwd": "cleartext password",
                 "customData" : { employeeId: 12345 },
                 "roles" : [ { role: "clusterAdmin", db: "admin" },
                             { role: "readAnyDatabase", db: "admin" },
                             "readWrite"
                             ] },
               { w: "majority" , wtimeout: 5000 } )
```
## 二、删除
```
>db.dropUser("user1")
```
## 三、修改密码
```
>db.changeUserPassword('admin','tUDfqjDWHR4hSIXs')
```

[MongoDB Create User 官方文档][101]  
[MongoDB built-in-roles官方文档][102]  
[101]:https://docs.mongodb.org/manual/reference/method/db.createUser/
[102]:https://docs.mongodb.org/manual/reference/built-in-roles/#built-in-roles
