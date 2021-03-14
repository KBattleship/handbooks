---
title: "[ 设计模式 ] 5.单例模式"
date: 2019-07-11T23:36:27+08:00
lastmod: 2019-07-11T23:36:27+08:00
keywords: ['design_pattern']
description: ""
tags: ['design_pattern','java']
categories: ['java']
author: ""
---
# 单例模式
<font style="color:red;">**单例模式：**</font>`保证任何情况下都绝对的只生成一个实例的模式。`
#### ①.单例角色
+ `Singleton`: 有且仅有的一个角色(实例)。

**Tips:**`当存在多个实例时，实例间互相影响，会出现意想不到异常。当且仅当，第一次调用类中静态方法的时候，Singleton类会被初始化，生成唯一的静态实例，重复调用时直接返回已经初始化完成的实例。`
#### ②.实现单例
+ **方式一：**简化实现的单例模式

```java
public class Singleton {
    private static Singleton singleton=null;
    /*
        私有化，不给外界直接new的机会
     */
    private Singleton() {
    }
    /*
        1.简化实现的单例模式，但遇到多线程肯定有问题
     */
    public static Singleton getInstance(){
        if (singleton==null){
            singleton=new Singleton();
        }
        return singleton;
    }
}
```

+ **方式二：**加锁控制多线程

```java
public class Singleton {
    private static Singleton singleton=null;
    /*
        私有化，不给外界直接new的机会
     */
    private Singleton() {
    }
    /*
        2.通过加锁进行控制多线程临界资源冲突，不推荐，对线程资源消耗大
     */
    public static synchronized Singleton getInstance(){
        if(singleton==null){
            singleton=new Singleton();
        }
        return singleton;
    }
}
```
+ **方式三：**懒汉模式

```java
public class Singleton {
    /*
        私有化，不给外界直接new的机会
     */
    private Singleton() {
    }
    /*
       3.懒汉模式，直接new出来
     */
    privite  static Singleton singleton=new Singleton();
}
```
+ **方式四：**双重加锁机制

```java
public class Singleton {
    privite volatile static Singleton singleton=null;
    /*
        私有化，不给外界直接new的机会
     */
    private Singleton() {
    }
    /*
        4.双重加锁机制
     */
    public static Singleton getInstance(){
        if(singleton==null){
            synchronized(Singleton.class){
                if(singleton==null){
                    singleton=new Singleton();
                }
            }
        }
        return singleton;
    }
}
```