---
title: "Etcd基础入门(2)"
date: 2020-04-30
lastmod: 2020-04-30
menu: "etcd"
weight: 50
categories: ['K8s']
tags: ['etcd','k8s','分布式']
comment: true

---
# 1.Etcd 与 Zookeeper 对比
+ 一致性协议:配置共享&服务发现组件的核心基础。
  + Zookeeper采用ZAB协议(一种类Paxos协议)实现一致性
  + Etcd采用Raft协议，相比Paxos协议更容易理解，工程化。
+ API接口: 包含有两个版本V2、V3
  + V2: 提供HTTP+Json方式调用
  + V3: 提供grpc方式调用
+ 性能
  + 官方测试数据显示：10000+/s写入(优于Zookeeper性能)
+ 安全
  + Etcd支持TSL(权限控制优于Zookeeper)

# 2.Etcd源码结构
```shell
(base) {11:45}~/etcd:master ✗ ➭ tree -d -L 1 .
.
├── Documentation           # 项目文档
├── auth                    # 认证授权
├── client                  # 客户端相关(v2)通过HTTP+Json方式与服务端交互
├── clientv3                # 客户端相关(v3)通过grpc方式与服务端交互
├── contrib                 # (待验证)
├── default.etcd            # 已编译完成的etcd
├── embed                   # 封装的etcd函数
├── etcdctl                 # etcd操作命令，命令行客户端
├── etcdmain                # main函数入口这里
├── etcdserver              # 服务端相关功能
├── functional              # 目测验证功能测试套件
├── hack                    # 开发者相关
├── integration             # (待验证)
├── lease                   # 实现etcd租约
├── logos                   # 日志相关
├── mvcc                    # V3版本存储(底层采用BoltDB实现持久化)
├── pkg                     # 通用依赖库
├── proxy                   # 代理相关Http、Https、Socks
├── raft                    # raft一致性协议实现
├── scripts                 # 各类脚本
├── security                # 安全相关
├── tests                   # (待验证)
├── tools                   # 工具
├── vendor                  # go vendor依赖环境
├── version                 # 版本信息
└── wal                     # Write-Ahead-Log实现
```