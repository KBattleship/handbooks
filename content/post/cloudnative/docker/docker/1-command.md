---
title: "1.Docker命令"
date: 2020-08-22T15:36:27+08:00
lastmod: 2020-08-22T15:36:27+08:00
keywords: ['Docker']
description: ""
tags: ['docker','k8s','容器']
categories: ['K8s']
author: ""
---
# Docker命令
> docker [option] command 

+ **option**
    + **--config string:** 客户端配置文件的位置
    + **--context string[-c]:** 用于连接到守护程序的上下文的名称
    + **--debug[-D]:** 调试模式
    + **--host list[-H]:** 要连接的守护程序套接字
    + **--log-level string[-l]:** 日志等级[ debug | info | warn | error | fatal ]默认为*info*
    + **--tls:** 使用加密模式
    + **--tlscacert string:** 签名证书文件路径
    + **--tlscert string:** 密钥文件路径
    + **--tlskey string:** key文件路径
    + **--tlsverify:** 使用加密并验证远程连接
    + **--version[-v]:** 版本信息

    
+ **Management Commands**(管理命令)
    + **builder:** 管理构建
    + **config:** 管理Docker配置
    + **container:** 管理容器
    + **context:** 管理镜像构建上下文
    + **image:** 管理镜像
    + **network:** 管理网络
    + **node:** 管理Swarm节点
    + **plugin:** 管理插件
    + **secret:** 管理Docker secrets
    + **service:** 管理服务
    + **stack:** 管理Docker stacks
    + **swarm:** 管理Swarm集群
    + **system:** 查看系统信息
    + **trust:** 管理对Docker映像的信任
    + **volume:** 管理卷

    
+ **Commands**(命令)
    + **attach:**  将本地标准输入，输出和错误流附加到正在运行的容器
    + **build:**  从Dockerfile构建镜像
    + **commit:**  根据容器的更改创建新镜像
    + **cp:**  在容器和本地文件系统之间复制文件/文件夹
    + **create:**  创建一个新的容器
    + **deploy:**  部署新堆栈或更新现有堆栈
    + **diff:**  检查容器文件系统上文件或目录的更改
    + **events:**  从服务器获取实时事件
    + **exec:**  在正在运行的容器中运行命令
    + **export:**  将容器的文件系统导出为tar存档
    + **history:**  显示镜像的历史记录
    + **images:**  显示镜像列表
    + **import:**  从tarball导入内容以创建文件系统映像
    + **info:**  显示系统范围的信息
    + **inspect:**  返回有关Docker对象的低级信息
    + **kill:**  杀死一个或多个正在运行的容器
    + **load:**  从tar存档或STDIN加载镜像
    + **login:**  登录Docker镜像仓库
    + **logout:**  退出Docker镜像仓库
    + **logs:**  提取容器的日志
    + **pause:**  暂停一个或多个容器中的所有进程
    + **port:**  列出端口映射或容器的特定映射
    + **ps:**  显示所有容器
    + **pull:**  从镜像仓库中提取镜像或存储库
    + **push:**  提交镜像或存储库到镜像仓库
    + **rename:**  为容器重新命名
    + **restart:**  重启一个或多个容器
    + **rm:**  移除一个或者多个容器
    + **rmi:**  移除一个或者多个镜像
    + **run:**  在新容器中执行命令
    + **save:**  保存一个或多个镜像到tar存档（默认情况下流式传输到STDOUT）
    + **search:**  从Docker Hub中搜索镜像
    + **start:**  运行一个或者多个停止状态的容器
    + **stats:**   显示实时的容器资源使用情况统计流
    + **stop:**  停止一个或者多个运行状态的容器
    + **tag:**  创建一个引用了SOURCE_IMAGE的标签TARGET_IMAGE
    + **top:**  显示某个容器的运行进程
    + **unpause:**  取消暂停一个或多个容器中的所有进程
    + **update:**  更新一个或多个容器的配置
    + **version:**  显示docker的版本信息
    + **wait:**  阻塞一个或多个容器直到停止，然后打印其退出代码