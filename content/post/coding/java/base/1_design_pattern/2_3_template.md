---
title: "[ 设计模式 ] 3.模板模式"
date: 2019-07-08T15:36:27+08:00
lastmod: 2019-07-08T15:36:27+08:00
keywords: ['design_pattern']
description: ""
tags: ['design_pattern','java']
categories: ['java']
author: ""
---
# 模板模式
<font style="color:red;"> **模板模式：** </font>`是带有模板功能的模式，组成模板的方法被定义在父类中，由于这些方法是抽像的，所以在父类代码中无法知道具体实现，唯一能知道的是父类在如何调用这些方法，具体的实现全由子类方法处理。`

#### ①.UML图
![模板模式UML](/image/docs_img/coding/java/media/1_2_3_template_uml.jpg)

#### ②.代码清单
+ AbstractDisplay类


```java
public abstract class AbstractDisplay {
    /* 模板类中的模板方法规定了实现的流程,但不做具体抽象方法的具体实现 */
    public void display() {
        open();
        print();
        close();
    }
    /* 定义流程中指定的抽象方法，具体细节交给子类去实现 */
    abstract void open();
    abstract void print();
    abstract void close();
}
```
+ CharDisplayTemplate类

```java
public class CharDisplayTemplate extends AbstractDisplay {

    private char aChar;
    public CharDisplayTemplate(char a) {
        this.aChar = a;
    }
    /* 具体实现父类模板类中抽象方法 */
    @Override
    void open() {
        System.out.println("Char Display open()");
    }
    @Override
    void print() {
        System.out.println("Char Display print():" + this.aChar);
    }
    @Override
    void close() {
        System.out.println("Char Display close()");
    }
}

```
+ StringDisplayTemplate类

```java 
public class StringDisplayTemplate extends AbstractDisplay {

    private String charString;
    public StringDisplayTemplate(String string) {
        this.charString = string;
    }
    /* 具体实现父类模板类中抽象方法 */
    @Override
    void open() {
        System.out.println("String Display open()");
    }
    @Override
    void print() {
        System.out.println(this.charString);
    }
    @Override
    void close() {
        System.out.println("String Display close()");
    }
}

```
#### ③.思路分析
+ 逻辑处理通用化

`由于在父类模板方法中编写了算法，无需在子类中在编写算法。当发现模板方法中存在Bug，仅需要改动模板方法即可解决问题。并非像传统编码方式上的修改所有ConcreteClass角色类的相关逻辑代码。`
+ 父、子类间的协作性

`子类实现父类抽象方法时，必须理解这些抽象方法被调用的时机、逻辑。如果看不到父类代码，不能了解到父类方法中所定义的相关逻辑，想要编写子类抽象方法实现代码是很困难的。`
+ 父、子类间的一致性

`通过父类类型定义子类实例变量，即时不使用instanceof指定子类的类型，程序依然可以正常运行。` ***(里氏替换原则[LSP],通用的继承原则。)***
#### ④.类的层次与抽象类
+ 父类对子类的约束
    + 在子类中可以使用父类中定义的方法
    + 可以通过在子类中增加方法的形式实现新功能
    + 在子类中重写父类方法可以改变程序的行为
    + 期待子类实现抽象方法
    + 要求子类实现抽象方法
+ 抽象类

`在模板方法中：抽象方法并不会有具体的实现代码，仅仅是确立了抽象方法的名称，通过调用使用了抽象方法的模板方法去实现编写实现。抽象类确立了实现的流程，子类决定了流程中具体要处理的内容。`