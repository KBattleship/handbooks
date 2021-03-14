---
title: "1.zookeeper分布式部署"
date: 2019-11-12T01:36:27+08:00
lastmod: 2019-11-12T01:36:27+08:00
keywords: ['middleware']
description: ""
tags: ['middleware','Zookeeper','Linux','分布式']
categories: ['middleware']
author: ""
---
# zookeeper分布式部署
### 一.配置服务器IP地址映射
`[root@localhost zk]~#: vim /etc/hosts`
```shell
192.168.1.111 zoo1
192.168.1.112 zoo2
192.168.1.113 zoo3
192.168.1.114 zoo4
192.168.1.115 zoo5
``` 
### 二.修改配置ZK文件
1.下载Zookeeper

```shell
# 进入ZK路径
wget https://mirrors.tuna.tsinghua.edu.cn/apache/zookeeper/zookeeper-3.5.6/apache-zookeeper-3.5.6-bin.tar.gz
```
2.修改配置文件
进入`conf`目录，在配置文件前，先`cp zoo_sample.cfg zoo.cfg`,然后`vim zoo.cfg`。配置如下：
```c
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/data/ops/zk/zookeeper-3.5.6-master/conf

clientPort=2181

server.1=zoo1:2888:3888
server.2=zoo2:2888:3888
server.3=zoo3:2888:3888
```
3.启动ZK
①.在每个节点的服务器依次启动服务：
`[root@localhost zk]~#: ./bin/zkServer.sh start`
在启动过程中日志会出现异常，由于其他节点还未启动，所以属于正常情况（正常情况下，仅有最后一个节点启动不会出现异常）。待所有节点全部启动，集群会逐渐稳定下来。
②.查询每一个节点角色
`[root@localhost zk]~#: ./bin/zkServer.sh status`
```shell
# LeaderNode
ZooKeeper JMX enabled by default
Using config: /data/ops/zk/zookeeper-3.5.6-follower/bin/../conf/zoo.cfg
Client port found: 2181. Client address: localhost.
Mode: leader
# FollowerNode
ZooKeeper JMX enabled by default
Using config: /data/ops/zk/zookeeper-3.5.6-master/bin/../conf/zoo.cfg
Client port found: 2181. Client address: localhost.
Mode: follower
```
