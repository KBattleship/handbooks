---
title: "[ Kubernetes ] 1-5.K8s之Helm包管理工具"
date: 2020-09-12T15:36:27+08:00
lastmod: 2020-09-12T15:36:27+08:00
keywords: ['Kubernetes']
description: ""
tags: ['k8s','容器']
categories: ['K8s']
author: ""
---
# K8s之Helm包管理工具

> Helm 是 Deis 开发的一个用于 Kubernetes 应用的包管理工具，主要用来管理 Charts。有点类似于 Ubuntu 中的 APT 或 CentOS 中的 YUM。

## 一、安装
[Helm Release Link](https://github.com/helm/helm/releases)
### 1.OS-CentOS
当前使用版本为[Helm v3.1.2 linux](https://get.helm.sh/helm-v3.1.2-linux-amd64.tar.gz)
```shell
# 下载二进制可执行文件压缩包
wget -O /data/helm.tar.gz https://get.helm.sh/helm-v3.1.2-linux-amd64.tar.gz

# 解压
tar -xzvf /data/helm.tar.gz

# 移动helm二进制文件，方便全局访问
mv linux-amd64/helm /usr/local/bin/helm
```
### 2.OS-MacOS
#### ①.自动安装
`PS：操作系统已安装brew工具`
```shell
brew install helm
```
#### ②.手动安装
当前使用版本为[Helm v3.1.2 darwin](https://get.helm.sh/helm-v3.1.2-darwin-amd64.tar.gz)
```shell
# 下载二进制可执行文件压缩包
wget -O ~/helm.tar.gz https://get.helm.sh/helm-v3.1.2-darwin-amd64.tar.gz

# 解压缩
tar -xzvf ~/helm.tar.gz

# 移动helm二进制文件，方便全局访问
mv darwin-amd64/helm /usr/local/bin/helm
```
## 二、入门
### 1.调整helm源
```shell
# 查看源
helm repo list

# 设置国内镜像源(选用阿里云源)
helm repo add stable https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts
```
### 2.搜索应用
搜索Nginx-Ingress
```shell
helm search repo nginx-ingress
### 搜索结果如下 ###
# NAME                    CHART VERSION   APP VERSION     DESCRIPTION                                       
#stable/nginx-ingress    0.9.5           0.10.2          An nginx Ingress controller that uses ConfigMap...
#stable/nginx-lego       0.3.1                           Chart for nginx-ingress-controller and kube-lego 
```

### 3.安装应用
```shell
# 开启rbac权限，并通过externalIP方式进行工作
helm install --name nginx-ingress --set "rbac.create=true,controller.service.externalIPs[0]=192.168.100.211,controller.service.externalIPs[1]=192.168.100.212,controller.service.externalIPs[2]=192.168.100.213" stable/nginx-ingress
```
