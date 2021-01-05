---
title: "[ Docker ] 8.文件系统之UFS"
date: 2020-08-23T15:36:27+08:00
lastmod: 2020-08-23T15:36:27+08:00
keywords: ['Docker']
description: ""
tags: ['ufs','docker','k8s','容器']
categories: ['K8s']
author: ""
---
# 文件系统之UFS

## UFS
联合文件系统[***Union File System***]，把其他文件系统联合到一个联合挂载点的文件系统服务(适用于Linux、FreeBSD、NetBSD OS)。
> ***原理：*** 使用branch把不同文件系统的文件、目录「透明的」进行覆盖，形成一个单一一直的文件系统。branch具有要么**read-only**,要么**read-write**的特点。

> ***思想：*** **写时复制(copy-on-write)**,如果一个资源重复，但并未被修改，将不被立即创建出新的资源，直接为新旧实例提供共享。创建新资源将发生在第一次被修改写入时。该资源共享方式，可以明显降低未修改资源复制时的消耗，但同样的，也会在写入修改是增加部分开销。

+ **AFUS**(Advanced Multi-Layered Unification FileSystem)