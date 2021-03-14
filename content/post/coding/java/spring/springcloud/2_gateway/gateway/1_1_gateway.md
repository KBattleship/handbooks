# GatewayCloud网关服务
## 1.项目配置说明
### 1.1 关于Project Yaml配置文件
```yaml
spring:
  cloud:
    gateway:
      routes:
      # 自定义路由ID，要保持唯一
      - id: device
        # 目标服务地址(lb标识集群)
        uri: lb://cloud-service-media
        # 路由条件，Predicate接受一个输入参数，返回一个bool值。通过多种默认方法将Predicates组合成一个复杂路由逻辑
        # Predicate函数源于Java8，具体使用可参考Java源码进一步了解
        # 当匹配规则存在多种情况时，一个请求满足多个路由条件是请求将被第一个路由转发
        predicates:
        # 通过请求路径匹配
        - Path=/device
        # 通过时间匹配
        - After=2020-04-20T06:06:06+08:00[Asia/Shanghai]
        # 通过Cookie匹配
        - Cookie=cloudp.cc,cheneyin
        # 通过Header匹配
        - Header=X-Request-Id, \d+
        # 通过Host匹配
        - Host=**.cloudp.cc
        # 通过请求匹配
        - Method=GET
        # 通过请求方式匹配
        - Query=deviceMac
        # 通过请求IP匹配
        - RemoteAddr=192.168.1.1/24
```