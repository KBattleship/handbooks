---
title: "Dubbo基础概念"
date: 2020-12-22T15:36:27+08:00
lastmod: 2020-12-22T15:36:27+08:00
keywords: ['dubbo']
description: ""
tags: ['java','dubbo','分布式']
categories: ['Java']
author: ""
---
# Dubbo基础概念

## 1.Dubbo核心组件
+ Provider： 暴露服务的服务提供方
+ Consumer： 调用远程服务的消费方
+ Register： 服务注册与发现注册中心
+ Monitor：  监控中心和访问调用统计
+ Container：服务运行时容器

> Dubbo分层主要为业务层、RPC层和Remote层，如果把每层进行详细划分的话，整体划分为：

+ 业务层：
    + **service:** 包含各业务代码的接口与实现；
+ RPC层：
    + **config:** 配置层，主要围绕ServiceConfig(暴露的服务配置)和ReferenceConfig(引用的服务配置)两个类展开，初始化配置信息；
    + **proxy:**    服务代理层，不论生产者还是消费者，Dubbo都会生成一个代理类，在调用远程接口时，就可以像本地接口一样，代理层自动做远程调用并返回结果；
    + **registry:**    注册层，负责Dubbo框架的服务注册与发现；
    + **cluster:**     集群容错层，主要负责远程调用失败时的集群容错策略(如快速失败、快速重试等)；
    + **monitor:**    监控层，负责监控统计调用次数和调用时间等；
    + **protocol:**    远程调用层，封装RPC调用具体过程，是Invoker暴露和引用的主要功能入口，负责管理Invoker的整个生命周期；
+ Remote层：
    + **exchange:**  信息交换层，封装请求相应模式，如同步请求转换为异步请求；
    + **transport:**  网络传输层，把网络传输抽象为统一接口；
    + **serialize:**  序列化层，将需要网络传输的数据极性序列化，转为二进制流。

## 2.Dubbo服务器注册与发现流程
- a. Container负责启动，加载，运行服务提供者
- b. Provider启动时，向注册中心注册自己并提供服务
- c. Consumer启动时，向注册中心订阅自已需调用服务
- d. Register返回服务提供者地址列表给服务消费者，如运行期间，服务提供者发生变动，将通过长连接推送至服务消费者
- e. Consumer通过负载均衡算法(软方式)，选取注册中心所返回的服务提供者列表中的一个节点进行调用，如果调用失败将尝试其他节点进行调用
- f. Consumer、Provider将调用次数、时间记录于内存中，并定时每分钟发送至Monitor监控中心

## 3.Dubbo项目结构
```shell
$ tree -L 1
.
├── dubbo-all
├── dubbo-bom
├── dubbo-build-tools
├── dubbo-cluster               # 集群容错模块，涵盖负载均衡策略、集群容错策略及路由
├── dubbo-common                # 通用逻辑模块，提供工具类和通用类型
├── dubbo-compatible            # 兼容性模块
├── dubbo-config                # 配置模块，主要实现API配置、属性配置、XML配置等
├── dubbo-configcenter          # 动态配置中心模块
├── dubbo-container             # 容器运行时，采用Main方法加载Spring容器
├── dubbo-demo                  # 三种远程调用方式实例
├── dubbo-dependencies          # 
├── dubbo-dependencies-bom
├── dubbo-distribution
├── dubbo-filter                # 过滤器
├── dubbo-metadata              # 元数据信息
├── dubbo-monitor               # 监控模块，主要监控接口调用次数及时间等信息
├── dubbo-plugin                # 插件拓展模块
├── dubbo-registry              # 服务发现与注册中心模块
├── dubbo-remoting              # 远程通信模块，为消费者、生产者间提供远程调用能力
├── dubbo-rpc                   # 抽象各种通信协议以及动态代理(易混淆remoting)
├── dubbo-serialization
```
