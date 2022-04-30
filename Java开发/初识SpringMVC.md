---
title: 初始SpringMVC
tags: Spring
categories: Java后端
description: SSM框架部分SpringMVC的学习内容，回顾了servlet实现controller，以及原生态SpringMVC和使用注解实现SpirngMVC
---


## 1、SpringMVC原理剖析

SpringMVC是基于Java实现MVC的轻量级Web框架。原来的MVC框架是由servlet，javabean，jsp构成。在SpringMVC中，一切的核心是DispatcherServlet。DispatcherServlet的本质也是一个servlet，用户的所有请求都会通过这个核心的DispatcherServlet，进而协调整个MVC框架。
SpringMVC的工作流程如下：

- 用户的请求首先会通过DispatcherServlet，DispatcherServlet接收到用户请求的url时，调用HandlerMapping查找对应url的handler，并向DispatcherServlet返回对应的HandlerExecution(handler)。
- DispatcherServlet接收到对应的HandlerExecution，将传递给HandlerAdapter，HandlerAdapter再让具体的控制器(Controller）去执行
- Controller执行业务代码后，将ModelAndView返回给HanderAdapter，HanderAdapter再传递给DispatcherServlet。
- DispatcherServlet接收到ModelAndView结果后，会调用ViewResolver去解析视图。
- DispatcherServlet根据ViewResolver的解析结果，调用具体视图响应给用户。

## 2、回顾Servlet

因为SpringMVC的核心终究还是Servlet，因此在实现SpringMVC之前，我们先来回顾一下之前我们是怎么实现Servlet的。

- 首先新建一个空Maven项目，删掉其src文件夹，作为本篇文章所有项目的父项目，在父项目中导入相关依赖，同时固定jdk的版本。（因为笔者每次新建项目时jdk默认为1.5，目前对于笔者来说直接使用代码固定jdk版本更方便）

  ```xml
  <dependencies>
      <!-- Srping依赖 -->
      <dependency>
          <groupId>org.springframework</groupId>
          <artifactId>spring-webmvc</artifactId>
          <version>5.2.9.RELEASE</version>
      </dependency>
      <!-- junit单元测试-->
      <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <version>4.12</version>
      </dependency>
      <!-- Servlet依赖 -->
      <dependency>
          <groupId>javax.servlet</groupId>
          <artifactId>javax.servlet-api</artifactId>
          <version>4.0.1</version>
      </dependency>
      <!-- JSP依赖 -->
      <dependency>
          <groupId>javax.servlet.jsp</groupId>
          <artifactId>jsp-api</artifactId>
          <version>2.1</version>
      </dependency>
      <!-- JSTL表达式依赖 -->
      <dependency>
          <groupId>javax.servlet.jsp.jstl</groupId>
          <artifactId>jstl-api</artifactId>
          <version>1.2</version>
      </dependency>
      <!-- Standard标签库 -->
      <dependency>
          <groupId>taglibs</groupId>
          <artifactId>standard</artifactId>
          <version>1.1.2</version>
      </dependency>
  </dependencies>
  <!--固定jdk版本-->
  <properties>
      <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
      <maven.compiler.encoding>UTF-8</maven.compiler.encoding>
      <java.version>11</java.version>
      <maven.compiler.source>11</maven.compiler.source>
      <maven.compiler.target>11</maven.compiler.target>
  </properties>
  
  ```

- 新建一个module，添加Web框架支持。

- 编写HelloServlet

  ```java
  public class HelloServlet extends HttpServlet {
     @Override
     protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
  
         //业务代码
         String msg = "这个Servlet的结果";
         req.getSession().setAttribute("msg",msg);
         //转发
         req.getRequestDispatcher("/WEB-INF/jsp/test.jsp").forward(req,resp);
  
     }
     @Override
     protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
         doGet(req, resp);
     }
  }
  ```

- 编写前端页面，前端页面提取msg。

- 配置Tomcat，测试。

  ![](http://fl.ljuuu.com/img/20210119151924806.png)

  

## 3、原生态SpringMVC

- 新建一个module，这里建立一个空的Maven项目，同时添加web框架支持。同时在项目结构处新建lib文件夹，手动将依赖导入。（因为不使用模板创建Maven项目有时依赖会无法导入，运行时网页会报404，下面使用注解实现SpringMVC做同样操作）

- 编写web.xml配置文件。在这里注册了SpringMVC的核心DispatcherServlet。因为SpringMVC已经帮我们写好DispatcherServlet，我们只需要直接在web.xml中注册就好了。注册的同时需要绑定springmvc的核心配置文件springmvc-servlet.xml。

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
           version="4.0">
  
      <servlet>
          <servlet-name>springmvc</servlet-name>
          <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
          <init-param>
              <param-name>contextConfigLocation</param-name>
              <param-value>classpath:springmvc-servlet.xml</param-value>
          </init-param>
          <load-on-startup>1</load-on-startup>
      </servlet>
      <servlet-mapping>
          <servlet-name>springmvc</servlet-name>
          <url-pattern>/</url-pattern>
      </servlet-mapping>
  
  </web-app>
  ```

- 编写核心配置文件springmvc-servlet.xml。在该配置文件中注册SpringMVC中的HandlerMapping，HanderAdapter，ViewResolver。因为这三个SpringMVC也已经帮我们写好了，所以只需要直接注册就行~

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          https://www.springframework.org/schema/beans/spring-beans.xsd">
      
      <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
      <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
          <property name="prefix" value="/WEB-INF/jsp/"/>
          <property name="suffix" value=".jsp"/>
      </bean>
      
  </beans>
  ```

- 编写一个控制器HelloController。ModelAndView是用来传递业务结果的。

  ```java
  public class HelloController implements Controller {
      public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
          ModelAndView mav = new ModelAndView();
  
          mav.addObject("msg","这是SpringMVC的结果");
          mav.setViewName("test");
  
          return mav;
      }
  }
  ```

- 编写完控制器之后，别忘了在springmvc-servlet.xml中注册。

  ```xml
  <bean id="/t2" class="com.ferron.controller.HelloController"/>
  ```

- 编写前端页面，前端页面提取msg。

- 配置Tomcat，测试

  ![](http://fl.ljuuu.com/img/20210119153755827.png)

## 4、使用注解实现SpringMVC

- 新建module，添加web框架支持。

- 编写同上的web.xml配置文件

- 编写核心配置文件springmvc-servlet.xml。

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
  
      <!--扫描包下的注解，让注解生效-->
      <context:component-scan base-package="com.ferron.controller"/>
      <mvc:annotation-driven/>
  
      <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
          <property name="prefix" value="/WEB-INF/jsp/"/>
          <property name="suffix" value=".jsp"/>
      </bean>
  
  </beans>
  ```

- 编写一个控制器HelloControllerAnno。使用@Controller注解将其标记为一个控制器，此时就默认注册到springmvc-servlet.xml中了，使用@RequestMapping指定其请求url。

  ```java
  @Controller
  public class HelloControllerAnno {
  
      @RequestMapping("/t3")
      public String test3(Model model){
          model.addAttribute("msg","这是SpringMVC注解的结果");
  
          return "test";
      }
  }
  ```

- 编写前端页面，提取msg。

- 配置Tomcat，测试

  ![](http://fl.ljuuu.com/img/20210119155942891.png)

## 5、结尾

笔者是一个喜欢编程的985理工男，金融科技专业。目前在学习Java全栈技术，过段时间可能会开始接触大数据架构、区块链技术、云计算架构和人工智能等技术的学习，这是我第一次写博客，不足之处希望大家多多谅解.

![](http://fl.ljuuu.com/img/20210119163223168.png)————————————————
版权声明：本文为CSDN博主「Ferron Zhu」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/Ferronzhu/article/details/112792747