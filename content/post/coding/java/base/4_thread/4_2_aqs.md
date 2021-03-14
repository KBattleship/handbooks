---
title: "[ Java ] 4-2.JUC之AQS"
date: 2021-01-10T11:10:47+08:00
lastmod: 2021-01-10T11:10:47+08:00
keywords: ['JUC']
description: ""
tags: ['java','JUC','Lock']
categories: ['Java']
author: ""
---
# JUC之AQS

## 1.基础知识

### 1_1. AQS原理
`AQS(AbstractQueuedSynchronizer)`,是`java.util.concurrent.locks`下的一个普通类。用作构建 **锁** 和 **同步器** 框架。例如基于AQS构建的有：**ReentrantLock**, **Semaphore**, **ReentrantReadWriteLock**, **SynchronousQueue**, **FutureTask** 等等。

**核心思想：**
    
1. 如果被请求的共享资源处于 **空闲状态** ，则将当前请求资源的线程设置为 **有效的工作线程** ，并且将共享资源设置为 **锁定状态**。
2. 如果被请求的共享资源处于 **占用状态** ，则需要一套 **线程阻塞等待** 以及 **被唤醒时锁分配** 的流程机制。 `AQS` 通过 **CLH** *(Craig, Landin, Hagersten)* 队列锁实现：**将暂时获取不到锁的线程加入至队列中**。
3. AQS使用一个 **int** 成员变量来标识同步状态，通过内置的 **FIFO** 队列完成获取资源线程的排队工作，使用 **CAS(compare and swap)** 对改状态进行原子操作实现对其值的更改。

```java
// 共享变量，使用volatile修饰符保证线程可见性
private volatile int state;

/** 状态信息通过protected修饰符修饰 getState(), setState(), compareAndSetState()进行操作 */

// 返回同步状态的当前值
protected final int getState(){
    return state;
}

// 设置同步状态当前值
protected final void setState(int newState){
    this.state = newState;
}

// 原子操作(CAS操作)将同步状态值设置为给定值update，如果当前同步状态值等于expect(期望值)
protected final boolean compareAndSetState(int expect, int update){
    return unsafe.compareAndSwapInt(this, stateOffset, expect, update);
}
```

### 1_2. AQS对资源的共享方式
+ Exclusive(独占)

    
+ Share(共享)
## 2.相关组件