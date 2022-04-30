---
title: Mybatis学习笔记(一)
tags: Mybatis
categories: 学习笔记
description: Mybatis第一部分学习笔记，主要介绍了Mybatis的基本概念、运行了第一个Mybatis程序、做了第一个CRUD、以及去解析Mybatis的配置文件
---

## 1、简介

### 1.1、什么是Mybatis

- MyBatis 是一款优秀的**持久层框架**
- 它支持自定义 SQL、存储过程以及高级映射。
- MyBatis免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。
- MyBatis 本是apache的一个开源项目iBatis, 2010年这个项目由apache software foundation 迁移到google code，并且改名为MyBatis 。2013年11月迁移到Github。

如何获得Mybatis：

Maven：

```xml
<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.2</version>
</dependency>

```

### 1.2、什么是持久化

数据持久化

- 持久化就是将程序的数据在持久状态和瞬时状态转化的过程
- 内存：**断电即失**
- 数据库(jdbc)：io文件持久化

为什么需要持久化：

- 有一些对象我们不能让他丢掉
- 内存昂贵

### 1.3、持久层

Dao层，Service层，Controller层

- 完成持久化工作的代码块
- 层界限十分明显

### 1.4、为什么需要Mybatis

- 帮助程序员将数据存入到数据库中。

- 方便
- 传统的JDBC代码太复杂了。简化。框架。自动化。
- 不用Mybatis也可以。更容易上手。**技术没有高低之分**
- 优点：
  - 简单易学，灵活
  - sql和代码的分离，提高可维护性
  - 提供映射标签，支持对象与数据库的orm字段关系映射
  - 提供对象关系映射标签，支持对象关系组建维护
  - 提供xml标签，支持编写动态sql

最重要的一点：使用的人多！

Spring-->SpringMVC-->SpringBoot

## 2、第一个Mybatis程序

思路：搭建环境-->导入Mybatis-->测试

### 2.1、搭建环境

搭建数据库

新建项目

1. 新建一个普通的maven项目
2. 删除src目录，变成父工程
3. 导入Maven依赖

### 2.2、创建模块

- 编写mybatis核心配置文件

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <!--configuration核心配置文件-->
  <configuration>
      <environments default="development">
          <environment id="development">
              <transactionManager type="JDBC"/>
              <dataSource type="POOLED">
                  <property name="driver" value="com.mysql.jdbc.Driver"/>
                  <!--在xml文件中&amp;相当于一个&-->
                  <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                  <property name="username" value="root"/>
                  <property name="password" value="sanfashi"/>
              </dataSource>
          </environment>
      </environments>
      <mappers>
          <mapper resource="org/mybatis/example/BlogMapper.xml"/>
      </mappers>
  </configuration>
  ```

- 编写mybatis工具类

  ![image-20201222145851992](http://fl.ljuuu.com/image-20201222145851992.png)

  ```java
  //sqlSessionFactory --> sqlSession
  public class MybatisUtils {
      //上升作用域
      private static SqlSessionFactory sqlSessionFactory;
  
      static {
          try {
              //使用mybatis第一步：获取sqlSessionFactory对象
              String resource = "mybatis-config.xml";
              InputStream inputStream = Resources.getResourceAsStream(resource);
              sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
          } catch (IOException e) {
              e.printStackTrace();
          }
      }
  
      //既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。
      //SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。
      public static SqlSession getSqlSession(){
          //返回类似JDBC中connection的对象
          return sqlSessionFactory.openSession();
      }
  }
  ```

### 2.3、编写代码

- 实体类

  ```java
  public class User {
      private int id;
      private String name;
      private String pwd;
  
      public User() {
      }
  
      public User(int id, String name, String pwd) {
          this.id = id;
          this.name = name;
          this.pwd = pwd;
      }
  
      public int getId() {
          return id;
      }
  
      public void setId(int id) {
          this.id = id;
      }
  
      public String getName() {
          return name;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  
      public String getPwd() {
          return pwd;
      }
  
      public void setPwd(String pwd) {
          this.pwd = pwd;
      }
  
      @Override
      public String toString() {
          return "User{" +
                  "id=" + id +
                  ", name='" + name + '\'' +
                  ", pwd='" + pwd + '\'' +
                  '}';
      }
  }
  ```

- Dao接口

  ```java
  public interface UserDao {
      List<User> getUserList();
  }
  ```

- 接口实现类由原来的Impl转化成一个xml文件

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <!--namespace绑定一个对应的Dao/Mapper接口-->
  <!--相当于实现了UserDao接口-->
  <mapper namespace="com.ferron.dao.UserDao">
      <!--查询语句-->
      <!--写一个标签，相当于重写了接口里面的方法，方法名就是id-->
      <!--resultType是相当于返回值的类型（权限命名）-->
      <select id="getUserList" resultType="com.ferron.pojo.User">
          select * from mybatis.user
      </select>
  </mapper>
  ```

### 2.4、测试

注意点：

org.apache.ibatis.binding.BindingException: Type interface com.ferron.dao.UserDao is not known to the MapperRegistry.

MapperRegistry注册：

- junit测试

  ```java
  @Test
      public void test(){
          //第一步：获得SqlSession对象
          SqlSession sqlSession = MybatisUtils.getSqlSession();
          //执行sql
          UserDao mapper = sqlSession.getMapper(UserDao.class);
          List<User> userList = mapper.getUserList();
  
          //输出
          for (User user:userList) {
              System.out.println(user);
          }
  
          //关闭sqlSession
          sqlSession.close();
      }
  ```

![image-20201222195421200](http://fl.ljuuu.com/image-20201222195421200.png)

## 3、CRUD

### 3.1、namespace

namespace中的包名要和Dao/Mapper接口的包名要一致

### 3.2、select

选择，查询语句：

- id：就是对应的namespace中的方法名；
- resultType：sql语句执行的返回值！
- parameterType：参数类型

1. 编写接口

   ```java
   //根据id查询用户
   User getUserById(int id);
   ```

2. 编写对应的mapper中的sql语句

   ```xml
   <!--传入的参数用#{id}表示-->
       <select id="getUserById" parameterType="int" resultType="com.ferron.pojo.User">
           select * from mybatis.user where id=#{id}
       </select>
   ```

3. 测试

   ```java
   @Test
       public void getUserById() {
           //第一步：获得SqlSession对象
           SqlSession sqlSession = MybatisUtils.getSqlSession();
           try {
               //执行sql
               UserMapper mapper = sqlSession.getMapper(UserMapper.class);
               User user = mapper.getUserById(1);
               //输出
               System.out.println(user);
           } catch (Exception e) {
               e.printStackTrace();
           } finally {
               //关闭sqlSession
               sqlSession.close();
           }
       }
   ```

### 3.3、insert

```xml
<insert id="addUser" parameterType="com.ferron.pojo.User">
        insert into mybatis.user (id,name,pwd) values (#{id},#{name},#{pwd})
</insert>  
```

### 3.4、update

```xml
<update id="updateUser" parameterType="com.ferron.pojo.User">
        update mybatis.user set name=#{name},pwd=#{pwd} where id=#{id};
</update>    
```

### 3.5、delete

```xml
<delete id="deleteUser" parameterType="int">
        delete from mybatis.user where id=#{id}
</delete>
```

注意点：**增删改需要提交事务**

### 3.6、分析错误

- 标签不要匹配错
- recourse绑定mapper需要用/不要.

### 3.7、万能Map

假设我们的实体类，或者数据库中的表，字段或者参数过多，我们应该考虑使用Map！

- 使用Map添加用户

```java
//万能Mapper
int addUser2(Map<String,Object> map);
```

```xml
<!--万能Mapper-->
<insert id="addUser" parameterType="map">
    insert into mybatis.user (id,pwd) values (#{userid},#{password})
</insert>
```

- 使用Map查询用户

```java
User getUserById2(Map<String,Object> map);
```

```xml
<select id="getUserById2" parameterType="map" resultType="com.ferron.pojo.User">
    select * from mybatis.user where id=#{id}
</select>
```

```java
@Test
    public void getUserById2() {
        //第一步：获得SqlSession对象
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        try {
            //执行sql
            UserMapper mapper = sqlSession.getMapper(UserMapper.class);

            Map<String, Object> map = new HashMap<String, Object>();
            map.put("id",1);
            User user = mapper.getUserById2(map);

            //输出
            System.out.println(user);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //关闭sqlSession
            sqlSession.close();
        }
    }
```

Mapper传递参数，直接在sql取出key即可【parameterType="map"】

而对象传递参数，直接在sql中取出对象即可【parameterType="Object"】

只有一个基本类型参数的情况下，可以直接在sql中取到

多个参数用Map，**或者注解**！

### 3.8、模糊查询

1. java执行的时候，传递通配符%%

   ```java
   List<User> userlist = mapper.getUserLike("%ferron%");
   ```

2. 在sql拼接中使用通配符！

   ```sql
   select * from mybatis.user where name like "%"#{value}"%";
   ```

## 4、配置解析

### 4.1、核心配置文件

- mybatis-config.xml

- MyBatis 的配置文件包含了会深深影响 MyBatis 行为的设置和属性信息。

  ```xml
  configuration（配置）
  properties（属性）
  settings（设置）
  typeAliases（类型别名）
  typeHandlers（类型处理器）
  objectFactory（对象工厂）
  plugins（插件）
  environments（环境配置）
  environment（环境变量）
  transactionManager（事务管理器）
  dataSource（数据源）
  databaseIdProvider（数据库厂商标识）
  mappers（映射器）
  ```

### 4.2、环境配置（environments）

**尽管可以配置多个环境，但每个 SqlSessionFactory实例只能选择一种环境。**

![image-20201223161254544](http://fl.ljuuu.com/image-20201223161254544.png)



学会使用配置多套运行环境！

Mybatis默认的事务管理器就是JDBC，连接池是POOLED

### 4.3、属性（properties）

我们可以通过properties属性实现来引用配置文件

这些属性可以在外部进行配置，并可以进行动态替换。你既可以在典型的 Java 属性文件中配置这些属性，也可以在 properties 元素的子元素中设置。【db.properties】

1. 编写一个配置文件

   ```properties
   driver="com.mysql.jdbc.Driver"
   url="jdbc:mysql://localhost:3306/mybatis?useSSL=false&useUnicode=truecharacterEncoding=UTF-8"
   username=root
   password=sanfashi
   ```

2. 在核心配置文件中引入【在所有的xml中，标签都要按顺序写】

   ```xml
   <properties resource="db.properties"/>
   ```

- 可以直接引入外部文件
- 可以在其中增加一些属性配置
- 如果两个文件有同一个字段，优先使用外部配置文件

### 4.4、类型别名（typeAliases）

- 类型别名可为 Java 类型设置一个缩写名字。

- 它仅用于 XML 配置，意在降低冗余的全限定类名书写。

  - 第一种方式

  ```xml
  <!--可以给实体类取别名-->
  <typeAliases>
      <typeAlias type="com.ferron.pojo.User" alias="User"/>
  </typeAliases>
  ```

  - 第二种方式：也可以指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean，扫描实体类的包，它的默认别名就是这个类的类名，**首字母小写**！

  ```xml
  <typeAliases>
      <package name="com.ferron.pojo"/>
  </typeAliases>
  ```

- 在实体类比较少的时候，使用第一种方式，实体类十分多的时候使用第二种方法，第一种可以DIY别名，第二种只能通过**注解**来DIY

下面是一些为常见的 Java 类型内建的类型别名。

| 别名       | 映射的类型 |
| ---------- | ---------- |
| _byte      | byte       |
| _long      | long       |
| _short     | short      |
| _int       | int        |
| _integer   | int        |
| _double    | double     |
| _float     | float      |
| _boolean   | boolean    |
| string     | String     |
| byte       | Byte       |
| long       | Long       |
| short      | Short      |
| int        | Integer    |
| integer    | Integer    |
| double     | Double     |
| float      | Float      |
| boolean    | Boolean    |
| date       | Date       |
| decimal    | BigDecimal |
| bigdecimal | BigDecimal |
| object     | Object     |
| map        | Map        |
| hashmap    | HashMap    |
| list       | List       |
| arraylist  | ArrayList  |
| collection | Collection |
| iterator   | Iterator   |

### 4.5、设置

| 设置名             | 描述                                                         | 有效值                                                       | 默认值 |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------ |
| cacheEnabled       | 全局性地开启或关闭所有映射器配置文件中已配置的任何缓存。     | true \| false                                                | true   |
| lazyLoadingEnabled | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。                特定关联关系中可通过设置 `fetchType`                属性来覆盖该项的开关状态。 | true \| false                                                | false  |
| logImpl            | 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。        | SLF4J \| LOG4J \| LOG4J2 \| JDK_LOGGING \| COMMONS_LOGGING \| STDOUT_LOGGING \| NO_LOGGING | 未设置 |

### 4.6、映射器

MapperRegistry：注册绑定我们的Mapper文件

方式一：使用相对于类路径的资源引用【推荐使用】

```xml
<mappers>
    <mapper resource="com/ferron/dao/UserMapper.xml"/>
</mappers>
```

方式二：使用映射器接口实现类的完全限定类名

```xml
<mappers>
    <mapper class="com.ferron.dao.UserMapper"/>
</mappers>
```

方式三：将包内的映射器接口实现全部注册为映射器

```xml
<mappers>
    <package class="com.ferron.dao"/>
</mappers>
```

方式二和方式三注意点：

- 接口和他的Mapper配置文件必须同名！
- 接口和他的Mapper配置文件必须在同一个包下！

### 4.7、其他

- typeHandlers（类型处理器）
- objectFactory（对象工厂）
- plugins插件
  - mybatis-generator-core
  - mybatis plus

### 4.8、生命周期和作用域

作用域和生命周期类别是至关重要的，因为错误的使用会导致非常严重的**并发问题**。

**SqlSessionFactoryBuilder**：

- 一旦创建了 SqlSessionFactory，就不再需要它了。
- 局部变量

**SqlSessionFactory**：

- 说白了可以想象成就是数据库连接池
- 一旦被创建就应该在应用的运行期间一直存在，**没有任何理由丢弃它或重新创建另一个实例**。  
- 佳作用域是应用作用域。 
- 最佳的作用域是请求或方法作用域。

**SqlSession**：

- 连接池的一个请求！
- SqlSession的实例不是线程安全的，因此是不能被共享的，所以它的最佳的作用域是请求或方法作用域。
- 用完之后需要感觉关闭，否则资源会被占用

![image-20201223172046489](http://fl.ljuuu.com/image-20201223172046489.png)

这里的每一个Mapper，就代表一个具体的业务：

![image-20201223172334636](http://fl.ljuuu.com/image-20201223172334636.png)



## 5、解决属性名和字段名不一致的问题

### 5.1、问题

数据库中的字段

![image-20201224102731795](http://fl.ljuuu.com/image-20201224102731795.png)



新建一个项目，拷贝之前的，测试实体类字段与数据库字段不一致的情况

```java
public class User {
    private int id;
    private String name;
    private String password;
}
```

测试出现问题

![image-20201224103807437](http://fl.ljuuu.com/image-20201224103807437.png)



```xml
select * from mybatis.user where id=#{id}
//类型处理器
select id,name,pwd from mybatis.user where id=#{id}
```

解决方法：

- 起别名

  ```sql
  select id,name,pwd as password from mybatis.user where id=#{id}
  ```

### 5.2、resultMap

结果集映射

```java
id name pwd
id name password
```

```xml
<!--解决字段不一致的方法就是使用resultMap-->
<select id="getUserById" resultMap="UserMap">
    select * from mybatis.user where id=#{id}
</select>
<!--结果集映射-->
<resultMap id="UserMap" type="User">
    <!--column是数据库中的字段，property是实体类中的属性-->
    <result column="id" property="id"/>
    <result column="name" property="name"/>
    <result column="pwd" property="password"/>
</resultMap>
```

- `resultMap` 元素是 MyBatis 中最重要最强大的元素。
- ResultMap的设计思想是，对简单的语句做到零配置，对于复杂一点的语句，只需要描述语句之间的关系就行了。        

![image-20201224111146840](http://fl.ljuuu.com/image-20201224111146840.png)
