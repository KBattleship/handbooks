---
title: "[ Java ] 4-2.JUC之Synchronized"
date: 2021-01-10T11:10:47+08:00
lastmod: 2021-01-10T11:10:47+08:00
keywords: ['JUC']
description: ""
tags: ['java','JUC','Lock']
categories: ['Java']
author: ""
---
# 4-3.Synchronized关键字
## 一、 Synchronized的作用范围：适用于**方法**以及**代码块**中。

| 类型 | 具体类型 | 作用对象 | 代码示例|
|:-----:|----|:----:|----|
|方法|实例方法|类的实例对象|`public synchronized void method(){`<br>`}`
|方法|静态方法|类对象|`public static synchronized void method(){`<br>`}`
|代码块|实例对象|类的实例对象|`synchronized(this){`<br>`//同步代码块，锁住的是此类的实例对象;`<br>`}`
|代码块|Class对象|类对象|`synchronized(SynchronizedDemo.class){`<br>`//锁住的是此类的实例对象;`<br>`}`
|代码块|任意实例对象|任意实例对象|`String demo='synchronized';`<br>`synchronized(demo){`<br>`//锁住的为demo对象;`<br>`}`
`Tips: 如果Synchronized锁作用于`**类对象**`,不管多少个new实例对象,它们终究属于此类,都会被锁住,即线程之间保证同步关系.`


## 二、Synchronized锁原理分析
### **`当一个线程访问同步代码块时，它首先是需要得到锁才能执行同步代码，当退出或者抛出异常时必须要释放锁。`**

### 1.通过javap命令查看生成的字节码文件进行分析Synchronized锁的实现:
```java
public class SynchronizedTest {
    public synchronized void demo(){

    }

    public void demoTwo(){
        synchronized (this){

        }
    }
}
```
将上述代码片段进行编译，使用<b>`javap -v  SynchronizedTest.class`</b>命令进行详细信息查看（摘录部分信息如下）:
```java
{
 public SynchronizedTest();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1    // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 1: 0

  public synchronized void demo();
    descriptor: ()V
    flags: ACC_PUBLIC, ACC_SYNCHRONIZED
    Code:
      stack=0, locals=1, args_size=1
         0: return
      LineNumberTable:
        line 4: 0

  public void demoTwo();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=3, args_size=1
         0: aload_0
         1: dup
         2: astore_1
         3: monitorenter  //监视器进入，获取锁
         4: aload_1
         5: monitorexit   //监视器退出，释放锁
         6: goto          14
         9: astore_2
        10: aload_1
        11: monitorexit
        12: aload_2
        13: athrow
        14: return
}
```

### 2.从以上信息中可以看出：

- Synchronized锁作用于<b>`代码块`</b>时是通过<b>`monitorenter`</b>和<b>`monitorexit`</b>指令进行实现的，<b>`Monitor`</b>对象是同步的基本实现单元:
    <br>

    `
       ①.monitorenter指令插入在同步代码段开始的位置,monitorexit指令插入在同步代码段结束的位置。JVM需要保证每一个monitorenter与monitorexit都是一一对应的关系。
    `
    <br>

    `
    ②.任何对象都会存在一个对应的monitor,当这个monitor被占有,将进入锁定状态（即获取锁）使Synchronized锁进行同步，当线程获取monitor后才能继续往下执行，否则就只能等待(获取monitor的过程是互斥的，即同一时刻只有一个线程能够获取到monitor)。
    `
- Synchronized锁作用于<b>`方法`</b>时是通过在方法修饰符上的<b>`ACC_SYNCHRONIZED`</b>标志实现的:
    <br>
    `
    Synchronized作用于方法时，会被编译成普通方法的调用和返回指令，在VM字节码层并没有任何特别的指令来实现被synchronized修饰的方法，但是在Class文件的方法表中将该方法的access_flags字段中的synchronized标志位置1,表示该方法是同步方法并使用调用该方法的对象或该方法所属的Class在JVM的内部对象表示Klass做为锁对象。
    `

## 三、关于Monitor和对象头
### 1.聊聊Monitor
Monitor（内部锁），我们可以理解成为是一个同步工具,也可以描述成一种同步机制。与一切皆对象一样，所有的Java对象是天生的Monitor，每一个Java对象都有成为Monitor的潜质，因为在Java的设计中 ，每一个Java对象自打娘胎里出来就带了一把看不见的锁，它叫做内部锁或者Monitor锁。

- 在Java6版本以前，Monitor的实现完全是依赖于操作系统内部的互斥锁,犹豫需要进行用户态到系统内核态的切换，所以同步操作是一个无差别的重量级操作。

- 但是在目前版本的JDK中，JVM进行了调整，提供有三种不同的Monitor实现(即常见的三类锁):**偏斜锁**(Biased Locking)、**轻量级锁**和**重量级锁**。

## 锁升级
1、当没有被当做锁的时候，这就是个普通对象，锁标志位为01，是否偏向锁为0

2、当对象被当做同步锁时，一个线程A抢到锁时，锁标志位依然是01，是否偏向锁为1，前23位记录A线程的线程ID，此时锁升级为偏向锁

3、当线程A再次试图来获得锁时，JVM发现同步锁对象的标志位是01，是否偏向锁是1，也就是偏向状态，Mark Word中记录的线程id就是线程A自己的id，表示线程A已经获得了这个偏向锁，可以执行同步锁的代码，这也是偏向锁的意义

4、当一个线程B尝试获取锁，JVM发现当前的锁处于偏向状态，并且现场ID不是B线程的ID，那么线程B会先用CAS将线程id改为自己的，这里是有可能成功的，因为A线程一般不会释放偏向锁。如果失败，则执行5

5、偏向锁抢锁失败，则说明当前锁存在一定的竞争，偏向锁就升级为轻量级锁。JVM会在当前线程的现场栈中开辟一块单独的空间，里面保存指向对象锁Mark Word的指针，同时在对象锁MarkWord中保存指向这片空间的指针。上面的保存都是CAS操作，如果竞争成功，代表线程B抢到了锁，可以执行同步代码。如果抢锁失败，则继续执行6

6、轻量级锁抢锁失败，则JVM会使用自旋锁，自旋锁并非是一个锁，则是一个循环操作，不断的尝试获取锁。从JDK1.7开始，自旋锁默认开启，自旋次数由JVM决定。如果抢锁成功，则执行同步代码；如果抢锁失败，则执行7

7、自旋锁重试之后仍然未抢到锁，同步锁会升级至重量级锁，锁标志位改为10，在这个状态下，未抢到锁的线程都会被阻塞，由Monitor来管理，并会有线程的park与unpark，因为这个存在用户态和内核态的转换，比较消耗资源，故名重量级锁


## threadlocal的原理和应用
+ 原理
  + 线程中创建副本，访问自己内部的副本变量，内部实现是其内部类名叫ThreadLocalMap的成员变量threadLocals，key为本身，value为实际存值的变量副本

+ 应用
  + 用来解决数据库连接，存放connection对象，不同线程存放各自session；
  + 解决simpleDateFormat线程安全问题；
  + 会出现内存泄漏，显式remove..不要与线程池配合，因为worker往往是不会退出的；

## threadlocal内存泄漏问题
+ 如果是强引用，设置threadlocal=null，但是key的引用依然指向ThreadLocal对象，所以会有内存泄漏，而使用弱引用则不会； 
+ 但是还是会有内存泄漏存在，ThreadLocal被回收，key的值变成null，导致整个value再也无法被访问到；
+  解决办法：在使用结束时，调用ThreadLocal.remove来释放其value的引用；