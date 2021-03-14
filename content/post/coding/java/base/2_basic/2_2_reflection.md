---
title: "[ Java ] 2.Java反射"
date: 2019-06-22T15:36:27+08:00
lastmod: 2019-06-22T15:36:27+08:00
keywords: ['reflection']
description: ""
tags: ['java','programming','反射']
categories: ['java']
author: ""
---
# Java反射
### 一、反射：

##### 1.概念：

	反射，即：动态获取类的信息，以及动态调用对象的方法的功能。

##### 2.作用：

- 在运行时判断任意一个对象所属的类

- 在运行时构造任意一个类的对象

- 在运行时判断任意一个类所具有的成员变量和方法

- 在运行时调用任意一个对象的方法

	 可以生成动态代理	

通过Java反射机制，可以在程序中访问已经装载到**jvm**中的**Java**对象的描述，以实现访问、检测和修改描述**Java**对象本身信息的功能。**Java**反射机制很强大。其中，**java.lang.reflect**包中提供了对该功能的支持。

---

### 二、实现反射获取类的方法：

##### 方式一 ：  通过Class.forName("类名字符串")获取（最为常用的方法）

```java
//类名字符串是“包名+类名”，返回Class的对象。
Class class = Class.forName("cheneyHao.Student");
```

##### 方式二 ：  通过示例对象的getClass()方法获取

```java
//先创建一个对象，在调用对象的getClass()方法。（任何一个Java对象都会有getClass()方法）
Student student = new Student();
Class class = student.getClass();
```

##### 方法三 ： 通过 ***.class*** 获取

```java
//通过类名.class,返回Class对象。(每个类都有Class属性)
Class class = Student.class;
```