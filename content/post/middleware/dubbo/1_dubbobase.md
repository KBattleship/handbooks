---
title: "[ Dubbo ] 1.Dubbo基础概念"
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

## 2.Dubbo服务注册与发现流程
  1. Container负责启动，加载，运行服务提供者
  2. Provider启动时，向注册中心注册自己并提供服务
  3. Consumer启动时，向注册中心订阅自已需调用服务
  4. Register返回服务提供者地址列表给服务消费者，如运行期间，服务提供者发生变动，将通过长连接推送至服务消费者
  5. Consumer通过负载均衡算法(软方式)，选取注册中心所返回的服务提供者列表中的一个节点进行调用，如果调用失败将尝试其他节点进行调用
  6. Consumer、Provider将调用次数、时间记录于内存中，并定时每分钟发送至Monitor监控中心

## 3-1. Dubbo服务暴露过程

+ Dubbo 会在 Spring 实例化完 bean 之后，
+ 在刷新容器最后一步发布 ContextRefreshEvent 事件的时候，通知实现了 ApplicationListener 的 ServiceBean 类进行回调 onApplicationEvent 事件方法。
+ Dubbo 会在这个方法中调用 ServiceBean 父类 ServiceConfig 的 export 方法，而该方法真正实现了服务的发布。

## 3-2. Dubbo服务引用过程
1. 服务暴露之后，客户端就要引用服务，然后才是调用的过程。
2. 首先客户端根据配置文件信息从注册中心订阅服务
3. 之后DubboProtocol根据订阅的得到provider地址和接口信息连接到服务端server，开启客户端client，然后创建invoker
4. invoker创建完成之后，通过invoker为服务接口生成代理对象，这个代理对象用于远程调用provider，服务的引用就完成了

## 4. Dubbo的管理控制台能做什么
+ 路由规则
+ 动态配置
+ 服务降级
+ 访问控制
+ 权重调整
+ 负载均衡等管理功能

## 5. Dubbo集群容错方案

+ Failover Cluster(默认方案)
  + 失败后自动切换，出现失败将会重试其他服务节点(retries重试次数)
  + 通常用于读操作，但重试会带来很重的延迟
+ Failfast Cluster
  + 快速失败
  + 只发起一次调用，失败后立即抛出异常
  + 用于写操作
+ Failsafe Cluster
  + 失败安全机制
  + 出现异常时，将进行忽略
  + 通常用于写入审计日志
+ Failback Cluster
  + 失败自动恢复
  + 后台记录下失败请求，通过定时回调进行重试
  + 常用于消息通知
+ Forking Cluster
  + 并行调用
  + 只要存在一个成功就返回
  + 通常用于实时性较高的读操作，但浪费资源
  + 可通过设定`forks`的值为2，限制最大并行数
+ Broadcast Cluster
  + 广播调用
  + 任意一台报错则报错，通常用于通知所有提供者更新缓存或者日志等资源。

## 6. Dubbo支持什么协议
+ dubbo：单一长连接、NIO异步通讯，适用于小数据量大并发的服务调用，以及服务消费者机器数远大于服务提供者机器数的情况
+ hessian：短连接，http，适合页面传输、文件传输。与原生hessian服务互操作
+ http：适用于同时给应用程序和浏览器使用时
+ webservice：适用于系统集成，跨语言使用
+ rmi：适用于常规的远程服务方法调用，与原生RMI服务互操作

## 7. Dubbo如何做负载均衡

```xml
<dubbo:service interface="..."loadbalance="random"/>
<dubbo:reference interface="..."loadbalance=" random "/>
```
+ **RandomLoadBalance**: 随机负载均衡(默认方式)
+ **RoundRobinLoadBalance**: 轮询负载均衡
+ **LeastActiveLoadBalance**: 最少活跃调用数(相同活跃数的随机)
  + 活跃数指调用前后计数差
  + 使响应慢的Provider收到更少的请求，因为越慢的provider的调用前后计数差越大
+ **ConsistentHashLoadBalance**: 一致性Hash负载均衡，相同参数的请求总是落在同一台机器上

## 8. Dubbo如何实现异步调用
+ API注入时添加异步调用标识
  + @Reference(interfaceClass=xxx.class,async-true)
+ 启动类开启异步调用
  + @EnableAsync
+ 异步调用接口添加异步调用代码
  + RpcContext.getContext.future()

## 9. 在Provider上可以配置Consumer端的属性有哪些
+ timeout：调用超时
+ retries：重试次数(默认是2次)
+ LoadBalance：负载均衡算法
+ actives：消费者端最大并发调用限制

```xml
<dubbo:service interface="com.alibaba.hello.api.HelloService" version="1.0.0" ref="helloService"
   timeout="300" retry="2" loadbalance="random" actives="0" />
 
<dubbo:service interface="com.alibaba.hello.api.WorldService" version="1.0.0" ref="helloService"
    timeout="300" retry="2" loadbalance="random" actives="0" >
    <dubbo:method name="findAllPerson" timeout="10000" retries="9" loadbalance="leastactive" actives="5" />
<dubbo:service/>
```

## 10. Zookeeper和Dubbo的关系
+ Dubbo将注册中心进行抽象，使得它可以外接不同的存储媒介给注册中心提供服务
+ 使用Zookeeper作为存储媒介
  + 负载均衡：
    + 单注册中心的承载能力是有限的，在流量达到一定程度的时候就需要分流，负载均衡就是为了分流而存在的，一个 ZooKeeper 群配合相应的 Web 应用就可以很容易达到负载均衡
  + 资源同步：
    + 节点之间的数据和资源需要同步，ZooKeeper集群就天然具备有这样的功能
  + 命名服务：
    + 通过树状结构用于维护全局的服务地址列表
    + 服务提供者在启动的时候，向 ZooKeeper上的指定节点/dubbo/${serviceName}/providers目录下写入自己的URL地址，这样就可以完成服务发布
  + Master选举：
     
  + 分布式锁：
    + 独占锁
      + 即一次只能有一个线程使用资源
    + 共享锁
      + 读锁共享，读写互斥，即可以有多线线程同时读同一个资源，如果要使用写锁也只能有一个线程使用

## 11. Dubbo分层

+ 分为三层：
  + business层：(业务逻辑层)自己提供接口进行实现、以及一些配置
  + RPC层：(RPC调用核心层)封装了调用过程、负载均衡、集群容错、代理功能
  + remoting层：对网络传输协议和数据转换的封装

+ 分为十层：
  + service：业务逻辑层
  + config：配置层，初始化配置信息
  + proxy：代理层，为Provider和Consumer提供代理
  + register：服务注册层，封装服务注册与发现
  + cluster：路由层，封装provider路由与负载均衡
  + monitor：监控统计层，提供rpc调用时间、次数监控与统计
  + protocol：远程调用层，封装rpc调用
  + exchange：信息交换层，封装请求响应模式，同步转异步
  + transport：网络传输层，对Netty的封装
  + serialize：序列化层，对数据进行序列化、反序列化

## x. Dubbo项目结构
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
