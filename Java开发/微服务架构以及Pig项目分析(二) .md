---
title: 微服务架构及Pig项目分析(二)：SpringCloud微服务技术
tags: SpringCloud
categories: Java后端
description: 软件体系架构课程论文，这部分主要介绍了微服务存在的问题以及SpringCloud是怎么解决这些问题的
---

## 2.1、微服务核心问题

1. 微服务架构中有很多服务组件，不同的用户怎么调用不同的服务

2. 微服务架构中不同的服务之间怎么进行互相调用

3. 微服务架构中怎么对这些不同的服务进行治理

4. 微服务架构中某个服务出问题了，怎么解决

## 2.2、问题一：Zuul+微服务网关

​	Zuul是Netflix开源的一个API Gateway 服务器,在云平台上提供动态路由，监控，弹性，安全等边缘服务的框架。Zuul相当于是设备和Netflix流应用的Web网站后端所有请求的前门。SpringCloud中集成了Zuul，使用它来解决微服务架构中的问题一。

​	Zuul的核心便是Servlet中的filter机制的实现，而filter本质上就是一个Servlet，而filter的应用则是Spring中AOP思想的体现，通过在用户在前端与后端通信的过程中增加一个filter，实现动态路由的功能。例如，用户在前端与后端进行交互时，需要一个具体的服务，而后端往往有成百上千个服务，想让前端记住后端服务的名称往往是不现实的，而加入Zuul网关的作用就是，当前端想要调用后端的一个或多个服务时，往往不直接调用，而是统一从Zuul网关调用，Zuul网关将对应的请求转发或重定向到对应的服务。

![](http://fl.ljuuu.com/img/20210317083241.png)

## 2.3、问题二：Feign+异步调用

​	Feign是Netflix开发的声明式，模板化的HTTP客户端，其灵感来自Retrofit,JAXRS-2.0以及WebSocket. Feign远程调用的核心就是通过一系列的封装和处理，将以Java注解的方式定义的远程调用API接口，最终转换成HTTP的请求形式，然后将HTTP的请求的响应结果，解码成JavaBean，放回给调用者，其调用过程如下图所示。

![](http://fl.ljuuu.com/img/20210317083309.png)

​	Feign的核心机制就是动态代理，与静态代理不同的是，动态代理的代码不用我们手写，而是可以自动生成。Feign中的动态代理是基于接口的动态代理模式，它是由JDK动态代理的，接口为InvocationHandler，动态代理也是AOP思想的核心。Feign的动态代理会根据你在接口上的@RequestMapping等注解，来动态构造出你要请求的服务的地址最后针对这个地址，发起请求、解析响应。

## 2.4、问题三：Eureka+服务注册与发现

​	Eureka是Netflix开发的服务发现框架，本身是一个基于REST的服务，主要用于定位运行在AWS域中的中间层服务，以达到负载均衡和中间层服务故障转移的目的。SpringCloud将它集成在其子项目spring-cloud-netflix中，以实现SpringCloud的服务发现功能。Eureka中分为eureka server和eureka client。其中eureka server是作为服务的注册与发现中心。eureka client既可以作为服务的生产者，又可以作为服务的消费者。

![](http://fl.ljuuu.com/img/20210317083340.png)

​	其整体架构如上图所示：多个eureka server同步保留着相同的服务信息，作为服务生产者的eureka client需要在eureka server中注册其服务信息，而作为服务消费者的eureka client想要查找一个具体的服务时，首先得去eureka server查找对应服务所对应的eureka client，然后再从对应的eureka client调用对应的服务。

## 2.5、问题四：Hystrix+降级

​	微服务架构的特点就是各个不同的服务分离了，当网站或应用程序有很高的工作量时，就有可能导致部分服务挂了，然而往往不同的服务之间有相互调用，当一个服务挂了，就可能导致其他服务的部分功能也实现不了，这就是服务雪崩效应。

​	为了避免这种服务雪崩效应，Hystrix提出的一个解决方法就是服务降级。其实现的具体思想就是每个服务发起的请求都通过Hystrix的线程池不同的服务走不同的线程池，实现了不同的服务调度隔离，从而避免服务雪崩的问题。



















