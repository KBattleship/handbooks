---
title: "[ JVM ] 4.JVM类加载机制"
date: 2020-12-27T21:10:47+08:00
lastmod: 2020-12-27T21:10:47+08:00
keywords: ['jvm']
description: ""
tags: ['java','jvm','command']
categories: ['Java']
author: ""
---
# JVM类加载机制

## 1. Java类加载机制

JVM中类的装载是由

## 2. 描述JVM加载Class文件的原理机制

## 3. 什么是类加载器？类加载器又有哪些？

+ **启动类加载器(Bootstrap ClassLoader)：**
  
    负责加载`${JAVA_HOME}/lib`目录下或通过`-Xbootclasspath`参数指定路径中被JVM认可的类
+ **扩展类加载器(Extension ClassLoader)：**

    负责加载`${JAVA_HOME}/lib/ext`目录中或通过`java.ext.dirs`系统变量指定路径中的类库
+ **应用程序类加载器(Application ClassLoader)：**

    负责加载用户路径`classpath`下的类库。JVM通过 **双亲委派模型** 进行类的加载，也可通过 **继承 java.lang.ClassLoader** 实现自定义的类加载器。
## 4. 类装载的执行过程。

+ 加载 获取类的二进制字节流，将其静态存储结构转化为方法区的运行时数据结构

+ 校验 文件格式验证，元数据验证，字节码验证，符号引用验证

+ 准备 在方法区中对类的static变量分配内存并设置类变量数据类型默认的初始值，不包括实例变量，实例变量将会在对象实例化的时候随着对象一起分配在Java堆中

+ 解析 将常量池内的符号引用替换为直接引用的过程

+ 初始化 为类的静态变量赋予正确的初始值（Java代码中被显式地赋予的值）

## 5. 什么是双亲委派模型？

1. 当一个类收到 **类加载请求** ，首先不去尝试自己加载此类，而是把此请求 委派 父类去完成；
2. 每一个层次类加载器都是如此，因此所有的加载请求都应该传送到 **启动类加载器** 中；
3. 只有当 **父类加载器** 反馈无法完成此请求是，**子类加载器** 才会尝试自己加载。

> 采用双亲委派的一个好处： 加载位于rt.jar包中的类 java.lang.Object，不管是哪个类加载器加载的这个类，最终都将委托给顶层的 **启动类加载器** 进行加载，即可以保证使用不同的类加载器，也能保证加载的都是同一个Object对象。


## 6. Tomcat打破双亲委派
tomcat有着特殊性，它需要容纳多个应用，需要做到应用级别的隔离，而且需要减少重复性加载，所以划分为：
+ /common 容器和应用共享的类信息，
+ /server容器本身的类信息，
+ /share应用通用的类信息,
+ /WEB-INF/lib应用级别的类信息。

整体可以分为：

boostrapClassLoader->

ExtensionClassLoader->

ApplicationClassLoader->

CommonClassLoader->

CatalinaClassLoader（容器本身的加载器）/ShareClassLoader（共享的）->

WebAppClassLoader。

虽然第一眼是满足双亲委派模型的，但是不是的，因为双亲委派模型是要先提交给父类装载，而tomcat是优先判断是否是自己负责的文件位置，进行加载的。