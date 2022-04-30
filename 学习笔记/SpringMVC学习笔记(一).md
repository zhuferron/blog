---
title: SpringMVC学习笔记(一)
tags: SpringMVC
categories: 学习笔记
description: SpringMVC第一部分学习笔记，主要介绍了MVC三层架构，以及Spring中MVC如何实现、以及SpirngMVC的RequestMapping
---


## 1、回顾Servlet

原来的JavaWeb中的MVC三层架构模式：

M：模型层，dao和service

V：视图层，jsp

C：控制层，servlet

1. 从前端获取参数，然后编写对应的servlet

   HelloServlet：

   ```java
   @WebServlet(name = "HelloServlet")
   public class HelloServlet extends HttpServlet {
       protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
           doGet(request,response);
   
   
       }
   
       protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
           //1.获取前端参数
           String method = request.getParameter("method");
           if(method.equals("add")){
               request.getSession().setAttribute("msg","执行了add方法");
           }
           if(method.equals("delete")){
               request.getSession().setAttribute("msg","执行了delete方法");
           }
           //2.调用业务层
           //3.视图转发或者重定向
           request.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(request,response);
       }
   }
   ```

   test.jsp：

   ```jsp
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
   
   ${msg}
   
   </body>
   </html>
   ```

2. 注册servlet

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
   
       <servlet>
           <servlet-name>HelloServlet</servlet-name>
           <servlet-class>com.ferron.servlet.HelloServlet</servlet-class>
       </servlet>
       <servlet-mapping>
           <servlet-name>HelloServlet</servlet-name>
           <url-pattern>/hello</url-pattern>
       </servlet-mapping>
   
       <welcome-file-list>
           <welcome-file>index.jsp</welcome-file>
       </welcome-file-list>
       
   
   </web-app>
   ```

3. 编写前端页面，测试

   ```jsp
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
   
   <form action="/hello" method="post">
       <input type="text" name="method">
       <input type="submit">
   </form>
   
   </body>
   </html>
   ```

MVC框架要做的事情

- 将url映射到java类或java类的方法
- 封装用户提交的数据
- 处理请求--调用相关的业务处理--封装响应数据
- 将响应的数据进行渲染    .jsp/html等表示层数据

MVVM：

- M：model
- V：view
- VM：viewmodel，双向绑定

## 2、SpringMVC

### 2.1、概述

- SpringMVC是基于Java实现MVC的轻量级Web框架。

- 轻量级，简单易学
- 高效，基于请求响应的MVC框架
- 与Spring兼容性好，无缝结合
- 约定大于配置
- 功能强大：RESTful、数据验证、格式化、本地化、主题等
- 简洁灵活

### 2.2、中心控制器

Spring的web框架围绕DispatcherServlet设计。DispatcherServlet的作用就是将请求分发到不同的处理器。

**SpringMVC框架像许多其他MVC框架一样，以请求为驱动，围绕一个中心Servlet分派请求及提供其他功能，DispatcherServlet是一个实际的Servlet（它继承HttpServlet基类）**

![image-20210118094349458](http://fl.ljuuu.com/image-20210118094349458.png)

### 2.3、原理

![image-20210118095408690](http://fl.ljuuu.com/image-20210118095408690.png)

### 2.4、HelloMVC

1. 新建一个module，添加web支持

2. 注意是否有lib

3. 编写web.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
   
       <!--1.注册DispatcherServlet-->
       <servlet>
           <servlet-name>springmvc</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
           <!--关联一个springmvc的配置文件：springmvc-servlet.xml-->
           <init-param>
               <param-name>contextConfigLocation</param-name>
               <param-value>classpath:springmvc-servlet.xml</param-value>
           </init-param>
           <!--启动级别-->
           <load-on-startup>1</load-on-startup>
       </servlet>
       <!-- / 匹配所有的请求(不包括.jsp)-->
       <!-- /* 匹配所有的请求(包括.jsp)-->
       <servlet-mapping>
           <servlet-name>springmvc</servlet-name>
           <url-pattern>/</url-pattern>
       </servlet-mapping>
   
   </web-app>
   ```

4. 编写springmvc配置文件：springmvc-servlet.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd">
   
       <!--处理器映射器-->
       <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
       <!--处理器适配器-->
       <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
       <!--视图解析器： 未来其他的模板引擎：Thymeleaf，Freemarker-->
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
           <!--前缀-->
           <property name="prefix" value="/WEB-INF/jsp/"/>
           <!--后缀-->
           <property name="suffix" value=".jsp"/>
       </bean>
   
   
       <bean id="/test" class="com.ferron.controller.HelloController"/>
   
   
   
   </beans>
   ```

5. 编写控制器HelloController

   ```java
   package com.ferron.controller;
   
   import org.springframework.web.servlet.ModelAndView;
   import org.springframework.web.servlet.mvc.Controller;
   
   import javax.servlet.http.HttpServletRequest;
   import javax.servlet.http.HttpServletResponse;
   
   public class HelloController implements Controller {
       @Override
       public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
   
           //ModelAndView 模型和视图
           ModelAndView mv = new ModelAndView();
           //封装对象，放在ModelAndView中
           mv.addObject("msg","HelloSpringMVC!");
           //封装要跳转的视图，放在ModelAndView中
           mv.setViewName("hello"); //   WEB-INF/jsp/hello.jsp
           return mv;
   
   
       }
   }
   ```

6. 将HelloController注册到springmvc-servlet.xml

   ```java
   <bean id="/hello" class="com.ferron.controller.HelloController"/>
   ```

7. 编写前端跳转页面：WEB-INF/jsp/hello.jsp

   ```jsp
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
   ${msg}
   
   </body>
   </html>
   ```

8. 测试

### 2.5、原理剖析

1. 用户发送请求/hello
2. 请求通过前端控制器(DispatcherServlet)，前端控制器将请求委托给处理器HelloController
3. 处理器处理业务，同时返回ModelAndView给前端控制器
4. 前端控制器接收ModelAndView，同时读取其中的数据，渲染视图
5. 用户产生响应

### 2.6、执行流程

![image-20210118112056067](http://fl.ljuuu.com/image-20210118112056067.png)

1. DispatcherServlet表示前端控制器，是整个SpringMVC的控制中心。用户发出请求，DispatcherServlet接收请求并拦截请求
   - url：http://localhost:8080/SpringMVC/hello
   - http://local:8080是服务器域名
   - SpringMVC是部署在服务器的web站点
   - hello表示控制器
2. HandlerMapping为处理器映射。DispatcherServlet调用HandlerMapping，HandlerMapping根据请求url查找Handler
3. HandlerExecution表示具体的Handler，其主要作用是根据url查找控制器，入上url被查找控制器为：/hello
4. HandlerExecution将解析后的信息传递给DispatcherServlet，如解析控制器映射等
5. HandlerAdapter表示处理器适配器，其按照特定的规则去执行Handler
6. Handler让具体的Controller执行
7. Controller将具体的执行信息返回给HanderAdapter，如ModelAndView
8. HandlerAdapter将视图逻辑名或模型传递给DispatcherServlet
9. DispatcherServlet调用视图解析器（ViewResolver）来解析HandlerAdapter传递的逻辑视图名
10. 视图解析器将解析的逻辑视图名传给DispatcherServlet
11. DispatcherServlet根据视图解析器解析的视图结果，调用具体的视图
12. 最终视图呈现给用户

## 3、使用注解开发SpringMVC

步骤

1. 新建module

2. 防止资源过滤问题

3. 导入依赖

4. 配置web.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
   
   
       <!--1.注册DispatcherServlet，核心-->
       <servlet>
           <servlet-name>springmvc</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
           <!--关联一个springmvc的配置文件：springmvc-servlet.xml-->
           <init-param>
               <param-name>contextConfigLocation</param-name>
               <param-value>classpath:springmvc-servlet.xml</param-value>
           </init-param>
           <!--启动级别：1-->
           <load-on-startup>1</load-on-startup>
       </servlet>
       <servlet-mapping>
           <servlet-name>springmvc</servlet-name>
           <!-- / 匹配所有的请求(不包括.jsp)-->
           <!-- /* 匹配所有的请求(包括.jsp)-->
           <url-pattern>/</url-pattern>
       </servlet-mapping>
   
   </web-app>
   ```

5. 编写springmvc-servlet.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:mvc="http://www.springframework.org/schema/mvc"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           https://www.springframework.org/schema/context/spring-context.xsd
           http://www.springframework.org/schema/mvc
           https://www.springframework.org/schema/mvc/spring-mvc.xsd">
   
       <!--自动扫描包，让指定包下的注解生效，荣IOC容器统一管理-->
       <context:component-scan base-package="com.ferron.controller"/>
       <!-- 让SpringMVC不处理静态资源，.css .js .html .mp3 .mp4-->
       <mvc:default-servlet-handler/>
       <!--支持mvc注解驱动
       在spring中一般采用@RequestMapping注解来完成映射关系
       想要使@RequestMapping注解生效
       必须向上下文注册DefaultAnnotationHandlerMapping
       和一个AnnotationMethodHandlerAdapter实例
       这两个实例分别在类级别和方法级别处理
       而annotation-driven配置帮助我们自动完成上述两个实例的注入-->
       <mvc:annotation-driven/>
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
           <!--前缀-->
           <property name="prefix" value="/WEB-INF/jsp/"/>
           <!--后缀-->
           <property name="suffix" value=".jsp"/>
       </bean>
       
   </beans>
   ```

6. 创建Controller

   编写一个java控制类。

   ```java
   package com.ferron.controller;
   
   import org.springframework.stereotype.Controller;
   import org.springframework.ui.Model;
   import org.springframework.web.bind.annotation.RequestMapping;
   
   @Controller
   //该注解会让HelloController自动注册springmvc-servlet.xml
   @RequestMapping("/HelloController")
   public class HelloController {
   
       //真实访问地址：项目名/HelloController/hello
       @RequestMapping("/hello")
       public String Hello(Model model){
           //封装数据
           model.addAttribute("msg","Hello,SpringMVCAnnotation!");
           //  WEB-INF/jsp/hello.jsp
           return "hello";  //会被视图解析器处理
       }
   }
   ```

   - @Controller是为了让Spring IOC容器初始化时能自动扫描到
   - @RequestMapping是为了映射请求路径，这里因为类与方法上都有映射所有访问时应该是/HelloMapping/hello
   - 方法中声明Model类型的参数是为了把Action中的数据带到视图中
   - 方法返回的结果是视图的名称hello，加上配置文件中的前后缀变成WEB-INF/jsp/hello.jsp

7. 创建视图层hello.jsp

   ```jsp
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
   ${msg}
   
   </body>
   </html>
   ```

8. 测试

## 4、Controller

两种创建Controller的方法

方式一：实现Controller的接口

方式二：注解

## 5、RequestMapping

@RequestMapping注解用于映射url到控制器或特定的处理程序方法。可以用于类或方法上。

- 用在类上

  ```java
  @Controller
  @RequestMapping("/c3")
  public class ControllerTest3 {
  }
  ```

- 用在方法上

  ```java
  @RequestMapping("/t3")
      public String test1(Model model){
          model.addAttribute("msg","ControllerTest3");
          return "test";
  }
  ```


## 