---
title: SSM整合
tags: SSM
categories: 学习笔记
description: S(Spring)S(SpringMVC)M(Mybatis)整合流程
---

### 1、环境要求

- IDEA
- MySQL 5.7
- Tomcat 9
- Maven 3.6

### 2、数据库环境

创建一个存放书籍数据的数据库表

```sql
CREATE DATABASE `ssmbuild`

USE `ssmbuild`

CREATE TABLE `books`(
`bookID` INT(10) NOT NULL AUTO_INCREMENT COMMENT '书id',
`bookName` VARBINARY(100) NOT NULL COMMENT '书名',
`bookCounts` INT(11) NOT NULL COMMENT '数量',
`detail` VARBINARY(200) NOT NULL COMMENT '描述',
KEY `bookID`(`bookID`)
)ENGINE=INNODB DEFAULT CHARSET=utf8

INSERT INTO `books`(`bookID`,`bookName`,`bookCounts`,`detail`) VALUES
(1,'Java',1,'从入门到放弃'),
(2,'MySQL',10,'从删库到跑路'),
(3,'Linux',5,'从进门到进牢');
```

### 3、基本环境搭建

1. 新建Maven项目

2. 导入依赖

   ```xml
   <!--依赖：junit 数据库驱动 连接池 servlet jsp mybatis mybatis-spring spring-->
   <dependencies>
       <!-- junit单元测试-->
       <dependency>
           <groupId>junit</groupId>
           <artifactId>junit</artifactId>
           <version>4.12</version>
       </dependency>
       <!-- mysql驱动 -->
       <dependency>
           <groupId>mysql</groupId>
           <artifactId>mysql-connector-java</artifactId>
           <version>5.1.47</version>
       </dependency>
       <!-- 数据库连接池 -->
       <dependency>
           <groupId>com.mchange</groupId>
           <artifactId>c3p0</artifactId>
           <version>0.9.5.2</version>
       </dependency>
       <!-- Spring连接数据库 -->
       <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-jdbc</artifactId>
           <version>5.2.12.RELEASE</version>
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
       <!-- mybatis -->
       <dependency>
           <groupId>org.mybatis</groupId>
           <artifactId>mybatis</artifactId>
           <version>3.5.2</version>
       </dependency>
       <!-- 整合mybatis和spring -->
       <dependency>
           <groupId>org.mybatis</groupId>
           <artifactId>mybatis-spring</artifactId>
           <version>2.0.6</version>
       </dependency>
       <!-- Srping依赖 -->
       <dependency>
           <groupId>org.springframework</groupId>
           <artifactId>spring-webmvc</artifactId>
           <version>5.2.9.RELEASE</version>
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
   ```

3. 解决静态资源导出

   ```xml
   <!--静态资源导出-->
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

4. 固定jdk版本

   ```xml
   <!--固定java版本-->
   <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
       <maven.compiler.encoding>UTF-8</maven.compiler.encoding>
    <java.version>11</java.version>
       <maven.compiler.source>11</maven.compiler.source>
    <maven.compiler.target>11</maven.compiler.target>
   </properties>
   ```

```
   
5. 建立基本结构和配置框架

   - pojo

   - dao

   - service

   - controller

   - mybatis-config.xml

     ```xml
     <?xml version="1.0" encoding="UTF-8" ?>
     <!DOCTYPE configuration
             PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
             "http://mybatis.org/dtd/mybatis-3-config.dtd">
     
     <configuration>
         
     </configuration>
```

   - applicationContext.xml

     ```xml
     <?xml version="1.0" encoding="UTF-8"?>
     <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
             https://www.springframework.org/schema/beans/spring-beans.xsd">
         
     
     </beans>
     ```

### 4、Mybatis层

1. 数据库配置文件database.properties

   如果使用MySQL8.0+，需要增加一个时区的配置：&serverTimezone=Asia/Shanghai

   ```properties
   jdbc.driver=com.mysql.jdbc.Driver
   jdbc.url=jdbc:mysql://localhost:3306/mybatis?useSSL=false&useUnicode=true&characterEncoding=UTF-8
   jdbc.username=root
   jdbc.password=sanfashi
   ```

2. IDEA关联数据库

3. 写pojo实体类Books

   ```java
   public class Books {
   
       private int bookID;
       private String bookName;
       private int bookCounts;
       private String detail;
   }
   ```

4. 在dao层写接口BookMapper

   **注：这里面的@Param注解是在写BookMapper.xml时再加上去的**

   ```java
   package com.ferron.dao;
   
   import com.ferron.pojo.Books;
   import org.apache.ibatis.annotations.Param;
   
   import java.util.List;
   
   public interface BookMapper {
       //增加一本书
       int addBook(Books books);
   
       //删除一本书
       int deleteBookById(@Param("bookId") int id);
   
       //更新一本书
       int updateBook(Books books);
   
       //查询一本书
       Books queryBookById(@Param("bookId") int id);
   
       //查询全部书
       List<Books> queryAllBook();
   
   }
   ```

5. 写完一个dao层的接口，就写一个对应的接口实现Mapper.xml，这里是BookMapper.xml

   注意以下几点：

   - Mapper.java和Mapper.xml最好分屏写，参数名不对应则加上@Param注解
   - 注意**返回类型**

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   
   <mapper namespace="com.ferron.dao.BookMapper">
       <insert id="addBook" parameterType="Books">
           insert into ssmbuild.books (bookName, bookCounts, detail)
           values (#{id},#{bookCounts},#{detail});
       </insert>
       <delete id="deleteBookById" parameterType="int">
           delete from ssmbuild.books where bookID = #{bookId}
       </delete>
       <update id="updateBook" parameterType="Books">
           update ssmbuild.books
           set bookName = #{bookName}, bookCounts=#{bookCounts},detail=#{detail}
           where bookID=#{bookID};
       </update>
       <select id="queryBookById" parameterType="Books">
           select * from ssmbuild.books where bookID = #{bookId};
       </select>
       <select id="queryAllBook" resultType="Books">
           select * from ssmbuild.books;
       </select>
       
   </mapper>
   ```

6. **写完一个Mapper，一要在mybatis配置文件中注册。**

7. 写完dao层，就去写service层，写一个BookService的接口

   ```java
   package com.ferron.service;
   
   import com.ferron.pojo.Books;
   
   import java.util.List;
   
   public interface BookService {
   
       //增加一本书
       int addBook(Books books);
   
       //删除一本书
       int deleteBookById(int id);
   
       //更新一本书
       int updateBook(Books books);
   
       //查询一本书
       Books queryBookById(int id);
   
       //查询全部书
       List<Books> queryAllBook();
   
   }
   ```

8. 写完一个service的接口，就写一个对应的接口实现类，这里为BookServiceImpl

   实际上service层就是一个代理层，dao层是实际上的调数据，而service则是一个代理层，需要加什么附属操作就在service层加，而**service则只是调dao层**。

   ```java
   package com.ferron.service;
   
   import com.ferron.dao.BookMapper;
   import com.ferron.pojo.Books;
   
   import java.util.List;
   
   public class BookServiceImpl implements BookService {
   
       //service层调dao层：组合Dao层
       private BookMapper bookMapper;
       public void setBookMapper(BookMapper bookMapper){
           this.bookMapper=bookMapper;
       }
   
   
       public int addBook(Books books) {
           return bookMapper.addBook(books);
       }
   
       public int deleteBookById(int id) {
           return bookMapper.deleteBookById(id);
       }
   
       public int updateBook(Books books) {
           return bookMapper.updateBook(books);
       }
   
       public Books queryBookById(int id) {
           return bookMapper.queryBookById(id);
       }
   
       public List<Books> queryAllBook() {
           return bookMapper.queryAllBook();
       }
   }
   ```

### 5、Spring层

**Spring层只是整合dao层和service层**

1. 编写spring-dao.xml文件，整合dao层

   1. 关联数据库配置文件，原本是mybatis，现在让spring做
   2. 配置连接池(数据源)
   3. 注入sqlSessionFactory
   4. 配置dao接口扫描包（之前是手动写一个Impl实现类，现在自动扫描）

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
   
   
       <!--1.关联数据库配置文件-->
       <context:property-placeholder location="classpath:database.properties"/>
   
       <!--2.连接池：
       dbcp:半自动化操作，不能自动连接
       c3p0:自动化操作（自动化的加载配置文件，并且可以自动设置到对象中）
       druid:
       hikari:
       -->
       <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
           <property name="driverClass" value="${jdbc.driver}"/>
           <property name="jdbcUrl" value="${jdbc.url}"/>
           <property name="user" value="${jdbc.username}"/>
           <property name="password" value="${jdbc.password}"/>
           <!--c3p0连接池私有属性-->
           <property name="maxPoolSize" value="30"/>
           <property name="minPoolSize" value="10"/>
           <!--关闭连接后不自动commit-->
           <property name="autoCommitOnClose" value="false"/>
           <!--获取连接超时时间-->
           <property name="checkoutTimeout" value="10000"/>
           <!--当获取连接失败后重试次数-->
           <property name="acquireRetryAttempts" value="2"/>
       </bean>
   
       <!--3.sqlSessionFactory-->
       <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
           <property name="dataSource" ref="dataSource"/>
           <!--绑定mybatis的配置文件-->
           <property name="configLocation" value="classpath:mybatis-config.xml"/>
       </bean>
   
       <!--配置dao接口扫描包，动态的实现了dao接口可以注入到spring容器中-->
       <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
           <!--注入 sqlSessionFactory-->
           <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
           <!--要扫描的包-->
           <property name="basePackage" value="com.ferron.dao"/>
       </bean>
   
   </beans>
   ```

2. 编写spring-service.xml配置文件，整合service层

   1. 配置自动扫描service的包
   2. 将我们的业务类注入spring，可以使用配置，也可以使用注解
   3. 声明式事务配置
   4. aop事务支持

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
   
       <!--1.扫描service的包-->
       <context:component-scan base-package="com.ferron.service"/>
   
       <!--2.将我们所有的业务类，注入到Spring，可以通过配置，也可以通过注解@Service-->
       <bean id="BookServiceimpl" class="com.ferron.service.BookServiceImpl">
           <property name="bookMapper" ref="bookMapper"/>
       </bean>
   
       <!--3.声明式事务配置-->
       <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
           <!--注入数据源-->
           <property name="dataSource" ref="dataSource"/>
       </bean>
   
       <!--4.aop事务支持-->
   
   </beans>
   ```

3. 在applicationContext.xml中import写好的spring-dao.xml和spring-service.xml

### 6、SpringMVC层

1. 添加web框架支持

2. 编写web.xml

   **注意：这里绑定的contextConfigLocation是总的applicationContext.xml**

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
            version="4.0">
   
       <!--DispatcherServlet-->
       <servlet>
           <servlet-name>springmvc</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
           <init-param>
               <param-name>contextConfigLocation</param-name>
               <param-value>classpath:applicationContext.xml</param-value>
           </init-param>
           <load-on-startup>1</load-on-startup>
       </servlet>
       <servlet-mapping>
           <servlet-name>springmvc</servlet-name>
           <url-pattern>/</url-pattern>
       </servlet-mapping>
   
       <!--json乱码过滤-->
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
       
       <!--Session-->
       <session-config>
           <session-timeout>15</session-timeout>
       </session-config>
   
   </web-app>
   ```

3. 编写spring-mvc.xml

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
           http://www.springframework.org/schema/mvc/spring-mvc.xsd">
   
       <!--注解驱动-->
       <mvc:annotation-driven/>
       <!--静态资源过滤，让其不走视图解析器-->
       <mvc:default-servlet-handler/>
       <!--扫描包-->
       <context:component-scan base-package="com.ferron.controller"/>
   
       <!--视图解析器-->
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <property name="prefix" value="/WEB-INF/jsp/"/>
           <property name="suffix" value=".jsp"/>
       </bean>
   
       
   </beans>
   ```

4. 在WEB-INF目录下建jsp文件夹

### 7、小提示

1. 在项目结构中添加lib。

### 8、业务

#### 8.1、查询所有书籍

1. 用户进入首页，点击进入书籍页面
2. 控制器收到请求，调service方法查询所有书籍，并且返回视图层
3. 进入allBook.jsp页面，展示书籍

首页

```jsp
<h3>
  <a href="${pageContext.request.contextPath}/book/allBooks" >进入书籍页面</a>
</h3>
```

控制器

```java
//查询全部书籍，并且返回一个书籍展示页面
@RequestMapping("/allBooks")
public String list(Model model){
    List<Books> list = bookService.queryAllBook();
    model.addAttribute("list",list);

    return "allBook";
}
```

allbook.jsp

```jsp
<div class="row clearfix">
    <div class="col-md-12 columns">
        <table class="table table-hover table-striped">
            <thead>
                <tr>
                    <th>书籍编号</th>
                    <th>书籍名称</th>
                    <th>书籍数量</th>
                    <th>书籍详情</th>
                    <th>操作</th>

                </tr>
            </thead>
            <%--书籍从数据库中查询出来，从这个list中遍历出来：foreach--%>
            <tbody>
                <c:forEach var="book" items="${list}">
                    <tr>
                        <td>${book.bookID}</td>
                        <td>${book.bookName}</td>
                        <td>${book.bookCounts}</td>
                        <td>${book.detail}</td>
                    </tr>
                </c:forEach>
            </tbody>
        </table>
    </div>
</div>
```

#### 8.2、增加书籍

1. 用户在全部书籍页面点击增加书籍按钮，跳转到增加书籍页面
2. 编写跳转到增加书籍页面控制器
3. 增加书籍页面有form表单，输入书籍信息之后点击增加，跳转回所有书籍页面
4. 编写增加书籍控制器。



增加书籍

```jsp
<a class="btn btn-primary" href="${pageContext.request.contextPath}/book/toAddPaper">新增书籍</a>
```

跳转到增加书籍页面控制器

```java
//跳转到增加书籍页面
@RequestMapping("toAddPaper")
public String toAddPaper(){
    return "addBook";
}
```

addBook.jsp

- name为实体类中的字段名
- required表示表单必须全部填满。

```jsp
<form action="${pageContext.request.contextPath}/book/addBook" method="post">
    <div class="form-group">
        <label>书籍名称：</label>
        <input type="text" name="bookName" class="form-control" required>
    </div>
    <div class="form-group">
        <label>书籍数量：</label>
        <input type="text" name="bookCounts" class="form-control" required>
    </div>
    <div class="form-group">
        <label>书籍描述：</label>
        <input type="text" name="detail" class="form-control" required>
    </div>
    <div class="form-group">
        <input type="submit" class="form-control" value="添加">
    </div>
</form>
```

增加书籍控制器

```java
//添加书籍的请求
@RequestMapping("/addBook")
public String addBook(Books books){
    bookService.addBook(books);
    return "redirect:/book/allBooks";  //重定向到一个请求
}
```

#### 8.3、修改和删除书籍

1. 在所有书籍背后增加一个修改和删除的按钮
2. 点击修改，跳转至修改书籍页面，编写跳转修改书籍页面的控制器和页面
3. 点击修改书籍页面的修改按钮，实现修改。编写修改书籍的控制器
4. 点击删除按钮，实现删除功能，编写删除控制器

按钮

```jsp
<td>
    <a href="${pageContext.request.contextPath}/book/toUpdatePaper?id=${book.bookID}">修改</a>
    &nbsp; | &nbsp;
    <a href="${pageContext.request.contextPath}/book/deleteBook/${book.bookID}">删除</a>
</td>
```

跳转修改书籍页面的控制器

- 跳转过去之后需要显示原书籍的信息，因此需要携带原书籍id。

```java
//跳转到修改书籍页面
//需要携带一些数据
@RequestMapping("toUpdatePaper")
public String toUpdatePaper(int id, Model model){
    //修改书籍的时候需要携带原书籍的信息
    Books books = bookService.queryBookById(id);
    model.addAttribute("QBook",books);
    return "updateBook";
}
```

修改书籍页面

- 修改时需要传递书籍id，但是这里并没有，需要前端传递隐藏域来传递

```jsp
<form action="${pageContext.request.contextPath}/book/updateBook" method="post">

    <%--出现的问题：我们提交了修改的SQL请求，但是修改失败，初次考虑是事务问题，配置完事务依然失败
    看一下SQL语句，SQL执行失败，修改未完成--%>
    <%--需要前端传递隐藏域--%>
    <input type="hidden" name="bookID" value="${QBook.bookID}">
    <div class="form-group">
        <label>书籍名称：</label>
        <input type="text" name="bookName" class="form-control" value="${QBook.bookName}" required>
    </div>
    <div class="form-group">
        <label>书籍数量：</label>
        <input type="text" name="bookCounts" class="form-control" value="${QBook.bookCounts}" required>
    </div>
    <div class="form-group">
        <label>书籍描述：</label>
        <input type="text" name="detail" class="form-control" value="${QBook.detail}" required>
    </div>
    <div class="form-group">
        <input type="submit" class="form-control" value="修改">
    </div>
</form>
```

删除控制器

```java
//删除数据
//Restful风格
@RequestMapping("/deleteBook/{bookId}")
public String deleteBook(@PathVariable("bookId") int id){
    bookService.deleteBookById(id);
    return "redirect:/book/allBooks";
}
```

#### 8.4、根据书名查询

1. 在所有书籍页面增加查询框
2. 输入书名，点击查询。
3. 先重新写dao层的接口，然后写实现的sql，再写service层的接口和实现类。
4. 编写查询的控制器
5. 查询到则显示查询到的结果，查询不到则显示原数据





