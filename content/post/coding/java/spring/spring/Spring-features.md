背景：不是为了推荐大家去使用Spring5
       文档很少、资料难找
	   没有实战实践，包括老师自己没有用过
目的：1、带大家来看看眼界
      2、了解一下未来的一个发展趋势
	  
鸡肋
	  
推荐：SpringBoot，Spring生态链（框架的框架）


Spring5新特性：

* 依赖JDK 8+和Java EE7+以上版本
支持使用注解进行编程
* 新增函数式编程
* 支持使用REST断点执行反应式编程
* 支持HTTP 2.0
* 新增Kotlin和Spring WebFlux
可使用Lambda表达式注册Bean
Spring WebMVC支持最新的API
* 使用JUnit5执行条件和并发测试
使用Spring WebFlux执行集成测试
核心容器优化


Spring5对WebFlux的支持


Sevlet2.x  单实例多线程的阻塞式IO  （BIO）
Sevlet3.x  单实例多线程异步非阻塞式IO  （AIO异步非阻塞、NIO 同步非阻塞）


WebFlux 是在Servlet3.x 的基础之上应运而生


特点：
1、基于Servlet3.x 实现异步非阻塞的HTTP响应
2、API完美支持Rest风格
3、支持函数式编程
4、Mono是表示返回单个对象、Flux表示返回集合


结论：
1、为了让大家开一下眼界，了解未来的发展趋势
2、如果要应用在项目之中，建议大家直接用Spring Boot



Spring5集成测试
JUnit集成
1、
     * @BeforeEach 相当于 JUnit 4 @Before
     * @BeforeAll 相当于 JUnit 4 @BeforeClass
     * @AfterEach 相当于 JUnit 4 @After
     * @AfterAll 相当于 JUnit 4  @AfterClass
	 * @Test 不在org.unit这个包下，转到了org.junit.jupiter.api下
2、支持从外部注入参数 



问题收集：

* 问：刚才那个点重点说了多个线程，而完全没有强调非阻塞，
所有我才有一问好吧，如果讲过的所有点都必须百度过才懂，
课又有多大意义
答：只是为告诉大家有这个特性存在，多线程提升性能（高吞吐量的情况才能体现）
    从框架内部解决了一些问题。


* 问：Spring和spring Boot的关系
答：Spring Boot是一个框架的框架，只要是在Spring生态之内，它真的能够实现零配置启动
    集成的工具，框架（自己内部就实现了Web容器）
	http://start.spring.io/


* 问：老师说推荐用SpringBoot，不用WebFlux。
就是说Spring Boot Rest接口性能比Spring的好？
答：不是这个意思，没有性能区别，只有方便与不方便的区别


* 问：SpringBoot有什么优势呢 面试会问的
答：以上可以回答此问题

* 问：SpringBoot不是方便创建工程吗？和Spring WebFlux关系大吗？
答：以上可以回答此问题

* 问：怎么从官方文档之类的知道Spring需要哪些jar包？
答：spring.io 官网上，啥都有
    例如：https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html


* 问：AOP和Aspect的关系
答：asm是一个Java字节码操纵框架，它能被用来动态生成类或者增强既有类的功能。
asm --> cglib(替代JDK Proxy) -->  aspect(作为SpringAOP底层支持)  --> AOP


* 问：MessageConverter 和Mono的作用相同，有没有区别和优劣
答：无所谓优势与劣势之说，无非就是应用环境不一样，在发展中进步
    MessageConverter 旧版本的实现方式，手动
	Mono 是新版的实现方式，使用更加方便，自动
	
	都是针对：Response 输出而进行处理 resp.getWrite("")
	最底层实际都是对字符串的输出格式进行处理而已

	
* 问：Spring Bean IOC 容器为什么使用ConcurrentHashMap来存储，是由哪些原因导致
答：Spring的IOC容器包括很多种，最终都是会调用到DefaultListableBeanFactory(只实现了部分功能)
    XML
	Annotation
	Proxy
	Web
	最终的容器会归总成一个，是不是有可能重复操作？（删、改、覆盖，取不会影响）


* 问：Spring事务，在Service方法中必须要抛异常，事务才会回滚么？
答：当然，必须要抛异常，没有异常就认为没有出问题,也就不涉及到数据回滚。


* 问：能再详细说下Spring Web Flux的异步么？多线程就等于异步
答：是异步的。但是多线程不一定就是异步，但是在Java异步必须由多线程来实现。



