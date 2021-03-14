---
title: "[ 设计模式 ] 6.原型模式"
date: 2019-07-16T23:36:27+08:00
lastmod: 2019-07-16T23:36:27+08:00
keywords: ['design_pattern']
description: ""
tags: ['design_pattern','java']
categories: ['java']
author: ""
---
# 原型模式
`原型模式`：通过复制生成实例。
#### ①.角色分配
+ **Prototype(原型)**

    负责***定义***用于复制现有实例来生成新实例的方法。
+ **ConcretePrototype(具体原型)**

    负责***实现***现有实例并生成新实例的方法。
+ **Client(使用者)**

    负责***使用***复制实例的方法生成的新实例。
    
#### ②.UML图
![原型模式-简单形式](media/15776179234376.jpg)
![原型模式-登记方式](media/15776194782980.jpg)


#### ③.代码清单
+ **简单形式**

定义抽象接口
```java
public interface Prototype extends Cloneable {
    Object clone();
}
```
具体实现原型拷贝
```java
public class ConcretePrototype implements Prototype {
    public ConcretePrototype(){
        System.out.println("Prototype Design Patterns");
    }
    @Override
    public Object clone() {
        ConcretePrototype concretePrototype=null;
        try {
            concretePrototype=(ConcretePrototype)super.clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
        return concretePrototype;
    }
}
```
原型实例创建
```java
public static void main(String[] args) {
        ConcretePrototype concretePrototype1=new ConcretePrototype();
        ConcretePrototype concretePrototype2=(ConcretePrototype) concretePrototype1.clone();
        System.out.println(concretePrototype1.hashCode());
        System.out.println(concretePrototype2.hashCode());
}
```
+ **登记形式**

抽象原型接口
```java
public interface Prototype {
    Object clone();
    String getName();
    void setName(String name);
}
```

具体实现原型接口方法
```java
public class ConcretePrototype implements Prototype {
    private String name;

    @Override
    public Object clone() {
        ConcretePrototype concretePrototype = new ConcretePrototype();
        concretePrototype.setName(this.name);
        return concretePrototype;
    }

    @Override
    public String getName() {
        return this.name;
    }

    @Override
    public void setName(String name) {
        this.name = name;
    }
}
```
原型登记管理器
```java
public class PrototypeManager {
    private static Map<String, Prototype> map = new HashMap<>();

    private PrototypeManager() {
    }

    public synchronized static void setPrototype(String prototypeId, Prototype prototype) {
        map.put(prototypeId, prototype);
    }

    public synchronized static void removePrototype(String prototypeId) {
        map.remove(prototypeId);
    }

    public synchronized static Prototype getPrototype(String prototypeId) throws Exception {
        Prototype prototype = map.get(prototypeId);
        if (prototype == null) {
            throw new Exception("未找到" + prototypeId + "所对应的Prototype");
        }
        return prototype;
    }
}

```

客户端调用
```java
public static void main(String[] args) {
    Prototype prototype=new ConcretePrototype();
    System.out.println(prototype.hashCode());

    PrototypeManager.setPrototype("one",prototype);
    Prototype prototype1=(Prototype) PrototypeManager.getPrototype("one").clone();
    System.out.println(prototype1.hashCode());

    Prototype prototype2=new ConcretePrototype();
    System.out.println(prototype2.hashCode());
    PrototypeManager.setPrototype("one",prototype2);

    Prototype prototype3=(Prototype) PrototypeManager.getPrototype("one").clone();
    System.out.println(prototype3.hashCode());
    prototype3.setName("registration type");
    System.out.println(prototype3.hashCode());

    PrototypeManager.removePrototype("one");
    Prototype prototype4=(Prototype) PrototypeManager.getPrototype("one").clone();
    System.out.println(prototype4.hashCode());
}
```

#### ④.思路拓展
+ 为什么要使用原型模式？

```shell
1.对象种类繁多，无法将他们整合至一个类中
2.难以根据类生成实例
3.想解耦框架与生成的实例
```
