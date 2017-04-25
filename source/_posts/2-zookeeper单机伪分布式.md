---
title: zookeeper单机伪分布式
description: 记录一次部署zookeeper单机伪分布式的过程
tags: Zookeeper
categories: tech 
abbrlink: 54324
date: 2017-03-15 22:39:33
---

本文介绍zookeeper单机伪分布式的环境搭建，本次采用3.4.6版本。
[zookeeper下载地址](http://www.apache.org/dist/zookeeper/)

### 下载

```
wget http://www.apache.org/dist/zookeeper/zookeeper-3.4.6/zookeeper-3.4.6.tar.gz
tar -zxvf zookeeper-3.4.6.tar.gz
mv zookeeper-3.4.6 zookeeper
cd zookeeper
cp conf/zoo_sample.cfg conf/zoo.cfg
```

### 配置

```
vi conf/zoo.cfg
```

如果不需要集群，配置如下

```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/root/dev/zookeeper-3.4.6/data
clientPort=2181
```

集群配置
```
mkdir server001
cd server001
mkdir data
mkdir logs
echo "1" >> ./data/myid
```

zoo.cfg
```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/root/dev/server001/data
dataLogDir=/root/dev/server001/logs
clientPort=2181
server.1=127.0.0.1:2555:3555
server.2=127.0.0.1:2556:3556
server.3=127.0.0.1:2557:3557
```

* tickTime：这个时间是作为 Zookeeper 服务器之间或客户端与服务器之间维持心跳的时间间隔，也就是每个 tickTime 时间就会发送一个心跳。
* dataDir：顾名思义就是 Zookeeper 保存数据的目录，默认情况下，Zookeeper 将写数据的日志文件也保存在这个目录里。
* clientPort：这个端口就是客户端连接 Zookeeper 服务器的端口，Zookeeper 会监听这个端口，接受客户端的访问请求。
* initLimit：这个配置项是用来配置 Zookeeper 接受客户端（这里所说的客户端不是用户连接 Zookeeper 服务器的客户端，而是 Zookeeper 服务器集群中连接到 Leader 的 Follower 服务器）初始化连接时最长能忍受多少个心跳时间间隔数。当已经超过 5个心跳的时间（也就是 tickTime）长度后 Zookeeper 服务器还没有收到客户端的返回信息，那么表明这个客户端连接失败。总的时间长度就是 5*2000=10 秒
* syncLimit：这个配置项标识 Leader 与 Follower 之间发送消息，请求和应答时间长度，最长不能超过多少个 tickTime 的时间长度，总的时间长度就是 2*2000=4 秒
* server.A=B:C:D，其中 A 是一个数字，表示这个是第几号服务器；B 是这个服务器的 ip 地址；C 表示的是这个服务器与集群中的 Leader 服务器交换信息的端口；D 表示的是万一集群中的 Leader 服务器挂了，需要一个端口来重新进行选举，选出一个新的 Leader，而这个端口就是用来执行选举时服务器相互通信的端口。如果是伪集群的配置方式，由于 B 都是一样，所以不同的 Zookeeper 实例通信端口号不能一样，所以要给它们分配不同的端口号。

拷贝两份server002、server003，这里说明一下为什么需要拷贝2份，因为最少3个节点才能算是一个集群，所以最少需要三个节点。

### 目录结构

```
[root@yf-docker-app-97-180 dev]# tree -d -L 2
.
├── server001
│   ├── data
│   ├── logs
│   └── zookeeper
├── server002
│   ├── data
│   ├── logs
│   └── zookeeper
└── server003
    ├── data
    ├── logs
    └── zookeeper
```

修改对应的myid和zoo.cfg

### 启动

```
[root@yf-docker-app-97-180 dev]# ./server001/zookeeper/bin/zkServer.sh start
JMX enabled by default
Using config: /root/dev/server001/zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
```
分别启动每个节点

查看集群的日志文件zookeeper.out查看是否有错误

### 验证zookeeper伪分布式是否成功

```
[root@yf-docker-app-97-180 dev]# ./server001/zookeeper/bin/zkCli.sh -server 127.0.0.1:2181
Welcome to ZooKeeper!

WATCHER::

WatchedEvent state:SyncConnected type:None path:null

[zk: 127.0.0.1:2181(CONNECTED) 0] 
```
到此zookeeper伪分布式环境安装完成。

### 参考

* http://zookeeper.apache.org/
* http://dubbo.io/Administrator+Guide.htm#AdministratorGuide-ZookeeperRegistryInstallation
