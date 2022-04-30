---
title: 微服务架构及Pig项目分析(三)：Pig项目分析
tags: SpringCloud
categories: Java后端
description: 软件体系架构课程论文，这部分主要对Pig项目分析和源码解读
---

## 3.1、项目简介

  本文微服务案例选择的是码云上19.2K star的pig项目，该项目是基于 Spring Cloud Hoxton、Spring Boot 2.4、OAuth2的RBAC权限管理系统。

## 3.2、项目技术

### 3.2.1、Nacos 服务注册与发现

​	该项目通过Nacos注册中心来作为协调所有服务，所有服务需要在注册中心进行注册，而一个服务需要调用另外一个服务的时候则先取注册中心查找，然后得到查找结果后再去调用对应的服务。具体过程如下图所示：所有的订单服务都在注册中心中注册，但某一个用户服务需要订单服务时，则去注册中心查找需要的订单服务名字，然后去调用对应的服务。

![](http://fl.ljuuu.com/img/20210317083536.png)

### 3.2.2、Ribbon负载均衡

​	Ribbon是Netflix发布的云中间层服务开源项目，其主要功能是提供客户端实现负载均衡算法。负载均衡的调度规则一般为轮询调度或者加权轮询策略，轮询调度算法的原理是每一次把来自用户的请求轮流分配给内部中的服务器，从1开始，直到N(内部服务器个数)，然后重新开始循环。轮询调度算法假设所有服务器的处理性能都相同，不关心每台服务器的当前连接数和响应速度。当请求服务间隔时间变化比较大时，轮询调度算法容易导致服务器间的负载不平衡。所以此种均衡算法适合于服务器组中的所有服务器都有相同的软硬件配置并且平均服务请求相对均衡的情况，因此现实中往往会采用加权轮询策略，而限于本文的篇幅，加权轮询策略原理不再展开。

![](http://fl.ljuuu.com/img/20210317083557.png)

​	在SpringCloud中往往使用Ribbon来实现负载均值，其过程如下图所示：当有一个服务需要调用另外某一个服务时，该服务会向注册中心进行查询，注册中心会返回一个可用的服务实例列表，例如前文的三个订单服务中只有一个订单服务空闲，另外两个订单服务处于高工作量时，用户服务就能得到空闲的订单服务的名称，此时用户服务就可直接调用该空闲的订单服务。

![](http://fl.ljuuu.com/img/20210317083609.png)

### 3.2.3、GateWay业务网关

​	面对互联网复杂的业务系统，基本可以将API网关分成两类：流量网关和业务网关。流量网关是指跟具体的后端业务系统和服务完全无关的部分，比如安全策略、全局性流控策略、流量分发策略等。而业务网关针对具体的后端业务系统，或者是服务和业务有一定关联性的部分，并且一般被直接部署在业务服务的前面。业务网关一般部署在流量网关之后，业务系统之前，比流量网关更靠近系统。我们大部分情况下说的 API 网关，狭义上指的是业务网关。并且如果系统的规模不大，我们也会将两者合二为一，使用一个网关来处理所有的工作。

​	SpringCloud中GateWay作为业务网关的一种，整个作用原理如下图：客户端发送的请求不会直接到服务端，而是首先通过网关系统，如果请求与网关程序定义的路由匹配，则将其发送到网关 Web 处理程序，此处理程序运行特定的请求过滤器链。过滤器之间用虚线分开的原因是过滤器可能会在发送代理请求之前或之后执行逻辑。所有 "pre" 过滤器逻辑先执行，然后执行代理请求，代理请求完成后，执行 "post" 过滤器逻辑。

![](http://fl.ljuuu.com/img/20210317083629.png)

### 3.2.4、Sentinel 流量保护

​	Hystrix的产生和原理在前文已经讲述，Sentinel 和 Hystrix 的原则是一致的：当调用链路中某个资源出现不稳定，例如，表现为 timeout，异常比例升高的时候，则对这个资源的调用进行限制，并让请求快速失败，避免影响到其它的资源，最终产生雪崩的效果。但是在限制的手段上，Sentinel 和 Hystrix 采取了完全不一样的方法。Sentinel 对这个问题采取了两种手段：

1. 通过并发线程数进行限制：和资源池隔离的方法不同，Sentinel 通过限制资源并发线程的数量，来减少不稳定资源对其它资源的影响。这样不但没有线程切换的损耗，也不需要您预先分配线程池的大小。当某个资源出现不稳定的情况下，例如响应时间变长，对资源的直接影响就是会造成线程数的逐步堆积。当线程数在特定资源上堆积到一定的数量之后，对该资源的新请求就会被拒绝。堆积的线程完成任务后才开始继续接收请求。

2. 通过响应时间对资源进行降级：除了对并发线程数进行控制以外，Sentinel 还可以通过响应时间来快速降级不稳定的资源。当依赖的资源出现响应时间过长后，所有对该资源的访问都会被直接拒绝，直到过了指定的时间窗口之后才重新恢复。

## 3.3、源码分析

### 3.3.1、注册中心源码分析

​	从码云将pig项目clone到本地之后，使用IDEA打开该项目，SpringBoot项目本质还是一个Maven项目，故将该项目解析成一个Maven项目。

​	Springcloud-commons包中自动装配了AutoServiceRegistrationAutoConfiguration类，这个类就是与服务注册相关的配置类。如下图所示，找到这个配置类，这个配置类注入了一个AutoServiceRegistration实例。

![](http://fl.ljuuu.com/img/20210317083729.png)

​	分析与AutoServiceRegistration类相关的其他类，发现有个很重要的AbstractAutoServiceRegistration抽象类实现了AutoServiceRegistration这个接口。

![](http://fl.ljuuu.com/img/20210317083742.png)

​	AbstractAutoServiceRegistration类实现了onApplicationEvent抽象方法,并且监听WebServerInitializedEvent事件(当Webserver初始化完成之后) , 调用this.bind ( event )方法。

![](http://fl.ljuuu.com/img/20210317083755.png)

​	最终会调用NacosServiceREgistry.register()方法进行服务注册。

![](http://fl.ljuuu.com/img/20210317083808.png)

![](http://fl.ljuuu.com/img/20210317083812.png)

### 3.3.2、GateWay路由网关源码分析

​	因为目前SpringCloud未集成Zuul，并且Zuul也已经停止维护，而GateWay与Spring完美集成，因此Pig项目选择用GateWay来做API网关。

​	GateWay中定义了一个GlobalFilter接口，该接口直译为全局的过滤器，我们知道API网关的本质就是一个或者多个过滤器，因此我们找到该项目中GlobalFilter接口的实现类。

![](http://fl.ljuuu.com/img/20210317083832.png)

​	该类重写了接口中的filter()方法，接收一个ServerWebExchange参数和GatewayFilterChain参数，GatewayFilterChain是一个放行链，其接收参数为一系列过滤器的列表，表示可以对这些过滤器进行建立或者放行。我们重点关注ServerWebExchange参数。

![](http://fl.ljuuu.com/img/20210317083843.png)

​	如上图，ServerWebExchange参数有请求和相应两个数据域，表示从ServerWebExchange参数中我们可以得到从用户那传递过来的请求的具体信息。于是有了下面这一步，从ServerWebExchange中得到请求的信息。

![](http://fl.ljuuu.com/img/20210317083854.png)

然后根据具体的内容，GateWay重写对应的请求路径，将请求实现转发或者重定向。

![](http://fl.ljuuu.com/img/20210317083903.png)

实际上，这里GateWay整个可以看成进入应用程序之前的过滤器，拦截所有请求，然后根据请求内容将请求转发或重定向到具体的业务实现部分，GateWay充当一个中介的作用。

## 参考

1. https://www.cnblogs.com/zhangs1986/p/10546973.html

2. https://blog.csdn.net/weixin_41217541/article/details/104718834

3. https://pig4cloud.com/home