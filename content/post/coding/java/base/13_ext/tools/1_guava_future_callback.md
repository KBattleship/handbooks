---
title: "[ Tools ] 1.关于Guava增强包异步回调"
date: 2020-06-30T21:10:47+08:00
lastmod: 2020-06-30T21:10:47+08:00
keywords: ['guava']
description: ""
tags: ['java','guava','java tools']
categories: ['Java']
author: ""
---
# 关于Guava增强包异步回调

### 1.概述
Guava针对Java异步回调做出以下两点增强功能：
+ 1.引入新接口**ListenableFuture**。此接口继承于Java的Future接口，可监控、获取非阻塞异步执行结果。
+ 2.引入新接口**FutureCallback**。此接口是新增独立接口，在异步任务完成后，根据异步结果，完成不同的回调事件，并处理异步结果。