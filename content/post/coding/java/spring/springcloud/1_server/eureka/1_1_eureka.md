# SpringCloudEureka注册中心
## 1.基础注解
   > @EnableEurekaServer:一般用在SpringBoot的启动类上,直接声明当前Project为注册与发现中心。

   > @EnableEurekaClient:一般用在SpringBoot的启动类上，表明当前Project是一个EurekaClient。
   需要将其本身连接并发送基本信息至注册中心（这一过程即发现注册）
   
## 2.配置及用法
所有Eureka服务都被称作实例，但其角色有所不同，可将其分为两类**EurekaServer**与**EurekaClient**。EurekaClient也将有两种不同的角色出现：**Application Provider**与**Application Consumer**。
+ EurekaInstance
    + EurekaServer：即为发现注册中心(同时EurekaServer也将是一个特殊的EurekaClient)
    + EurekaClient：除发现注册中心之外的所有Eureka实例
        + ApplicationProvider：服务提供者
        + ApplicationConsumer：服务调用者

```yaml
### 以下一段内容将在EurekaInstanceProject中必须出现
spring:
  application:
    name: icc-transfer
eureka:
  instance:
    # 声明当前实例的主机名称，用于发现注册
    hostname: cloud-eureka-peer1
    # 心跳频率，默认值30s
    lease-renewal-interval-in-seconds: 30
    # 无心跳超时频率，默认值90s
    lease-expiration-duration-in-seconds: 90 
  # 以下内容将作为EurekaClient必须配置
  # 如果配置一个高可用的EurekaServer，也许进行此配置，EurekaServer将作为EurekaClient与其他节点进行数据同步
  client:
    serviceUrl:
      # 声明发现注册中心(EurekaServer)的地址
      defaultZone: http://cloud-eureka-peer:8080/eureka/,http://cloud-eureka-peer:8080/eureka/
    # 当前实例是否向EurekaServer注册自己，默认值为true即注册此实例
    register-with-eureka: true
    # 当前实例是否向EurekaServer服务器获取所有实例的注册信息表,默认值true
    fetch-registry: true
    # 拉取EurekaServer中EurekaInstance注册信息表频率,默认值为30s
    register-fetch-interval-seconds: 30
    
```