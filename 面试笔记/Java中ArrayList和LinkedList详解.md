---
title: Java中ArrayList和LinkedList详解
tags: 数据结构
categories: Java面试之数据结构与算法
description: 详解Java中ArrayList和LinkedList，以及对比两者区别
---

## 1、ArrayList详解

### 1.1、简介

​	ArrayList是通过动态数组实现的，底层基于动态数组实现容量的动态改变。继承自 AbstractList，实现了 List 接口。

![](http://fl.ljuuu.com/img/v2-1cd5e4c4fecb60d30382944a4c2eecb3_720w.jpg)



### 1.2、详解

- 扩容：ArrayList默认构造方法初始化的数组长度为10，,每次扩容后的容量为原来数组的1.5倍。扩容通过数组拷贝来实现，效率较低。
- `get()`方法：首先检查索引是否越界，然后返回对应索引的值。
- `set()`方法，首先检查索引是否越界，然后设置对应索引的值。
- `remove()`方法：首先判断索引是否越界，然后通过移动覆盖的思想，拷贝要删除元素后面的所有元素，向前覆盖，然后将最后多出来的元素设为null，GC机制自动回收。
- `add()`方法，思想与`remove()`方法类似，通过向后覆盖的思想，先拷贝要添加索引后的元素，然后添加元素。

## 2、LinkedList详解

### 2.1、简介

​	LinkedList的底层是通过双向链表实现的。

![](http://fl.ljuuu.com/img/272345393446232.jpg)

### 2.2、详解

- `add()`方法：添加一个元素
- `set()`方法：修改某个结点的值
- `get()`方法：获取某个结点的值
- `remove()`：删除一个结点
- 虚拟头结点：我们在使用时经常会使用虚拟头结点这个操作，即将原本的head的上一个结点设为一个空的结点，这样就会在例如`add()`方法中减少一次`index==0`的判断。

## 3、区别

- 底层实现不同：ArrayList是基于动态数组实现的；LinkedList是基于双向链表实现的。
- 方法效率不同：ArrayList的`get()`和`set()`方法效率高；LinkedList的`remove()`和`add()`方法效率高。
- 扩容机制不同：ArrayList扩容需要新建一个数组，效率低；LinkedList没有容量限制。









