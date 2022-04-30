# Mybatis

## 1、简介

#### 1.1、什么是Mybatis

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

#### 1.2、什么是持久化

数据持久化

- 持久化就是将程序的数据在持久状态和瞬时状态转化的过程
- 内存：**断电即失**
- 数据库(jdbc)：io文件持久化

为什么需要持久化：

- 有一些对象我们不能让他丢掉
- 内存昂贵

#### 1.3、持久层

Dao层，Service层，Controller层

- 完成持久化工作的代码块
- 层界限十分明显

#### 1.4、为什么需要Mybatis

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

#### 2.1、搭建环境

搭建数据库

新建项目

1. 新建一个普通的maven项目
2. 删除src目录，变成父工程
3. 导入Maven依赖

#### 2.2、创建模块

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

  ![image-20201222145851992](D:\Application\Typora\image\image-20201222145851992.png)

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

#### 2.3、编写代码

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

#### 2.4、测试

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

![image-20201222195421200](D:\Application\Typora\image\image-20201222195421200.png)



## 3、CRUD

#### 3.1、namespace

namespace中的包名要和Dao/Mapper接口的包名要一致

#### 3.2、select

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

#### 3.3、insert

```xml
<insert id="addUser" parameterType="com.ferron.pojo.User">
        insert into mybatis.user (id,name,pwd) values (#{id},#{name},#{pwd})
</insert>  
```

#### 3.4、update

```xml
<update id="updateUser" parameterType="com.ferron.pojo.User">
        update mybatis.user set name=#{name},pwd=#{pwd} where id=#{id};
</update>    
```

#### 3.5、delete

```xml
<delete id="deleteUser" parameterType="int">
        delete from mybatis.user where id=#{id}
</delete>
```

注意点：**增删改需要提交事务**

#### 3.6、分析错误

- 标签不要匹配错
- recourse绑定mapper需要用/不要.

#### 3.7、万能Map

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

#### 3.8、模糊查询

1. java执行的时候，传递通配符%%

   ```java
   List<User> userlist = mapper.getUserLike("%ferron%");
   ```

2. 在sql拼接中使用通配符！

   ```sql
   select * from mybatis.user where name like "%"#{value}"%";
   ```

## 4、配置解析

#### 4.1、核心配置文件

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

#### 4.2、环境配置（environments）

**尽管可以配置多个环境，但每个 SqlSessionFactory实例只能选择一种环境。**

![image-20201223161254544](D:\Application\Typora\image\image-20201223161254544.png)



学会使用配置多套运行环境！

Mybatis默认的事务管理器就是JDBC，连接池是POOLED

#### 4.3、属性（properties）

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

#### 4.4、类型别名（typeAliases）

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

#### 4.5、设置

| 设置名             | 描述                                                         | 有效值                                                       | 默认值 |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------ |
| cacheEnabled       | 全局性地开启或关闭所有映射器配置文件中已配置的任何缓存。     | true \| false                                                | true   |
| lazyLoadingEnabled | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。                特定关联关系中可通过设置 `fetchType`                属性来覆盖该项的开关状态。 | true \| false                                                | false  |
| logImpl            | 指定 MyBatis 所用日志的具体实现，未指定时将自动查找。        | SLF4J \| LOG4J \| LOG4J2 \| JDK_LOGGING \| COMMONS_LOGGING \| STDOUT_LOGGING \| NO_LOGGING | 未设置 |

#### 3.6、映射器

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

#### 3.7、其他

- typeHandlers（类型处理器）
- objectFactory（对象工厂）
- plugins插件
  - mybatis-generator-core
  - mybatis plus

#### 3.8、生命周期和作用域

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

![image-20201223172046489](D:\Application\Typora\image\image-20201223172046489.png)

这里的每一个Mapper，就代表一个具体的业务：

![image-20201223172334636](D:\Application\Typora\image\image-20201223172334636.png)



## 5、解决属性名和字段名不一致的问题

#### 5.1、问题

数据库中的字段

![image-20201224102731795](D:\Application\Typora\image\image-20201224102731795.png)



新建一个项目，拷贝之前的，测试实体类字段与数据库字段不一致的情况

```java
public class User {
    private int id;
    private String name;
    private String password;
}
```

测试出现问题

![image-20201224103807437](D:\Application\Typora\image\image-20201224103807437.png)



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

#### 5.2、resultMap

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

![image-20201224111146840](D:\Application\Typora\image\image-20201224111146840.png)



## 6、日志

#### 6.1、日志工厂

如果一个数据库操作，出现了异常，我们需要排错。日志就是最好的助手

曾经：sout，debug

现在：日志工厂！

![image-20201224150253402](D:\Application\Typora\image\image-20201224150253402.png)



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

![image-20201224150919855](D:\Application\Typora\image\image-20201224150919855.png)



#### 6.2、LOG4J

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

#### 7.1、使用Limit分页

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

#### 7.2、RowBounds分页

不再使用sql实现分页，而是使用java实现分页。

#### 7.3、分页插件

PageHelper

## 8、使用注解开发

#### 8.1、面向接口编程

**根本原因：解耦，可拓展，提高复用，分层开发中，上层不用管具体的实现，大家都遵守共同的标准，使得开发变得容易，规范性更好**

**关于接口的理解**

- 接口从更深层次理解，应该是定义(规范，约束)与实现(名实分离)的分离
- 接口的本身反映了系统设计人员对系统的抽象理解
- 接口有两类：
  - 第一类是对一个个体的抽象，它对应abstract class
  - 第二类是对一个个体某一方面的抽象，它对应interface

#### 8.2、使用注解开发

![image-20201225150821441](D:\Application\Typora\image\image-20201225150821441.png)



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

![image-20201225153906284](D:\Application\Typora\image\image-20201225153906284.png)



#### 8.3、CRUD

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

#### 8.4关于@Param()注解

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

## 10、多对一

多对一：

- 多个学生，对应一个老师
- 对于学生而言，**关联**..多个学生关联一个老师
- 对于老师而言，**集合**..一个老师有很多学生



SQL：

![image-20201225165913686](D:\Application\Typora\image\image-20201225165913686.png)



#### 10.1、按照查询嵌套处理（子查询）

```xml
<!--
    子查询思路:
    1.查询所有学生
    2.根据查询出来的学生的tid，寻找对应的老师
    -->
<select id="getStudent" resultMap="StudentTeacher">
    select * from student;
</select>
<resultMap id="StudentTeacher" type="Student">
    <result property="id" column="id"/>
    <result property="name" column="name"/>
    <!--复杂的属性我们需要单独处理   对象：association  集合：collection-->
    <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"/>
</resultMap>

<select id="getTeacher" resultType="Teacher">
    select * from teacher where id=#{id}
</select>
```

#### 10.2、按照结果嵌套处理（联表查询）

```java
<!--
按照结果嵌套处理思路:
1.查询所有学生
2.根据查询出来的学生的tid，寻找对应的老师
-->
<select id="getStudent2" resultMap="StudentTeacher2">
    select s.id sid,s.name sname,t.name tname
    from student s,teacher t
    where s.tid = t.id;
</select>
<resultMap id="StudentTeacher2" type="Student">
    <!--查询中使用了别名，column得使用别名-->
    <result property="id" column="sid"/>
    <result property="name" column="sname"/>
    <association property="teacher" javaType="Teacher">
    	<result property="name" column="tname"/>
    </association>
</resultMap>
```

回顾Mysql多对一查询的方式：

- 子查询
- 联表查询

## 11、一对多

比如：一个老师拥有多个学生

对于老师而言，就是一对多的关系。

实体类：

```java
@Data
public class Student {
    private int id;
    private String name;
    private int tid;

}
```

```java
@Data
public class Teacher {
    private int id;
    private String name;
    //一个老师拥有多个学生
    private List<Student> students;
}
```

#### 11.1、按照查询嵌套处理（子查询）

```xml
<!--按查询嵌套查询-->
<select id="getTeacher2" resultMap="TeacherStudent2">
    select * from teacher where id = #{tid}
</select>
<resultMap id="TeacherStudent2" type="Teacher">
    <!--这里要注明javatype和oftype-->
    <collection property="students" javaType="ArrayList" ofType="Student" select="getStudent" column="id"/>
</resultMap>
<select id="getStudent" resultType="Student">
    select * from student where tid=#{tid}
</select>
```

#### 11.2、按照结果嵌套处理（联表查询）

```xml
<!--按结果嵌套查询-->
<select id="getTeacher" resultMap="TeacherStudent">
    select s.id sid, s.name sname, t.name tname, t.id tid
    from teacher t, student s
    where t.id=s.tid and t.id=#{tid};
</select>
<resultMap id="TeacherStudent" type="Teacher">
    <result property="id" column="tid"/>
    <result property="name" column="tname"/>
    <!--复杂的属性我们需要单独处理  对象：association  集合：collection
        javaType:指定属性的类型
        这里不需要使用javatype，集合中的泛型信息，我们使用ofType
        -->
    <collection property="students" ofType="Student">
        <result property="id" column="sid"/>
        <result property="name" column="sname"/>
        <result property="tid" column="tid"/>
    </collection>
</resultMap>
```

小结：

1. 关联：association【多对一】
2. 集合：collection【一对多】
3. javaType  ofType
   1. javatype用来指定实体类中的类型
   2. ofType用来指定映射到List或者集合中的pojo类型，泛型中的约束类型！

注意点：

- 保证sql的可读性，尽量保证通俗易懂
- 注意一对多和多对一，属性名和字段的问题
- 如果问题不好排查错误，可以使用日志

![image-20201226153255041](D:\Application\Typora\image\image-20201226153255041.png)



面试高频：

- MySQL引擎
- InnoDB底层原理
- 索引
- 索引优化

## 12、动态SQL

**什么是动态SQL：就是指不同的条件生成不同的SQL语句**

- if
- choose (when, otherwise)
- trim (where, set)
- foreach

搭建环境

```sql
CREATE TABLE `blog`(
`id` VARCHAR(50) NOT NULL COMMENT '博客id',
`title` VARCHAR(100) NOT NULL COMMENT '博客标题',
`author` VARCHAR(30) NOT NULL COMMENT '博客作者',
`create_time` DATETIME NOT NULL COMMENT '创建时间',
`views` INT(30) NOT NULL COMMENT '浏览量'
)ENGINE=INNODB DEFAULT CHARSET=utf8
```

创建一个基础工程

1. 导包

2. 编写配置文件

3. 编写实体类

   ```java
   @Data
   public class Blog {
       private int id;
       private String title;
       private String author;
       private Date createTime; //属性名和字段名不一致使用"mapUnderscoreToCamelCase" 
       private int views;
   }
   ```

4. 编写实体类对应的Mapper和Mapper.xml文件

#### 12.1、if

```xml
<select id="queryBlogIF" parameterType="map" resultType="blog">
    select * from mybatis.blog where 1=1
    <if test="title != null">
        and title=#{title}
    </if>
    <if test="author != null">
        and author=#{author}
    </if>
</select>
```

#### 12.2、choose(when, otherwise)

**choose:相当于java中的switch，当符合其中一个时就输出符合其中的一个的即可！**

```xml
<select id="queryBlogChoose" parameterType="map" resultType="blog">
    select * from mybatis.blog
    <where>
        <choose>
            <when test="title != null">
                title=#{title}
            </when>
            <when test="author != null">
                and author=#{author}
            </when>
            <otherwise>
                and views=#{views}
            </otherwise>
        </choose>
    </where>
</select>
```

```java
@Test
public void queryBlogChoose(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);

    HashMap map = new HashMap();
    map.put("title","Spring如此简单");
    map.put("author","Ferron1");
    map.put("views",9999);

    List<Blog> blogs = mapper.queryBlogChoose(map);
    for (Blog blog : blogs) {
        System.out.println(blog);
    }

    sqlSession.close();
}
```

#### 12.3、trim(where, set)

where的处理：

![image-20201226164621343](D:\Application\Typora\image\image-20201226164621343.png)



set的处理：

![image-20201226165748174](D:\Application\Typora\image\image-20201226165748174.png)

```xml
<insert id="updateBlog" parameterType="map">
    update mybatis.blog
    <set>
        <if test="title != null">title=#{title},</if>
        <if test="author != null">author=#{author}</if>

    </set>
    where id=#{id}
</insert>
```

```java
@Test
public void updateBlog(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);

    HashMap map = new HashMap();
    map.put("id","8c5be758d87d43ff946eb55683c85f05");
    //map.put("title","Spring如此难");
    map.put("author","Ferron2");
    //map.put("views",9999);

    mapper.updateBlog(map);
    sqlSession.commit();

    sqlSession.close();
}
```

![image-20201226170621731](D:\Application\Typora\image\image-20201226170621731.png)



**所谓的动态SQL，本质还是SQL语句，只不过我们可以在SQL层面去执行一个逻辑代码**

#### 12.4、SQL片段

有的时候，我们可能会将一些功能提取出来，方便复用。

1. 使用SQL标签抽取公共的部分

   ```xml
   <sql id="if-title-author">
       <if test="title != null">
           and title=#{title}
       </if>
       <if test="author != null">
           and author=#{author}
       </if>
   </sql>
   ```

2. 在需要的时候使用include引用即可

   ```xml
   <include refid="if-title-author"></include>
   ```

注意事项：

- 最好基于单表来定义SQL片段
- 不要存在where标签

#### 12.5、foreach

```sql
select * from user where (id=1 or id=2 or id=3)
```

![image-20201226171755106](D:\Application\Typora\image\image-20201226171755106.png)



```xml
<!--
    select * from user where (id=1 or id=2 or id=3)
    我们现在传递一个万能的map，这map中可以存在一个集合！
    -->
<select id="queryBlogForeach" parameterType="map" resultType="blog">
    select * from mybatis.blog
    <where>
        <foreach collection="ids" item="id" open="and (" separator="or" close=")">
            id=#{id}
        </foreach>
    </where>

</select>
```

```java
@Test
public void testforeach(){
    SqlSession sqlSession = MybatisUtils.getSqlSession();
    BlogMapper mapper = sqlSession.getMapper(BlogMapper.class);

    HashMap map = new HashMap();

    ArrayList<Integer> ids = new ArrayList<>();
    ids.add(1);
    ids.add(2);
    ids.add(3);

    map.put("ids",ids);

    List<Blog> blogs = mapper.queryBlogForeach(map);
    for (Blog blog : blogs) {
        System.out.println(blog);
    }

    sqlSession.close();
}
```

总结：

- **什么是动态SQL：就是指不同的条件生成不同的SQL语句**
- **所谓的动态SQL，本质还是SQL语句，只不过我们可以在SQL层面去执行一个逻辑代码**

- **动态SQL就是在拼接SQL语句，我们只要保证SQL的正确性，按照SQL的格式，去排列组合就行了**

## 13、缓存

#### 13.1、简介

```
查询：连接数据库，耗资源！
一次查询的结果，给他暂存一个可以直接取到的地方！-->内存：缓存


```

**读写分离，主从复制**

什么是缓存

- 存在内存中的临时数据
- 将用户经常查询的数据放在缓存中，用户去查询数据不用从磁盘（关系型数据库数据文件）查询，从缓存中查询，从而提高查询效率，解决高并发系统的性能问题。

为什么要使用

- 减少和数据库的交互次数，减少系统开销，提高效率

什么时候使用

- 经常查询并且不经常改变的数据【可以使用缓存】

#### 13.2、Mybatis缓存

- Mybatis包含一个非常强大的查询缓存特性，它可以非常方便地定制和配置缓存。缓存可以极大地提升查询效率
- Mybatis系统默认定义了两级缓存：一级缓存和二级缓存
  - 默认情况下，只有一级缓存开启（SqlSession级别的缓存，也称本地缓存）
  - 二级缓存需要手动开启和配置，是基于namespace级别的缓存
  - 为了提高扩展性，Mybatis定义了缓存接口Cache。我们可以通过Cache接口来定义二级缓存

#### 13.3、一级缓存

一级缓存也叫本地缓存：SqlSession

- 与数据库同一次会话期间查询到的数据会放在本地缓存中
- 以后如果需要获取相同的数据，直接从缓存中拿，不用再去查询数据库



测试：

1. 开启日志

2. 测试在一个Session中查询两次相同记录

3. 查看日志输出

   ![image-20201227143316552](D:\Application\Typora\image\image-20201227143316552.png)

失效的情况：

- 查询不一样的内容
- 使用过增删改方式，重新刷新缓存，因为增删改有可能改变原来的数据，所以需要刷新！
- 查询不同的Mapper
- 手动清除缓存，sqlSession.clearCache();

小结：一级缓存默认是开启的，只在一次SqlSession中有效！也就是连接到关闭连接这个区间。

#### 13.4、二级缓存

- 二级缓存也叫作全局缓存，一级缓存作用域太低。
- 基于namespace级别的缓存，一个名称空间，对应一个二级缓存
- 工作机制
  - 一个会话查询到一条数据，这个数据就会被放在当前会话的一级缓存中
  - 如果会话被关闭，这个会话对应的一级缓存就没有了，但我们想要的是，会话被关闭了，一级缓存中的数据被保存到二级缓存中
  - 新的会话查询信息，就可以从二级缓存中获取内容
  - 不同的mapper查出的数据会放在自己对应的缓存(map)中

步骤：

1. 显式开启全局缓存

   ```xml
   <setting name="cacheEnabled" value="true"/>
   ```

2. 在要使用二级缓存的mapper中使用<Cache/>

   ```xml
   <cache
     eviction="FIFO"
     flushInterval="60000"
     size="512"
     readOnly="true"/>
   ```

   也可以自定义参数

3. 测试

   1. 问题：我们需要将实体类序列化，否则就会报错

      ```java
      implements Ser@Data
      public class User implements Serializable {
          private int id;
          private String name;
          private String pwd;
      }
      ```



小结：

- 只要开启了二级缓存，在同一个Mapper下就有效
- 所有的数据都会放在一级缓存中
- 只有当会话提交，或者关闭时，才会提交到二级缓存中

#### 13.5、缓存原理

![image-20201227145919497](D:\Application\Typora\image\image-20201227145919497.png)

缓存顺序

1. 第一次查询走数据库，先看二级缓存中有没有

2. 若二级缓存没有，再看一级缓存中有没有，

3. 若都没有，查询数据库，并且将数据保存在一级保存

#### 13.6、自定义缓存：ehcache

**Ehcache是一种广泛使用的开源Java分布式缓存。**

1. 导包

   ```xml
   <!-- https://mvnrepository.com/artifact/org.mybatis.caches/mybatis-ehcache -->
   <dependency>
       <groupId>org.mybatis.caches</groupId>
       <artifactId>mybatis-ehcache</artifactId>
       <version>1.2.1</version>
   </dependency>
   
   ```

2. 在mapper中使用

   ```xml
   <cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
   ```

3. 编写文件ehcache.xml缓存配置文件：**可定义缓存策略**

**工作中用缓存：Redis数据库做缓存**

**K-V键值对**

## 14、常用代码

```xml
<!-- mysql驱动 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.47</version>
</dependency>
<!-- mybatis -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.2</version>
</dependency>
<!-- junit单元测试-->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>
<!-- lombok -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.12</version>
    <scope>provided</scope>
</dependency>
```

