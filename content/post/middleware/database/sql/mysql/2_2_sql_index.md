---
title: "[ MySQL ] 2.MySQL索引"
date: 2020-01-06T01:13:47+08:00
lastmod: 2020-01-06T01:13:47+08:00
keywords: ['mysql']
description: ""
tags: ['middleware','mysql','index','索引']
categories: ['MiddleWare']
author: ""
---

# MySQL索引

> Index(索引)，在存储引擎中用于快速找到记录的一种数据结构。索引用来快速寻找特定值的记录。

如果没有索引，执行查询时，MySQL必须从第一个记录开始扫描整个表的所有记录，知道找到符合要求的记录。<br/>
如果存在索引，MySQL无需扫描全表即可迅速查找到目标记录所在的位置。

## 1. 索引类型
+ 数据结构角度：
  + B+ Tree索引
  + Hash索引

    只有Memory存储引擎显示支持hash索引
  + FullText索引

    目前MyISAM和InnoDB引擎已支持。
  + R- Tree索引
    
    用于对GIS数据类型进行创建Spatial索引。
+ 物理存储角度：
+ 逻辑角度：
## 2. MySQL索引失效的几种情况
  + 如果条件中有or，即使其中有条件带索引也不会使用
  + 对于多列索引，不是使用的第一部分(第一个)，则不会使用索引
  + like查询是以%开头
  + 如果列类型是字符串，那一定要在条件中将数据使用引号引用起来,否则不使用索引
  + 如果mysql估计使用全表扫描要比使用索引快,则不使用索引
  + not in ,not exist.
  + 范围查询

## 3. 强制索引

```sql
# 强制索引
select * from table force index(PRI) limit 2;

# 禁止索引
select * from table ignore index(PRI) limit 2;
```