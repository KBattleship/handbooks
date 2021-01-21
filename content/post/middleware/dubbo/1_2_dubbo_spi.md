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

## **1. Dubbo SPI与Java SPI**
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
      4. Dubbo SPI增加了对拓展点IOC和AOP的支持，一个拓展点可以直接通过Setter注入其他拓展点。
      5. Java SPI会一次性实例化拓展点所有实现，如果有拓展实现初始化过程很耗时，并且用不上，将会造成资源浪费。
    + Dubbo中SPI的具体实现
      + [协议扩展(Protocol)]()：`RPC协议扩展，用于封装远程调用细节`
      + [调用拦截扩展(Filter)]()：`服务提供方和服务消费方调用过程拦截，Dubbo本身大多数功能都是基于此扩展点实现，每次远程方法执行，该拦截都会被执行`
      + [引用监听扩展(InvokerListener)]()：`当有服务被引用时触发此事件`
      + [暴露监听扩展(ExporterListener)]()：`当有服务被暴露时触发此事件`
      + [集群扩展(Cluster)]()：`当存在多个服务提供方时，将多个服务提供方组成一个集群，并伪装成一个服务提供方`
      + [路由扩展(RouterFactory)]()：`从多个服务提供方中选择一个进行调用`
      + [负载均衡(LoadBalance)]()：`从多个服务提供方中选择一个进行调用`
      + [合并扩展(Merger)]()：`合并返回结果，用于分组聚合`
      + [注册中心扩展(Registry)]()：`负责服务的注册与发现`
      + [监控扩展(Monitor)]() ：`负责服务调用次数以及调用时间的监控`
      + [扩展点加载扩展(ExtensionFactory)]()：`扩展本身的加载容器，可从不同容器加载扩展点`
      + [动态代理扩展(ProxyFactory)]()：`将Invoker接口转换成业务接口`
      + [编译器扩展(Compiler)]()： `Java代码编译器，用于动态生成字节码，加速调用`
      + [配置中心扩展(DynamicConfiguration)]()：`作为Key-Value存储`
      + [消息派发扩展(Dispatcher)]()：`通道信息派发器，用于指定线程池模型`
      + [线程池扩展(ThreadPool)]()：`服务提供方线程池的实现策略`
      + [序列化扩展(Serialization)]()：`将对象转化成字节流，用于网络传输，以及将字节流转为对象，用于接收到字节流数据时还原成对象`
      + [网络传输扩展(Transporter)]()：`远程通信的服务器以及客户端传输实现`
      + [信息交换扩展(Exchange)]()：``
      + [组网扩展(Networker)]()：`对等网络节点组网器`
      + [Telnet命令扩展(TelnetHandler)]()：`所有服务器均支持telnet访问，用于人工干预`
      + [状态检查扩展(StatusChecker)]()：`检查服务依赖各种资源的状态，此状态检查可同时用于telnet的status命令和hosting的status页面`
      + [容器扩展(Container)]()：`服务容器扩展，用于自定义加载内容`
      + [缓存扩展(CacheFactory)]()：`用于请求参数作为Key，缓存返回结果`
      + [验证扩展(Validation)]()：`参数验证扩展点`
      + [日志适配扩展(LoggerAdapter)]()：`日志输出适配扩展点`

## **2. 详细了解Dubbo SPI**

### a. SPI注解
+ 使用@SPI仅能配置一种实现类，但是可以根据`@SPI("dubbo")`指定不同的实现类，再调用ExtensionLoader获取实现类的时候，会加载配置文件中key所对应的实现类。
+ 多个模块编译后会将`META-INF/dubbo/`或`META-INFO/dubbo/internal/`下的统一拓展点的配置文件中的配置合并在一起。

### b. Activate注解
+ 被@Activate注解注释的扩展点默认被激活启用，还可以通过注解的`value`属性指定此扩展点在什么情况下被自动激活
+ 被@Activate注解注释的扩展点，在获取实现类是不能再使用ExtensionLoader.getExtension(String name)，而应该使用`ExtensionLoader.getActivateExtension(URL url, String[] values)`
+ 目的也是为了支持一个扩展点存在多个实现类需要同时启用的场景。
+ 主要是用扩展点(接口)的实现类上，所以此扩展点被@Activate注解的实现类都会在指定条件下自动激活
### c. Adaptive注解
+ `@Adaptive`注解是Dubbo自适应拓展机制，最重要的三个字应该就是`自适应`
+ 目的： `拓展点在方法被调用的时候，根据运行时参数进行加载`
+ 实现： `Dubbo使用javassist为拓展接口生成具有代理功能的代码，然后通过JDK编译这段代码得到Class类，最后在通过反射创建代理类。`
+ 应用： 
  + 在RPC远程调用过程中，会在url上携带参数，比如调用的目标是XxxService的yy()，而服务提供者XxxService具有多个实现类，那么可以在url上指定使用哪个实现类(配置文件中该实现类的key)，然后在通过SPI获取到此实现类的实例。
  + javassist生成的代码，就是拿到方法的url参数，从url中动态获取配置的参数，然后通过SPI加载具体的实现类，最后调用实现类的方法
  + 所以，先判断方法url参数是否为Null，如果为Null则抛出异常，否则从url中获取参数，如果没有获取到也抛出异常，如果获取到就通过SPI获取实例。