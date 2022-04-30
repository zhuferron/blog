---
title: 详解Java中的String、StringBuffer和StringBuilder
tags: 数据结构
categories: Java面试之数据结构与算法
description: 本文讲解了Java中的String、StringBuffer和StringBuilder，比较了StringBuffer和StringBuilder的性能和线程是否安全。
---

## 1、介绍

- java.lang.String：这个类产生的对象是不可变的，每次都会产生一个新的对象，这就会导致内存空间的浪费。String类是被`final`修饰符修饰的，这也就表示它不能被继承。String实现了三个接口:Serializable、Comparable<String>、CarSequence，其中Comparable<String>表示了其可以使用`compareTo()`方法进行比较。
- java.lang.StringBuffer()：这个类产生的对象是可变的，且线程安全的。该类继承了`AbstractStringBuilder`父类，实现了Serializable, Comparable<StringBuffer>, CharSequence接口，但是其`compareTo()`方法无法进行字符串的比较。
- java.lang.StringBuilder()：这个类产生的对象是可变的，但是线程是不安全的。该类也继承了`AbstractStringBuilder`父类，实现了Serializable, Comparable<StringBuilder>, CharSequence接口，但也是其`compareTo()`方法无法进行字符串的比较。

## 2、性能区别

- String类每次都会创建一个新的对象，相当于每次都需要新建一个对象同时回收掉旧的对象，因此，操作String的效率是十分低下的。而StringBuilder和StringBuffer均为字符串变量，其创建之后是可以更改的，因此StringBuilder和StringBuffer执行速度会比String速度快。
- 我们再来比较StringBuilder和StringBuffer的执行速度。StringBuffer是线程安全的，其所有公共方法会被`synchronized`关键字所修饰，因此每次调用StringBuffer的方法都会加锁同步，而StringBuilder则无需加锁，因此StringBuilder的性能优于StringBuffer
- StringBuffer缓冲区优化：StringBuffer每次调用`toString`方法时都会直接调用缓冲区`toStringCache`的值来构造字符串，而StringBuilder则是通过复制字符数组的方法来构造字符串，因此在这一点上StringBuffer是优于StringBuilder的。不过，StringBuffer的`toString`方法仍然是线程安全的。
- 性能：StringBuilder>StringBuffer>String

## 3、线程安全

- 而在线程是否安全上，StringBuffer是线程安全的，StringBuilder是非线程安全的。原因就是StringBuffer的所有公共方法都是用`synchronized`关键字修饰的
- StringBulider适用于单线程情况下，StringBuffer适用于多线程情况下。





