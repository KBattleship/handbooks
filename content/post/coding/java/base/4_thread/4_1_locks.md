---
title: "[ Java ] 4-1.关于锁"
date: 2020-12-27T21:10:47+08:00
lastmod: 2020-12-27T21:10:47+08:00
keywords: ['jvm']
description: ""
tags: ['java','jvm','command']
categories: ['Java']
author: ""
---
# 关于锁

## 1.自旋锁（SpinLock）
    
> 当一个线程在获取锁的时候，如果锁已经被其他线程获取，那么该线程将循环等待，然后不断的判断锁是否已经被其他线程释放，可以成功获取到锁，直到可以获取到锁才退出循环。

无论 **自旋锁** ，还是 **互斥锁** ， 在任何时刻，都最多只有一个执行单元获得锁。

## 2.可重复锁/不可重复锁

> **可重复锁：** 广义上定义为，可重复可递归调用的锁，在外层使用之后，在内层依旧可以使用，并且不会发生死锁(前提保证是同一个**Obj**或**Class**)。例如：`ReentrantLock` 和 `synchronized` 是可重复锁。

**Coding Demo One：**
```java
synchronized void setA() throw Exception{
    Thread.sleep(1000);
    // 如果不是可重入锁，setB()将可能不会被当前线程执行，造成死锁。
    setB();
}

synchronized void setB() throw Exception{
    Thread.sleep(1000);
}
```


> **不可重复锁：** 与 **可重入锁**刚好相反，不可重复进行递归调用，递归调用将造成死锁。


**Coding Demo One：**
```java
// 通过 自旋锁 实现一个不可重入锁

public class UnreentrantLock{

    private AtomicReference<Thread> owner = new AtomicReference<>();

    public void lock(){
        Thread curr = Thread.currentThread();
        // 按照 自旋锁 原理进行实现
        for(;;){
            if(!owner.compareAndSet(null,curr)){
                return;
            }
        }
    }

    public void unlock(){
        Thread curr = Thread.currentThread();
        owner.compareAndSet(curr,null);
    }
}
```

**Coding Demo Two：**
```java
// ReentrantLock中可重入锁实现；
// 非公平锁的获取方式。
final boolean nonfairTryAcquire(int acquires){
    final Thread curr = Thread.currentThread();
    int c = getState();
    if (c == 0){
        if (compareAndSetState(0,acquires)){
            setExclusiveOwnerThread(curr);
            return true;
        }
    }else if(curr == getExclusiveOwnerThread()){
        int next = c + acquires;
        if(next < 0){
            throw new Error("Maximum lock count exceeded.");
        }
        setState(next);
        return true;
    }
    return false;
}
```

## 3.公平锁


## 4.共享锁

## 5.分段锁

## 