---
title: "[ Java ] 3.Java注解"
date: 2019-06-22T15:36:27+08:00
lastmod: 2019-06-22T15:36:27+08:00
keywords: ['annotation']
description: ""
tags: ['java','programming','Annotation']
categories: ['java']
author: ""
---
# Java注解

## 1.基本语法

首先是注解的声明方式如下

```java
// @Target注解传入ElementType指明Nullable的作用域
@Target({ElementType.METHOD, ElementType.PARAMETER, ElementType.FIELD})
// @Retention注解用来指定其声明周期为运行时
@Retention(RetentionPolicy.RUNTIME)
// @Documented指定Javadoc进行记录此对象
@Documented
// @Nonnull为了告诉编译器这个域不可能为空
@Nonnull(when = When.MAYBE)
// @TypeQualifierNickname为类型限定符别称(备注部分具体解释此注解)
@TypeQualifierNickname
public @interface Nullable {
 // @interface 声明注解
}
```

## 2.JDK元注解
| Annotation |Type| Action |
|:---:|:---:|:---:|
|@Override|普通注解|用来限定子类重写父类的方法|
|@Deprecated|普通注解|标记已经过时的方法|
|@SuppressWarnings|普通注解|抑制编译器的警告|
|@SafeVarargs|普通注解|Java7 抑制“堆污染”警告|
|@FunctionalInterface|普通注解|Java8 函数式接口|
|@Retention|元注解|指定本修饰的注解可以保留多长时间|
|@Target|元注解|指定被修饰的注解能用于哪些程序元素|
|@Documented|元注解|被该注解修饰的注解会被javadoc工具提取成文档|
|@Inherited|元注解|此注解修饰的注解具有继承性|
|@Repeatable|元注解|重复注解，Java8的新特性|

## 3.Retention
|Retention|Effect|
|:---:|:---:|
|CLASS| 默认值，编译器将注解记录在字节码文件中，程序运行时，JVM不保留注解信息。|
|RUNTIME|编译器将注解记录在字节码文件中，程序运行时，JVM可以获得注解信息，可通过反射获取该注解的信息。|
|SOURCE|注解只保留在源代码中，编译器直接丢弃。|

## 4.ElementType注解类型
| ElementType | Effect |
|:----:|:----:|
|TYPE|作用于类，接口，枚举|
|FIELD|作用于属性|
|METHOD|作用于函数|
|PARAMETER|作用于参数|
|CONSTRUCTOR|作用于构造函数|
|LOCAL_VARIABLE|作用于局部变量|
|ANNOTATION_TYPE|作用于注解|
|PACKAGE|作用于包|
|TYPE_PARAMETER|作用于类型变量(since 1.8)|
|TYPE_USER|作用于任何类型(since 1.8)|

