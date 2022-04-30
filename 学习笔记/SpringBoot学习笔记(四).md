---
title: SpringBoot学习笔记(四)
tags: SpringBoot
categories: 学习笔记
description: SpringBoot第四部分学习笔记，主要介绍了SpringBoot如何集成接口文档Swagger、SpringBoot中的三大任务、以及SpringBoot中Dubbo+Zookeeper的集成
---

## 11、swagger

### 11.1、概述

前后端分离：Vue+springboot

后端时代：前端只用管理静态页面；html==>后端，模板引擎：jsp==>后端是主力

前后端分离时代：

- 后端：后端控制层，服务层，数据访问层【后端工程】
- 前端：前端控制层，视图层【前端工程】
  - 伪造后端数据，json。已经存在了，不需要后端，前端工程依然能跑起来
- 前后端如何交互？==>API
- 前后端相对独立，松耦合；
- 前后端甚至可以部署在不同的服务器上

产生问题：

- 前后端集成联调，前端人员和后端人员无法做到，即使协商，今早解决。

解决方案：

- 首先制定schema计划的提纲，实时更新最新的API，降低集成的风险
- 前后端分离：
  - 前端测试后端接口：postman
  - 后端提供接口，需要实时更新最新的消息及改动

**swagger**

- 号称世界上最流行的api框架
- restful api文档在线自动生成工具=>api文档与api同步更新
- 直接运行，可以在线测试api接口

在项目中使用swagger需要springbox

- swagger2
- ui

### 11.2、springboot集成

- 新建一个项目

- 导入相关依赖

  ```xml
  <!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
  <dependency>
      <groupId>io.springfox</groupId>
      <artifactId>springfox-swagger2</artifactId>
      <version>2.9.2</version>
  </dependency>
  <!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
  <dependency>
      <groupId>io.springfox</groupId>
      <artifactId>springfox-swagger-ui</artifactId>
      <version>2.9.2</version>
  </dependency>
  ```

- 配置swagger

  ```java
  @Configuration
  @EnableSwagger2           //开启Swagger2
  public class SwaggerConfig {
      
  
  }
  ```

- 访问`/swagger-ui`

  ![image-20210130102158388](http://fl.ljuuu.com/image-20210130102158388.png)

### 11.3、配置swagger

swagger的bean实例Docket

```java
@Configuration
@EnableSwagger2           //开启Swagger2
public class SwaggerConfig {

    //配置了Swagger的Docket的bean实例
    @Bean
    public Docket docket(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo());
    }

    //配置Swagger信息=apiInfo
    public ApiInfo apiInfo(){
        //自定义swagger网站信息
        return new ApiInfo(
                "Ferron的Swagger文档",
                "Api Documentation",
                "1.0",
                "urn:tos", DEFAULT_CONTACT,
                "Apache 2.0",
                "http://www.apache.org/licenses/LICENSE-2.0",
                new ArrayList());
    }
}
```

### 11.4、Swagger配置扫描接口

**Docket.select()**

配置扫描的接口：

```java
@Bean
public Docket docket(){
    return new Docket(DocumentationType.SWAGGER_2)
        .apiInfo(apiInfo())
        .select()
        //RequestHandlerSelectors 配置要扫描接口的方式
        //basePackage()：指定要扫描接口的包
        //any():扫描全部
        //none()：全部不扫描
        //withClassAnnotation:扫描类上的注解，参数是一个注解的反射对象
        //withMethodAnnotation:扫描方法上的注解
        .apis(RequestHandlerSelectors.basePackage("com.ferron.controller"))
        //过滤什么路径：过滤只有ferron的请求
        .paths(PathSelectors.ant("/ferron/**"))
        .build();  //bulid 工厂模式
}
```

配置是否启动Swagger

```java
@Bean
public Docket docket(){
    return new Docket(DocumentationType.SWAGGER_2)
        .apiInfo(apiInfo())
        .enable(false) //不启用swagger
}
```

我只希望在生产环境中使用，在发布的时候不使用

- 设置两套环境

### 11.5、配置API分组

上面分组框的分组

```java
//配置了Swagger的Docket的bean实例
@Bean
public Docket docket(){

    return new Docket(DocumentationType.SWAGGER_2)
        .apiInfo(apiInfo())
        .groupName("ferron")
        .select()
        .apis(RequestHandlerSelectors.basePackage("com.ferron.controller"))
        .paths(PathSelectors.ant("/ferron/**"))
        .build();
}
```

如何配置多个组：

在SwaggerConfig类中创建多个返回Docket的不同方法，创建多个分组。

```java
@Bean
public Docket docket1(){
    return new Docket(DocumentationType.SWAGGER_2).groupName("A");
}

@Bean
public Docket docket2(){
    return new Docket(DocumentationType.SWAGGER_2).groupName("B");
}

@Bean
public Docket docket3(){
    return new Docket(DocumentationType.SWAGGER_2).groupName("C");
}
```

### 11.6、实体类配置

**Model：**

- 创建pojo类，新建User实体类

- 写一个请求，返回值为实体类

  ```java
  //只要我们的请求返回值中存在实体类，model中就会有实体类
  @PostMapping(value = "/user")
  public User user(){
      return new User();
  }
  ```

- 使用`@ApiModel`给实体类加注释，使用`@ApiModelProperty`给实体类字段加注释

  ```java
  @ApiModel("用户实体类")
  public class User {
  
      @ApiModelProperty("用户名")
      public String username;
      @ApiModelProperty("密码")
      public String password;
  
  }
  ```

### 11.7、接口文档配置

- 不能给整个控制器加注释

- 给控制器里面的某个接口加注释

  ```java
  @ApiOperation("Hello控制类")
  @GetMapping(value = "/hello2")
  public String hello2(@ApiParam("用户名") String username){
      return "hello"+username;
  }
  ```

- 给某个接口里面的参数加注释

  ```java
  @ApiOperation("Hello控制类")
  @GetMapping(value = "/hello2")
  public String hello2(@ApiParam("用户名") String username){
      return "hello"+username;
  }
  ```

### 11.8、总结

- 在线测试
- 实时更新
- 【注意点】在正式发布的时候，关闭Swagger！

## 12、任务

### 12.1、异步任务

**使用多线程体验：**

service层：

```java
@Service
public class AsyncService {

    public void hello(){
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("消息正在处理");
    }    
}
```

Controller层

```java
@RestController
public class AsycController {

    @Autowired
    AsyncService asyncService;

    @RequestMapping("/hello")
    public String hello(){
        asyncService.hello();
        return "OK";
    }
}
```

**使用异步任务来完成：**

Service层：

```java
@Service
public class AsyncService {

    //这个一个异步任务
    @Async
    public void hello(){
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("消息正在处理");
    }

}
```

Controller层

```java
@RestController
public class AsycController {

    @Autowired
    AsyncService asyncService;

    @RequestMapping("/hello")
    public String hello(){
        asyncService.hello();
        return "OK";
    }
}
```

**注：要在主类中开启异步任务注解**

```java
//开启异步注解功能
@EnableAsync
@SpringBootApplication
public class SpringBoot08TestApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBoot08TestApplication.class, args);
    }

}
```

### 12.2、邮件任务

- 导入相关依赖

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-mail</artifactId>
  </dependency>
  ```

- 在application.properties中配置

  ```properties
  spring.mail.username=2963624004@qq.com
  spring.mail.password=wsklzxzjitzadgca
  spring.mail.host=smtp.qq.com
  spring.mail.properties.mail.smtp.ssl.enable=true
  ```

- 测试

  ```java
  @SpringBootTest
  class SpringBoot08TestApplicationTests {
  
      @Autowired
      JavaMailSenderImpl mailSender;
  
      @Test
      void contextLoads() {
  
          //一个简单的邮件
          SimpleMailMessage mailMessage = new SimpleMailMessage();
          mailMessage.setSubject("你好");
          mailMessage.setText("谢谢你");
          mailMessage.setTo("2963624004@qq.com");
          mailMessage.setFrom("2963624004@qq.com");
  
          mailSender.send(mailMessage);
      }
  
  }
  ```

- 一个复杂的邮件发送

  ```java
  @Test
  void contextLoads2() throws MessagingException {
  
      //一个复杂的邮件
      MimeMessage mimeMessage = mailSender.createMimeMessage();
      //组装
      MimeMessageHelper helper = new MimeMessageHelper(mimeMessage,true);
  
      helper.setSubject("你好plus");
      //发送一个html段落
      helper.setText("<p style='color:red'>这个一个段落</p>",true);
      //附件
      helper.addAttachment("1.jpg",new File("C:\\Users\\朱小龙\\Desktop\\1.jpg"));
  
      helper.setTo("2963624004@qq.com");
      helper.setFrom("2963624004@qq.com");
  
      mailSender.send(mimeMessage);
  
  
  }
  ```

- **封装成一个方法**

### 12.3、定时任务

- 开启定时任务的注解

  ```java
  @EnableScheduling 
  ```

- 写一个定时任务【重点：cron表达式】

  ```java
  @Service
  public class ScheduledService {
  
      //在一个特定的时间执行这个方法
      //cron表达式
      //秒 分 时 日 月 周几
      @Scheduled(cron = "0 23 12 * * ?")
      public void hello(){
          System.out.println("Hello,你被执行了");
      }
  
  }
  ```


## 13、分布式 Dubbo Zookeeper

### 13.1、分布式

分布式系统是若干个独立计算机的集合，但是对于用户来说就像一个系统

- 服务器内部交互：http，RPC
- 最开始服务只是一个单机，当单机处理不了时，再考虑分布式

![image](https://dubbo.apache.org/imgs/user/dubbo-architecture-roadmap.jpg)

### 13.2、RPC

- Remote Procedure Call是远程过程调用，是一种进程间通信方式
- 它是一种技术的思想，而不是规范
- 它允许程序调用另一个地址空间(通常是共享网络的另一台机器上)的过程或函数，而不用程序员显式编码这个远程调用的细节
- 即程序员无论是调用本地的还是远程的函数，本质上编写的调用代码基本相同
- 本地过程调用就是类比同一台电脑A和B两个函数，A调用B；远程过程调用就类比A和B函数在不同的电脑中，互相调用
- RPC两个核心模块
  - 通讯
  - 序列化：数据传输需要转换【反序列化】

### 13.3、Dubbo

- Dubbo是一款高性能、轻量级的开源Java RPC框架
- 面向接口的远程方法调用
- 智能容错和负载均衡
- 服务自动注册和发现

![dubbo-architucture](https://dubbo.apache.org/imgs/user/dubbo-architecture.jpg)

### 13.4、Zookeeper：管理Hadoop Hive..

- Zookeeper是 Apache Hadoop 的子项目，是一个树型的目录服务
- 支持变更推送，适合作为 Dubbo 服务的注册中心，工业强度较高，可用于生产环境，并推荐使用

### 13.5、Zookeeper安装

- 使用镜像下载Zookeeper，并解压【注意：要下载带有bin的】
- 启动bin目录下的`zkServer.cmd`，注意加入`pause`排错
- 进入config目录加入`zoo.cfg`文件，再启动。
- 保持服务端启动，启动用户端`zkCli.cmd`，连接成功

### 13.6、Dubbo-admin安装

- Dubbo本身不是一个服务软件，实质上就是一个jar包，能够帮你的java程序连接到Zookeeper，并且利用Zookeeper消费，提供服务
- 为了让用户更好的管理监控众多的dubbo服务，官方提供了一个可视化的监控程序dubbo-admin

安装：

- 下载：https://github.com/apache/dubbo-admin/tree/master

- 解压并且进入目录：

  修改dubbo-admin\src\main\resources\application.properties指定zookeeper地址

  ```properties
  server.port=7001
  spring.velocity.cache=false
  spring.velocity.charset=UTF-8
  spring.velocity.layout-url=/templates/default.vm
  spring.messages.fallback-to-system-locale=false
  spring.messages.basename=i18n/message
  spring.root.password=root
  spring.guest.password=guest
  #注册中心的地址
  dubbo.registry.address=zookeeper://127.0.0.1:2181
  ```

- 在项目目录下`D:\Work\JAVA\dubbo-admin-master\dubbo-admin`打包`dubbo-admin`

  ```xshell
  mvn clean package -Dmaven.test.skip=true
  ```

- 在target目录下执行该jar包【注意：zookeeper需要在运行中】

  ```xshell
  java -jar xxx.jar
  ```

- 或者直接在idea中打开dubbo-admin项目，运行，账户密码需要在`application.properties`查看，进入dubbo-admin

  ![image-20210131132922027](http://fl.ljuuu.com/image-20210131132922027.png)

- 注意：

  - zookeeper：注册中心
  - dubbo-admin：是一个监控管理后台，也可以没有。可以查看我们注册了哪些服务，哪些服务被消费了
  - dubbo：jar包

### 13.7、整合dubbo+zookeeper

- 新建两个module，一个是provider，一个是consumer

- provider中编写对应的service接口和具体实现类

  ```java
  package com.ferron.service;
  
  public interface TicketService {
  
      public String getTicket();
  
  }
  ```

  ```java
  package com.ferron.service;
  
  public class TicketServiceImpl implements TicketService {
      @Override
      public String getTicket() {
          return "Ferron";
      }
  }
  ```

- provider导入相关依赖

  ```xml
  <!-- https://mvnrepository.com/artifact/org.apache.dubbo/dubbo-spring-boot-starter -->
  <dependency>
      <groupId>org.apache.dubbo</groupId>
      <artifactId>dubbo-spring-boot-starter</artifactId>
      <version>2.7.8</version>
  </dependency>
  <!-- https://mvnrepository.com/artifact/com.github.sgroschupf/zkclient -->
  <dependency>
      <groupId>com.github.sgroschupf</groupId>
      <artifactId>zkclient</artifactId>
      <version>0.1</version>
  </dependency>
  <!--日志会冲突-->
  <!--引入zookeeper-->
  <dependency>
      <groupId>org.apache.curator</groupId>
      <artifactId>curator-framework</artifactId>
      <version>2.12.0</version>
  </dependency>
  <dependency>
      <groupId>org.apache.curator</groupId>
      <artifactId>curator-recipes</artifactId>
      <version>2.12.0</version>
  </dependency>
  <dependency>
      <groupId>org.apache.zookeeper</groupId>
      <artifactId>zookeeper</artifactId>
      <version>3.4.14</version>
      <!--排除这个slf4j-log4j12-->
      <exclusions>
          <exclusion>
              <groupId>org.slf4j</groupId>
              <artifactId>slf4j-log4j12</artifactId>
          </exclusion>
      </exclusions>
  </dependency>
  ```

- 编写provider的配置文件

  【注意：这里的注册中心地址需要看你下载的zookeeper的application.properties中的注册中心地址】

  ```properties
  server.port=8001
  
  #服务应用名字
  dubbo.application.name=provider-server
  #注册中心地址
  dubbo.registry.address=zookeeper://127.0.0.1:2181
  #哪些服务要被注册
  dubbo.scan.base-packages=com.ferron.service
  ```

- 将provider的服务注册

  ```java
  package com.ferron.service;
  
  import org.apache.dubbo.config.annotation.DubboService;
  import org.springframework.stereotype.Component;
  
  //服务注册与发现
  @DubboService
  @Component //使用了dubbo后尽量不要使用@Service注解
  public class TicketServiceImpl implements TicketService {
      @Override
      public String getTicket() {
          return "Ferron";
      }
  }
  ```

- 启动zookeeper之后，进入dubbo-admin查看，发现我们的提供者注册成功

  ![image-20210131162152044](http://fl.ljuuu.com/image-20210131162152044.png)

- 到这里，服务者就算完成了，接下来进行消费者的配置，首先**导入依赖**

- 编写配置文件：名称及注册中心地址

  ```properties
  server.port=8002
  
  #消费者要去哪里拿，暴露自己的名字
  dubbo.application.name=consumer-server
  #注册中心地址
  dubbo.registry.address=zookeeper://127.0.0.1:2181
  
  ```

- 在consumer端编写实现类，从远程调用对应的实现类，但是前提是要本地有一样的接口

  【**注意：本地要有同样的包名以及同样的接口**】

  ```java
  package com.ferron.service;
  
  import org.apache.dubbo.config.annotation.DubboReference;
  import org.springframework.stereotype.Service;
  
  @Service  //使用spring的注解放到容器中
  public class UserService {
  
      //想拿到provider的ticket,要去注册中心拿到服务
      //引用， Pom坐标，可以定义路径相同的接口名
      @DubboReference
      TicketService ticketService;
  
      public void buyTicker(){
          String ticket = ticketService.getTicket();
          System.out.println("在注册中心拿到"+ticket);
      }
  
  }
  ```

- 测试

  ```java
  @Autowired
  UserService userService;
  
  
  @Test
  void contextLoads() {
  
      userService.buyTicker();
  
  }
  ```

### 13.8、使用步骤

**前提：zookeeper服务一直开启**

1. 提供者提供服务
   1. 导入依赖
   2. 配置注册中心的地址，以及服务发现名，和要扫描的包
   3. 在想要被注册的服务上面，增加一个注解`DubboService`
2. 消费者如何消费
   1. 导入依赖
   2. 配置注册中心的地址，以及自己的服务名
   3. 从远程注入服务，使用`@Reference`
