---
title: Mongodb简单上手
date: 2016-10-14
categories: 
- 数据库
- mongoDB
tags: 
- mongoDB
init: 1
---


# MongoDB是一个文档数据库

官方反复强调说：MongoDB是一个文档数据库，这是因为在MongoDB中的**记录**（record）都是一个**文档**(document），它是数据库中的最小数据集合，由字段和值对组成的数据结构。其中字段的值可以包括其他文档，数组和文档数组。

你初看MongoDB的文档的结构可以发现，它就类似JSON对象（实际上它就是以二进制的JSON存储的）

![MongoDB文档。](https://docs.mongodb.com/manual/_images/crud-annotated-document.bakedsvg.svg)

使用文档的好处是：

- 文档（即对象）对应于许多编程语言中的本机数据类型（特别是Javascript）。
- 嵌入式文档和数组减少了对昂贵连接的需求。
- 动态模式支持流畅的多态性。

# MongoDB与SQL的数据结构对比详解

![image-20190323143249276](https://ws2.sinaimg.cn/large/006tKfTcly1g1cpt2erqkj310i0m8gsm.jpg)

| **SQL Terms/Concepts**          | **MongoDB Terms/Concepts**     |
| - |  |
| **database**                    | database                       |
| **table**                       | collection                     |
| **row**                         | document / BSON document       |
| **column**                      | field                          |
| **index**                       | index                          |
| **table joins**                 | embedded documents and linking |
| **primary key**                 | _id                            |
| **aggregation (e.g. group by)** | aggregation pipeline           |

# 特点

- 使用BSON存储数据（二进制的JSON）
- 支持相对丰富的查询操作
- 支持索引
- 副本集
- 分片
- 无模式
- 部署简单方便（默认无身份认证）

# 服务启动

在本地运行mongoDB分两块：

## 服务端进程

服务端进程一般我们需要以守护进程形式启动：

    mongod [opt] --fork --logpath=...

**常用命令参数**：

- fork : 以守护进程起来（必须制定log存储位置 ）
- logpath ： 指定log路径
- syslog ：使用默认系统log（我的在/var/log/mongodb/mongodb.log）

![](http://img.hksite.cn/1483527212989)

更多命令参数：<https://docs.mongodb.com/manual/mongo/>

### 关闭服务

在客户端中关闭：

```shell
> db.shutdownServer()
shutdown command only works with the admin database; try 'use admin'
> use admin
> db.shutdownServer()
server should be down...
```

命令行：

```
$ kill -2 PID
　　原理：-2表示向mongod进程发送SIGINT信号。
或
$ kill -4 PID
　　原理：-4表示向mognod进程发送SIGTERM信号。
```

注：不要使用**kill -9**。数据库直接关闭，数据丢失，数据文件损失，修复数据库（成本高，有风险）

### 监控日志

**注：**如果要持续监视log的命令：用tail的-f参数可以持续监听目录

```
tail -f mongodb.log
```

![](http://img.hksite.cn/1483527357856)

## 客户端使用

    mongo IP:PORT

![](http://img.hksite.cn/1478075611496)

## 基本操作命令

由于是是nosql数据库，所以**不需要特定的命令去创建数据库创建表**，如果要使用一个数据库，但是它不存在的话，可以直接使用的

更加详细的命令请参考[官方手册](https://docs.mongodb.com/manual/crud/)：

## 数据库操作

- **show** dbs ：查看所有数据库
- **show** collections ：查看数据库里的所有的集合
- **use** `db` ：切换到某个数据库（哪怕是创建一个全新的数据库，也可以直接use）

    ![](http://img.hksite.cn/1483527714439)



# CRUD操作

## 创建文档

- db**.`CollectionName`.**insertOne ({"`key`":"`value`"})   插入一条数据 *v3.2中加入*
- db**.`CollectionName`.**insertMany([{"`key`":"`value`"}, {"`key2`":"`value2`"}……])  插入多条数据 *v3.2中加入*

- **db**.`CollectionName`.**insert** (……)  插入一条或多条数据



![](http://img.hksite.cn/1483528125879)

因为mongodb是无模式的，所以它不要求数据库中的数据必须使用相同的格式，也就是说，你可以再插入以下数据：

![](http://img.hksite.cn/1483528527859)

**注意：**这里的`_id`字段是所有的文档都有的字段（是mongoDB自动生成的唯一值），主要是通过它来查询不同的文档的。

## 查询文档

**db**.`CollectionName`.**find** (`条件`) (如果不加参数则为**查询全部**)

```javascript
db.CollectionName.find()   // 查询全部内容
```

### 查询条件

查询条件是mongodb一般来说查询条件有两种形式：

- 完全匹配：`{ <field1> : <value1> , <field2> : <value2>, … }`

```
db.restaurants.find( { "borough": "Manhattan" } )
```

- 条件匹配：`{ <field1>: { <operator1>: <value1> } }`


### 条件匹配

  - **in**：{`$in`: [ ] }

    ```
    db.inventory.find( { status: { $in: [ "A", "D" ] } } )
    ```

    类比sql：

    ```sql
    SELECT * FROM inventory WHERE status in ("A", "D")
    ```

    

  - **大于/小于**：{`$gt`/`$lt` :  }

    ```
    db.restaurants.find( { "grades.score": { $gt: 30 } } )
    ```

  - **或**：{`$or`: `[{ <field1>: <value1>},{<field2>: <value2>}] }`

    ```
    db.inventory.find( { $or: [ { status: "A" }, { qty: { $lt: 30 } } ] } )
    ```

    类比sql：

    ```sql
    SELECT * FROM inventory WHERE status = "A" OR qty < 30
    ```

    

  - **与：**直接用逗号连接

    ```
    db.inventory.find( { status: "A", qty: { $lt: 30 } } )
    ```

    类比sql：

    ```sql
    SELECT * FROM inventory WHERE status = "A" AND qty < 30
    ```

    

- 符合操作

  ```
  db.inventory.find( {
       status: "A",
       $or: [ { qty: { $lt: 30 } }, { item: /^p/ } ]
  } )
  ```

  类比sql: 

  ```Sql
  SELECT * FROM inventory WHERE status = "A" AND ( qty < 30 OR item LIKE "p%")
  ```


### 其他功能 

**db**.`CollectionName`.**find**().**count**() 可以统计查询出的数目

## 删除文档

- **db**.`CollectionName`.**deleteOne**(`删除条件` , `参数`)    ： 删除一条或多条数据  *v3.2中加入*

- **db**.`CollectionName`.**deleteMany**(`删除条件` , `参数`)    ： 删除一条或多条数据  *v3.2中加入*

- **db**.`CollectionName`.**remove**(`删除条件` , `参数`)    ： 删除一条或多条数据

```
db.restaurants.remove( { "borough": "Manhattan" } )
```

### 删除一条

如果不加参数，默认删除的是所有匹配的文档，如果你只想删除一个文档，加入参数：`{ justOne: true }`

```
db.restaurants.remove( { "borough": "Queens" }, { justOne: true } )
```

或者使用新api：deleteOne

```
db.restaurants.deleteOne( { "borough": "Queens" })
```

### 删除全部文档

```
db.restaurants.remove( { } )
```

或者使用新api：deleteMany

```
db.restaurants.deleteMany( { } )
```

但是，其实还有一个更高效全面的删除全部文档的方法：**把集合删除**

- **db**.`CollectionName`.**drop**()

，这时候要用`drop`命令，这时候，**它会把包括索引在内的所有集合内容删除**。

## 修改文档

- **db**.`CollectionName`.**updateOne**(`条件`，`修改内容` ，`参数`) ：  更新一条数据  *v3.2中加入*

- **db**.`CollectionName`.**updateMany**(`条件`，`修改内容` ，`参数`) :   更新多条数据  *v3.2中加入*

- **db**.`CollectionName`.**update**(`条件`，`修改内容` ，`参数`) ：更新一条数据或多条数据

> 查询条件参考之前的**“查询文档”**

update语句是更新，所以它是会新增或修改原有的值。修改内容表达式的书写方式是：`$set` 

```
db.class.update( {"name":"ZhangQiang"}, { $set:{"No": "123"} } )
```

![](http://img.hksite.cn/1484209181920)

### 修改多条数据

update**默认修改只修改单条数据**，如果要修改多条数据，可以设置参数`{multi:true}`

```
db.class.update( {"name":"ZhangQiang"}, { $set:{"No": "123"} } , {multi: true})
```

或者使用新API：updateMany

```
db.class.updateMany( {"name":"ZhangQiang"}, { $set:{"No": "123"} } , {multi: true})
```

### 替换一条数据

修改和替换是完全不同的概念，修改是在原来文档中进行修改，但是替换是用新的文档替换旧文档。

在*v3.2*之前，我们只有`save`方法进行文档替换，`save`方法在不提供`_id`字段的时候，会添加一个没有`_id`字段的文档，如果添加了`_id`字段，则会替换已存在的文档

- **db**.`CollectionName`.**save**()  

```
db.class.save( {"_id": ObjectId("586……"), "name":"ZhangQiang", "Age": "13"})
```

在*v3.2*之后，有了replaceOne

```
db.class.replaceOne( {"name":"ZhangQiang"},{"name":"ZhangQiang", "Age": "13"})
```






​    