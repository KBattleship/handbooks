---
title: "[ 设计模式 ] 1.迭代器模式 "
date: 2019-07-06T05:16:27+08:00
lastmod: 2019-07-06T05:36:27+08:00
keywords: ["design_pattern"]
description: ""
tags: ["java","design_pattern"]
categories: ["Java"]
author: ""
---

# 迭代器模式
![迭代器模式UML图](/image/docs_img/coding/java/media/1_2_1iterator.png)
### 表1-1 类与接口说明
| 类名 | 备注 |
| :---: | :---: |
| Aggregate | 表示集合的接口  |
| Iterator | 遍历集合接口 |
| Book | 表示书的类 |
| BookShelf | 表示书架的类  |
| BookShelfIterator | 遍历书架的类 |
### 代码清单
+ Aggregate接口

```java
/**
 * 表示集合接口
 */
public interface Aggregate {
    /* 用于生成一个遍历集合的迭代器 */
    public abstract Iterator iterator();
}
```
+ Iterator接口

```java
/**
 * 遍历集合接口
 */
public interface Iterator {

    /* 判断是否存在下一个元素 */
    public abstract boolean hasNext();

    /* 获取下一个元素 */
    public abstract Object next();

}
```
+ Book类

```java
/**
 * 表示书的类
 */
public class Book {
    /* 书名属性 */
    private String name;

    /* Default constructor */
    public Book(String name) {
        this.name=name;
    }

    /* 获取书名方法 */
    public String getName() {
        return name;
    }

}
```
+ BookShelf类

```java
/**
 * 表示书架的类
 * 当外部想要遍历书架时则会调用iterator方法
 */
public class BookShelf implements Aggregate {
    /* 书架的属性--书
     * 私有属性，防止外部进行篡改    
     */
    private Book[] books;
    /* 书架的容量 */
    private int last=0;

    public BookShelf(int maxSize){
        this.books=new Book[maxSize];
    }
    
    /* 根据位置获取对应的书 */
    public Book getBookAt(int index){
        return books[index];
    }

    /* 向书架添加书 */
    public void appendBook(Book book){
        this.books[last]=book;
        last++;
    }

    /* 获取书架存放书的数量 */
    public int getLength(){
        return last;
    }
    
    /* 迭代器 */
    @Override
    public Iterator iterator(){
        return new BookShelfIterator(this);
    }
}
```
+ BookShelfIterator类

```java
/**
 * 遍历书架的类,发挥Iterator的作用
 */
public class BookShelfIterator implements Iterator {
    /* 表示BookShelfIterator迭代器所要遍历的书架 */
    private BookShelf bookShelf;
    /* 表示迭代器当前指向书的下标 */
    private int index;

    public BookShelfIterator(BookShelf bookShelf) {
        this.bookShelf = bookShelf;
        this.index = 0;
    }

    /* 判断是否存在下一个元素 */
    @Override
    public boolean hasNext(){
        if (index<bookShelf.getLength()){
            return true;
        }
        return false;
    }
    
    /* 获取下一个元素 */
    @Override
    public Object next() {
        /* 获取迭代器当前所指向的书 */
        Book book=bookShelf.getBookAt(index);
        /* 并把下标指向下一本书的下标 */
        index++;
        return book;
    }
}
```
+ Main主类

```java
public class Main {
    public static void main(String[] args) {
        /* 先初始化来一个存放书的书架 */
        BookShelf bookShelf = new BookShelf(4);
        bookShelf.appendBook(new Book("firstOneBook"));
        bookShelf.appendBook(new Book("firstTwoBook"));
        bookShelf.appendBook(new Book("firstThreeBook"));
        bookShelf.appendBook(new Book("firstFourBook"));
        /* 通过Iterator拿到遍历书架的实例 */
        Iterator iterator = bookShelf.iterator();
        /* 没有下一个元素就停止遍历 */
        while (iterator.hasNext()) {
            /* 通过next()方法拿出下一本书 */
            System.out.println(((Book) iterator.next()).getName());
        }
    }
}
```
### Iterator之角色职责
+ Iterator迭代器

    `负责定义按照顺序逐个遍历元素的迭代器接口。`
+ ConcreateIterator具体迭代器

    `负责具体实现Iterator接口中的具体实现。`
+ Aggregate集合

    `负责定义创建Iterator角色的接口。`
+ ConcreteAggregate具体集合

    `负责实现Aggregate角色所定义的接口,会创建出具体的Iterator角色。`

```shell
若不使用Aggregate、Iterator接口，直接通过ConcreteAggregate、
ConcreateIterator具体类解决所有的问题，
极容易导致类之间的强耦合；同时也难以作为组件供其他类调用。
为弱化耦合，需要引入抽象类与接口。
```