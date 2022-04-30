---
title: JavaWeb学习笔记(三)
tags: JavaWeb
categories: 学习笔记
description: JavaWeb第三部分学习笔记，主要介绍了MVC三层架构的知识，以及过滤器和监听器
---

## 10、MVC三层架构


什么是MVC：

Servlet和jsp都可以写java代码：为了易于维护和使用；Servlet专注于处理请求，以及控制视图跳转；jsp专注于显示数据。

- Model：模型 ，实体类中对应的一个个字段 
- View ：视图，JSP页面
  - 展示数据
  - 提供可以供我们操作的请求
- Controller ：控制器，Servlet控制器。
  - 接收用户的请求
  - 响应给客户端内容
  - 重定向或者转发

### 10.1、以前架构

用户直接访问控制层，控制层就可以直接操作数据库

```java
servlet--->CRUD--->数据库
弊端：程序十分臃肿，不利于维护   
servlet的代码中：处理请求、响应、视图跳转、处理JDBC，处理业务代码，处理逻辑代码
    
架构：没有什么是加一层解决不了的！
```

![image-20201217142330933](http://fl.ljuuu.com/image-20201217142330933.png)

### 10.2、三层架构

![image-20201217144918428](http://fl.ljuuu.com/image-20201217144918428.png)



Model：

- 业务处理：业务逻辑(Service)
- 数据持久层：CRUD(Dao)

View：

- 展示数据
- 提供链接发起Servlet请求(a，form，img....)

Controller：(Servlet)

- 接收用户的请求：(req：请求参数、Session信息...)
- 交给业务层处理对于的代码
- 控制视图跳转

```java
登录--->接收用户的登录请求--->处理用户的请求(获取用户登录的参数，username，password)--->交给业务层处理登录业务(判断用户名密码是否正确：事务)--->Dao层查询用户名和密码是否正确--->数据库
```

## 11、Filter（重点）

Shiro

Filter：过滤器，用来过滤网站的数据；

- 处理中文乱码
- 登录验证....

![image-20201217150332564](http://fl.ljuuu.com/image-20201217150332564.png)



Filter开发步骤：

1. 导包

2. 编写Filter

   1. 导包不要错
   2. 实现Filter接口，重写方法即可

   ```java
   public class CharacterEncodingFilter implements Filter {
       //初始化:web服务器启动，都已经启动了，随时等待过滤对象
       public void init(FilterConfig filterConfig) throws ServletException {
           System.out.println("CharacterEncodingFilter初始化");
       }
       //Chain:链
       //1.过滤中的所有代码，在过滤特定请求时都会执行
       //2.必须要让过滤器继续通过：chain.doFilter(request,response)
       public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
           request.setCharacterEncoding("utf-8");
           response.setCharacterEncoding("utf-8");
           response.setContentType("text/html;charset=UTF-8");
   
           System.out.println("CharacterEncodingFilter执行前...");
           chain.doFilter(request,response); //让请求继续走，如果不写，程序到这里就被拦截停止
           System.out.println("CharacterEncodingFilter执行后...");
       }
       //销毁:关闭服务器时销毁
       public void destroy() {
           System.out.println("CharacterEncodingFilter销毁");
       }
   }
   ```

3. 在web.xml中配置Filter过滤器

   ```java
   <filter>
           <filter-name>CharacterEncodingFilter</filter-name>
           <filter-class>com.ferron.filter.CharacterEncodingFilter</filter-class>
       </filter>
       <filter-mapping>
           <filter-name>CharacterEncodingFilter</filter-name>
           <!--只要是/service的任何请求，都会经过这个过滤器-->
           <url-pattern>/servlet/*</url-pattern>
       </filter-mapping>
   ```

## 12、监听器

实现一个监听器的接口；(有N种)

1. 编写一个监听器

   1. 实现监听器里面的接口

   ```java
   //统计网站在线人数：统计session
   public class OnlineCountListener implements HttpSessionListener {
       @Override
       //创建session监听
       //一旦创建一个Session就会触发一次这个
       public void sessionCreated(HttpSessionEvent se) {
           ServletContext ctx = se.getSession().getServletContext();
   
           System.out.println(se.getSession().getId());
   
           Integer onlineCount = (Integer) ctx.getAttribute("OnlineCount");
   
           if(onlineCount == null){
               onlineCount = new Integer(1);
           }else{
               int count = onlineCount.intValue();
               onlineCount = new Integer(count+1);
           }
           ctx.setAttribute("OnlineCount",onlineCount);
   
       }
   
       @Override
       //销毁session监听
       //一旦销毁一个Session就会触发一次这个
       public void sessionDestroyed(HttpSessionEvent se) {
           ServletContext ctx = se.getSession().getServletContext();
           Object onlineCount = ctx.getAttribute("OnlineCount");
   
           if(onlineCount == null){
               onlineCount = new Integer(0);
           }else{
               int count = (int) onlineCount;
               onlineCount = new Integer(count-1);
           }
           ctx.setAttribute("OnlineCount",onlineCount);
   
       }
   }
   ```

2. web.xml中配置监听器

   ```java
   <listener>
           <listener-class>com.ferron.listen.OnlineCountListener</listener-class>
       </listener>
   ```

3. 看情况是否使用

## 13、过滤器，监听器的常见应用

用户登录之后才能进入主页！用户注销之后就不能进入

1. 用户登录之后，向session放入用户的数据

2. 进入主页的时候要判断用户是否登录；要求：在过滤器中实现

   ```java
   HttpServletRequest request = (HttpServletRequest) servletRequest;
           HttpServletResponse response = (HttpServletResponse) servletResponse;
   
           if(request.getSession().getAttribute(Constant.USER_SESSION) == null){
               response.sendRedirect("/error.jsp");
           }
   
           filterChain.doFilter(servletRequest,servletResponse);
   ```

## 14、常用代码

配置类：

pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.ferron</groupId>
  <artifactId>javaweb-session-cookie</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>
</project>
```

web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
         http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         id="WebApp_ID" version="4.0"
         metadata-complete="true">
    

</web-app>
```

依赖类：

```java
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
<!-- 下面两个包一起使用 -->
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
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.47</version>
</dependency>

```

操作类：

```java
//解决中文乱码
resp.setHeader("content-type","text/html;charset=UTF-8");
req.setCharacterEncoding("utf-8");
resp.setCharacterEncoding("utf-8");
```

固定java版本

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.encoding>UTF-8</maven.compiler.encoding>
    <java.version>11</java.version>
    <maven.compiler.source>11</maven.compiler.source>
    <maven.compiler.target>11</maven.compiler.target>
</properties>
```

资源过滤问题

```xml
<!--在build中配置resources,来防止我们资源导出失败的问题-->
<build>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
    </resources>
</build>
```



