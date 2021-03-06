---
title: "[ Java ] 1.对象入门"
date: 2019-06-22T15:36:27+08:00
lastmod: 2019-06-22T15:36:27+08:00
keywords: ['base']
description: ""
tags: ['java','programming','对象']
categories: ['java']
author: ""
---
# 对象入门

​	首先一个重要的概念：**OOP** *(Object Oriented Programming)*,即面向对象编程。对于面向对象会有三大特征：**封装**、**继承**、**多态**。

- 封装：就是把客观事物封装成具体抽象的类，并且类可以把自己的数据和方法只让可信的类或者对象进行操作，对不可信的进行隐藏。一个类就是封装了数据以及操作这些数据的代码的逻辑实体。在一个对象内部，某些代码或者某些数据可以是私有的，不能被外界所访问。通过这种方式，对象对内部数据提供了不同级别的保护，以防止程序中无关的部分意外的改变或者错误的使用了对象的私有部分。
- 继承：指可以让某个类型的对象获得另一个类型的对象的属性的方法。它支持按级分类的概念。继承是指这样一种能力：它可以使用现有类的所有功能，并在无需重新编写原来的类的情况下对这些功能进行扩展。 通过继承创建的新类称为“子类”或“派生类”，被继承的类称为“基类”、“父类”或“超类”。继承的过程，就是从一般到特殊的过程。要实现继承，可以通过 “继承”（Inheritance）和“组合”（Composition）来实现。继承概念的实现方式有二类：实现继承与接口继承。实现继承是指直接使用 基类的属性和方法而无需额外编码的能力；接口继承是指仅使用属性和方法的名称、但是子类必须提供实现的能力。
- 多态：是指一个类实例的相同方法在不同情形有不同表现形式。多态机制使具有不同内部结构的对象可以共享相同的外部接口。这意味着，虽然针对不同对象的具体操作不同，但通过一个公共的类，它们（那些操作）可以通过相同的方式予以调用。

### 一、抽象

​	Java基础语言的面向对象程序设计方法具有的一下五大基本特征：

- 所有东西都是对象。可将对象想象成一种新型变量；它保存着数据，但是可要求它对自身进行操作。理论来说，可以从要解决的问题身上提出所有概念性的组件，然后在程序中将其表达为一个对象。
- 程序是一大堆对象的组合。通过消息的传递，各对象知道自己该做什么。为了向面向对象发出请求，需向那个对象发送一条消息。更确切的来说，可以将消息想象成为一个请求，他调用的是从属于目标对象的一个子例程或者说是函数。
- 每个对象都有自己的存储空间，可以容纳其他对象。或者说是，通过封装现有的对象，可以制造出一个新型对象。所以，尽管对象的概念非常简单，但是在程序中却可以达到任意高的复杂程度。
- 每个对象都有一个类型。根据语法，每个对象都是某个类中的一个实例。其中，类**(Class)**是类型(type)的同义词。一个类最为重要的特征就是“能将什么消息发送给它”。
- 同一个类所有的对象都能接收相同的消息。这实际是别有含义的一种说法。由于类型为“圆”的一个对象也属于类型为“形状”的一个对象。所以一个圆完全可以接收形状消息。这意味着可以让程序代码统一指挥“形状”，使得其自动控制所有的形状对象。其中自然包括圆这一个对象。这一个特性成为对象的可替代性。是OOP的重要概念之一。

### 二、对象的接口



