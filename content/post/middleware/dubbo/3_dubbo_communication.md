---
title: "[ Dubbo ] 3. Dubbo通信原理"
date: 2020-12-25T15:36:27+08:00
lastmod: 2020-12-25T15:36:27+08:00
keywords: ['dubbo']
description: ""
tags: ['java','dubbo','分布式']
categories: ['Java']
author: ""
---
# Dubbo通信原理

## 1. Dubbo多线程通信原理
+ 获取DubboInvoker对象；
+ 将请求体信息封装在一个Request对象中，Request中会包括一个自增的id；
+ 然后将Request存到一个ConcurrentHashMap中（key=id，value= DefaultFuture）,将request数据写入Channel
+ Consumer Thread执行Defaultfuture#get()方法等待返回结果
+ 服务提供方创建多线程处理用户请求，并将放回结果封装在Response中（包括Request#id）将response写入Channel
+ 消费方从Channel中收到数据以后，解析出id，从Map中解析出DefaultFuture唤醒Consumer Thread，返回结果
+ DefaultFuture也会启动一个定时程序，检查在timeout内，结果是否返回，如果未返回，将DefaultFuture从map中移除，并抛出超时异常
