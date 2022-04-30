# JavaWeb

## 1、基本概念

#### 1.1、前言

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

#### 1.2、web应用程序

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

#### 1.3、静态web

- .htm,html,这些都是网页的后缀，如果服务器上一直存在这些东西，我们就可以直接进行读取。
- 静态web存在的缺点
  - web页面无法动态更新，所有用户看到都是同一个页面
    - 轮播图，点击特效：伪动态
    - JavaScript
  - 它无法和数据库交互(数据无法持久化，用户无法交互)

#### 1.4、动态web

页面会动态展示：web的展示会因人而异

缺点：

- 假如服务器的动态web资源出现了错误，我们需要重新编写我们的**后台程序**，重新发布；
  - 停机维护

优点

- web可以动态更新
- 可以与数据库交互(数据持久化：注册，商品信息，用户信息)

## 2、web服务器

#### 2.1、技术讲解

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

#### 2.2、web服务器

服务器是一种被动的操作，用来处理用户的一些请求，和给用户一些相应信息

**IIS**：微软的，ASP,....微软自带的

**Tomcat**：面向百度编程。

**工作3-5年之后，可以尝试手写Tomcat服务器**

安装Tomcat：

1. 安装 or 解压
2. 了解配置文件及其目录结构
3. 这个东西的作用

## 3、Tomcat

#### 3.1、安装Tomcat

官网安装

#### 3.2、配置和目录

启动和关闭Tomcat

访问测试：localhost8080

1. java环境变量没有配置
2. 闪退问题：需要配置兼容性
3. 乱码问题：配置文件中设置

#### 3.3、配置

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

#### 3.4、发布一个网站

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

#### 4.1、什么是Http

http(超文本传输协议)是一个简单的请求-响应协议，它通常运行在TCP之上。

- 文本：html，字符串....
- 超文本：图片，音乐，视频，定位，地图....
- Http：80 
- Https:443

#### 4.2、两个时代

- http1.0
  - HTTP/1.0：客户端可以与web服务器连接后，只能获得一个web资源，断开连接
- http2.0
  - HTTP/1.1：客户端可以与web服务器连接后，可以获得多个web资源。

#### 4.3、Http请求

- 客户端--发请求(Request)--服务器

百度：

```java
Request URL: https://www.baidu.com/  请求地址
Request Method: GET  get方法/Post方法
Status Code: 200 OK  状态码：200
Remote Address: 163.177.151.109:443  地址
Referrer Policy: no-referrer-when-downgrade
```

##### 4.3.1、请求行

- 请求行中的请求方式：Get
- 请求方式：Get，Post，HEAD，DELETE.....
  - get：请求能够携带的参数比较少，大小有限制，会在浏览器的URL地址栏显示数据内容，不安全，但高效
  - post：请求能够携带的参数没有限制，大小没有限制，不会在浏览器的URL地址栏显示数据内容，安全，不高效

##### 4.3.2、消息头

```java
Accept:告诉浏览器它所支持的数据类型
Accept-Encoding:支持哪种编码方式：GBK,UTF-8,GB2312,ISO8859-1
Accept-Language:告诉浏览器它的语言环境
Cache-Control:缓存控制
Connection:告诉浏览器，请求完成是断开还是保持连接
HOST:主机
```

#### 4.4、Http响应

```java
Cache-Control: private    缓存控制
Connection: keep-alive    连接
Content-Encoding: gzip    编码
Content-Type: text/html   类型
```

##### 4.4.1、相应体

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

##### 4.4.2、响应状态码

200：请求响应成功

3**：请求重定向

- 重定向：你重新到我给你的新位置去

4**：资源找不到，404

5**：服务器代码错误 

- 500：
- 502：网关错误

## 5、Maven

**为什么要学Maven**

1. 在Javaweb开发中，需要导入大量的jar包，我们需要手动导入；
2. Maven能让一个东西自动帮我导入和配置这些jar包

#### 5.1、Maven架构管理工具

我们目前用来就是方便导入jar包

Maven核心思想：**约定大于配置**

- 有约束，不要去违反

Maven会规定好你该如何去编写java代码，必须要按照这个规范来；

#### 5.2、下载按照Maven

下载完成后解压

#### 5.3、配置环境变量

在我们的系统环境变量中

配置如下配置：

- M2_HOME：Maven目录下的bin目录
- MAVEN_HOME：Maven目录
- 在系统的path中配置MAVEN_HOME

#### 5.4、阿里云镜像

- 镜像：mirrors
  - 作用：加速我们下载
- 国内建议使用阿里云的镜像

```xml
<mirror>
		<id>nexus-aliyun</id>
		<mirrorOf>*,!jeecg,!jeecg-snapshots</mirrorOf>
		<name>Nexus aliyun</name>
		<url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror>
```

#### 5.5、本地仓库

在本地的仓库，远程仓库；

**建立一个仓库**：

```xml
<localRepository>
    D:\Work\JAVA\JavaWeb\Maven3.5\maven-repo
</localRepository>
```

#### 5.6、在Idea中使用Maven

1. 创建Maven项目，初始化，选择Maven家目录，本地仓库，配置文件

2. Maven配置

   Idea项目创建成功后，看一眼Maven的配置

3. 到这里，Maven在Idea中创建成功。

#### 5.7、创建一个普通的Maven项目

#### 5.8、在Idea中标记文件夹功能

#### 5.9、Idea配置Tomcat

1. 选择加上Tomcat
2. 解决警告问题

**为什么会出现这个问题：我们访问一个网站，需要指定一个文件夹名字**

![image-20201204205043966](D:\Application\Typora\image\image-20201204205043966.png)

虚拟路径映射

#### 5.10、pom文件

pom.xml是maven的核心配置文件

![image-20201204205556303](D:\Application\Typora\image\image-20201204205556303.png)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Maven版本和头文件-->
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!--这里就是我们刚刚配置的GAV-->
  <groupId>com.ferron</groupId>
  <artifactId>javaweb-01-maven</artifactId>
  <version>1.0-SNAPSHOT</version>
  <!--Package：项目的打包方式
    jar：java应用
    war：javaweb应用-->
  <packaging>war</packaging>

  <name>javaweb-01-maven Maven Webapp</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <!--配置  -->
  <properties>
    <!--项目的默认构建编码     -->
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <!--编码版本    -->
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
  </properties>

  <!--项目依赖  -->
  <dependencies>
    <!--具体依赖的jar包的配置文件-->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <!--项目构建的东西  -->
  <build>
    <finalName>javaweb-01-maven</finalName>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <plugin>
          <artifactId>maven-clean-plugin</artifactId>
          <version>3.1.0</version>
        </plugin>
        <!-- see http://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_war_packaging -->
        <plugin>
          <artifactId>maven-resources-plugin</artifactId>
          <version>3.0.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.8.0</version>
        </plugin>
        <plugin>
          <artifactId>maven-surefire-plugin</artifactId>
          <version>2.22.1</version>
        </plugin>
        <plugin>
          <artifactId>maven-war-plugin</artifactId>
          <version>3.2.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-install-plugin</artifactId>
          <version>2.5.2</version>
        </plugin>
        <plugin>
          <artifactId>maven-deploy-plugin</artifactId>
          <version>2.8.2</version>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>

```

**Maven的高级之处并不是帮你导入这个jar包，而是帮你导入这个jar包所依赖的jar包**

maven由于他的约定大于配置，我们之后可能遇到我们写的配置文件，无法被导出或者生效的问题，解决方案：

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

#### 5.11、Maven骚操作

![image-20201204212041258](D:\Application\Typora\image\image-20201204212041258.png)

## 6、Servlet

#### 6.1、简介

- Servlet就是sun公司开发动态web的一门技术
- sun公司在这些API中提供一个接口叫做：Servlet，如果你想开发一个Servlet程序，只需要完成两个小步骤：
  - 编写一个类，实现Servlet接口
  - 把开发好的Java类部署到web服务器中

**把实现了Servlet接口的Java程序叫做Servlet**

#### 6.2、HelloServlet

Servlet接口sun公司有两个默认的实现类：HttpServlet，

1. 构建一个Maven项目，删掉里面的src文件夹，以后就在这个项目里面建立Moudel。这个空的工程就是Maven主工程；
2. 关于Maven父子工程的理解：

父项目会有

```xml
<modules>
        <module>servlet-01</module>
</modules>
```

子项目中会有

```xml
<parent>
        <artifactId>javaweb-02-servlet</artifactId>
        <groupId>com.ferron</groupId>
        <version>1.0-SNAPSHOT</version>
</parent>
```

父项目中的java子项目可以直接使用

```java
son extends father
```

3. Maven环境优化
   1. 修改web.xml为最新的
   2. 将maven的结构搭建完整
4. 编写一个Servlet程序
   1. 编写一个普通类
   2. 实现Servlet接口，继承HttpServlet
   3. 编写类

```java
public class HelloServlet extends HttpServlet {
    //由于get或者post只是请求实现的不同的方式，可以相互调用，业务逻辑都一样。
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        //ServletOutputStream outputStream = resp.getOutputStream();
        PrintWriter writer = resp.getWriter(); //相应流

        writer.println("hello,Servlet");

    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
```

5. 编写Servlet的映射

   为什么要映射：我们写的是java程序，但是要通过浏览器访问，而浏览器需要连接web服务器，所以我们需要在web服务器中注册我们写的Servlet，还需要给他一个浏览器能够访问的路径

6. 配置Tomcat

   注意：配置项目发布的路径即可。

7. 启动测试

#### 6.3、Servlet原理

Servlet是由Web服务器调用，web服务器在收到浏览器请求之后，会：

1. 首先浏览器会发生http请求给web容器，第一次访问时会产生Servlet
2. web容器会发送到请求(请求头和请求体)和响应(响应头和相应体)给Servlet，调用Servlet中的service方法，然后通过响应返回给web容器
3. web容器读取响应内容，传给浏览器
4. 我们自己编写的实现类，重写这些方法
   1. 接受并处理这些请求
   2. 给出响应信息

#### 6.4、Mapping问题

在web.xml中配置servlet的请求路径

1. 一个Servlet可以指定一个映射路径

   ```xml
   <servlet-mapping>
       <servlet-name>hello</servlet-name>
       <url-pattern>/hello</url-pattern>
   </servlet-mapping>
   ```

2. 一个Servlet可以指定多个映射路径

   ```xml
   <servlet-mapping>
       <servlet-name>hello</servlet-name>
       <url-pattern>/hello1</url-pattern>
   </servlet-mapping>
   <servlet-mapping>
       <servlet-name>hello</servlet-name>
       <url-pattern>/hello2</url-pattern>
   </servlet-mapping>
   <servlet-mapping>
       <servlet-name>hello</servlet-name>
       <url-pattern>/hello3</url-pattern>
   </servlet-mapping>
   ```

3. 一个Servlet可以指定通用映射路径

   ```xml
   <servlet-mapping>
       <servlet-name>hello</servlet-name>
       <url-pattern>/hello/*</url-pattern>
   </servlet-mapping>
   ```

4. 默认请求路径

   ```xml
   <servlet-mapping>
       <servlet-name>hello</servlet-name>
       <url-pattern>/*</url-pattern>
   </servlet-mapping>
   ```

5. 指定一些后缀和前缀等等

```xml
#可以自定义后缀实现请求映射，
#注意，*前面不能加项目映射的路径
#以.do结尾的虚拟路径都可以进入
<servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>*.do</url-pattern>
</servlet-mapping>
```

6. 优先级问题

   指定了固有的映射路径优先级最高，如果找不到就会走默认的处理请求

#### 6.5、ServletContext

web容器在启动的时候，它会为每个web程序都创建一个对应的ServletContext对象，它代表了当前的web应用；

##### 6.5.1、共享数据

我在这个Servlet中保存的数据，可以在另一个Servlet中拿到

**先set**：

```java
public class HelloServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        //this.getInitParameter()  初始化参数
        //this.getInitParameterNames()   Servlet配置
        //this.getServletContext()  Servlet上下文
        ServletContext context = this.getServletContext();
        String username = "飞龙";  //数据
        context.setAttribute("username",username); //将一个数据保存在ServletContext中，名字为username，值为username

        System.out.println("hello");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
```

**再get**：

```java
public class GetServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        ServletContext context = this.getServletContext();
        String username = (String)context.getAttribute("username");

        resp.setContentType("text/html");
        resp.setCharacterEncoding("utf-8");
        resp.getWriter().println("名字:"+username);

    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
```

**最后配置：**

```xml
  <servlet>
    <servlet-name>hello</servlet-name>
    <servlet-class>com.ferron.servlet.HelloServlet</servlet-class>
  </servlet>
  <servlet>
    <servlet-name>get</servlet-name>
    <servlet-class>com.ferron.servlet.GetServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>hello</servlet-name>
    <url-pattern>/hello</url-pattern>
  </servlet-mapping>
  <servlet-mapping>
    <servlet-name>get</servlet-name>
    <url-pattern>/get</url-pattern>
  </servlet-mapping>

```

测试访问结果

##### 6.5.2、获取初始化参数

配置初始化参数

```xml
<!--配置url初始参数-->
  <context-param>
    <param-name>url</param-name>
    <param-value>jdbc:mysql://localhost:3306/mybatis</param-value>
  </context-param>
```

从context里拿

```java
public class ServletDemo03 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        ServletContext context = this.getServletContext();

        String url = context.getInitParameter("url");
        resp.getWriter().println(url);

    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
```

##### 6.5.3、请求转发

请求转发：A要拿C的资源，去B那里拿，B再找C拿，返回给A

重定向：A要拿C的资源，去B那里拿，拿不到，再自己去跟C拿

```java
public class ServletDemo04 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        ServletContext context = this.getServletContext();

        //RequestDispatcher requestDispatcher = context.getRequestDispatcher("/gp"); //转发的请求路径
        //requestDispatcher.forward(req,resp); //调用forward实现请求转发实现转发
        System.out.println("进入了ServletDemo04");
        context.getRequestDispatcher("/gp").forward(req,resp);

    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
```

##### 6.5.4、读取资源文件

Properties

- 在java目录下新建properties
- 在resources目录下新建properties

发现：都被打包到了同一个路径下：classes，我们俗称这个路径为classpath

思路：需要一个文件流

```java
public class ServletDemo05 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        InputStream is = this.getServletContext().getResourceAsStream("/WEB-INF/classes/com/ferron/servlet/aa.properties");
        Properties prop = new Properties();
        prop.load(is);
        String username = prop.getProperty("username");
        String password = prop.getProperty("password");
        resp.getWriter().println(username+":"+password);
        //在java目录下读取资源文件
        //InputStream is1 = this.getServletContext().getResourceAsStream("/WEB-INF/classes/db.properties");
        //Properties prop1 = new Properties();
        //prop1.load(is1);
        //String username1 = prop1.getProperty("username");
        //String password1 = prop1.getProperty("password");
        //resp.getWriter().println(username1+":"+password1);

    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doPost(req, resp);
    }
}
```

#### 6.6、HttpServletResponse

web服务器接收到客户端的http请求，针对这个请求，分别创建一个代表请求的HttpServletRequest对象，代表响应的一个HttpServletSponse

- 如果要获取客户端请求过来的参数：找HttpServletRequest
- 如果要给客户端响应一些信息：找HttpServletResponse

##### 6.6.1、简单分类

**负责向浏览器发送数据的方法**：

```java
ServletOutputStream getOutputStream() throws IOException;
PrintWriter getWriter() throws IOException;
```

**负责向浏览器发送响应头的方法**

```java
void setCharacterEncoding(String var1);
void setContentLength(int var1);
void setContentLengthLong(long var1);
void setContentType(String var1);
void setBufferSize(int var1);
void setDateHeader(String var1, long var2);
void addDateHeader(String var1, long var2);
void setHeader(String var1, String var2);
void addHeader(String var1, String var2);
void setIntHeader(String var1, int var2);
void addIntHeader(String var1, int var2);
```

**响应状态码**

```java
int SC_CONTINUE = 100;
int SC_SWITCHING_PROTOCOLS = 101;
int SC_OK = 200;
int SC_CREATED = 201;
int SC_ACCEPTED = 202;
int SC_NON_AUTHORITATIVE_INFORMATION = 203;
int SC_NO_CONTENT = 204;
int SC_RESET_CONTENT = 205;
int SC_PARTIAL_CONTENT = 206;
int SC_MULTIPLE_CHOICES = 300;
int SC_MOVED_PERMANENTLY = 301;
int SC_MOVED_TEMPORARILY = 302;
int SC_FOUND = 302;
int SC_SEE_OTHER = 303;
int SC_NOT_MODIFIED = 304;
int SC_USE_PROXY = 305;
int SC_TEMPORARY_REDIRECT = 307;
int SC_BAD_REQUEST = 400;
int SC_UNAUTHORIZED = 401;
int SC_PAYMENT_REQUIRED = 402;
int SC_FORBIDDEN = 403;
int SC_NOT_FOUND = 404;
int SC_METHOD_NOT_ALLOWED = 405;
int SC_NOT_ACCEPTABLE = 406;
int SC_PROXY_AUTHENTICATION_REQUIRED = 407;
int SC_REQUEST_TIMEOUT = 408;
int SC_CONFLICT = 409;
int SC_GONE = 410;
int SC_LENGTH_REQUIRED = 411;
int SC_PRECONDITION_FAILED = 412;
int SC_REQUEST_ENTITY_TOO_LARGE = 413;
int SC_REQUEST_URI_TOO_LONG = 414;
int SC_UNSUPPORTED_MEDIA_TYPE = 415;
int SC_REQUESTED_RANGE_NOT_SATISFIABLE = 416;
int SC_EXPECTATION_FAILED = 417;
int SC_INTERNAL_SERVER_ERROR = 500;
int SC_NOT_IMPLEMENTED = 501;
int SC_BAD_GATEWAY = 502;
int SC_SERVICE_UNAVAILABLE = 503;
int SC_GATEWAY_TIMEOUT = 504;
int SC_HTTP_VERSION_NOT_SUPPORTED = 505;
```

##### 6.6.2、下载文件

下载文件

1. 要获取下载文件的路径
2. 下载的文件名是啥？
3. 设置想办法让浏览器能够支持下载我们需要的东西
4. 获取下载文件的输入流
5. 创建缓冲区
6. 获取OutputStream对象
7. 将FileOutputStream流写入到buffer缓冲区
8. 使用OutputStream将缓冲区中的数据输出到客户端

##### 6.6.3、验证码功能

验证码怎么来的？

- 前端实现
- 后端实现，需要用到Java图片类，生成一个图片

##### 6.6.4、实现重定向

一个web资源收到客户端请求后，他会通知客户端去访问另外一个web资源，这个过程叫重定向。

常见场景：

- 用户登录

  ```java
  void sendRedirect(String var1) throws IOException;
  ```

  ![image-20201210100422905](D:\Application\Typora\image\image-20201210100422905.png)

  测试：

  ```java
  protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          //需要加上项目名称
          resp.sendRedirect("/response_war/image"); //重定向
  
          //剖析原理
          resp.setHeader("Location","/response_war/image");
          resp.setStatus(302);
  
  
      }
  ```

面试题：重定向和转发的区别：

相同点：

- 页面都会实现跳转

不同点：

- 请求转发url不会变化，重定向url会变化

#### 6.7、HttpServletRequest

HttpServletRequest代表客户端的请求，用户通过Http协议访问服务器，Http请求中所有信息会被封装到HttpServletRequest，通过这个HttpServletRequest的方法，获得客户端所有信息。

##### 6.7.1、获取前端传递的参数

![image-20201211085813130](D:\Application\Typora\image\image-20201211085813130.png)



```java
//解决中文乱码
req.setCharacterEncoding("utf-8");

String username = req.getParameter("username");
String password = req.getParameter("password");
String[] hobbies = req.getParameterValues("hobby");

System.out.println("=============");
System.out.println(username);
System.out.println(password);
System.out.println(Arrays.toString(hobbies));
System.out.println("=============");

//通过请求转发
System.out.println(req.getContextPath());
//这里的/代表当前的web应用
req.getRequestDispatcher("/success.jsp").forward(req,resp);
//解决乱码问题
resp.setCharacterEncoding("utf-8");
```

##### 6.7.2、请求转发

转发是在服务器内部流转，不需要写项目路径，与重定向的寻找文件路径方式不一样！

重定向和请求转发区别：

- 请求转发：url不会改变；**307**

- 重定向：url会改变；**302**

## 7、Cookie，Session

#### 7.1、Session(会话)

用户打开一个浏览器，点击了很多超链接，访问多个web资源，关闭浏览器，这个过程可以称为**session**

一个网站，怎么证明你来过？

客户端          服务端

1. 服务端给客户端一个信件，客户端下次访问服务端带上信件就可以了(cookie)
2. 服务器登记你来过了，下次你来的时候我来匹配你(session)

#### 7.2、保存Session的两种技术

cookie

- 客户端技术(响应，请求)

session

- 服务器技术，利用这个技术，可以保存用户的会话信息，我们可以把信息放在Session中

常见场景：网站登录之后，你下次不用再登录了

#### 7.3、Cookie

响应中的设置Cookie

![image-20201212093540445](D:\Application\Typora\image\image-20201212093540445.png)

请求中的返回Cookie

![image-20201212093638508](D:\Application\Typora\image\image-20201212093638508.png)



1. 从请求中拿到cookie信息
2. 服务器响应给客户端cookie

```java
Cookie[] cookies = req.getCookies(); //获得cookie
cookie.getName(); //获得cookie中的key
cookie.getValue(); //获得cookie中的value
Cookie cookie = new Cookie("name", "value"); //新建cookie
cookie.setMaxAge(24*60*60); //设置cookie有效期
resp.addCookie(cookie); //响应给客户端一个cookie
```

**cookie：一般会保存在本地的用户目录下的appdata目录下**

有效期：

- 不设置有效期，关闭浏览器，自动失效
- 设置有效期为0

编码解码：

```java
URLEncoder.encode("朱飞龙","utf-8");
URLDecoder.decode(cookie.getValue(),"UTF-8");
```

#### 7.4、Session(重点)

什么是Session：

- 服务器会给每个用户(浏览器)创建一个Sesstion
- 一个Session独占一个浏览器，只要浏览器没有关闭，这个Session就存在
- 用户登录之后，整个网站它都可以访问！ -->保存用户(购物车)的信息

![image-20201212101824076](D:\Application\Typora\image\image-20201212101824076.png)



![image-20201212102336098](D:\Application\Typora\image\image-20201212102336098.png)



Session和cookie的区别：

- Cookie是把用户的数据写给用户的浏览器，浏览器本地保存
- Session把用户的数据写到用户独占Session中，服务器端保存(保存重要的信息，减少服务器资源的浪费)
- Session对象由服务器创建、

使用场景：

- 保存一个登录用户的信息
- 购物车信息
- 在整个网站中经常会使用的数据，我们将他保存在Session中

使用Session

```java
HttpSession session = req.getSession(); //创建Session
session.setAttribute("person",new Person("朱飞龙",18));  //在Session中保存数据
System.out.println(session.getAttribute("name")); //获取Session中数据
session.removeAttribute("name");//删除属性
session.invalidate();//手动注销session
```

```xml
<!--设置Session自动失效时间-->
<session-config>
    <!--15分钟后自动失效，以分钟为单位-->
    <session-timeout>15</session-timeout>
</session-config>
```

ServletContext：不同用户的信息

Session：一个用户的信息

Cookie：一个用户的信息

## 8、JSP

#### 8.1、什么是JSP

Java Server Pages：java服务端页面，也和Servlet一样，用于动态Web技术

最大的特点：

- 写JSP就像在写html
- 区别：
  - html只给用户提供静态数据
  - jsp页面中可以嵌入java代码，为用户提供动态数据

#### 8.2、JSP原理

思路：JSP到底是怎么样执行的

- 代码层面没有任何问题

- 服务器内部工作

  tomcat中有一个work目录

  IDEA中使用tomcat的会在IDEA的tomcat中生成一个work目录

**浏览器向服务器发送请求，不管访问什么资源，都是在访问Servlet**

JSP最终也会被转化成一个java类

![image-20201213084313884](D:\Application\Typora\image\image-20201213084313884.png)

![image-20201213084329450](D:\Application\Typora\image\image-20201213084329450.png)



**JSP本质上就是一个Servlet**

在JSP页面中：

只要是java代码就会被原封不动地输出

如果是html代码，就会被转换为

```html
out.print("<html>\r\n")
```

这样的格式，输出到前端

#### 8.3、JSP基础语法

任何语言都有自己的语法，java中有。JSP作为java技术的一种应用，它有一些自己扩充的语法(了解，知道即可)，java所有的语法都支持

**JSP表达式**

```jsp
<%--JSP表达式
    作用：用来将程序的输出，输出到客户端
    <%= 变量或者表达式%>
--%>
  <%= new java.util.Date()%>
```

**JSP脚本片段**

```jsp
<%--jsp脚本片段--%>
  <%
    int sum = 0;
    for (int i = 0; i < 100; i++) {
      sum+=i;
    }
    out.print("<h1>Sum"+sum+"</h1>");
  %>
```

**脚本片段的再实现**

```jsp
<%--在代码中嵌入html元素--%>
<%--${}为EL表达式--%>
  <%
    for (int i = 0; i < 20; i++) {
  %>
  <h1>hello ${i}</h1>
  <%
    }
  %>
```

**JSP声明**

```jsp
<%--作用域更高--%>
<%!
    static {
      System.out.println("Loading Servlet");
    }

    private int glovalVal = 0;

    public void get(){
      System.out.println("进入了方法");
    }

  %>
```

JSP声明：会被编译到JSP生成的java类中！其他的，就会被生成到_jspService方法中

在JSP，嵌入java代码即可

```jsp
<%%>
<%=%>
<%!%>
<%----%>
```

JSP的注释，不会在客户端里显示，而html的注释会被显示

#### 8.4、JSP指令

```jsp
<%@ page args...%>
<%@ include file=""%>
```

#### 8.5、9大内置对象

- PageContext 存东西
- Request 存东西
- Response
- Session 存东西
- Application(ServletContext) 存东西
- config(ServletConfig)
- out
- page 不用了解
- exception  

```java
pageContext.setAttribute("name1","ferron1"); //保存的数据只在一个页面有效
request.setAttribute("name2","ferron2"); //保存的数据只在一次请求中有效，请求转发会携带这个数据
session.setAttribute("name3","ferron3"); //保存的数据只在一次会话中有效，从打开浏览器到关闭浏览器
application.setAttribute("name4","ferron4"); //保存的数据在服务器中有效， 从打开服务器到关闭服务器
```

request：客户端向服务器发送请求，产生的数据，用户看完就没用了，比如：新闻

session：客户端向服务器发送请求，产生的数据，用户看完一会还有用，比如：购物车

application：客户端向服务器发送请求，产生的数据，一个用户用完了，其他用户还可能看到，比如：聊天记录

#### 8.6、JSP标签、JSTL标签、EL表达式

EL表达式：${}

```xml
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
```

- **获取数据**
- **执行运算**
- **获取web开发的常用对象**

**JSP标签**

```
<%--http://localhost:8080/jsptag/jsp?name=ferron&age=12--%>
<jsp:forward page="jsptag2.jsp">
    <jsp:param name="name" value="ferron"/>
    <jsp:param name="age" value="12"/>
</jsp:forward>
```

**JSTL表达式**

JSTL标签库的使用就是为了弥补HTML标签的不足；它自定义了许多标签，可以供我们使用，标签的功能和java代码一样！

**核心标签(掌握部分)**

![image-20201216152357568](D:\Application\Typora\image\image-20201216152357568.png)



使用步骤：

- 引入对应的taglib
- 使用其中的标签
- 在Tomcat也需要引入jstl的包，否则会报错：JSTL解析错误

```jsp
<%--给页面本身提交数据，以便获取--%>
<form action="coreif.jsp" method="get">
    <%--EL表达式获取表单中的数据  ${param.参数名}--%>
    <input type="text" name="username" value="${param.username}">
    <input type="submit" value="登录">
</form>

<%--判断如果提交的用户名是管理员，则登录成功--%>

<c:if test="${param.username == 'admin'}" var="isAdmin">
    <c:out value="管理员欢迎你"></c:out>
</c:if>
<c:out value="${isAdmin}"/>

```

```jsp
<%--定义一个变量为score，值为85--%>
<c:set var="score" value="85"/>
<c:choose>
    <c:when test="${score>=90}">
        你的成绩为优秀
    </c:when>
    <c:when test="${score>=80}">
        你的成绩为一般
    </c:when>
    <c:when test="${score>=70}">
        你的成绩为良好
    </c:when>
    <c:when test="${score>=60}">
        你的成绩为及格
    </c:when>
</c:choose>
```

```xml
<%
    ArrayList<Object> people = new ArrayList<>();
    people.add(0,"张三");
    people.add(1,"李四");
    people.add(2,"王五");
    people.add(3,"赵六");
    request.setAttribute("list",people);

%>

<c:forEach var="people" items="${list}">
    <c:out value="${people}"/><br>
</c:forEach>
```

**格式化标签**

**SQL标签**

**XML标签**

## 9、JavaBean

实体类

javabean有特定的写法：

- 必须要有一个无参构造
- 属性必须私有化
- 必须有对应的get/set方法

一般用来和数据库字段做映射 ORM；

ORM：对象关系映射

- 表--->类
- 字段--->属性
- 行记录---->对象

| id   | name    | age  | address |
| ---- | ------- | ---- | ------- |
| 1    | ferron1 | 12   | 广州    |
| 2    | ferron2 | 18   | 河源    |
| 3    | ferron3 | 24   | 深圳    |

```java
//实体类一般用来对应数据库的字段
//一般都是和数据库中的表结构一一对应
class People{
    private int id;
    private String name;
    private int age;
    private String Address;
}

class A{
    new People(1,"ferron1",12,"广州");
    new People(2,"ferron2",18,"河源");
    new People(3,"ferron3",24,"深圳");
}
```

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

#### 10.1、以前架构

用户直接访问控制层，控制层就可以直接操作数据库

```java
servlet--->CRUD--->数据库
弊端：程序十分臃肿，不利于维护   
servlet的代码中：处理请求、响应、视图跳转、处理JDBC，处理业务代码，处理逻辑代码
    
架构：没有什么是加一层解决不了的！
```

![image-20201217142330933](D:\Application\Typora\image\image-20201217142330933.png)

#### 10.2、三层架构

![image-20201217144918428](D:\Application\Typora\image\image-20201217144918428.png)



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

![image-20201217150332564](D:\Application\Typora\image\image-20201217150332564.png)



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


## 14、SMBMS

项目如何搭建：使用Maven

1. 搭建一个maven web项目

2. 配置tomcat

3. 测试项目是否能够跑起来

4. 导入项目中需要的jar包：

   jsp servlet mysql驱动 jstl standard

5. 创建项目结构

   ![image-20201219150841424](D:\Application\Typora\image\image-20201219150841424.png)

   

6. 编写实体类

   ORM映射：表-类映射

7. 编写基础公共类：

   1. 数据库配置文件

      ```properties
      driver=com.mysql.jdbc.Driver
      url = jdbc:mysql://localhost:3306/jdbcstudy?useUnicode=true&characterEncoding=utf8&useSSL=true
      username = root
      password = sanfashi
      ```

   2. 编写数据库公共类

      ```java
      //操作数据库的公共类
      public class BaseDao {
          private static String driver;
          private static String url;
          private static String username;
          private static String password;
      
          //静态代码块，类加载的时候就已经初始化
          static {
      
              Properties properties = new Properties();
              //通过类加载器读取对应的资源
              InputStream is = BaseDao.class.getClassLoader().getResourceAsStream("db.properties");
      
              try {
                  properties.load(is);
              } catch (IOException e) {
                  e.printStackTrace();
              }
      
              String driver = properties.getProperty("driver");
              String url = properties.getProperty("url");
              String username = properties.getProperty("username");
              String password = properties.getProperty("password");
          }
      
          //获取数据库连接
          public static Connection getConnection(){
              Connection connection = null;
              try {
                  Class.forName(driver);
                  connection = DriverManager.getConnection(url, username, password);
              } catch (Exception e) {
                  e.printStackTrace();
              }
              return connection;
      
          }
      
          //编写查询公共方法
          //提出来方便统一关闭
          public static ResultSet execute(Connection connection, String sql, Object[] params,ResultSet resultSet,PreparedStatement preparedStatement) throws SQLException {
              //预编译了sql，后面不需要传参
              preparedStatement = connection.prepareStatement(sql);
      
              for (int i = 0; i < params.length; i++) {
                  //setObject，占位符从1开始，但是我们的数组从0开始
                  preparedStatement.setObject(i+1,params[i]);
              }
      
              resultSet = preparedStatement.executeQuery();
              return resultSet;
      
          }
      
          //编写增删改公共方法
          public static int execute(Connection connection, String sql, Object[] params,PreparedStatement preparedStatement) throws SQLException {
              //预编译了sql，后面不需要传参
              preparedStatement = connection.prepareStatement(sql);
      
              for (int i = 0; i < params.length; i++) {
                  //setObject，占位符从1开始，但是我们的数组从0开始
                  preparedStatement.setObject(i+1,params[i]);
              }
      
              int updateRows = preparedStatement.executeUpdate();
              return updateRows;
      
          }
      
          //释放资源
          public static boolean closeResource(Connection connection,PreparedStatement preparedStatement,ResultSet resultSet){
              boolean flag = true;
      
              if (resultSet != null){
                  try {
                      resultSet.close();
                      //GC回收
                      resultSet=null;
                  } catch (SQLException e) {
                      e.printStackTrace();
                      flag = false;
                  }
              }
      
              if (preparedStatement != null){
                  try {
                      preparedStatement.close();
                      //GC回收
                      preparedStatement=null;
                  } catch (SQLException e) {
                      e.printStackTrace();
                      flag = false;
                  }
              }
      
              if (connection != null){
                  try {
                      connection.close();
                      //GC回收
                      connection=null;
                  } catch (SQLException e) {
                      e.printStackTrace();
                      flag = false;
                  }
              }
      
              return flag;
          }
      }
      ```

   3. 编写字符编码过滤器

      ```java
      <!--字符编码过滤器-->
          <filter>
              <filter-name>CharacterEncodingFilter</filter-name>
              <filter-class>com.ferron.filter.CharacterEncodingFilter</filter-class>
          </filter>
          <filter-mapping>
              <filter-name>CharacterEncodingFilter</filter-name>
              <url-pattern>/*</url-pattern>
          </filter-mapping>
      ```

8. 导入静态资源

## 15、常用代码

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



