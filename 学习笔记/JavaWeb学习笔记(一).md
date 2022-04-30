---
title: JavaWeb学习笔记(一)
tags: JavaWeb
categories: 学习笔记
description: JavaWeb第一部分学习笔记，主要介绍了JavaWeb的基本概念、web服务器、tomcat和http的知识
---


## 1、基本概念

### 1.1、前言

web开发：

- web，网页的意思
- 静态web
  - html，css
  - 提供给所有人看的数据始终不会发生改变
- 动态web
  - 淘宝，几乎所有的网站
  - 提供给所有人看的数据始终会发生改变，每个人在不同的时间，不同的地点看的的信息各不相同
  - 技术栈：Servlet/JSP，ASP，PHP

在java中，动态web资源开发的技术统称为JavaWeb

### 1.2、web应用程序

web应用程序：可以提高浏览器访问的程序

- a.html、b.html.....多个web资源，这些web资源可以被外界访问，对外界提供服务。
- 访问到的任何一个页面或者资源，都存在于这个世界的某一个角落的计算法基上。
- URL
- 这个同一的web资源会被放在同一个文件夹下，web应用程序 -->Tomcat：服务器
- 一个web应用由多部分组成（静态web，动态web）
  - html，css，js
  - jsp，servlet
  - java程序
  - jar包
  - 配置文件(Properties)

web应用程序编写完毕之后，若想提供给外界访问：需要一个服务器来同一管理

### 1.3、静态web

- .htm,html,这些都是网页的后缀，如果服务器上一直存在这些东西，我们就可以直接进行读取。
- 静态web存在的缺点
  - web页面无法动态更新，所有用户看到都是同一个页面
    - 轮播图，点击特效：伪动态
    - JavaScript
  - 它无法和数据库交互(数据无法持久化，用户无法交互)

### 1.4、动态web

页面会动态展示：web的展示会因人而异

缺点：

- 假如服务器的动态web资源出现了错误，我们需要重新编写我们的**后台程序**，重新发布；
  - 停机维护

优点

- web可以动态更新
- 可以与数据库交互(数据持久化：注册，商品信息，用户信息)

## 2、web服务器

### 2.1、技术讲解

ASP：

- 微软，国内最早流行
- 在html中嵌入VB的脚本，ASP+COM
- 在ASP开发中，基本一个页面都有几千行的业务代码，页面极其混乱
- 维护成本高。
- C#

PHP：

- PHP开发速度很快，功能很强大，跨平台，代码很简单(70%，WP)
- 无法承载大访问量的情况(有局限性)

JSP/Servlet：

B/S：浏览器和服务器

C/S:客户端和服务器

- sun公司主推的B/S架构
- 基于java语言(所有的大公司，或者一写开源组件，都是java写的)
- 可以承载三高(高并发，高可用，高性能)问题带来的影响
- 语法像ASP，ASP-->JSP，加强市场强度

### 2.2、web服务器

服务器是一种被动的操作，用来处理用户的一些请求，和给用户一些相应信息

**IIS**：微软的，ASP,....微软自带的

**Tomcat**：面向百度编程。

**工作3-5年之后，可以尝试手写Tomcat服务器**

安装Tomcat：

1. 安装 or 解压
2. 了解配置文件及其目录结构
3. 这个东西的作用

## 3、Tomcat

### 3.1、安装Tomcat

官网安装

### 3.2、配置和目录

启动和关闭Tomcat

访问测试：localhost8080

1. java环境变量没有配置
2. 闪退问题：需要配置兼容性
3. 乱码问题：配置文件中设置

### 3.3、配置

server.xml

可以配置启动的端口号

- tomcat的默认端口号：8080
- mysql：3306
- http：80
- https：443

可以配置主机的名称

- 默认的主机名：localhost->127.0.0.1
- 默认网站应用存放的位置为：webapps

**高难度面试题**

请你谈一谈网站是如何进行访问的

1. 输入一个域名，回车
2. 检查本机的C:\Windows\System32\drivers\etc\hosts配置文件下有没有这个域名映射；
   1. 有：直接返回对应ip地址，这个地址中，有我们需要访问的web程序，可以直接访问
   2. 没有，去DNS服务器(全世界域名管理)，找不到就返回找不到
3. 配置环境变量

### 3.4、发布一个网站

- 将自己写的网站，放到服务器(Tomcat)中指定的文件夹(webapps)下，就可以访问了

  ```java
  --webapps:Tomcat服务器的web目录
      -ROOT
      -Web
      	-WEB-INF
      		-classes:java程序
              -lib:web应用所依赖的jar包
              -web.xml
          -index.xml 默认的首页
          -static
              -css
              	-style.css
              -js
              -img
  ```


http协议：面试

Maven：构建工具

- Maven安装包

Servlet入门

- HelloWorld
- Servlet配置
- 原理

## 4、Http

### 4.1、什么是Http

http(超文本传输协议)是一个简单的请求-响应协议，它通常运行在TCP之上。

- 文本：html，字符串....
- 超文本：图片，音乐，视频，定位，地图....
- Http：80 
- Https:443

### 4.2、两个时代

- http1.0
  - HTTP/1.0：客户端可以与web服务器连接后，只能获得一个web资源，断开连接
- http2.0
  - HTTP/1.1：客户端可以与web服务器连接后，可以获得多个web资源。

### 4.3、Http请求

- 客户端--发请求(Request)--服务器

百度：

```java
Request URL: https://www.baidu.com/  请求地址
Request Method: GET  get方法/Post方法
Status Code: 200 OK  状态码：200
Remote Address: 163.177.151.109:443  地址
Referrer Policy: no-referrer-when-downgrade
```

**请求行**

- 请求行中的请求方式：Get
- 请求方式：Get，Post，HEAD，DELETE.....
  - get：请求能够携带的参数比较少，大小有限制，会在浏览器的URL地址栏显示数据内容，不安全，但高效
  - post：请求能够携带的参数没有限制，大小没有限制，不会在浏览器的URL地址栏显示数据内容，安全，不高效

**消息头**

```java
Accept:告诉浏览器它所支持的数据类型
Accept-Encoding:支持哪种编码方式：GBK,UTF-8,GB2312,ISO8859-1
Accept-Language:告诉浏览器它的语言环境
Cache-Control:缓存控制
Connection:告诉浏览器，请求完成是断开还是保持连接
HOST:主机
```

### 4.4、Http响应

```java
Cache-Control: private    缓存控制
Connection: keep-alive    连接
Content-Encoding: gzip    编码
Content-Type: text/html   类型
```

**相应体**

```java
Accept:告诉浏览器它所支持的数据类型
Accept-Encoding:支持哪种编码方式：GBK,UTF-8,GB2312,ISO8859-1
Accept-Language:告诉浏览器它的语言环境
Cache-Control:缓存控制
Connection:告诉浏览器，请求完成是断开还是保持连接
HOST:主机
Refresh:告诉客户端，多久刷新一次
Location:让网页重新定位
```

**响应状态码**

200：请求响应成功

3**：请求重定向

- 重定向：你重新到我给你的新位置去

4**：资源找不到，404

5**：服务器代码错误 

- 500：
- 502：网关错误