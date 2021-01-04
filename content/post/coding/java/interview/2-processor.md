---
title: "[Interview]Todo"
date: 2021-01-05
lastmod: 2021-01-05
keywords: ['interview']
description: ""
tags: ['java','interview','面试']
categories: ['Java']
author: ""
---

# 面试准备

## 一、学习路线

### 1.Java基础
+ 面向对象特性：封装、继承、多态(动态绑定、向上转型)
+ 泛型、类型擦除
+ 反射、其原理及其优缺点
+ `static`,`final`关键字
+ `String`,`StringBuffer`,`StringBuilder`底层区别
+ `BIO`、`NIO`、`AIO`
+ Object类的方法
+ 自动拆箱与自动装箱

### 2.集合框架
+ List
  + ArrayList
  + LinkedList
  + Vector
  + CopyOnWriteArrayList
+ Set
  + HashSet
  + TreeSet
  + LinkedHashSet
+ Queue
  + PriorityQueue
+ Map
  + HashMap
  + TreeMap
  + LinkedMap
+ fast-fail，fast-safe机制
+ 源码分析(底层数据结构，插入、扩容过程)、线程安全分析

### 3.Java虚拟机
+ 类加载机制、双亲委派模式、3种加载器(`BootstrapClassLoader`，`ExtensionClassLoader`，`ApplicationClassLoader`)
+ 运行时内存分区(PC，Java虚拟机栈，本地方法栈，堆，方法区[永久代、元空间])
+ JMM: Java内存模型分析
+ 引用计数、可达性分析
+ 垃圾回收算法：标记-清除、标记-整理、复制
+ 垃圾回收器：比较区别(Serial，ParNew，ParallelScavenge，CMS，G1)
+ 强、软、弱、虚引用
+ 内存溢出、内存泄漏排查
+ JVM调优、常用命令

### 4.Java并发
+ 三种线程初始化方法的区别(`Thread`,`Callable`,`Runnable`)
+ 线程池(ThreadPoolExecutor，7大参数、原理、四种拒绝策略、四个类型[Fixed、Single、Cached、Scheduled])
+ Synchronized
+ 使用：方法(静态、一般方法)；代码块(this，ClassName.class)
+ jdk1.6优化：锁粗化、锁消除、自适应自旋锁、偏向锁、轻量级锁
+ 锁升级的过程与细节：无锁->偏向锁->轻量级锁->重量级锁(不可逆)
+ ReentrantLock:与Synchronized区别、公平锁、非公平锁、可中断锁、原理、用法
+ 有界、无界任务队列，手写BlockingQueue
+ 乐观锁：CAS(优缺点，ABA问题，DCAS)
+ 悲观锁
+ ThreadLocal：底层数据结构、ThreadLocalMap、原理、应用场景
+ Atomic类：原理、应用场景
+ Volatile：原理、有序性、可见性

### 5.MySQL
+ 架构：Server层、引擎层(缓存、连接器、分析器、优化器、处理器)
+ 引擎：InnoDB、MyISAM、Memory的区别
+ 聚簇索引、非聚簇索引区别(从二叉平衡搜索树复习[AVL，红黑树]  ->  B树  -> B+树)
+ MySQL、SQL语句优化
+ 覆盖索引、最左前缀匹配
+ 当前读、快照读
+ MVCC原理(事务ID、隐藏字段、Undo、ReadView)
+ Gap Lock、Next-Key Lock、Record Lock
+ 三大范式
+ 常用SQL
+ 连接：自连接、内连接(等值、非等值、自然连接)、外连接(左、右、全)
+ Group BY 与 Having
+ Explain

### 5.Spring
+ AOP原理(JDK动态代理、CGLIB动态代理)、IOC原理
+ SpringBean生命周期
+ SpringMVC原理
+ SpringBoot常用注解

### 6.Unix Network
+ OSI模型、TCP/IP模型
+ TCP、UDP区别
+ TCP可靠性传输原理
  + 重传
  + 流量控制
  + 拥塞控制
  + 序列号、确认应答号
  + 校验和
+ 三次握手、四次挥手原理
+ timewait、closewait
+ HTTP
  + 报文格式
  + 协议
  + 状态码
  + 无状态解决(Cookie、Session)
+ HTTPS
  + CA证书
  + 对称、非对称加密
+ DNS解析过程原理
+ IP、ICMP、ARP、ROUTE协议
+ 攻击手段：XSS、CSRF、SQL注入、DOS、DDOS

### 7.OS
+ 进程、线程、协程区别
+ 进程通信方式(管道、消息队列、共享内存、信号、信号量、socket)
+ 进程调度算法：
  + 先进先出
  + 短作业优先
  + 时间片轮换
  + 多级反馈队列
  + 优先级调度
+ 内存管理
  + 页面置换算法：手写LRU
  + 分段
  + 虚拟内存

## 二、面试题锦
---
**1.算法**
1. 手写最大堆
2. 一道 LeetCode 难问题：接雨水（动态规划解决）
3. 手写 LRU（要求用泛型写）、手写 DCL
4. 一道动态规划题目：不同路径
5. 手写个归并排序
6. 了解 A*算法吗？
7. 手写地杰斯特拉算法？
8. 说说深度优先搜索算法、回溯算法
9. 一道算法题：一个走迷宫问题，DFS+回溯解决
10. 一道算法题：最长公共子串
11. 两个栈实现队列
12. 最近公共祖先节点
13. 一道算法题：完全平方数（动态规划）
14. 手写一个堆排序。
15. 手写快排
16. 算法题：按 K 位反转链表
17. 一百亿个数，n 个机器，怎么排序？（桶排序）
---
**2.设计模式**
1. 设计模式了解吗？几大类型？谈谈工厂模式？
---
**3.Java 集合**
1. 谈一下 Java 集合框架？HashMap 线程安全的吗？会出现什么问题？
2. 说说 fast-fail 和 fast-safe？
3. 讲讲 HashMap 的原理，put 过程？resize 过程？线程安全吗？死循环问题
---
**4.Java 并发**
1. 知道哪些 Java 的锁？CAS 的缺点？Synchronized 优化内容？锁升级过程？
2. 线程池了解吗？原理？可以写个 BlockingQueue 吗？
3. 了解死锁吗？怎么解决？
4. 哪些对象可以作为 GC ROOTS
5. 谈谈公平锁和非公平锁？
6. Synchronized 和 ReentrantLock 区别
7. ThreadLocal 了解吗？原理？
---
**5.JVM**
1. 谈谈Java虚拟机你的认识？垃圾回收算法？垃圾回收器？
2. Java是怎么进行垃圾回收的？
3. 谈谈Java虚拟机？类加载机制？
4. 知道双亲委派模式吗？有什么好处？
5. Java运行时内存分区？
6. 讲一下CMS垃圾回收过程
7. OOM 怎么排查？
---
**6.MySQL**
1. InnoDB 和 MyISAM 区别？谈谈 MySQL 的各种引擎？
2. 知道聚簇索引和非聚簇索引吗？B 树和 B+树区别？
3. 说说 MySQL 的架构
4. 说说 MYSQL 优化策略？
5. 覆盖索引和非覆盖索引区别？
6. MYSQL 优化方法有哪些？
7. MySQL 的索引为什么快？有哪些索引？原理数据结构？
8. 谈谈各种索引？为什么用 B+树不用 B 树？
---
**7.OS**
1. DNS 解析过程
2. 进程间通信方式？哪种最高效？
3. ARP 过程？
4. 进程调度算法？
---
**8.Network**
1. TCP 如何保证可靠性传输？（校验和，序列号和确认应答号，重传，流量控制，拥塞控制）
2. TCP拥塞控制算法？
3. TCP 和 UDP 区别？
4. HTTP 的状态码记得哪些？
5. ICMP 是哪层的？有什么用？
---
**9.Spring**
1. 谈谈 Spring AOP 和 IOC
2. SpringBoot 常用哪些注解？
---
**10.分布式系统**
1. 谈谈你对分布式系统的认识？