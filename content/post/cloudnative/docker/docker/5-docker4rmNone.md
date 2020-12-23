---
title: "5.Docker-本地构建none包处理"
date: 2020-08-22T15:36:27+08:00
lastmod: 2020-08-22T15:36:27+08:00
keywords: ['Docker']
description: ""
tags: ['docker','k8s','容器']
categories: ['K8s']
author: ""
---
# Docker-本地构建none包处理

### 踩坑①.打包构建Dockerfile镜像
`每次本地打包构建Dockerfile镜像，如果更新镜像版本号会出现none的镜像在仓库中`
```shell
# 停掉none相关的镜像进程占用
docker rm $(docker ps -a | grep "Exited" | awk '{print $1 }')

# 递归依次从仓库移除这些镜像
docker rmi $(docker images | grep "^<none>" | awk "{print $3}")

# 或者，使用一下命令进行移除
docker image prune
# (此命令用于删除未使用的映像)
# docker image prune [options]
#   -- options可选值：
#       -a  显示所有映像(默认隐藏中间映像)
#       -f  不提示确认，强制直接执行删除
```