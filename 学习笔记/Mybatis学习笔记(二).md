---
title: Mybatis学习笔记(二)
tags: Mybatis
categories: 学习笔记
description: Mybatis第二部分学习笔记，主要介绍了Mybatis的日志工厂、分页、以及如果使用注解开发，最后介绍了Lombok的使用
---


## 6、日志

### 6.1、日志工厂

如果一个数据库操作，出现了异常，我们需要排错。日志就是最好的助手

曾经：sout，debug

现在：日志工厂！

![image-20201224150253402](http://fl.ljuuu.com/image-20201224150253402.png)

- SLF4J 

- LOG4J 【掌握】

- LOG4J2 

- JDK_LOGGING 

- COMMONS_LOGGING

- STDOUT_LOGGING【掌握】 

- NO_LOGGING              

在Mybatis中具体使用哪一个日志实现，在设置中设定！

**STDOUT_LOGGING标准日志输出**

在mybatis核心配置文件中配置我们的日志

```xml
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

![image-20201224150919855](http://fl.ljuuu.com/image-20201224150919855.png)

### 6.2、LOG4J

什么是LOG4J：

- Log4j是Apache的一个开源项目，通过使用Log4j，我们可以控制日志信息输送的目的地是控制台、文件、GUI组件。
- 我们也可以控制每一条日志的输出格式
- 通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程
- 通过一个配置文件来灵活地进行配置，而不需要修改应用的代码。

1. 先导入LOG4J的包

   ```xml
   <!-- 导入LOG4J日志 -->
   <dependency>
       <groupId>log4j</groupId>
       <artifactId>log4j</artifactId>
       <version>1.2.17</version>
   </dependency>
   ```

2. log4j.properties

   ```properties
   ### 配置根 ###
   log4j.rootLogger = DEBUG,console,file
   
   ### 设置输出sql的级别，其中logger后面的内容全部为jar包中所包含的包名 ###
   log4j.logger.org.apache=DEBUG
   log4j.logger.java.sql.Connection=DEBUG
   log4j.logger.java.sql.Statement=DEBUG
   log4j.logger.java.sql.PreparedStatement=DEBUG
   log4j.logger.java.sql.ResultSet=DEBUG
   
   ### 配置输出到控制台 ###
   log4j.appender.console = org.apache.log4j.ConsoleAppender
   log4j.appender.console.Target = System.out
   log4j.appender.console.Threshold = DEBUG
   log4j.appender.console.layout = org.apache.log4j.PatternLayout
   log4j.appender.console.layout.ConversionPattern =  %d{ABSOLUTE} %5p %c{ 1 }:%L - %m%n
   
   ### 配置输出到文件 ###
   log4j.appender.file = org.apache.log4j.FileAppender
   log4j.appender.filer.File = ./log/ferron.log
   log4j.appender.file.Append = true
   log4j.appender.file.Threshold = DEBUG
   log4j.appender.file.layout = org.apache.log4j.PatternLayout
   log4j.appender.file.layout.ConversionPattern = %-d{yyyy-MM-dd HH:mm:ss}  [ %t:%r ] - [ %p ]  %m%n
   ```

3. 配置log4j为日志的实现

   ```xml
   <settings>
       <!--LOG4J日志的实现-->
       <setting name="logImpl" value="LOG4J"/>
   </settings>
   ```

简单使用

1. 在要使用Log4j的类中，导入包import org.apache.log4j.Logger

2. 日志对象，参数为当前类的class

   ```java
   static Logger logger = Logger.getLogger(UserMapperTest.class);
   ```

3. 日志级别

   ```java
   logger.info("info:进入了testLog4j");
   logger.debug("debug:进入了testLog4j");
   logger.error("error:进入了testLog4j");
   ```


## 7、分页

**思考：为什么要分页？**

- 减少数据的处理量

### 7.1、使用Limit分页

```sql
select * from user limit startIndex,pageSize;
```

使用Mybatis实现分页，核心SQL

1. 接口

   ```java
   List<User> getUserByLimit(Map<String,Integer> map);
   ```

2. Mapper.XML

   ```xml
   <!--分页-->
   <select id="getUserByLimit" parameterType="map" resultMap="UserMap">
       select * from mybatis.user limit #{startIndex}, #{pageSize}
   </select>
   ```

3. 测试

   ```java
   @Test
   public void getUserByLimit(){
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       UserMapper mapper = sqlSession.getMapper(UserMapper.class);
   
       HashMap<String, Integer> map = new HashMap<>();
       map.put("startIndex",1);
       map.put("pageSize",2);
   
       List<User> userList = mapper.getUserByLimit(map);
       for (User user : userList) {
           System.out.println(user);
       }
   
       sqlSession.close();
   
   }
   ```

### 7.2、RowBounds分页

不再使用sql实现分页，而是使用java实现分页。

### 7.3、分页插件

PageHelper

## 8、使用注解开发

### 8.1、面向接口编程

**根本原因：解耦，可拓展，提高复用，分层开发中，上层不用管具体的实现，大家都遵守共同的标准，使得开发变得容易，规范性更好**

**关于接口的理解**

- 接口从更深层次理解，应该是定义(规范，约束)与实现(名实分离)的分离
- 接口的本身反映了系统设计人员对系统的抽象理解
- 接口有两类：
  - 第一类是对一个个体的抽象，它对应abstract class
  - 第二类是对一个个体某一方面的抽象，它对应interface

### 8.2、使用注解开发

![image-20201225150821441](http://fl.ljuuu.com/image-20201225150821441.png)



- 使用注解来映射简单语句会使代码显得更加简洁，但对于稍微复杂一点的语句，Java注解不仅力不从心，还会让你本就复杂的 SQL 语句更加混乱不堪。

- 如果你需要做一些很复杂的操作，最好用 XML 来映射语句。

1. 注解在接口上实现

   ```java
   @Select("select * from user")
   List<User> getUsers();
   ```

2. 需要在核心配置文件绑定接口

   ```xml
   <!--绑定接口-->
   <mappers>
       <mapper class="com.ferron.dao.UserMapper"/>
   </mappers>
   ```

3. 测试使用

   ```java
   @Test
   public void test(){
       SqlSession sqlSession = MybatisUtils.getSqlSession();
       UserMapper mapper = sqlSession.getMapper(UserMapper.class);
   
       List<User> users = mapper.getUsers();
       for (User user : users) {
           System.out.println(user);
       }
   
       sqlSession.close();
   }
   ```

**本质：反射机制实现**

**底层：动态代理**

Mybatis详细的执行流程：

![image-20201225153906284](http://fl.ljuuu.com/image-20201225153906284.png)

### 8.4、CRUD

**我们可以在工具类创建的时候实现自动提交事务**

```java
public static SqlSession getSqlSession(){
    //返回类似JDBC中connection的对象
    return sqlSessionFactory.openSession(true);
}
```

步骤：

1. 接口

   ```java
   @Select("select * from user")
   List<User> getUsers();
   
   //方法存在多个参数时，所有的参数必须加上@Param注解
   @Select("select * from user where id=#{id}")
   User getUserByID(@Param("id") int id);
   
   @Insert("insert into user(id,name,pwd) values(#{id},#{name},#{password})")
   int addUser(User user);
   
   @Update("update user set name=#{name}, pwd=#{password} where id=#{id}")
   int updateUser(User user);
   
   @Delete("delete from user where id=#{uid}")
   int deleteUser(@Param("uid") int id);
   ```

2. 在核心配置文件中绑定接口

   **重点注意：方法存在多个参数时，所有的参数必须加上@Param注解**

   ```xml
   <!--绑定接口-->
   <mappers>
       <mapper class="com.ferron.dao.UserMapper"/>
   </mappers>
   ```

3. 测试

   ```java
   //查询所有用户
   List<User> users = mapper.getUsers();
   for (User user : users) {
       System.out.println(user);
   }
   
   //按id查询用户
   User user = mapper.getUserByID(1);
   System.out.println(user);
   
   //增加用户
   mapper.addUser(new User(5,"ferron5","456123"));
   //修改用户
   mapper.updateUser(new User(5,"ferron5","423568"));
   //删除用户
   mapper.deleteUser(5);
   ```

### 8.5、关于@Param()注解

- 基本类型的参数或者String类型，需要加上
- 引用类型不需要加
- 如果只有一个基本类型的话，可以忽略，但是建议加上
- 在SQL语句中引用的就是我们这里的@Param()中设定的属性名！

￥{}和#{}的区别

## 9、Lombok

**简化POJO**

```java
Project Lombok is a java library that automatically plugs into your editor and build tools, spicing up your java.
Never write another getter or equals method again, with one annotation your class has a fully featured builder, Automate your logging variables, and much more. 
```

1. 在idea中安装Lombok，setting->plugin

2. 在项目导入lombok的jar包

3. 使用

   ```java
   @Getter and @Setter
   @FieldNameConstants
   @ToString
   @EqualsAndHashCode
   @AllArgsConstructor, @RequiredArgsConstructor and @NoArgsConstructor
   @Log, @Log4j, @Log4j2, @Slf4j, @XSlf4j, @CommonsLog, @JBossLog, @Flogger, @CustomLog
   @Data
   @Builder
   @SuperBuilder
   @Singular
   @Delegate
   @Value
   @Accessors
   @Wither
   @With
   @SneakyThrows
   @val
   @var
   ```

   说明

   ```java
   @Data：无参构造，get、set、tostring，hashcode，equals
   @AllArgsConstructor：有参
   @NoArgsConstructor：无参
   .....
   ```
