---
title: "[ Kubernetes ] 3-1.深入Service"
date: 2020-09-18T15:36:27+08:00
lastmod: 2020-09-18T15:36:27+08:00
keywords: ['Kubernetes']
description: ""
tags: ['k8s','容器']
categories: ['K8s']
author: ""
---
# 深入Service
>Service是Kubernetes最为核心的概念。Service可以为一组具有相同功能的容器应用提供一个统一的入口地址，并且将请求负载分发至后端的各个容器应用上。

#### 1.Service参数定义

```yaml
# 必填，版本号
apiVersion: v1
# 必填
kind: Service
# 必填，元数据
metadata:
    # 必填，Service名称(符合RFC 1035规范)
    name: string
    # 必填，命名空间(默认default)
    namespace: string
    # 自定义标签属性列表
    labels:
      - name: string
    # 自定义注解属性列表
    annotations:
      - name: string
# 必填，配置内容详细描述  
spec:
    # 必填，LabelSelector配置，将选择具有特定Label标签的Pod对象作为管理对象
    selector: []
    # 必填，可选值[ClusterIP | NodePort | LoadBalancer]
    #
    # ClusterIP:虚拟服务IP地址，该地址用于Kubernetes集群内部Pod对象访问，
    #在Node节点上Kube-proxy通过设置的Iptables规则进行转发
    #
    # NodePort:使用宿主机端口，使能够访问各Node的外部客户端通过Node的
    #IP地址和端口号即可访问到应用。
    #
    # LoadBalancer:使用外接负载均衡器完成到服务的负载分发，需要
    #spec.status.loadBalaner字段指定外部负载均衡器的IP地址，并同时定义
    #nodePort和clusterIP，用于公有云环境。
    type: string
    # 虚拟服务IP地址：当type为ClusterIP时，如果不指定将自动进行分配；也可手动指定。
    #当type为LoadBalancer时，必须指定
    clusterIP: string
    # 是否支持Session，可选值为ClientIP，默认值为空。
    #ClientIP表示将同一个客户端(有客户端IP地址决定)的访问请求都转发到同一个后端
    #Pod对象
    sessionAffinity: string
    # Service需要暴露的端口列表
    ports:
      - name: string
        # 端口协议：TCP/UDP(默认TCP)
        protocol: string
        # 服务监听端口号
        port: int
        # 需要转发到后端Pod对象的端口号
        targetPort: int
        # type为NodePort时，指定映射到物理机的端口号
        nodePort: int
    # type为LoadBalancer时，设置外部负载均衡器的地址，用于公有云环境。
    status:
        # 外部负载均衡器
        loadBalancer: 
            ingress:
                ip: string
                hostname: string
```