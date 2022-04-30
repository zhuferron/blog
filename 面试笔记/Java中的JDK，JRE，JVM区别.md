---
title: Java中的JDK，JRE，JVM区别
tags: Java基础
categories: Java面试之Java基础
description: 直接从JDK，JRE，JVM的概念入手，解析三者的区别
---

JDK(Java Development Kit)：英文翻译过来就是Java开发工具，JDK包括Java基础类库，Java运行环境(JRE)，和Java开发工具。当你需要使用Java进行开发时，JDK是必备的。

JRE(Java Runtime Environment)：英文翻译过来就是Java运行环境，它包括Java虚拟机(JVM)，和基础类库

JVM(Java Virtual Machine)：英文翻译过来就是Java虚拟机，它是虚拟出来的一个计算机，JVM是Java能够跨平台的核心，所有的Java源码(.java)会被编译成字节码(.class)，然后字节码会被JVM中的解释器所解释执行。

总结：JVM是用来解释Java源码编译产生的字节码的，而有时候JVM解释的时候需要调用一些类库，类库+JVM=JRE。而单单有JRE只能运行Java程序，而不能进行开发，因此JRE+开发工具=JDK，有了JDK才能开发Java程序。

