---
title: "[ Interview ] 2.面试之Boss"
date: 2021-01-07T01:13:47+08:00
lastmod: 2021-01-07T01:13:47+08:00
keywords: ['interview']
description: ""
tags: ['interview','笔试','algorithm']
categories: ['Algorithm']
author: ""
---
# 面试之Boss

## 1.笔试

> 删除UserList中年龄大于20的User对象。

```Java
// User实体类
public class User{
    private Integer age;
    public Integet getAge(){
        return this.age;
    }
}
```
**解题思路：**

此题按照最简单的lambda方式进行解答(JDK8才支持)
```Java
public class Main{
    public static void remove(List<User> userList){
        // removeIf(Predicate<? super E> filter)
        userList.removeIf(x -> x != null && x.getAge() != null && x.getAge() > 20);
    }
}
```
> 从1000W个数中取出最小的10个数，并按照顺序打印。

**解题思路：**
 首先应该想到堆排序(Top K堆问题)，大根堆(前k小)或小根堆(后k大)。在Java中有已经实现的*PriorityQueue*，解决此问题将最为简单，复杂度为***O(NlogK)***。
 
 本题是求前`K`小，因此选用一个容量为`K`的大根堆，每次poll出最大的数，则堆中保留的则是前`K`项小。(谨记：需要不可用小根堆，小根堆需把全部元素入堆，时间复杂度为***O(NlogN)***,将不再是***O(NlogK)***)，Java中*PriorityQueue*默认为**小根堆**，需作出调整重写比较器。
```Java
public class MaxHeap {

    public static void main(String[] args) {
        int[] arr = new int[]{10,1,0,9,22,77,12,883,99983,2772,848,3663,2626,737,2772,8388,266,83,72,7272,83883,27727,263,840,2740,884};
        print(arr);
    }

    public static void print2(int[] arr) {
        int k = 10;
        int[] vec = new int[k];
        // 重写PriorityQueue为大根堆
        // PriorityQueue<Integer> queue = new PriorityQueue<>();默认为小根堆
        PriorityQueue<Integer> queue = new PriorityQueue<>((num1, num2) -> num2 - num1);
        for (int i = 0; i < k; ++i) {
            queue.offer(arr[i]);
        }
        for (int i = k; i < arr.length; ++i) {
            if (queue.peek() > arr[i]) {
                queue.poll();
                queue.offer(arr[i]);
            }
        }
        for (int i = 0; i < k; ++i) {
            vec[i] = queue.poll();
        }
        System.out.println(Arrays.toString(vec));
    }
}
```

> 3个线程A、B、C存在依赖关系，B依赖A执行结束，C依赖B执行结束，请设计实现。

**解题思路：**
主要考察多线程Thread对象的wait()、notify()以及线程间共享的信号量。
```Java
public class Main {
    public static void main(String[] args) {
        Lock lockA = new Lock();
        Lock lockB = new Lock();

        new A(lockA).start();
        new B(lockA, lockB).start();
        new C(lockB).start();
    }
}

class Lock{
    private int flag = 0;

    public int getFlag() {
        return flag;
    }

    public void setFlag(int flag) {
        this.flag = flag;
    }

}

class A extends Thread{
    private Lock lock;

    public A(Lock lock) {
        this.lock = lock;
    }

    @Override
    public void run() {
        synchronized (lock) {
            try {
                System.out.println("thread A starting ");
                if (lock.getFlag() > 0) {
                    lock.notify();
                } else {
                    lock.setFlag(1);
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}

class B extends Thread{
    private Lock lockA;
    private Lock lockB;

    public B(Lock lockA,Lock lockB) {
        this.lockA = lockA;
        this.lockB = lockB;
    }

    @Override
    public void run() {
        synchronized (lockA) {

            try {
                if (lockA.getFlag() == 0) {
                    lockA.setFlag(1);
                    lockA.wait();
                }
                System.out.println("Thread B starting");

                synchronized (lockB) {
                    if (lockB.getFlag() > 0) {
                        lockB.notify();
                    } else {
                        lockB.setFlag(1);
                    }
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}

class C extends Thread {
    private Lock lock;

    public C(Lock lock) {
        this.lock = lock;
    }

    @Override
    public void run() {
        synchronized (lock) {
            try {
                if (lock.getFlag() == 0) {
                    lock.setFlag(1);
                    lock.wait();
                }
                System.out.println("Thread C starting");
            } catch (Exception e){
                e.printStackTrace();
            }
        }
    }
}
```