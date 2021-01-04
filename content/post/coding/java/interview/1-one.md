---
title: "[Interview]1.面试准备"
date: 2020-12-31T11:13:47+08:00
lastmod: 2020-12-31T11:13:47+08:00
keywords: ['interview']
description: ""
tags: ['java','interview','面试']
categories: ['Java']
author: ""
---
# 面试准备
+ [ ] Java线程池
  ```shell

  ```
+ [ ] JDK中JUC包
  ```shell

  ```
+ [ ] JVM调优：如果发现某个服务慢，如何排查，如何处理；发现某个服务器CPU100%了，应该如何处理
  ```shell
  遵循六步走原则：
    1）找到罪魁祸首的进程
    2）分析进程对应的线程
    3）生成JVM当前时刻线程快照
    4）分析定位代码问题
    5）
  ```
+ [ ] 垃圾回收器G1与GC算法CMS
  ```shell

  ```
+ [ ] Tomcat 如何实现类隔离
  ```shell

  ```
+ [ ] Spring IOC原理
  ```shell

  ```
+ [ ] Spring AOP原理
  ```shell
    
  ```
+ [ ] Spring事务
  ```shell

  ```
+ [ ] Dubbo服务发现、注册流程
  ```shell

  ```
+ [ ] Dubbo通信原理
  ```shell

  ```
+ [x] Dubbo SPI与Java SPI
  ```shell
    SPI(Service Provider Interface)，主要用于框架，框架定义接口。
    不同使用者将存在不同需求，也必然出现不同实现方式。而SPI就是通过定义
    一个特定的位置，Java SPI约定在Classpath下的META-INF/services/
    路径下创建一个以服务接口命名的文件，然后文件中记录的是此jar包提供的
    具体实现类的全限定名，并由服务加载器读取配置文件，加载实现类，这样可
    以在运行时动态为接口替换实现类。

    Dubbo SPI
      1.并非是Java原生的SPI，而是重新实现的SPI。
        Java SPI通过ServiceLoader进行加载；
        Dubbo SPI通过ExtensionLoader进行拓展加载。
      2.支持的注解：
        - @SPI(标记为拓展接口)
        - @Adaptive(自适应拓展实现类标志)
        - @Activate(自动激活条件标记)
      3.配置文件放在classpath下的META-INF/dubbo/以及
        META-INF/dubbo/internal下
      4.Dubbo SPI增加了对拓展点IOC和AOP的支持，一个拓展点可以直接
        通过Setter注入其他拓展点。
      5.Java SPI会一次性实例化拓展点所有实现，如果有拓展实现初始化
        过程很耗时，并且用不上，将会造成资源浪费。
  ```
+ [ ] Zookeeper选举协议
  ```shell

  ```
+ [ ] ActiveMQ集群消息回流、死信队列如何处理
  ```shell

  ```
+ [ ] 大数据量的排序如何处理
  ```shell

  ```
+ [ ] 如何判断链表是回文链表、快排序、归并排序
  ```shell

  ```
+ [ ] MySQL调优：BTree、Hash原理
  ```shell

  ```
+ [x] Mybatis的一、二级缓存
  ```shell
    1.一级缓存：
        基于PrepetualCache的HashMap本地缓存，其作用域是Session。
        当Session在flush()或者close()之后，此Session中所有Cache都将被清除。
        默认情况下，一级缓存处于打开状态。
    
    2.二级缓存：
        二级缓存与一级缓存机制相同，一样基于PrepetualCache HashMap进行存储。
        但其作用于是Mapper(Namespace)，而且可以采用自定义存储源，
        例如Ehcache、Redis等。
        默认情况下，二级缓存处于关闭状态。
        二级缓存在使用中，其属性类型需要实现serialize序列化接口。
        可在其映射配置文件中进行<cache/>
    
    3.对于缓存更新机制，当某一作用域(一级Session/二级Namespace)执行C/U/D后
        默认情况下，此作用域下所有select中的缓存将被clear。
  ```
+ [x] Mybatis工作原理
  ```shell
    1.加载Mybatis全局配置文件mybatis-config.xml：
        此配置文件用于配置数据库连接信息。
    
    2.加载Mybatis映射配置文件xxx-mapper.xml:
        此配置文件包含有操作数据库的SQL语句，需在mybatis-config.xml文件中进行配置。
        mybatis-config.xml中可以配置多个xxx-mapper.xml文件
        一个xxx-mapper.xml文件对应数据库中一张表。
    
    3.构建会话工厂：
        通过配置信心初始化构建SQLSessionFactory对象。
    
    4.创建会话对象：
        基于会话工厂创建SQLSession对象。
        此Sqlsession对象中包含有执行SQL语句的所有方法。

    5.Executor执行器
        Mybatis底层封装了一个Executor的接口用于操作数据库。
        将根据SqlSession对象传递的参数动态生成所需要执行的SQL语句，同时负责缓存查询。

    6.MappedStatement对象：
        在Executor接口的执行方法中存在一个MappedStatement类型的参数，
        该参数是对映射地址信息进行封装，用于存储要映射的SQL语句的id，参数等信息。
    
    7.输入参数映射：
        输入类型可以为Map、List等集合，也可为基本类型和POJO类型。
        输入参数映射的过程类似于JDBC中preparedStatement对象设置参数的过程。
    
    8.输出结果映射：
        输出类型通输入类型一样。可为List、Map等集合，也可为基本类型和POJO类型。
        输出结果映射的过程类似于JDBC的解析过程。

  ```
+ [x] Redis Bitmap
  ```shell
  Bitmap并不是一种独立的数据结构，而是基于String数据结构进行的位图操作。
  最大空间即为String数据结构所支持的512MB，
  所以bitmap所支持的最大offset为2^32-1.

  一般使用场景用于大数据签到、日活统计、在线统计等等。
  其主要优点可以节省大量空间。
  例如：
    进行日活统计：
        使用日期作为key，用户ID作为偏移量，1为当日活跃，0为不活跃
  ---
  ### 基本操作演示(以下为Redis-cli命令)
  
  # 设置一个字符串 1Aa 
  # 存储在redis中的二进制为 0b001100010100000101100001
  set testkey 1Aa

  # 进行bitmap操作
  setbit testkey 10 1
  setbit testkey 18 0

  # testkey对象index为10的bit值为1
  getbit testkey 10 
  # testkey对象index为18的bit值为1
  getbit testkey 18

  # 进行位统计 ,结果为bit上为1的和，
  # testkey输出结果为 8
  bitcount testkey

  # testkey输出结果为 3
  bitcount testkey 0 0
  ```