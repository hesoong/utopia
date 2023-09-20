---
title: 高可用性的mongo集群搭建
date: 2019-06-25
updated: 2019-06-25
toc: true
tags:
- mongo集群
- 部署
---

## mongoDB安装

参照：<https://docs.mongodb.com/manual/tutorial/install-mongodb-on-red-hat/>

### 配置yum管理包

在路径/etc/yum.repos.d/下创建文件mongodb-org-3.4.repo
  
```repo
[mongodb-org-3.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.4.asc
```

* 安装mongo
  
```bash
yum install -y mongodb-org
```

* mongo相关操作

* 创建用户
  
```shell
mongo       //启动Mongo shell命令
use admin   //切换到admin数据库
db.createUser({user:"root",pwd:"123456",roles:["root"]})    //创建用户
db.auth("root","123456")    //验证用户创建是否成功
```

## 高可用性的mongo集群

参照：<http://www.lanceyan.com/tech/arch/mongodb_shard1.html>

### 三台服务器分配

按表格分别在各服务器进行目录创建：

* 192.168.1.91

| 服务               | 端口    | 日志路径                                  | 数据路径                                   |
| ---------------- | ----- | ------------------------------------- | -------------------------------------- |
| mongos           | 20001 | mkdir -p /data/mongodbtest/mongos/log | --                                     |
| Config server    | 21001 | mkdir -p /data/mongodbtest/config/log | mkdir -p /data/mongodbtest/config/data |
| Shard server1主节点 | 22001 | mkdir -p /data/mongodbtest/shard1/log | mkdir -p /data/mongodbtest/shard1/data |
| Shard server2副本  | 22002 | mkdir -p /data/mongodbtest/shard2/log | mkdir -p /data/mongodbtest/shard2/data |
| Shard server3仲裁  | 22003 | mkdir -p /data/mongodbtest/shard3/log | mkdir -p /data/mongodbtest/shard3/data |

* 192.168.1.92

| 服务               | 端口    | 日志路径                                  | 数据路径                                   |
| ---------------- | ----- | ------------------------------------- | -------------------------------------- |
| mongos           | 20001 | mkdir -p /data/mongodbtest/mongos/log | --                                     |
| Config server    | 21001 | mkdir -p /data/mongodbtest/config/log | mkdir -p /data/mongodbtest/config/data |
| Shard server1仲裁  | 22001 | mkdir -p /data/mongodbtest/shard1/log | mkdir -p /data/mongodbtest/shard1/data |
| Shard server2主节点 | 22002 | mkdir -p /data/mongodbtest/shard2/log | mkdir -p /data/mongodbtest/shard2/data |
| Shard server3副本  | 22003 | mkdir -p /data/mongodbtest/shard3/log | mkdir -p /data/mongodbtest/shard3/data |

* 192.168.1.93

| 服务               | 端口    | 日志路径                                  | 数据路径                                   |
| ---------------- | ----- | ------------------------------------- | -------------------------------------- |
| mongos           | 20001 | mkdir -p /data/mongodbtest/mongos/log | --                                     |
| Config server    | 21001 | mkdir -p /data/mongodbtest/config/log | mkdir -p /data/mongodbtest/config/data |
| Shard server1副本  | 22001 | mkdir -p /data/mongodbtest/shard1/log | mkdir -p /data/mongodbtest/shard1/data |
| Shard server2仲裁  | 22002 | mkdir -p /data/mongodbtest/shard2/log | mkdir -p /data/mongodbtest/shard2/data |
| Shard server3主节点 | 22003 | mkdir -p /data/mongodbtest/shard3/log | mkdir -p /data/mongodbtest/shard3/data |

### 启动Config server

1. 启动Config server

   * 分别配置三台config服务的配置文件，分别在对应config server的conf目录下(/data/mongodbtest/config/conf/)创建mongo_config.conf文件

	 ```yml
	 storage:
	 dbPath: /data/mongodbtest/config/data
	 indexBuildRetry: true
	 systemLog:
	 destination: file
	 path: /data/mongodbtest/config/log/config.log
	 net:
	 port: 21001
	 sharding:
	 clusterRole: configsvr
	 replication:
	 replSetName: docdetection
	 processManagement:
	 fork: true
	 ```

   * 通过配置文件分别启动config配置服务

     ```shell
     mongod --config /data/mongodbtest/config/conf/mongo_config.conf
     ```

2. 初始化config复本集

   ```shell
    #设置config副本集，登录其中一个config服务器
    /usr/bin/mongo  127.0.0.1:21001
   
    #使用admin数据库
    use admin
   
    #定义副本集配置
    config ={_id:'docdetection',members:[
                {_id:0,host:'192.168.1.91:21001',priority:3},
                {_id:1,host:'192.168.1.92:21001',priority:2},
                {_id:2,host:'192.168.1.93:21001',priority:1}
            ]
        } 
   
    #初始化副本集配置
    rs.initiate(config);
   
    #初始化结束（注意关闭各服务器防火墙）
    { "ok" : 1 }
   
    #查看状态
     rs.status()
   ```

### 启动Shard server（分片服务）

3. 启动各分片服务

   * 分别到各服务器上各shard的conf目录下建文件mongo_config.conf。eg:192.168.1.91

     ```yml
     # shard1
     storage:
     dbPath: /data/mongodbtest/shard1/data
     indexBuildRetry: true
     systemLog:
     destination: file
     path: /data/mongodbtest/shard1/log/shard1.log
     net:
     port: 22001
     sharding:
     clusterRole: shardsvr
     replication:
     replSetName: shard1
     processManagement:
     fork: true
     
     # shard2
     storage:
     dbPath: /data/mongodbtest/shard2/data
     indexBuildRetry: true
     systemLog:
     destination: file
     path: /data/mongodbtest/shard2/log/shard2.log
     net:
     port: 22002
     sharding:
     clusterRole: shardsvr
     replication:
     replSetName: shard2
     processManagement:
     fork: true
     
     # shard3
     storage:
     dbPath: /data/mongodbtest/shard3/data
     indexBuildRetry: true
     systemLog:
     destination: file
     path: /data/mongodbtest/shard3/log/shard3.log
     net:
     port: 22003
     sharding:
     clusterRole: shardsvr
     replication:
     replSetName: shard3
     processManagement:
     fork: true
     ```

   * 通过配置文件分别启动shard分片服务(3*3=9个)

     ```shell
     mongod --config /data/mongodbtest/shard1/conf/mongo_config.conf
     mongod --config /data/mongodbtest/shard2/conf/mongo_config.conf
     mongod --config /data/mongodbtest/shard3/conf/mongo_config.conf
     ```

4. 初始化各分片服务器副本集

设置第一个分片副本集:shard1（保证各副本已启动）**注：priority 的值决定主（值大）从关系；arbiterOnly 指定仲裁节点**

```shell
#设置第一个分片副本集，登录其中一个分片服务器
/usr/bin/mongo  127.0.0.1:22001
#使用admin数据库
use admin
#定义副本集配置
config = { _id:"shard1", members:[
                  {_id:0,host:"192.168.1.91:22001",priority:2},
                  {_id:1,host:"192.168.1.92:22001",arbiterOnly:true},
                  {_id:2,host:"192.168.1.93:22001",priority:1}
              ]
      }
#初始化副本集配置
rs.initiate(config);
```

设置第二个分片副本集：shard2

```shell
#设置第二个分片副本集，登录其中一个分片服务器
/usr/bin/mongo  127.0.0.1:22002
#使用admin数据库
use admin
#定义副本集配置
config = { _id:"shard2", members:[
                  {_id:0,host:"192.168.1.91:22002",priority:1},
                  {_id:1,host:"192.168.1.92:22002",priority:2},
                  {_id:2,host:"192.168.1.93:22002",arbiterOnly:true}
              ]
      }
#初始化副本集配置
rs.initiate(config);
```

设置第三个分片副本集：shard3

```shell
#设置第三个分片副本集，登录其中一个分片服务器
/usr/bin/mongo  127.0.0.1:22003
#使用admin数据库
use admin
#定义副本集配置
config = { _id:"shard3", members:[
                  {_id:0,host:"192.168.1.91:22003",arbiterOnly:true},
                  {_id:1,host:"192.168.1.92:22003",priority:1},
                  {_id:2,host:"192.168.1.93:22003",priority:2}
              ]
      }
#初始化副本集配置
rs.initiate(config);
```

### 启动mongos路由服务

* 配置mongos服务的配置文件，对应的conf目录下创建mongo_config.conf文件
  
```yml
systemLog:
destination: file
path: /data/mongodbtest/mongos/log/mongos.log
net:
port: 20001
sharding:
configDB: docdetection/192.168.1.91:21001,192.168.1.92:21001,192.168.1.93:21001
processManagement:
fork: true
```

* 通过配置文件分别启动config配置服务
```shell
mongos --config /data/mongodbtest/mongos/conf/mongo_config.conf
```
  
### 整合配置、路由、分片服务器
  
  整合配置服务器、路由服务器，各个分片服务器

1. 整合配置、路由、分片服务器

   * 目前搭建了mongodb配置服务器、路由服务器，各个分片服务器，不过应用程序连接到 mongos 路由服务器并不能使用分片机制，还需要在程序里设置分片配置，让分片生效。**分别连接三个mongos，添加分片副本集。**

     ```shell
     #设置分片配置，登录mongos路由服务器
     /usr/bin/mongo 127.0.0.1:20001
     
     #使用admin数据库
     use admin
     
     #串联路由服务器与分片副本集1
     sh.addShard("shard1/192.168.1.91:22001,192.168.1.92:22001,192.168.1.93:22001")
     
     #串联路由服务器与分片副本集2
     sh.addShard("shard2/192.168.1.91:22002,192.168.1.92:22002,192.168.1.93:22002")
     
     #串联路由服务器与分片副本集3
     sh.addShard("shard3/192.168.1.91:22003,192.168.1.92:22003,192.168.1.93:22003")
     ```

2. 添加创建用户

   * 集群管理员账号（mongos和配置服务器的集群管理员用户是可以通用的）

     ```shell
     #以下是创建集群管理员用户步骤（连接其中一台Mongos服务上执行即可）
     #登录mongos路由服务器
     /usr/bin/mongo 127.0.0.1:20001
     #使用admin数据库
     use admin
     #创建账号及指定权限
     db.createUser({
       user: "adminRoot",
       pwd: "adminbear",
          roles: [
              { role: "root", db: "admin" },
              { role: "clusterAdmin", db: "admin" },
              { role: "userAdmin", db: "admin" }
       ]
     });
     ```

   * 数据库用户

     ```shell
     #登录mongos路由服务器
     /usr/bin/mongo 127.0.0.1:20001
     #以下是为数据库名mongoTest，创建数据库用户
     use mongoTest;
     db.createUser({
       user: "mongoTestUser",
       pwd: "bear",
       roles: [
           { role: "readWrite", db: " mongoTest" }
       ]
     }
     );
     ```

     目前配置服务、路由服务、分片服务、副本集服务都已经串联起来了，但我们的目的是希望插入数据，数据能够自动分片。连接在mongos上，准备让指定的数据库、指定的集合分片生效。

3. 创建数据库，让指定数据库分片生效

   1. 建库、表、指定应用分片的库

      ```shell
      #登录mongos路由服务器
      /usr/bin/mongo 127.0.0.1:20001
      #创建或者使用mongoTest数据库
      use mongoTest;
      #创建mongoTest数据库中的集合
      db.createCollection("table1")  #新建集合：table1
       ·
       ·
       ·
      #切换到admin数据库
      use admin
      #指定testdb分片生效，mongoTest为数据库名称
      db.runCommand( { enablesharding :"mongoTest"});
      ```

   2. 指定分片的表。两种分片方式： Hashed Shard Key与Ranged Shard Key

      * Hashed Shard Key：它采用字段的索引哈希值作为 shard key 的取值，这样做可以保证数据的均匀分布。在 mongos 和各个 shard 集群之间存在一个哈希值计算方法，所有的数据在迁移时都是根据这个方法来计算数据应当被迁移到什么地方。当 mongos 接收到一条语句时，通常他会把这条语句广播到所有的 shard 上去执行。

      * Ranged Shard Key: 根据 shard key 的取值，它把数据切分成连续的几个区间。取值相近的纪录会放进同一个 shard 服务器。好处是查询连续取值纪录时，查询效率可以得到保证。当数据库查询语句发送到 mongos 中时，mongos 会很快的找到目标 shard，而且不需要将语句发送到所有的 shard 上，一般只需要少量的 shard 就可以完成查询操作。缺点是不能保证数据的平均分配，在数据插入和修改时会产生比较严重的性能瓶颈

        ```shell
        #指定数据库里需要分片的集合和片键，table1为mongoTest数据库中集合名称
        db.runCommand({shardcollection:"mongoTest.table1",key:{"ZZJGDM": "hashed"}}) //Hashed Shard Key
        db.runCommand({shardcollection:"mongoTest.table1",key:{_id:1}})//    Ranged Shard Key
        ```
