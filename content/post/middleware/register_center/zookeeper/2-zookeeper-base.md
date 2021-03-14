---
title: " [ Zookeeper ] 2. Zookeeper基础"
date: 2019-11-12T01:36:27+08:00
lastmod: 2019-11-12T01:36:27+08:00
keywords: ['middleware']
description: ""
tags: ['middleware','Zookeeper','Linux','分布式']
categories: ['middleware']
author: ""
---
# Zookeeper
## 1. ZAB协议
+ ZAB协议是为分布式协调服务Zookeeper专有的一种协议，此协议是为了应对崩溃恢复的原子广播
+ 崩溃恢复
  + 整个zk集群刚启动或者Leader节点宕机、重启或者不可以正常提供服务时超出一半的情况下，所有节点将会进入崩溃恢复模式
  + 首先通过选举产生Leader
  + 然后集群中的Follwer节点与新产生的Leader节点进行数据同步
  + 一旦集群中一半数量的节点与Leader节点完成了数据同步，集群就会退出崩溃恢复模式，进入到消息广播模式
+ 消息广播
  + Leader节点开始接受客户端的事务请求，生成事务的提案进行事务请求处理。