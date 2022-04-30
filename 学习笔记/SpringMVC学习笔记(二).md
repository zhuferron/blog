---
title: SpringMVC学习笔记(一)
tags: SpringMVC
categories: 学习笔记
description: SpringMVC第二部分学习笔记，主要介绍了的Restful风格、转发和重定向、json和Ajax、拦截器、文件上传和下载等内容
---

## 6、RestFul风格

### 6.1、概念

Restful就是一个资源定位及资源操作的风格。不是标准也不是协议，只是一种风格

### 6.2、功能

- 资源：互联网所有的事物都可以被抽象成资源
- 资源操作：使用Post、Delete、Put、Get，使用不同方法对资源进行操作
- 分别对应添加、删除、修改、查询

### 6.3、测试

1. 新建一个类RestFulController

   ```java
   @Controller
   public class RestFulController {
   }
   ```

2. 在springMVC中可以使用**@PathVariable**注解，让方法参数的值对应绑定到一个URI模板变量上

   ```java
   //原来：http://localhost:8080/add?a=1&b=1
   //Restful:http://localhost:8080/add/a/b
   @RequestMapping("/add/{a}/{b}")
   public String test1(@PathVariable int a, @PathVariable int b, Model model){
   
       int res = a+b;
       model.addAttribute("msg","结果为"+res);
   
       return "test";
   }
   ```

3. 在@RequestMapping注解中使用特定的method，可以指定特定的方法。

   ```java
   @RequestMapping(value = "/add/{a}/{b}",method = RequestMethod.GET)
   public String test1(@PathVariable int a, @PathVariable int b, Model model){
   
       int res = a+b;
       model.addAttribute("msg","结果为"+res);
   
       return "test";
   }
   ```

4. url的复用

   正常来说，走/add/1/2是使用get请求走结果1，但是经过post请求的表单走post方法，得到结果2

   ```java
   @GetMapping("/add/{a}/{b}")
   public String test2(@PathVariable int a, @PathVariable int b, Model model){
   
       int res = a+b;
       model.addAttribute("msg","结果1为"+res);
   
       return "test";
   }
   @PostMapping("/add/{a}/{b}")
   public String test3(@PathVariable int a, @PathVariable int b, Model model){
   
       int res = a+b;
       model.addAttribute("msg","结果2为"+res);
   
       return "test";
   }
   ```

### 6.4、总结

- 实现方法一：使用@RequestMapping注解，在注解中增加method参数

- 实现方法二：**使用衍生注解**

  ```java
  @GetMapping
  @PostMapping
  @PutMapping
  @DeleteMapping
  @PatchMapping
  ```

## 7、转发和重定向

在有视图解析器的情况下：

- 转发

  ```java
  @RequestMapping("/m1/t1")
  public String test(Model model){
      model.addAttribute("msg", "forward");
      return "test";
  }
  ```

- 重定向：需要在返回字符串中加上**redirect:**

  ```java
  @RequestMapping("/m1/t2")
  public String test2(Model model){
      model.addAttribute("msg", "forward");
      return "redirect:/index.jsp";
  }
  ```

## 8、数据处理

原本在Servlet中，使用?后携带参数来处理数据，而在SpringMVC中，有以下几种方式处理数据

- 提交的域名称和处理方法的参数名一致

  提交数据：http://localhost:8080/hello?name=ferron

  ```java
  //http://localhost:8080/hello?name=ferron
  @GetMapping("/t1")
  public String test1(String name, Model model){
  
      //1.接受前端参数
      System.out.println("接收前端的参数为"+name);
      //2.将返回的结果传递给前端
      model.addAttribute("msg", name);
      //3.视图跳转
      return "test";
  }
  ```

- 提交的域名称和处理方法的参数名不一致

  使用@RequestParam注解定义前端接收的参数，**注意：最好都加上**

  ```java
  //http://localhost:8080/hello?username=ferron
  @GetMapping("/t1")
  public String test1(@RequestParam("username") String name, Model model){
  
      //1.接受前端参数
      System.out.println("接收前端的参数为"+name);
      //2.将返回的结果传递给前端
      model.addAttribute("msg", name);
      //3.视图跳转
      return "test";
  }
  ```

- 前端传递的参数是一个对象

  提交数据为：http://localhost:8080/user/t2?id=1&name=ferron&age=3

  ```java
  @GetMapping("/t2")
  public String test2(User user, Model model){
      //1.接受前端参数
      System.out.println(user);
  
      return "test";
  
  }
  ```

关于Model和ModelMap：

- Model：精简版的ModelMap，大部分情况下都是使用Model
- ModelMap：继承了LinkedHashMap
- 使用起来，Model和ModelMap基本一样

## 9、乱码问题

1. 编写一个表单网页

   ```jsp
   <%@ page contentType="text/html;charset=UTF-8" language="java" %>
   <html>
   <head>
       <title>Title</title>
   </head>
   <body>
   <form action="/e/t1" method="post">
       <input type="text" name="name">
       <input type="submit">
   
   </form>
   
   </body>
   </html>
   ```

2. 编写一个Controller

   ```java
   package com.ferron.controller;
   
   import org.springframework.stereotype.Controller;
   import org.springframework.ui.Model;
   import org.springframework.web.bind.annotation.PostMapping;
   
   @Controller
   public class EncodingController {
   
       @PostMapping("/e/t1")
       public String test1(String name, Model model){
           System.out.println(name);
           model.addAttribute("msg",name);
           return "test";
       }
   }
   ```

3. 发现出现乱码

4. 使用过滤器解决乱码问题

   编写过滤器：

   ```java
   package com.ferron.filter;
   
   import javax.servlet.*;
   import java.io.IOException;
   
   public class EncodingFilter implements Filter {
   
       @Override
       public void init(FilterConfig filterConfig) throws ServletException {
       }
   
       @Override
       public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
           servletRequest.setCharacterEncoding("utf-8");
           servletResponse.setCharacterEncoding("utf-8");
   
           filterChain.doFilter(servletRequest,servletResponse);
       }
   
       @Override
       public void destroy() {
       }
   }
   ```

   注册过滤器

   ```xml
   <!--配置字符编码过滤器-->
   <filter>
       <filter-name>encoding</filter-name>
       <filter-class>com.ferron.filter.EncodingFilter</filter-class>
   </filter>
   <filter-mapping>
       <filter-name>encoding</filter-name>
       <url-pattern>/</url-pattern>
   </filter-mapping>
   ```

   **注意：过滤器只能过滤get方法请求的参数，使用post方法仍然会乱码**

**解决方法**：SpringMVC已经写好的乱码过滤器，我们直接配置就好！

```xml
<!--SpringMVC自带的乱码过滤-->
<filter>
    <filter-name>encoding</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>encoding</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

## 10、json

前后端分离：使用**json格式**的数据来传输。

后端部署后端，提供接口，提供数据

前端独立部署，负责渲染后端的数据

### 10.1、概述

- JSON（JavaScript Object Notation）是一种轻量级的数据交换格式
- 采用完全独立于编程语言的文本格式来存储和表示数据
- 简洁和清晰的层次结构

### 10.2、特点

- 对象表示键值对，数据由逗号分隔
- 花括号保存对象
- 方括号表示数组

### 10.3、js对象与json对象互相转化

```html
<script type="text/javascript">
    //编写一个JavaScript对象  ES6
    var user = {
        name : "ferron",
        age : 3,
        sex : "男"
    }
    console.log(user)
    
    //将js对象转化成json对象
    var json = JSON.stringify(user);
    console.log(json)
    
    console.log("===============")
    
    //将json对象转化为js对象
    var obj = JSON.parse(json);
    console.log(obj)

</script>
```

### 10.4、在Controller实现传输json

1. 新建module，添加lib，导入Jackson(json解析器)

2. 解决json乱码问题

   springmvc-servlet.xml

   ```xml
   <mvc:annotation-driven>
       <!--解决json乱码-->
       <mvc:message-converters register-defaults="true">
           <bean class="org.springframework.http.converter.StringHttpMessageConverter">
               <constructor-arg value="UTF-8"/>
           </bean>
           <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
               <property name="objectMapper">
                   <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                       <property name="failOnEmptyBeans" value="false"/>
                   </bean>
               </property>
           </bean>
       </mvc:message-converters>
   </mvc:annotation-driven>
   ```

3. 编写一个User实体类

4. 编写一个Controller控制类

5. 使用jackson将java中对象转化成字符串

   ```java
   //jackson， ObjectMapper
   ObjectMapper mapper = new ObjectMapper();
   //创建一个对象
   User user = new User("ferron1", 3, "男");
   //将对象变成字符串
   String str = mapper.writeValueAsString(user);
   ```

小知识点：**在前后端分离情况下，不走视图解析器，只返回字符串**

```
@RestController  在类上使用该注解，该类下所有方法只会返回字符串，不走视图解析器
@ResponseBody    在方法上使用该注解，该方法只会放回字符串，不走视图解析器
```

### 10.5、传输json列表

```java
@RequestMapping("/j2")
public String json2() throws JsonProcessingException {
	//创建一个列表
    List<User> userList = new ArrayList<>();

    User user1 = new User("ferron1", 3, "男");
    User user2 = new User("ferron1", 3, "男");
    User user3 = new User("ferron1", 3, "男");
    User user4 = new User("ferron1", 3, "男");

    userList.add(user1);
    userList.add(user2);
    userList.add(user3);
    userList.add(user4);

    return new ObjectMapper().writeValueAsString(userList);
}
```

### 10.6、传输时间

Java自定义

```java
@RequestMapping("/j3")
    public String json3() throws JsonProcessingException {
        //ObjectMapper，时间解析后的默认格式为：Timestamp
        Date date = new Date();
        //时间格式转化
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

        return new ObjectMapper().writeValueAsString(sdf.format(date));
    }
```

通过Jackson解析

```java
@RequestMapping("/j3")
public String json3() throws JsonProcessingException {

    ObjectMapper mapper = new ObjectMapper();
    mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS,false);

    //时间格式转化
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    mapper.setDateFormat(sdf);
    //ObjectMapper，时间解析后的默认格式为：Timestamp
    Date date = new Date();
    
    return mapper.writeValueAsString(date);
}
```

### 10.7、实现代码复用

Utils包下的JsonUtils工具类：

```java
public class JsonUtils {

    //实现相互调用
    public static String getJson(Object object){
        return getJson(object,"yyyy-MM-dd HH:mm:ss");
    }
    
    public static String getJson(Object object, String dateFormat){

        ObjectMapper mapper = new ObjectMapper();
        mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS,false);

        SimpleDateFormat sdf = new SimpleDateFormat(dateFormat);
        mapper.setDateFormat(sdf);

        try {
            return mapper.writeValueAsString(object);
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }
        return null;
    }
    
}
```

重写原来的代码：

传输列表

```java
@RequestMapping("/j2")
public String json2() throws JsonProcessingException {

    List<User> userList = new ArrayList<>();

    User user1 = new User("ferron1", 3, "男");
    User user2 = new User("ferron1", 3, "男");
    User user3 = new User("ferron1", 3, "男");
    User user4 = new User("ferron1", 3, "男");

    userList.add(user1);
    userList.add(user2);
    userList.add(user3);
    userList.add(user4);

    return JsonUtils.getJson(userList);
}
```

传输时间

```java
@RequestMapping("/j3")
public String json3() throws JsonProcessingException {

    Date date = new Date();

    return JsonUtils.getJson(date,"yyyy-MM-dd HH:mm:ss");
}
```

### 10.8、FastJson

1. 导包

   ```xml
   <!-- https://mvnrepository.com/artifact/com.alibaba/fastjson -->
   <dependency>
       <groupId>com.alibaba</groupId>
       <artifactId>fastjson</artifactId>
       <version>1.2.70</version>
   </dependency>
   ```

2. 测试

   ```java
   @RequestMapping("/j4")
   public String json4() throws JsonProcessingException {
   
       List<User> userList = new ArrayList<>();
   
       User user1 = new User("ferron1", 3, "男");
       User user2 = new User("ferron1", 3, "男");
       User user3 = new User("ferron1", 3, "男");
       User user4 = new User("ferron1", 3, "男");
   
       userList.add(user1);
       userList.add(user2);
       userList.add(user3);
       userList.add(user4);
   
       return JSON.toJSONString(userList);
   }
   ```

## 11、Ajax

### 11.1、概述

AJAX（Asynchronous JavaScript and XML），异步的JS和XML，是一种无需刷新页面的情况下，局部的刷新页面数据的技术。**XHR**就是AJAX的请求。

### 11.2、利用jQuery实现Ajax，初体验

![image-20210121103107703](http://qpuvd47ie.hn-bkt.clouddn.com/image-20210121103107703.png)

1. 在applicationContext.xml中配置静态资源过滤

   ```xml
   <mvc:default-servlet-handler/>
   ```

2. 在web目录下创建statics.js文件夹，加入jquery.js的源码，并且在html页面中导入

   ```html
   <script src="${pageContext.request.contextPath}/statics/js/jquery.js"></script>
   ```

3. 前端写一个输入框，设置失去焦点事件

   ```html
   <%--失去焦点的时候，发起一个请求（携带信息）到后台--%>
   用户名：<input type="text" id="username" onblur="a()">
   ```

4. 利用script绑定一个失去焦点事件，发送出一个ajax请求

   ```html
   <script>
       function a() {
           $.post({
               url:"${pageContext.request.contextPath}/a1",
               data:{"name":$("#username").val()}, 
               success:function (data) {
                   alert(data);
               },
               error:function () {
   
               }
           })
       }
   </script>
   ```

5. 在后端写一个对应的a1请求

   ```java
   @RequestMapping("a1")
   public void a1(String name, HttpServletResponse response) throws IOException {
       System.out.println("a1");
       if("ferron".equals(name)) {
           response.getWriter().print("true");
       }else {
           response.getWriter().print("false");
       }
   }
   ```

### 11.3、真实Ajax+Json

1. 写一个实体类

2. 写后端的接口

   ```java
   @RequestMapping("/a2")
   public List<User> a2(){
   
       List<User> userList = new ArrayList<>();
   
       //添加数据
       userList.add(new User("ferron1",5,"男"));
       userList.add(new User("ferron2",5,"男"));
       userList.add(new User("ferron3",5,"男"));
       userList.add(new User("ferron4",5,"男"));
   
       return userList;
   
   }
   ```

3. 写一个html网页，在一个按钮处添加点击事件

4. 点击时，发送出一个ajax请求，携带数据返回

   ```javascript
   <script>
       $(function () {
       $("#btn").click(function () {
           // 简写：$.post(url,param[可以省略],success)
           $.post("${pageContext.request.contextPath}/a2",function (data) {
               var html="";
   
               for (var i = 0; i < data.length; i++) {
                   html+="<tr>" +
                       "<td>"+ data[i].name +"</td>" +
                       "<td>"+ data[i].age +"</td>" +
                       "<td>"+ data[i].sex +"</td>" +
                       "</tr>"
               }
   
               $("#content").html(html);
   
           })
       })
   })
   
   </script>
   ```

5. 前端接收到数据并且显示

### 11.4、使用ajax实现用户名登录

1. 编写登录页面，两个输入框

   ```html
   <body>
   <p>
       账户：<input type="text" id="name" onblur="a1()">
       <span id="userInfo"></span>
   </p>
   <p>
       密码：<input type="text" id="pwd" onblur="a2()">
       <span id="pwdInfo"></span>
   </p>
   
   </body>
   ```

2. 编写接口

   ```java
   @RequestMapping("/a3")
   public String a3(String name, String pwd){
       String msg = "";
       if(name != null){
           //这些数据应该在数据库的
           if("admin".equals(name)){
               msg="ok";
           }else{
               msg="用户名有误";
           }
       }
       if(pwd != null){
           //这些数据应该在数据库的
           if("123456".equals(pwd)){
               msg="ok";
           }else{
               msg="密码有误";
           }
       }
       return msg;
   }
   ```

3. 编写ajax请求

   ```javascript
   <script>
       function a1() {
       $.post({
           url:"${pageContext.request.contextPath}/a3",
           data:{"name":$("#name").val()},
           success:function (data) {
               if(data.toString() === 'ok'){
                   $("#userInfo").css("color","green");
               }else {
                   $("#userInfo").css("color","red");
               }
               $("#userInfo").html(data);
           }
       })
   }
   function a2() {
       $.post({
           url:"${pageContext.request.contextPath}/a3",
           data:{"pwd":$("#pwd").val()},
           success:function (data) {
               if(data.toString() === 'ok'){
                   $("#pwdInfo").css("color","green");
               }else {
                   $("#pwdInfo").css("color","red");
               }
               $("#pwdInfo").html(data);
          }
       })
   }
   </script>
   ```

## 12、拦截器

### 12.1、概述

- 拦截器是AOP思想的体现，是横切进去的
- 拦截器是SpringMVC框架的，只有SpringMVC框架的工程才可以使用
- 拦截器只会拦截访问的控制器方法，对于jsp/html/css/image/js是不会拦截的

### 12.2、自定义拦截器

1. 建立module，添加web框架支持，添加lib，编写web.xml，applicationContext.xml以及测试controller。

   ```java
   @RestController
   public class TestController {
   
       @RequestMapping("/t1")
       public String test(){
           return "OK";
       }
   }
   ```

2. 建立com.ferron.config文件夹，编写MyInterceptor

   ```java
   public class MyInterceptor implements HandlerInterceptor {
   
       //return true； 执行下一个拦截器，放行
       //return false； 阻止下一个拦截器
       @Override
       public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
           System.out.println("============处理前============");
           return true;
       }
   
       @Override
       public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
           System.out.println("============处理后============");
       }
   
       @Override
       public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
           System.out.println("============清理============");
       }
   }
   ```

3. 配置拦截器

   ```xml
   <!--拦截器配置-->
   <mvc:interceptors>
       <mvc:interceptor>
           <!--/**表示所有请求，/admin/**表示admin下的所有请求都拦截-->
           <mvc:mapping path="/**"/>
           <bean class="com.ferron.config.MyInterceptor"/>
       </mvc:interceptor>
   </mvc:interceptors>
   ```

4. 测试结果

   因为拦截器是AOP思想的体现，因此当重新请求/t1的时候，会被拦截器拦截，并且处理。

### 12.3、使用拦截器实现用户登录

1. 在index.jsp页面写一个登录页面按钮和首页按钮

   ```jsp
   <h1>
       <a href="${pageContext.request.contextPath}/user/goLogin">登录页面</a>
   </h1>
   <h1>
       <a href="${pageContext.request.contextPath}/user/main">首页</a>
   </h1>
   ```

2. 写对应的接口

   ```java
   @RequestMapping("/main")
   public String main(){
       return "main";
   }
   @RequestMapping("/goLogin")
   public String login(){
       return "login";
   }
   ```

3. 编写login.jsp页面

   ```jsp
   <form action="${pageContext.request.contextPath}/user/login" method="post">
       账户：<input type="text" name="username"/>
       密码：<input type="text" name="password"/>
       <input type="submit" value="提交">
   </form>
   ```

4. 写login接口

   - 将用户的信息存在session中

   ```java
   @RequestMapping("/login")
   public String login(HttpSession session, String username, String password, Model model){
       //把用户的信息存在session中：
       session.setAttribute("userLoginInfo",username);
       model.addAttribute("username",username);
       return "main";
   }
   ```

5. 编写拦截器

   - 放行去登录页面的请求和登录的请求
   - 当session中存在userLoginInfo的时候放行
   - 否则，转发到登录页面。

   ```java
   public class LoginInterceptor implements HandlerInterceptor {
   
       @Override
       public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
           HttpSession session = request.getSession();
           //判断什么情况是登录的：
           //1.本身就在登录页面
           if(request.getRequestURI().contains("goLogin")){
               return true;
           }
           //2.说明我在提交登录
           if(request.getRequestURI().contains("login")){
               return true;
           }
   
           //3.session中有用户信息
           if(session.getAttribute("userLoginInfo") != null){
               return true;
           }
           //如果没有登录:
           request.getRequestDispatcher("/WEB-INF/jsp/login.jsp").forward(request,response);
           return false;
       }
   }
   ```

6. 注册拦截器，拦截/user下的一切请求

7. 在main.jsp页面下增加注销按钮

8. 编写goOut接口

   ```java
   @RequestMapping("/goOut")
   public String goOut(HttpSession session){
   
       session.removeAttribute("userLoginInfo");
       return "main";
   }
   ```

## 13、文件上传和下载

1. 导入相应依赖

   ```xml
   <!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
   <dependency>
       <groupId>commons-fileupload</groupId>
       <artifactId>commons-fileupload</artifactId>
       <version>1.3.3</version>
   </dependency>
   ```

2. 编写前端页面

   ```jsp
   <form action="" enctype="multipart/form-data" method="post">
     <input type="file" name="file"/>
     <input type="submit" value="upload">
   </form>
   ```

3. 编写对应接口

   ```java
   @RestController
   public class FileController {
   
       //@RequestParam("file") 将name=file控件得到的文件封装成commonsMultipartFile对象
       //批量上传CommonsMultipartFile则为数组即可
       @RequestMapping("/upload")
       public String fileUpload(@RequestParam("file") CommonsMultipartFile file, HttpServletRequest request) throws IOException {
   
           //获取文件名
           String uploadFileName = file.getOriginalFilename();
           //如果文件名为空，返回首页
           if("".equals(uploadFileName)){
               return "redirect:/index.jsp";
           }
           System.out.println("上传文件名："+uploadFileName);
   
           //上传路径保存设置
           String path = request.getServletContext().getRealPath("/upload");
           //如果路径不存在，创建一个
           File realPath = new File(path);
           if(!realPath.exists()){
               realPath.mkdir();
           }
           System.out.println("上传文件保存地址："+realPath);
   
           InputStream is = file.getInputStream(); //文件输入流
           FileOutputStream os = new FileOutputStream(new File(realPath, uploadFileName)); //文件输出流
   
           //读取写出
           int len=0;
           byte[] buffer = new byte[1024];
           while ((len=is.read(buffer)) != -1){
               os.write(buffer,0,len);
               os.flush();
           }
           os.close();
           is.close();
           return "redirect:/index.jsp";
       }
   
   
   
   }
   ```

4. 编写文件上传配置

   ```xml
   <!--文件上传配置-->
   <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
       <!--请求的编码格式，必须和jsp的pageEncoding属性一致，以便正确读取表单的内容，默认为ISO-8859-1-->
       <property name="defaultEncoding" value="utf-8"/>
       <!--上传文件大小上限，单位为字节：（10485760=10M）-->
       <property name="maxUploadSize" value="10485760"/>
       <property name="maxInMemorySize" value="40960"/>
   </bean>
   ```

### 13.1、文件下载接口

```java
@RequestMapping("/download")
public String downloads(HttpServletResponse response, HttpServletRequest request) throws Exception{

    //要下载的图片地址
    String path = request.getServletContext().getRealPath("/upload");
    String fileName = "基础语法.jpg";

    //1.设置响应头
    response.reset();
    response.setCharacterEncoding("UTF-8");
    response.setContentType("multipart/form-data");
    //设置响应头
    response.setHeader("Content-Disposition","attachment;fileName"+ URLEncoder.encode(fileName,"UTF-8"));

    File file = new File(path, fileName);
    //2.读取文件
    FileInputStream input = new FileInputStream(file);
    ServletOutputStream out = response.getOutputStream();

    byte[] buff = new byte[1024];
    int index=0;

    while ((index = input.read(buff)) != -1){
        out.write(buff,0,index);
        out.flush();
    }

    out.close();
    input.close();

    return null;
}
```

## 14、核心代码

spring配置文件头代码

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
    
    <context:component-scan base-package="com.ferron.controller"/>
    <mvc:default-servlet-handler/>
    <mvc:annotation-driven/>
    
    
</beans>
```

固定web.xml配置

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

    <!--解决json乱码问题-->
    <filter>
        <filter-name>encoding</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encoding</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>


</web-app>
```

springmvc-servlet.xml

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

    <context:component-scan base-package="com.ferron.controller"/>
    <mvc:default-servlet-handler/>
    <mvc:annotation-driven>
        <!--解决json乱码-->
        <mvc:message-converters register-defaults="true">
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <constructor-arg value="UTF-8"/>
            </bean>
            <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
                <property name="objectMapper">
                    <bean class="org.springframework.http.converter.json.Jackson2ObjectMapperFactoryBean">
                        <property name="failOnEmptyBeans" value="false"/>
                    </bean>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>

    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>


</beans>
```
