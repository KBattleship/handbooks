---
title: "[ Spring ] 1. Spring"
date: 2021-01-08T01:13:47+08:00
lastmod: 2021-01-08T01:13:47+08:00
keywords: ['spring']
description: ""
tags: ['interview','java','spring']
categories: ['Java']
author: ""
---

# Spring

## 1. Spring BeanFactory与FactoryBean的区别

+ BeanFactory:

    + BeanFactory 是一个工厂类(接口）。同时是IOC容器的核心接口，用于 **管理Bean** 的工厂。
    + 职责包括：**实例化**、**定位**、**配置应用程序中对象** 以及 **对象间的依赖关系**。

+ FactoryBean:
    + 为IOC容器中的Bean的实现提供更加灵活的方式。
    + FactoryBean在IOC容器的基础上为Bean的实现加上了一个简单工厂模式和装饰模式，可以通过在getObject()方法灵活配置。
    + 当IOC容器中的Bean实现了FactoryBean接口后，通过getBean()获取到的Bean对象并不是FactoryBean的实现类对象，而是这个实现类中getObject()返回的对象。
    + 如果想要获取FactoryBean的实现，需要在getBean()参数`BeanName`前添加`&`符号。
  

## 2. Bean的生命周期?
+ 实例化Bean： Ioc容器通过获取BeanDefinition对象中的信息进行实例化，实例化对象被包装在BeanWrapper对象中
+ 设置对象属性（DI）：通过BeanWrapper提供的设置属性的接口完成属性依赖注入；
+ 注入Aware接口（BeanFactoryAware， 可以用这个方式来获取其它 Bean，ApplicationContextAware）：Spring会检测该对象是否实现了xxxAware接口，并将相关的xxxAware实例注入给bean
+ BeanPostProcessor：自定义的处理（分前置处理和后置处理）
+ InitializingBean和init-method：执行我们自己定义的初始化方法
使用
+ destroy：bean的销毁
  
IOC：控制反转：将对象的创建权，由Spring管理. DI（依赖注入）：在Spring创建对象的过程中，把对象依赖的属性注入到类中。

## 4. SpringIOC

  控制反转，通过依赖注入方式实现，IOC利用java反射机制，AOP利用代理模式。所谓控制反转是指，本来被调用者的实例是由调用者来创建的，这样的缺点是耦合性太强，IOC则是统一交给spring来管理创建，将对象交给容器管理，你只需要在spring配置文件总配置相应的bean，以及设置相关的属性，让spring容器来生成类的实例对象以及管理对象。在spring容器启动的时候，spring会把你在配置文件中配置的bean都初始化好，然后在你需要调用的时候，就把它已经初始化好的那些bean分配给你需要调用这些bean的类

## 5. Spring容器加载Bean
  1. BeanDefinitionReader读取Resource所执行的配置文件资源，解析配置文件，并将生成的BeanDefinition对象保存到BeanDefinitionRegistry中。
  2. 容器扫描Bean定义注册表中的BeanDefinition对象，调用InstantiationStrategy进行Bean实例化的工作；采用BeanWrapper完成Bean属性的设置工作。
  3. 若是单例的Bean，则将Bean缓存在Bean缓存器中。

## 6. Spring三级缓存

  + 一级缓存：Map<String, Object> singletonObjects
    + 作用
      + 用于存储单例模式下创建的Bean实例（已经创建完毕）。
      + 该缓存是对外使用的，指的就是使用Spring框架的程序员。
    + 对象
      + K：bean的名称
      + V：bean的实例对象（有代理对象则指的是代理对象，已经创建完毕）
  + 第二级缓存：Map<String, Object> earlySingletonObjects
    + 作用：
      + 用于存储单例模式下创建的Bean实例（该Bean被提前暴露的引用,该Bean还在创建中）。
      + 该缓存是对内使用的，指的就是Spring框架内部逻辑使用该缓存。
    + 对象：
      + K：bean的名称
      + V：bean的实例对象（有代理对象则指的是代理对象，该Bean还在创建中）
  + 第三级缓存：Map<String, ObjectFactory<?>> singletonFactories
    + 作用：
      + 通过ObjectFactory对象来存储单例模式下提前暴露的Bean实例的引用（正在创建中）。
      + 该缓存是对内使用的，指的就是Spring框架内部逻辑使用该缓存。
      + 此缓存是解决循环依赖最大的功臣
    + 对象：
      + K：bean的名称
      + V：ObjectFactory，该对象持有提前暴露的bean的引用

`举例描述三级缓存`
+ 实例化 A，此时 A 还未完成属性填充和初始化方法（@PostConstruct）的执行，A 只是一个半成品。
+ 为 A 创建一个 Bean 工厂，并放入到  singletonFactories 中。
+ 发现 A 需要注入 B 对象，但是一级、二级、三级缓存均为发现对象 B。
+ 实例化 B，此时 B 还未完成属性填充和初始化方法（@PostConstruct）的执行，B 只是一个半成品。
+ 为 B 创建一个 Bean 工厂，并放入到  singletonFactories 中。
+ 发现 B 需要注入 A 对象，此时在一级、二级未发现对象 A，但是在三级缓存中发现了对象 A，从三级缓存中得到对象 A，并将对象 A 放入二级缓存中，同时删除三级缓存中的对象 A。（注意，此时的 A 还是一个半成品，并没有完成属性填充和执行初始化方法）
将对象 A 注入到对象 B 中。
+ 对象 B 完成属性填充，执行初始化方法，并放入到一级缓存中，同时删除二级缓存中的对象 B。（此时对象 B 已经是一个成品）
+ 对象 A 得到对象 B，将对象 B 注入到对象 A 中。（对象 A 得到的是一个完整的对象 B）
+ 对象 A 完成属性填充，执行初始化方法，并放入到一级缓存中，同时删除二级缓存中的对象 A。

## 7. Spring循环依赖
  + 出现循环依赖的Bean必须要是单例
  + 依赖注入的方式不能全是构造器注入的方式
  + Spring通过三级缓存解决了循环依赖，其中一级缓存为单例池（singletonObjects）,二级缓存为早期曝光对象earlySingletonObjects，三级缓存为早期曝光对象工厂（singletonFactories）

## 8. SpringAOP

- 代理创建
  - 首先，需要创建代理工厂，代理工厂需要 3 个重要的信息：拦截器数组，目标对象接口数组，目标对象。
  - 创建代理工厂时，默认会在拦截器数组尾部再增加一个默认拦截器 —— 用于最终的调用目标方法。
  - 当调用 getProxy 方法的时候，会根据接口数量大余 0 条件返回一个代理对象（JDK or Cglib）。
- 代理调用
  - 当对代理对象进行调用时，就会触发外层拦截器。
  + 外层拦截器根据代理配置信息，创建内层拦截器链。创建的过程中，会根据表达式判断当前拦截是否匹配这个拦截器。而这个拦截器链设计模式就是职责链模式。
  + 当整个链条执行到最后时，就会触发创建代理时那个尾部的默认拦截器，从而调用目标方法。最后返回

- SpringAOP的关键点
  - 切面（Aspect） @AspectJ
  - 连接点
  - 通知
  - 切入点
  - 引入
  - 目标对象
  - 织入

## 9. JDK动态代理和CGLIB动态代理的区别

  + JDK动态代理只提供接口的代理，不支持类的代理。核心InvocationHandler接口和Proxy类，InvocationHandler 通过invoke()方法反射来调用目标类中的代码，动态地将横切逻辑和业务编织在一起；接着，Proxy利用 InvocationHandler动态创建一个符合某一接口的的实例, 生成目标类的代理对象。
  + 如果代理类没有实现 InvocationHandler 接口，那么Spring AOP会选择使用CGLIB来动态代理目标类。CGLIB（Code Generation Library），是一个代码生成的类库，可以在运行时动态的生成指定类的一个子类对象，并覆盖其中特定方法并添加增强代码，从而实现AOP。CGLIB是通过继承的方式做的动态代理，因此如果某个类被标记为final，那么它是无法使用CGLIB做动态代理的。
  + 静态代理与动态代理区别在于生成AOP代理对象的时机不同，相对来说AspectJ的静态代理方式具有更好的性能，但是AspectJ需要特定的编译器进行处理，而Spring AOP则无需特定的编译器处理。

## 10. 自动装配

  在Spring框架中，在配置文件中设定bean的依赖关系是一个很好的机制，Spring 容器能够自动装配相互合作的bean，这意味着容器不需要和配置，能通过Bean工厂自动处理bean之间的协作。这意味着 Spring可以通过向Bean Factory中注入的方式自动搞定bean之间的依赖关系。自动装配可以设置在每个bean上，也可以设定在特定的bean上。

## 11. Spring启动加载配置文件的流程

在Spring中的，如果一个Bean实现了ApplicationListener接口，并且已经发布到容器中去，每次ApplicationContext发布一个ApplicationEvent事件，这个Bean就会接到通知。Spring事件机制是观察者模式的实现。



## 12. @Autowired与@Resource区别

  - @Autowired默认是按照类型装配注入的，默认情况下它要求依赖对象必须存在（可以设置它required属性为false）。@Autowired 注解提供了更细粒度的控制，包括在何处以及如何完成自动装配。它的用法和@Required一样，修饰setter方法、构造器、属性或者具有任意名称和/或多个参数的PN方法。
  - @Autowired默认是按照类型装配注入的，默认情况下它要求依赖对象必须存在（可以设置它required属性为false）。
  - @Resource默认是按照名称来装配注入的，只有当找不到与名称匹配的bean才会按照类型来装配注入。
  - @Autowired可用于：构造函数、成员变量、Setter方法

## 13. @Qualifier与@Autowired
- 当您创建多个相同类型的 bean 并希望仅使用属性装配其中一个 bean 时，您可以使用@Qualifier 注解和 @Autowired 通过指定应该装配哪个确切的 bean 来消除歧义。

## 14. SpringBoot启动流程
+ new springApplication对象，利用spi机制加载applicationContextInitializer， applicationLister接口实例（META-INF/spring.factories）；
+ 调run方法准备Environment，加载应用上下文（applicationContext），发布事件 很多通过lister实现
+ 创建spring容器， refreshContext（） ，实现starter自动化配置，spring.factories文件加载， bean实例化

##  SpringBoot自动配置的原理
+ @EnableAutoConfiguration找到META-INF/spring.factories（需要创建的bean在里面）配置文件
+ 读取每个starter中的spring.factories文件

## 15. SpringAOP常用的切入点

## 16. SpringIOC常用注解

## 17. SpringBoot订阅发布模式。ApplicationEvent处于什么角色，如何实现的？