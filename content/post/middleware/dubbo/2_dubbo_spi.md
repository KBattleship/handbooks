---
title: "[ Dubbo ] 2. Dubbo中的SPI"
date: 2020-12-25T15:36:27+08:00
lastmod: 2020-12-25T15:36:27+08:00
keywords: ['dubbo']
description: ""
tags: ['java','dubbo','分布式','SPI']
categories: ['Java']
author: ""
---
# Dubbo中的SPI

## 1. Dubbo SPI与Java SPI
+ SPI(Service Provider Interface)，主要用于框架，框架定义接口。
    + 不同使用者将存在不同需求，也必然出现不同实现方式。
    + 而SPI就是通过定义一个特定的位置，Java SPI约定在Classpath下的`META-INF/services/`路径下创建一个以服务接口命名的文件，然后文件中记录的是此jar包提供的具体实现类的全限定名，并由服务加载器`读取配置文件`，`加载实现类`，这样可以在运行时`动态为接口替换实现类`。

    + Dubbo SPI
      1. 并非是Java原生的SPI，而是重新实现的SPI。
        - Java SPI通过ServiceLoader进行加载；
        - Dubbo SPI通过ExtensionLoader进行拓展加载。
      2. 支持的注解：
           - @SPI(标记为拓展接口)
           - @Adaptive(自适应拓展实现类标志)
           - @Activate(自动激活条件标记)
      3. 配置文件放在classpath下的META-INF/dubbo/以及
        META-INF/dubbo/internal下
      4. Dubbo SPI增加了对拓展点IOC和AOP的支持，一个拓展点可以直接
        通过Setter注入其他拓展点。
      5. Java SPI会一次性实例化拓展点所有实现，如果有拓展实现初始化
        过程很耗时，并且用不上，将会造成资源浪费。
    + Dubbo中SPI应用
      + 协议扩展
      + 集群扩展
      + 路由扩展
      + 序列化扩展