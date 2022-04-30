---
title: Mybatis学习笔记(三)
tags: Mybatis
categories: 学习笔记
description: Mybatis第三部分学习笔记，主要介绍了Mybatis的多对一和一对多查询、动态SQL、缓存
---


## 10、多对一

多对一：

- 多个学生，对应一个老师
- 对于学生而言，**关联**..多个学生关联一个老师
- 对于老师而言，**集合**..一个老师有很多学生



SQL：

![image-20201225165913686](http://fl.ljuuu.com/image-20201225165913686.png)



### 10.1、按照查询嵌套处理（子查询）

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

### 10.2、按照结果嵌套处理（联表查询）

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

### 11.1、按照查询嵌套处理（子查询）

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

### 11.2、按照结果嵌套处理（联表查询）

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

![image-20201226153255041](http://fl.ljuuu.com/image-20201226153255041.png)



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

### 12.1、if

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

### 12.2、choose(when, otherwise)

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

### 12.3、trim(where, set)

where的处理：

![image-20201226164621343](http://fl.ljuuu.com/image-20201226164621343.png)

set的处理：

![image-20201226165748174](http://fl.ljuuu.com/image-20201226165748174.png)

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

![image-20201226170621731](http://fl.ljuuu.com/image-20201226170621731.png)

**所谓的动态SQL，本质还是SQL语句，只不过我们可以在SQL层面去执行一个逻辑代码**

### 12.4、SQL片段

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

### 12.5、foreach

```sql
select * from user where (id=1 or id=2 or id=3)
```

![image-20201226171755106](http://fl.ljuuu.com/image-20201226171755106.png)

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

### 13.1、简介

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

### 13.2、Mybatis缓存

- Mybatis包含一个非常强大的查询缓存特性，它可以非常方便地定制和配置缓存。缓存可以极大地提升查询效率
- Mybatis系统默认定义了两级缓存：一级缓存和二级缓存
  - 默认情况下，只有一级缓存开启（SqlSession级别的缓存，也称本地缓存）
  - 二级缓存需要手动开启和配置，是基于namespace级别的缓存
  - 为了提高扩展性，Mybatis定义了缓存接口Cache。我们可以通过Cache接口来定义二级缓存

### 13.3、一级缓存

一级缓存也叫本地缓存：SqlSession

- 与数据库同一次会话期间查询到的数据会放在本地缓存中
- 以后如果需要获取相同的数据，直接从缓存中拿，不用再去查询数据库

测试：

1. 开启日志

2. 测试在一个Session中查询两次相同记录

3. 查看日志输出

   ![image-20201227143316552](http://fl.ljuuu.com/image-20201227143316552.png)

失效的情况：

- 查询不一样的内容
- 使用过增删改方式，重新刷新缓存，因为增删改有可能改变原来的数据，所以需要刷新！
- 查询不同的Mapper
- 手动清除缓存，sqlSession.clearCache();

小结：一级缓存默认是开启的，只在一次SqlSession中有效！也就是连接到关闭连接这个区间。

### 13.4、二级缓存

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

### 13.5、缓存原理

![image-20201227145919497](http://fl.ljuuu.com/image-20201227145919497.png)

缓存顺序

1. 第一次查询走数据库，先看二级缓存中有没有

2. 若二级缓存没有，再看一级缓存中有没有，

3. 若都没有，查询数据库，并且将数据保存在一级保存

### 13.6、自定义缓存：ehcache

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
