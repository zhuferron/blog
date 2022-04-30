---
title: Redis学习笔记(一)
tags: Redis
categories: 学习笔记
description: Redis第一部分学习笔记，主要介绍了NoSQL概念、以及Redis入门和安装使用
---

## 1、概述

### 1.1、发展

- 单机Mysql时代

  ![image-20210228091732164](http://fl.ljuuu.com/image-20210228091732164.png)

  随着访问量增加会出现许多问题：

  - 数据量大，一个机器放不下
  - 数据的索引(B+ Tree)，一个机器内存放不下
  - 访问量(读写混合)，一个服务器承受不了

- 缓存+Mysql+垂直拆分

  发展过程：优化数据结构和索引-->文件缓存(IO)-->Memcached

  ![image-20210228091856599](http://fl.ljuuu.com/image-20210228091856599.png)

  - 缓存：减轻服务器的压力，使用缓存来保证效率
  - 垂直拆分：纵向扩展服务器，一般一个网站80%都是在读，因此读写分离(主从复制，主库写，从库读)

- 分库分表+水平拆分+Mysql集群

  早些年MyISAM：表锁，十分影响效率，高并发会出现严重的问题

  Innodb：行锁

  ![image-20210228092600004](http://fl.ljuuu.com/image-20210228092600004.png)

  - 每个集群存三分之一的数据

- 如今的时代

  ![image-20210228094123601](http://fl.ljuuu.com/image-20210228094123601.png)

  

  - 数据量很多，变化很快：Mysql等关系型数据库力不从心
  - 像图片、定位数据这种数据很难用关系型数据库来存储

- 为什么要用Nosql

  - 用户的个人信息、社交网络、地理位置、用户自己产生的数据、用户日志等数据爆发式增长

### 1.2、大数据

大数据时代的3V：

1. 海量Volume
2. 多样Variety
3. 实时Velocity

大数据时代的3高：

1. 高并发
2. 高可扩（随时可以水平拆分）
3. 高性能（保证用户体验和性能）

## 2、Nosql

### 2.1、概述

Nosql=not only sql

不仅仅是sql，泛指非关系型数据库。------->Map<String,Object>

Nosql特点：

1. 方便拓展（数据之间没有关系，很好拓展！）

2. 大数据量高性能（Redis一秒写8w次，读取11w次，Nosql的缓存记录级，是一种细粒度的缓存，性能会比较高）

3. 数据类型是多样性的（不需要事先设计数据库）

4. 传统RDBMS和Nosql区别

   ```
   传统的RDBMS
   - 结构化组织
   - SQL
   - 数据和关系都存在单独的表中
   - 操作操作，数据定义语言
   - 严格的一致性
   - 基础的事务
   - .....
   
   ```

   ```
   Nosql
   - 不仅仅是数据
   - 没有固定的查询语言
   - 键值对存储，列存储，文档存储，图形数据库(社交关系)
   - 最终一致性
   - CAP定理和BASE(异地多活)
   - 高性能，高可用，高可拓展
   - .....
   ```

5. 真正的实践：RDBMS+NoSQL

### 2.2、四大分类

**KV键值对：**

- 新浪：Redis
- 美团：Redis+Tair
- 阿里、百度：Redis+memachche

**文档型数据库(bson+json一样)：**

- MongoDB(一般必须要掌握)：
  - MongoDB是一个基于分布式文件存的数据库，主要用来处理大量的文档
  - MongoDB是一个介于关系型数据库和非关系型数据中中间的产品！**是非关系型数据库中功能最丰富，最像关系型数据库的！**
- ConthDB

**列存储数据库：**

- HBase
- 分布式文件系统

**图数据库：**

- 不是存图形的，放的是关系，就是图这种数据结构
- Neo4j，infoGrid

## 3、Redis入门

### 3.1、概述

- Redis（**Re**mote **Di**ctionary **S**erver )，即远程字典服务
- 是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。
- redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步。
- 读的速度是110000次/s,写的速度是81000次/s 。

### 3.2、功能

- 内存存储、持久化，内存是断电即失，所以说持久化很重要（rdb、aof）
- 效率高，可以用于高速缓存
- 发布订阅系统
- 地图信息分析
- 计时器、计数器（浏览量）
- ......

### 3.3、特性

- 多样的数据类型
- 持久化
- 集群
- 事务
- .......

### 3.4、下载安装（Linux）

- 下载安装包：`redis-6.0.6.tar.gz`，放到home目录

- 一般程序放到`/opt`目录下

  ```bash
  mv redis-6.0.6.tar.gz /opt
  ```

- 解压redis

  ```bash
  tar -zxvf redis-6.0.6.tar.gz
  ```

- 进入redis目录

  ![image-20210228133613720](http://fl.ljuuu.com/image-20210228133613720.png)

- 安装依赖环境（6.0.1之后版本）

  ```bash
  #安装c++
  yum install gcc-c++
  #查看需要安装的依赖
  gcc -v
  # 升级到9.1版本
  yum -y install centos-release-scl  
  yum -y install devtoolset-9-gcc devtoolset-9-gcc-c++ devtoolset-9-binutils
  scl enable devtoolset-9 bash
  #安装依赖
  make
  #确定安装
  make install
  ```

- 默认安装到`/usr/local/bin`目录下

  ![image-20210228135044676](http://fl.ljuuu.com/image-20210228135044676.png)

- 将redis配置文件复制到当前目录下，我们之后使用这个文件进行启动

  ```bash
  cp /opt/redis-6.0.6/redis.conf fconfig/
  ```

- redis默认不是后台启动的，我们修改配置文件使其后台启动

  ![image-20210228140010904](http://fl.ljuuu.com/image-20210228140010904.png)

- 在`/usr/local/bin`目录下启动redis

  ```bash
  redis-server fconfig/redis.conf #通过指定的配置文件启动服务
  ```

- 使用`redis-cli`连接指定的端口号

  ```bash
  redis-cli -p 6379
  ```

- 测试

  ![image-20210228140448044](http://fl.ljuuu.com/image-20210228140448044.png)

- 查看redis进程

  ![image-20210228140624062](http://fl.ljuuu.com/image-20210228140624062.png)

- 关闭redis服务

  ```bash
  shutdown
  exit
  ```

- 再次查看进程是否存在

  ![image-20210228140808293](http://fl.ljuuu.com/image-20210228140808293.png)

### 3.5、测试性能

**redis-benchmark**是一个压力性能测试工具

### 3.6、基础知识

redis默认有16个数据库，默认使用的是第0个数据库

`select x`：切换数据库，x表示第几个数据库

`BDSIZE`：查看数据库内数据大小

`keys *`：查看当前数据库所有的key

`flushdb`：清除当前数据库的所有数据

`flushall`：清除所有数据库的所有数据

### 3.7、单线程和多线程

	- **Redis是单线程的**。
	- 官方表示，Redis是基于内存操作的，CPU不是Redis性能瓶颈，Redis的瓶颈是根据机器的内存和网络带宽。
	- 误区1：高性能的服务器一定是多线程的？
	- 误区2：多线程一定比单线程效率高？
	- 核心：Redis是将所有的数据都放在内存中的，所有使用单线程去操作效率就是最高的，而多线程会涉及CPU上下文的切换，这会耗时！对于内存系统来说，如果没有上下文切换，效率就是最高的！多次读写都是在一个CPU上的，在内存情况下，这个就是最佳的方案

## 