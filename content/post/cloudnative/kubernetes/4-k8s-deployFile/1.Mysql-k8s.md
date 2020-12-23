# 4-1.K8s构建高可用Mysql集群
## 一、MySQL高可用架构图
### 1.主从复制+读写分离
> 此方案更适用于数据库读数据的场景(针对数据强一致性非严格的情况)，毕竟Replication存在一定的时延。
> 通过快速扩容Slave节点提高MySQL集群读取数据能力，不用过度依赖于Master节点。

![K8s之MySQL高可用架构图](media/Kubernetes-Mysql-HA1.png)


## 实现过程
### ①.为其提供PV存储盘