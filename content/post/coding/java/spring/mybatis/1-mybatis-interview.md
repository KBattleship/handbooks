---
title: "[ Mybatis ] 1. Mybatis"
date: 2021-01-07T01:13:47+08:00
lastmod: 2021-01-07T01:13:47+08:00
keywords: ['mybatis']
description: ""
tags: ['interview','Mybatis','java']
categories: ['Java']
author: ""
---
# MyBatis

## 1. MyBatis二级缓存
1. 一级缓存：
    + 基于PrepetualCache的HashMap本地缓存，其作用域是Session。
    + 当Session在flush()或者close()之后，此Session中所有Cache都将被清除。
    + 默认情况下，一级缓存处于打开状态。
    
2. 二级缓存：
    + 二级缓存与一级缓存机制相同，一样基于PrepetualCache HashMap进行存储。
    + 但其作用于是Mapper(Namespace)，而且可以采用自定义存储源，
        例如Ehcache、Redis等。
    + 默认情况下，二级缓存处于关闭状态。
    + 二级缓存在使用中，其属性类型需要实现serialize序列化接口。
    + 可在其映射配置文件中进行<cache/>
    
3. 对于缓存更新机制，当某一作用域(一级Session/二级Namespace)执行C/U/D后
        默认情况下，此作用域下所有select中的缓存将被clear。

## 2. Mybatis工作原理

1. 加载Mybatis全局配置文件mybatis-config.xml：
   + 此配置文件用于配置数据库连接信息。
    
2. 加载Mybatis映射配置文件xxx-mapper.xml:
    + 此配置文件包含有操作数据库的SQL语句，需在mybatis-config.xml文件中进行配置。
    + mybatis-config.xml中可以配置多个xxx-mapper.xml文件
    + 一个xxx-mapper.xml文件对应数据库中一张表。
3. 构建会话工厂：
    + 通过配置信心初始化构建SQLSessionFactory对象。
    
4. 创建会话对象：
   + 基于会话工厂创建SQLSession对象。
   + 此Sqlsession对象中包含有执行SQL语句的所有方法。
5. Executor执行器
   + Mybatis底层封装了一个Executor的接口用于操作数据库。
   + 将根据SqlSession对象传递的参数动态生成所需要执行的SQL语句，同时负责缓存查询。
  
6. MappedStatement对象：
   + 在Executor接口的执行方法中存在一个MappedStatement类型的参数，
   + 该参数是对映射地址信息进行封装，用于存储要映射的SQL语句的id，参数等信息。

7. 输入参数映射：
   + 输入类型可以为Map、List等集合，也可为基本类型和POJO类型。
   + 输入参数映射的过程类似于JDBC中preparedStatement对象设置参数的过程。

8. 输出结果映射：
   + 输出类型通输入类型一样。可为List、Map等集合，也可为基本类型和POJO类型。
   + 输出结果映射的过程类似于JDBC的解析过程。