---
title: "[ Java ] 2.4 HashMap虐杀，完败"
date: 2020-12-31T11:13:47+08:00
lastmod: 2020-12-31T11:13:47+08:00
keywords: ['hashMap']
description: ""
tags: ['java','hashMap','']
categories: ['Java']
author: ""
---

# HashMap虐杀，完败

> HashMap内部实现是一个 **桶数组** ，每个桶中存放着一个 **单链表的头结点** 。其中每个结点存储的是一个 **键值对整体**（Entry），HashMap采用 **拉链法** 解决哈希冲突。

### 1. HashMap的resize()流程
**源码解析**

```java
    // 扩容 hashTable 大小的方法
    final Node<K,V>[] resize() {
        // 扩容前 hashTable
        Node<K,V>[] oldTab = table;
        // 扩容前 HashTable 的容量
        int oldCap = (oldTab == null) ? 0 : oldTab.length;
        // 扩容前 扩容阈值
        int oldThr = threshold;
        int newCap, newThr = 0;
        // 扩容前HashTable不为null
        if (oldCap > 0) {
            // 如果扩容前容量已经大于HashMap最大容量阈值，将不再扩容，直接返回hashMap
            if (oldCap >= MAXIMUM_CAPACITY) {
                threshold = Integer.MAX_VALUE;
                return oldTab;
            }
            // 如果扩容一倍后容量 < 最大容量，并且 扩容前容量值 >= 初始化容量值 ，将 扩容阈值 也扩大一倍
            else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY &&
                     oldCap >= DEFAULT_INITIAL_CAPACITY)
                newThr = oldThr << 1; // double threshold
        }
        else if (oldThr > 0) // initial capacity was placed in threshold
            newCap = oldThr;
        else {               // zero initial threshold signifies using defaults
            // 如果 hashTable 为null 或者 长度 <= 0， 将重新初始化容量16、扩容阈值 12
            newCap = DEFAULT_INITIAL_CAPACITY;
            newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
        }
        if (newThr == 0) {
            // 计算新的扩容阈值
            float ft = (float)newCap * loadFactor;
            newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ?
                      (int)ft : Integer.MAX_VALUE);
        }
        threshold = newThr;
        @SuppressWarnings({"rawtypes","unchecked"})
        // 按照新的容量 创建新的hashTable
        Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
        table = newTab;
        // 把旧哈希表中的内容移动到新哈希表中
        if (oldTab != null) {
            for (int j = 0; j < oldCap; ++j) {
                Node<K,V> e;
                if ((e = oldTab[j]) != null) {
                    oldTab[j] = null;
                    if (e.next == null)
                        newTab[e.hash & (newCap - 1)] = e;
                    else if (e instanceof TreeNode)
                        ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                    else { // preserve order
                        Node<K,V> loHead = null, loTail = null;
                        Node<K,V> hiHead = null, hiTail = null;
                        Node<K,V> next;
                        do {
                            next = e.next;
                            if ((e.hash & oldCap) == 0) {
                                if (loTail == null)
                                    loHead = e;
                                else
                                    loTail.next = e;
                                loTail = e;
                            }
                            else {
                                if (hiTail == null)
                                    hiHead = e;
                                else
                                    hiTail.next = e;
                                hiTail = e;
                            }
                        } while ((e = next) != null);
                        if (loTail != null) {
                            loTail.next = null;
                            newTab[j] = loHead;
                        }
                        if (hiTail != null) {
                            hiTail.next = null;
                            newTab[j + oldCap] = hiHead;
                        }
                    }
                }
            }
        }
        return newTab;
    }
```

### 2. HashMap的put()流程

  `先根据key确定其在Hash表中的下标，找到对应的bucket，遍历链表(或者红黑树)进行插入操作。`

  + 先判断Hash表是否为null或者长度是否为0，是则进行一次扩容；
  + 根据 `key` 确定 `Hash表` 中的 **index**，在找到对应的 **bucket**，如果 **bucket** 上没有节点，直接新增一个节点；
  + 如果当前 **bucket** 上存在链表，且头节点可以匹配上，直接进行替换；
  + 如果当前 **bucket** 上是红黑树，则按照红黑树操作进行插入或替换；
  + 如果以上情况都不满足，将开始遍历链表，如果匹配到就进行替换；如果未匹配到将在链表尾部添加一个节点，同时会判断链表长度是否大于 **`8`** 、Hash表数组是否大于 **`64`**，同时满足这两个条件，将链表会转化为红黑树；如果链表长度仅大于 **8**，将会进行扩容；
  + 执行完成后，会判断 Map中 `K-V`对 数量是否大于 `threshold` ，是则进行扩容。

**源码解析**
```java
     final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
        // n : 当前tab容量
        // i : 根据 key 的 hash 值计算出的在存储数组上的下标
        Node<K,V>[] tab; Node<K,V> p; int n, i;
        if ((tab = table) == null || (n = tab.length) == 0)
            // 当 hashTable 为null 或者 长度为0时，进行扩容
            n = (tab = resize()).length;
        // 判断当前索引位置上是否存在节点
        if ((p = tab[i = (n - 1) & hash]) == null)
            // 不存在节点，直接在当前索引的位置创建一个新的节点
            tab[i] = newNode(hash, key, value, null);
        else {
            // 存在节点的情况
            Node<K,V> e; K k;
            // 如果 当前节点的key与待插入元素的key一直，直接进行替换
            if (p.hash == hash &&
                ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
            // 如果当前索引位置上是红黑树，按照红黑树操作进行替换或插入
            else if (p instanceof TreeNode)
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                // 剩下这种情况就是 当前索引位置上是链表的数据结构，进行链表上节点的遍历
                for (int binCount = 0; ; ++binCount) {
                    // 如果当前链表没有匹配到待插入元素key，在链表尾部新增一个节点
                    if ((e = p.next) == null) {
                        p.next = newNode(hash, key, value, null);
                        // 如果当前链表长度 > 8 ，将 链表 转化为 树
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            treeifyBin(tab, hash);
                        break;
                    }
                    // 在链表上匹配到与待插入元素key时，跳出循环
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    // 将指针指向下一个节点，继续循环
                    p = e;
                }
            }
            // e 不为 null 说明匹配到了待插入元素一样的key，此时应该进行替换，并将旧值进行返回
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                afterNodeAccess(e);
                return oldValue;
            }
        }
        ++modCount;
        // 判断当前HashMap中存储的K-V对数量是否超出 扩容阈值
        // threshold = DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        // 新插入的值，返回null
        return null;
    }
```
### 3. ConcurrentHashMap.get()是否线程安全，是否有加锁？
- 安全，读操作不受影响。
- 不加锁。
### 4. ConcurrentHashMap如何保证线程安全

+ 在JDK8以前，ConcurrentHashMap都是基于Segment分段锁来实现的，
+ 在JDK8以后，就换成synchronized和CAS这套实现机制了。
  1. 使用 **volatile** 保证当Node中的值变化时对于其他线程是 **可见的**
  2. 使用 **table数组的头结点** 作为synchronized的锁来 **保证写操作** 的安全
  3. 当头结点为 **null** 时，使用 **CAS操作** 来保证数据能正确的 **写入**
  + 和JDK1.8中的HashMap类似，对于hashCode相同的时候，在Node节点的数量少于8个时，这时的Node存储结构是链表形式，时间复杂度为O(N)，当Node节点的个数超过8个时，则会转换为红黑树，此时访问的时间复杂度为O(long(N))

### 5. HashMap、TreeMap、 LinkedHashMap区别

+ **HashMap：**
  
    + 常用的一个Map，根据键的 **HashCode** 值存储数据，同时根据 键 直接获取对应的值
    + 访问速度很快，通过遍历得到的数据是完全随机无需的。
    + 只允许一条记录的 **键** 为 Null，允许多条记录的 **值** 为 Null
    + HashMap不支持线程同步(即同一时刻多个线程同时写HashMap)，可能会导致数据不一致
    + HashMap如果支持线程同步可以通过 **Collections的synchronizedMap()** 实现，或者使用 **ConcurrentHashMap**
    + HashMap 与 HashTable 类似，HashTable 继承自 Dictionary 类。不同是
      
      + HashTable 不允许存在为Null的键值
      + 支持线程同步。(所以要比HashMap要慢)
+ **TreeMap：**

    + TreeMap是实现 **SortMap接口** ，它能把保存的记录根据键值进行排序。
    + 默认是按照键值升序进行排序，也可以指定排序比较器，使用Iterator进行排序，得到的记录是有序的。
+ **LinkedHashMap：**

    + 是`HashMap`的子类，保存了记录的插入顺序，在用Iterator遍历LinkedHashMap时，先得到的记录肯定是先插入的记录。
    + 遍历的情况下，要比HashMap慢，不过存在例外的情况：当HashMap容量很大时，实际数据较少，遍历时可能会比LinkedHashMap慢：

        `LinkedHashMap的遍历速度之和实际数据有关，与容量无关，但HashMap的遍历速度与容量相关。`

## List你使用过哪些
ArrayList和linkedList使用的最多，也最具代表性。

## 你知道vector和ArrayList和linkedList的区别嘛
ArrayList实现是一个数组，可变数组，默认初始化长度为10，也可以我们设置容量，但是没有设置的时候是默认的空数组，只有在第一步add的时候会进行扩容至10（重新创建了数组），后续扩容按照3/2的大小进行扩容，是线程不安全的，适用多读取，少插入的情况

linkedList是基于双向链表的实现，使用了尾插法的方式，内部维护了链表的长度，以及头节点和尾节点，所以获取长度不需要遍历。适合一些插入/删除频繁的情况。

Vector是线程安全的，实现方式和ArrayList相似，也是基于数组，但是方法上面都有synchronized关键词修饰。其扩容方式是原来的两倍。

## hashMap和hashTable和ConcurrentHashMap的区别
hashMap是map类型的一种最常用的数据结构，其底部实现是数组+链表（在1.8版本后变为了数组+链表/红黑树的方式），其key是可以为null的，默认hash值为0。扩容以2的幂等次（为什么。。。因为只有是2的幂等次的时候（n-1）&x==x%n，当然不一定只有一个原因）。是线程不安全的

hashTable的实现形式和hashMap差不多，它是线程安全的，是继承了Dictionary，也是key-value的模式，但是其key不能为null。

ConcurrentHashMap是JUC并发包的一种，在hashMap的基础上做了修改，因为hashmap其实是线程不安全的，那在并发情况下使用hashTable嘛，但是hashTable是全程加锁的，性能不好，所以采用分段的思想，把原本的一个数组分成默认16段，就可以最多容纳16个线程并发操作，16个段叫做Segment，是基于ReetrantLock来实现的

## 说说你了解的hashmap吧
hashMap是Map的结构，内部用了数组+链表的方式，在1.8后，当链表长度达到8的时候，会变成红黑树，这样子就可以把查询的复杂度变成O（nlogn）了，默认负载因子是0.75，为什么是0.75呢？

我们知道当负载因子太小，就很容易触发扩容，如果负载因子太大就容易出现碰撞。所以这个是空间和时间的一个均衡点，在1.8的hashmap介绍中，就有描述了，貌似是0.75的负载因子中，能让随机hash更加满足0.5的泊松分布。

除此之外，1.7的时候是头插法，1.8后就变成了尾插法，主要是为了解决rehash出现的死循环问题，而且1.7的时候是先扩容后插入，1.8则是先插入后扩容(为什么？正常来说，如果先插入，就有可能节点变为树化，那么是不是多做一次树转化，比1.7要多损耗，个人猜测，因为读写问题，因为hashmap并不是线程安全的，如果说是先扩容，后写入，那么在扩容期间，是访问不到新放入的值的，是不是不太合适，所以会先放入值，这样子在扩容期间，那个值是在的）。

1.7版本的时候用了9次扰动，5次异或，4次位移，减少hash冲突，但是1.8就只用了两次，觉得就足够了一次异或，一次位移。

## concurrentHashMap呢
concurrentHashMap是线程安全的map结构，它的核心思想是分段锁。在1.7版本的时候，内部维护了segment数组，默认是16个，segment中有一个table数组（相当于一个segmeng存放着一个hashmap。。。），segment继承了reentrantlock，使用了互斥锁，map的size其实就是segment数组的count和。而在1.8的时候做了一个大改版，废除了segment，采用了cas加synchronize方式来进行分段锁（还有自旋锁的保证），而且节点对象改用了Node不是之前的HashEntity。

Node可以支持链表和红黑树的转化，比如TreeBin就是继承了Node，这样子可以直接用instanceof来区分。1.8的put就很复杂来，会先计算出hash值，然后根据hash值选出Node数组的下标（默认数组是空的，所以一开始put的时候会初始化，指定负载因子是0.75，不可变），判断是否为空，如果为空，则用cas的操作来赋值首节点，如果失败，则因为自旋，会进入非空节点的逻辑，这个时候会用synchronize加锁头节点（保证整条链路锁定）这个时候还会进行二次判断，是否是同一个首节点，在分首节点到底是链表还是树结构，进行遍历判断。

## concurrentHashMap的扩容方式
1.7版本的concurrentHashMap是基于了segment的，segment内部维护了HashEntity数组，所以扩容是在这个基础上的，类比hashmap的扩容，

1.8版本的concurrentHashMap扩容方式比较复杂，利用了ForwardingNode,先会根据机器内核数来分配每个线程能分到的busket数，（最小是16），这样子可以做到多线程协助迁移，提升速度。然后根据自己分配的busket数来进行节点转移，如果为空，就放置ForwardingNode，代表已经迁移完成，如果是非空节点（判断是不是ForwardingNode，是就结束了），加锁，链路循环,进行迁移。

## hashMap的put方法的过程
判断key是否是null，如果是null对应的hash值就是0，获得hash值过后则进行扰动，（1.7是9次，5次异或，4次位移，1.8是2次），获取到的新hash值找出所在的index，（n-1）&hash，根据下标找到对应的Node/entity，然后遍历链表/红黑树，如果遇到hash值相同且equals相同，则覆盖值，如果不是则新增。如果节点数大于8了，则进行树化（1.8）。完成后，判断当前的长度是否大于阀值，是就扩容（1.7是先扩容在put）。

## 为什么修改hashcode方法要修改equals
都是map惹的祸，我们知道在map中判断是否是同一个对象的时候，会先判断hash值，在判断equals的，如果我们只是重写了hashcode，没有顺便修改equals，比如Intger，hashcode就是value值，如果我们不改写equals，而是用了Object的equals，那么就是判断两者指针是否一致了，那就会出现valueOf和new出来的对象会对于map而言是两个对象，那就是个问题了

## TreeMap了解嘛
TreeMap是Map中的一种很特殊的map，我们知道Map基本是无序的，但是TreeMap是会自动进行排序的，也就是一个有序Map(使用了红黑树来实现），如果设置了Comparator比较器，则会根据比较器来对比两者的大小，如果没有则key需要是Comparable的子类（代码中没有事先check，会直接抛出转化异常，有点坑啊）。

## LinkedHashMap了解嘛
LinkedHashMap是HashMap的一种特殊分支，是某种有序的hashMap，和TreeMap是不一样的概念，是用了HashMap+链表的方式来构造的，有两者有序模式：访问有序，插入顺序，插入顺序是一直存在的，因为是调用了hashMap的put方法，并没有重载，但是重载了newNode方法，在这个方法中，会把节点插入链表中，访问有序默认是关闭的，如果打开，则在每次get的时候都会把链表的节点移除掉，放到链表的最后面。这样子就是一个LRU的一种实现方式。
