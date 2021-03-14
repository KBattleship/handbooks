---
title: '[ 设计模式 ] 2.适配器模式'
date: 2019-07-06T15:36:27+08:00
lastmod: 2019-07-06T15:36:27+08:00
keywords: ['design_pattern']
description: ""
tags: ['design_pattern','java']
categories: ['java']
author: ""
---
# 适配器模式
#### 适配器模式包含两种：
+ 类适配器模式(使用继承的适配器)
+ 对象适配器模式(使用委托的适配器)

#### 适配器模式角色组成：
+ *目标角色*
+ *源角色*
+ *适配器角色*


### ①.类适配器模式
**`继承于源类的同时实现目标接口`**
#### 表1-1 类与接口说明
| 类名 | 备注 |
| :---: | :---: |
| Print | 表示目标角色  |
| Banner | 表示源角色 |
| PrintBanner | 表示适配器角色 |
#### 代码清单
+ Print(目标角色)

```java
public interface Print {
    abstract void printWeak();
    abstract void printStrong();
}
```
+ Banner(源角色)

```java
public class Banner {
    private String nameString;

    public Banner(String nameString) {
        this.nameString=nameString;
    }

    public void showWithParen() {
        System.out.println("("+nameString+")");
    }

    public void showWithAster() {
        System.out.println("*"+nameString+"*");
    }

}
```
+ PrintBanner(适配器)

```java
/**
 * 适配器角色
 */
public class PrintBanner extends Banner implements Print {

    public PrintBanner(String nameString) {
        super(nameString);
    }

    /* 
     * 实现Print接口(目标)的两个方法
     * 同时，继承Banner父类(源)的两个方法
     */
    @Override
    public void printWeak() {
        showWithParen();
    }
    @Override
    public void printStrong() {
        showWithAster();
    }

}
```

**Tips:`PrintBanner与Banner是继承关系，这决定了这个适配器模式是类的`**
### ②.对象适配器模式
#### 代码清单
+ PrintTwo(目标角色)

```java
/**
 * 如果目标角色不是接口而是抽象类
 */
public abstract class PrintTwo {
    public abstract void printWeak();
    public abstract void printStrong();
}
```
+ Banner(源角色)

```shell
# 源角色无变化，与继承适配器使用同一类
```
+ PrintBannerTwo(适配器)

```java
public class PrintBannerTwo extends PrintTwo {

    private Banner banner;
    // 初始化类的时候获取到委派对象
    public PrintBannerTwo(String nameString) {
        this.banner = new Banner(nameString);
    }

    @Override
    public void printWeak() {
        /**
         * 通过委派形式实现适配器
         */
        banner.showWithParen();
    }
    @Override
    public void printStrong() {
        banner.showWithAster();
    }
}

```
### ③.类适配器与对象适配器对比

|   | 优点  | 缺点  |
| :---: | --- | --- |
| 类适配器 | 使用方便，仅需要引入一个对象  | 高耦合、灵活性差。使用对象继承的方式，是静态的定义方式|
| 对象适配器 | 低耦合、灵活性高。采用 “对象组合”的方式，是动态组合方式| 使用复杂 |

### ④.使用要点
+ 在系统中，现有的类经过充分测试，Bug很少或不存在，可以使用**适配者模式**，在新功能上进行现有类的复用。
+ 在**适配者模式**中，并不一定需要有现成的代码，只要知道现有的类的功能，就可以编写出新的类。
+ 系统部分功能更新迭代兼容性问题，可以通过**适配者模式**做新老版本兼容。例如：新版本扮演**Adaptee角色**，旧版本扮演**Target角色**，编写一个新的**Adapter角色**的类（使用新版本的类实现旧版本类中的方法）。