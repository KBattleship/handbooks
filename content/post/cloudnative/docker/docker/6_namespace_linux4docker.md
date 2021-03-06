---
title: "[ Docker ] 6.关于命名空间(Linux Namespace)"
date: 2020-08-21T15:36:27+08:00
lastmod: 2020-08-21T15:36:27+08:00
keywords: ['Docker']
description: ""
tags: ['namespace','docker','k8s','容器']
categories: ['K8s']
author: ""
---
# 关于命名空间(Linux Namespace)

### 概念
> 1.Linux Namespace 是Kernel的一个功能，可以针对一系列的系统资源进行隔离。`例如：PID(process id)、UID(User id)、Network so on.`

> 2.就像chroot允许把当前目录变成根目录一样进行隔离。


> 3.Namespace进行隔离用户，当前用户将在特定的Namespace中具有root权限。但在真是物理机层面，此用户仍然是以UID运行的那个用户。

### Linux包含的Namespace类型
| Type | Params | Kernel|Effect Information|
|:----:|----|:----:|----|
|Mount|CLONE_NEWNS|2.4.19|隔离Namespace下的文件系统|
|UTS|CLONE_NEWUTS|2.6.19|用作隔离nodename和domainname|
|IPC|CLONE_NEWIPC|2.6.19|隔离System V IPC 和POSIX Message queues|
|PID|CLONE_NEWPID|2.6.24|针对进程ID进行隔离|
|Network|CLONE_NEWNET|2.6.29|用于隔离网络设备、IP地址端口等网络栈|
|User|CLONE_NEWUSER|3.8|用于隔离用户及用户组|

### [#Demo Coding](https://github.com/bungeerope/simple-docker/tree/master/src/simple/namespace)