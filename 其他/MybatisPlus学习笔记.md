---
title: MybatisPlus学习笔记
tags: MybatisPlus
categories: 中间件
description: 根据狂神学习MybatisPlus的笔记，本文深入浅出地讲解了MybatisPlus的使用
---





## 1、MybatisPlus概述

### 1.1、简介

Mybatis本来就是简化JDBC操作的，[MyBatis-Plus](https://github.com/baomidou/mybatis-plus)[ ](https://github.com/baomidou/mybatis-plus)[ (opens new window)](https://github.com/baomidou/mybatis-plus)（简称 MP）是一个 [MyBatis](http://www.mybatis.org/mybatis-3/)[ ](http://www.mybatis.org/mybatis-3/)[ (opens new window)](http://www.mybatis.org/mybatis-3/) 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

官网：https://mp.baomidou.com/

![](http://fl.ljuuu.com/img/20210324090355.png)

### 1.2、特性

- **无侵入**：只做增强不做改变，引入它不会对现有工程产生影响，如丝般顺滑
- **损耗小**：启动即会自动注入基本 CURD，性能基本无损耗，直接面向对象操作
- **强大的 CRUD 操作**：内置通用 Mapper、通用 Service，仅仅通过少量配置即可实现单表大部分 CRUD 操作，更有强大的条件构造器，满足各类使用需求
- **支持 Lambda 形式调用**：通过 Lambda 表达式，方便的编写各类查询条件，无需再担心字段写错
- **支持主键自动生成**：支持多达 4 种主键策略（内含分布式唯一 ID 生成器 - Sequence），可自由配置，完美解决主键问题
- **支持 ActiveRecord 模式**：支持 ActiveRecord 形式调用，实体类只需继承 Model 类即可进行强大的 CRUD 操作
- **支持自定义全局通用操作**：支持全局通用方法注入（ Write once, use anywhere ）
- **内置代码生成器**：采用代码或者 Maven 插件可快速生成 Mapper 、 Model 、 Service 、 Controller 层代码，支持模板引擎，更有超多自定义配置等您来使用（**自动帮你生成代码**）
- **内置分页插件**：基于 MyBatis 物理分页，开发者无需关心具体操作，配置好插件之后，写分页等同于普通 List 查询
- **分页插件支持多种数据库**：支持 MySQL、MariaDB、Oracle、DB2、H2、HSQL、SQLite、Postgre、SQLServer 等多种数据库
- **内置性能分析插件**：可输出 Sql 语句以及其执行时间，建议开发测试时启用该功能，能快速揪出慢查询
- **内置全局拦截插件**：提供全表 delete 、 update 操作智能分析阻断，也可自定义拦截规则，预防误操作

## 2、快速入门

- 创建数据库`db01`，创建表`user`：

  ```sql
  DROP TABLE IF EXISTS user;
  
  CREATE TABLE user
  (
  	id BIGINT(20) NOT NULL COMMENT '主键ID',
  	name VARCHAR(30) NULL DEFAULT NULL COMMENT '姓名',
  	age INT(11) NULL DEFAULT NULL COMMENT '年龄',
  	email VARCHAR(50) NULL DEFAULT NULL COMMENT '邮箱',
  	PRIMARY KEY (id)
  );
  --真实开发中，version(乐观锁)、delete(逻辑删除)、gmt_create、gmt_modified
  
  INSERT INTO user (id, name, age, email) VALUES
  (1, 'Jone', 18, 'test1@baomidou.com'),
  (2, 'Jack', 20, 'test2@baomidou.com'),
  (3, 'Tom', 28, 'test3@baomidou.com'),
  (4, 'Sandy', 21, 'test4@baomidou.com'),
  (5, 'Billie', 24, 'test5@baomidou.com');
  ```

- 编写springboot项目，导入依赖

  ```xml
  <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.47</version>
  </dependency>
  <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <version>1.18.12</version>
  </dependency>
  <!-- mybatisplus依赖 -->
  <dependency>
      <groupId>com.baomidou</groupId>
      <artifactId>mybatis-plus-boot-starter</artifactId>
      <version>3.0.5</version>
  </dependency>
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  ```

  说明：我们使用mybatisplus可以节省我们大量的代码，尽量不要同时导入mybatis和mybatisplus

- 连接数据库

  ```yml
  server:
    port: 8080
  
  spring:
    datasource:
      driver-class-name: com.mysql.jdbc.Driver
      url: jdbc:mysql://localhost:3306/db01?useSSL=false&useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC
      username: root
      password: sanfashi
  ```
  
- 传统：pojo-dao(连接mybatis、配置mapper.xml)-service-controller

- 使用mybatisplus

  - dao

    ```java
    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    public class User {
        private Long id;
        private String name;
        private Integer age;
        private String email;
    }
    ```

  - mapper：继承`BaseMapper`类即可，基本的CRUD方法都有了，你如果需要拓展的方法跟往常一样编写即可

    ```java
    //在对应的Mapper上面集成基本的类BaseMapper
    @Repository
    @Mapper  //这里加@Mapper注解，否则就是在主启动类上添加@MapperScan注解
    public interface UserMapper extends BaseMapper<User> {
        //所有的CRUD已经完成
    
    }
    ```

  - 测试

    ```java
    @Test
    void contextLoads() {
    
        //查询全部用户
        //参数是一个Wrapper，条件构造器
        List<User> users = userMapper.selectList(null);
        users.forEach(System.out::println);
    
    }
    ```

## 3、CRUD

### 3.1、插入

- 测试插入

  ```java
  //插入测试
  @Test
  void test1(){
      User user = new User();
      user.setName("ferron");
      user.setAge(20);
      user.setEmail("2963624004@qq.com");
      System.out.println(user);
  
      int insert = userMapper.insert(user);  //会帮我们自动生成id
      System.out.println(insert);
  
  
  }
  ```

- 发现我们没有填写id它竟然会自动帮我们生成id！--->**主键生成算法**

- 默认策略：全局唯一ID

  ![](http://fl.ljuuu.com/img/20210324090136.png)

- 雪花算法

  - 分布式唯一id生成算法汇总：https://blog.csdn.net/cyl101816/article/details/107002852
  - snowflake是Twitter开源的分布式ID生成算法，结果是一个long型的ID。其核心思想是：使用41bit作为毫秒数，10bit作为机器的ID（5个bit是数据中心，5个bit的机器ID），12bit作为毫秒内的流水号（意味着每个节点在每毫秒可以产生 4096 个 ID），最后还有一个符号位，永远是0。具体实现的代码可以参看https://github.com/twitter/snowflake。
  - snowflake算法可以根据自身项目的需要进行一定的修改。比如估算未来的数据中心个数，每个数据中心的机器数以及统一毫秒可以能的并发数来调整在算法中所需要的bit数。
  - 优点：
    - 不依赖于数据库，灵活方便，且性能优于数据库。
    - ID按照时间在单机上是递增的。
  - 缺点：
    - 在单机上是递增的，但是由于涉及到分布式环境，每台机器上的时钟不可能完全同步，也许有时候也会出现不是全局递增的情况。

- 主键自增：

  - 实体类字段上增加`@TableId(type = IdType.AUTO)`
  - 数据字段一定要是自增的

- 其他的主键生成策略源代码

  ```java
  public enum IdType {
      /**
       * 数据库ID自增
       */
      AUTO(0),
      /**
       * 该类型为未设置主键类型
       */
      NONE(1),
      /**
       * 用户输入ID
       * 该类型可以通过自己注册自动填充插件进行填充
       */
      INPUT(2),
  
      /* 以下3种类型、只有当插入对象ID 为空，才自动填充。 */
      /**
       * 全局唯一ID (idWorker)
       */
      ID_WORKER(3),
      /**
       * 全局唯一ID (UUID)
       */
      UUID(4),
      /**
       * 字符串全局唯一ID (idWorker 的字符串表示)
       */
      ID_WORKER_STR(5);
  
      private int key;
  
      IdType(int key) {
          this.key = key;
      }
  }
  ```

### 3.2、更新

- 测试更新

  ```java
  //更新测试
  @Test
  void test2(){
      User user = new User();
      user.setId(1L);
      user.setEmail("2963624004@qq.com");
      System.out.println(user);
  
      int i = userMapper.updateById(user);
      System.out.println(i);
  }
  ```

- 探索：发现了我们使用id来更新，想修改什么就创建好对象之后传什么就行了，mybatisplus会帮我们自动使用**动态SQL**

  ![](http://fl.ljuuu.com/img/20210324091342.png)

### 3.3、自动填充

创建时间、修改时间！这些操作一般都是自动化完成的，我们不希望手动更新

阿里巴巴开发手册：所有的数据库表都需要有`gmt_create`和`gmt_modified`这两个字段，而且需要自动化实现！

- 方式一：数据库修改(工作中不能使用)

  - 在表中新增字段`create_time`和`update_time`

    ![](http://fl.ljuuu.com/img/20210324091941.png)

  - 同步实体类：使用驼峰命名

    ```java
    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    public class User {
        private Long id;
        private String name;
        private int age;
        private String email;
        private Date createTime;
        private Date updateTime;
    
    }
    ```

  - 再次更新测试

- 方式二：代码修改

  - 恢复开始的修改，数据库只添加字段，不修改值等

    ![](http://fl.ljuuu.com/img/20210324092327.png)

  - 在实体类的字段上**添加注解**了！

    ```java
    @TableField(fill = FieldFill.INSERT)
    private Date createTime;
    @TableField(fill = FieldFill.INSERT_UPDATE)
    private Date updateTime;
    ```

  - 新建`handler`包，编写对应的处理器来处理注解

    ```java
    @Slf4j
    @Component  //一定不要忘记把处理器加到IOC容器中
    public class MyMetaObjectHandler implements MetaObjectHandler {
        //插入时候的填充策略
        @Override
        public void insertFill(MetaObject metaObject) {
            log.info("start insert fill......");
            //String fieldName, Object fieldVal, MetaObject metaObject
            this.setFieldValByName("createTime",new Date(),metaObject);
            this.setFieldValByName("updateTime",new Date(),metaObject);
        }
    
        //填充时候的填充策略
        @Override
        public void updateFill(MetaObject metaObject) {
            log.info("start update fill......");
            this.setFieldValByName("updateTime",new Date(),metaObject);
        }
    }
    ```

  - 测试插入

    ![](http://fl.ljuuu.com/img/20210324093255.png)

  - 测试更新

    ![](http://fl.ljuuu.com/img/20210324093358.png)

### 3.4、乐观锁

在面试过程中，我们经常会被遇到乐观锁和悲观锁。

**乐观锁**：它总是会认为不会出现问题，无论干什么都不会上锁，如果出现了问题，再次更新值测试(**version**)

**悲观锁**：它认为怎么都会出现问题，无论干嘛都会上锁，再去操作！

乐观锁实现方式：

- 取出记录时，获取当前version
- 更新时，带上这个version
- 执行更新时， set version = newVersion where version = oldVersion
- 如果version不对，就更新失败

**测试**：

- 将数据库中添加一个`version`字段

  ![](http://fl.ljuuu.com/img/20210324094133.png)

- 添加实体类字段

  ```java
  @Version
  private Integer version;
  ```

- 新建`config`包，编写`MybatisPlusConfig`类

  ```java
  @EnableTransactionManagement
  @Configuration
  public class MybatisPlusConfig {
  
      @Bean
      public OptimisticLockerInterceptor optimisticLockerInterceptor(){
          return new OptimisticLockerInterceptor();
      }
  
  }
  ```

- 测试修改成功

  ```java
  //测试乐观锁
  @Test
  void test3(){
      //查询用户信息
      User user = userMapper.selectById(1L);
      //修改用户信息
      user.setName("ferron测试");
      //执行更新
      userMapper.updateById(user);
  
  
  }
  ```

  ![](http://fl.ljuuu.com/img/20210324094924.png)

- 测试修改实现：乐观锁起作用

  ```java
  //测试乐观锁失败  多线程下
  @Test
  void test4(){
      //线程1
      User user1 = userMapper.selectById(1L);
      user1.setName("ferron测试1111");
  
      //线程2
      User user2 = userMapper.selectById(1L);
      user2.setName("ferron测试2222");
      userMapper.updateById(user2);
  
      userMapper.updateById(user1);  //如果没有乐观锁，user1就会覆盖user2的修改
  }
  ```

  ![](http://fl.ljuuu.com/img/20210324095240.png)

- user1修改失败，可以考虑使用**自旋锁**来尝试多次提交

### 3.5、查询

- 按单个id查询

  ```java
  //查询单个ID
  User user = userMapper.selectById(1L);
  System.out.println(user);
  ```

- 按多个id查询

  ```java
  //查询多个ID
  List<User> users = userMapper.selectBatchIds(Arrays.asList(1, 2, 3));
  users.forEach(System.out::println);
  ```

- 按条件查询

  ```java
  //条件查询
  //new一个查询Map
  HashMap<String, Object> map = new HashMap<>();
  //添加查询条件
  map.put("name","ferron");
  //执行查询
  List<User> users1 = userMapper.selectByMap(map);
  System.out.println(users1);
  ```

### 3.6、分页查询

原始的分页：原始的limit进行分页

MybatisPlus分页：

- 在`MybatisPlusConfig`中配置分页拦截器插件：

  ```java
  // 旧版
  @Bean
  public PaginationInterceptor paginationInterceptor() {
      PaginationInterceptor paginationInterceptor = new PaginationInterceptor();
      return paginationInterceptor;
  }
  ```

- 直接使用`Page`对象即可

  ```java
  //测试分页
  @Test
  void test6(){
      //参数一：当前页
      //参数二：页面大小
      Page<User> page = new Page<>(1,5);
      userMapper.selectPage(page,null);
  
      page.getRecords().forEach(System.out::println);
  
  }
  ```

### 3.7、删除

- 根据id删除记录

  ```java
  int i = userMapper.deleteById(1L);
  System.out.println(i);
  ```

- 根据id批量删除

  ```java
  int i1 = userMapper.deleteBatchIds(Arrays.asList(2, 3));
  System.out.println(i1);
  ```

- 根据条件删除

  ```java
  HashMap<String, Object> map = new HashMap<>();
  map.put("name","ferron");
  userMapper.deleteByMap(map);
  ```

### 3.8、逻辑删除

物理删除：从数据库中直接移除

逻辑删除：在数据库中没有被移除，而是通过一个变量来让他失效！delete=0 =>delete=1

逻辑删除就相当于一个回收站，网站管理员可以看到被删除的数据！

- 新增数据库字段

  ![](http://fl.ljuuu.com/img/20210324101615.png)

- 在实体类中添加字段

  ```java
  @TableLogic //逻辑删除
  private Integer deleted;
  ```

- 在`MybatisPlusConfig`中配置

  ```java
  //逻辑删除配置
  @Bean
  public ISqlInjector sqlInjector(){
      return new LogicSqlInjector();
  }
  ```

- 在yml文件中添加配置

  ```yml
  mybatis-plus:
    global-config:
      db-config:
        logic-delete-field: flag  # 全局逻辑删除的实体字段名(since 3.3.0,配置后可以忽略不配置步骤2)
        logic-delete-value: 1 # 逻辑已删除值(默认为 1)
        logic-not-delete-value: 0 # 逻辑未删除值(默认为 0)
  ```

- 测试**逻辑删除**

  ```java
  /测试逻辑删除
      @Test
      void test7(){
      int i = userMapper.deleteById(4L);
      System.out.println(i);
  
  }
  ```

- 注意：**逻辑删除本质是更新操作**

  ![](http://fl.ljuuu.com/img/20210324102115.png)

  ![](http://fl.ljuuu.com/img/20210324102151.png)

- 再去查询操作：发现查不到4号用户，deleted属性会被自动拼接上

  ![](http://fl.ljuuu.com/img/20210324102335.png)

### 3.9、配置日志

使用了mybatisplus之后，我们所有的SQL语句都不可见，我们需要输出日志

- 在yml文件中配置日志输出，将其输出在控制台

  ```yml
  mybatis-plus:
    configuration:
      log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  ```

- 测试

  ![](http://fl.ljuuu.com/img/20210324090326.png)

## 4、MybatisPlus进阶

### 4.1、性能分析插件

我们在平时的开发中，会遇到一些慢sql，MybatisPlus提供了性能分析插件，如果超过一个时间就停止运行

- 在`MybatisPlusConfig`中配置

  ```java
  //性能分析工具
  @Bean
  @Profile({"dev","test"})  //在开发环境和测试环境使用，提高效率
  public PerformanceInterceptor performanceInterceptor(){
  
      PerformanceInterceptor performanceInterceptor = new PerformanceInterceptor();
      performanceInterceptor.setMaxTime(100);  //毫秒级别  设置sql查询最大时间
      performanceInterceptor.setFormat(true);  //设置sql格式化
  
      return performanceInterceptor();
  }
  ```

- 在yml中设置为开发环境

  ```yml
  spring:
    profiles:
      active: dev
  ```

- 测试查询：只要超过了限制时间，就会报异常

### 4.2、条件查询器Wrapper

**十分重要：Wrapper**

我们如果要使用一些复制的sql就可以使用条件构造器

- 查询name不为空的用户，并且邮箱不为空的用户，年龄大于等于12

  ```java
  @Test
  void test1(){
  
      //查询name不为空的用户，并且邮箱不为空的用户，年龄大于等于12
      QueryWrapper<User> wrapper = new QueryWrapper<>();
      wrapper
          .isNotNull("name")
          .isNotNull("email")
          .ge("age",12);
      userMapper.selectList(wrapper).forEach(System.out::println);
  
  
  }
  ```

- 查询特定的一个用户，多个用户会模糊

  ```java
  @Test
  void test2(){
      //查询名字为Ferron超级无敌的用户
      QueryWrapper<User> wrapper = new QueryWrapper<>();
      wrapper.eq("name","Ferron超级无敌的用户");
      User user = userMapper.selectOne(wrapper);
      System.out.println(user);
  }
  ```

- 查询age在20-30的用户的用户个数

  ```java
  @Test
  void test3(){
  
      //查询age在20-30的用户的用户个数
      QueryWrapper<User> wrapper = new QueryWrapper<>();
      wrapper
          .between("age",20,30);
      Integer integer = userMapper.selectCount(wrapper);
      System.out.println(integer);
  }
  ```

- 模糊查询

  ```java
  @Test
  void test4(){
  
      //模糊查询
      QueryWrapper<User> wrapper = new QueryWrapper<>();
      //左和右 %e%
      wrapper.notLike("name","e")
          .likeRight("email","t");
  
      List<Map<String, Object>> maps = userMapper.selectMaps(wrapper);
      maps.forEach(System.out::println);
  
  
  }
  ```

  ![](http://fl.ljuuu.com/img/20210324104753.png)

- 内查询

  ```java
  @Test
  void test5(){
      //内查询
      QueryWrapper<User> wrapper = new QueryWrapper<>();
  
      //id在子查询中查询出来
      wrapper.inSql("id","select id from user where id>3");
  
      List<Object> objects = userMapper.selectObjs(wrapper);
      objects.forEach(System.out::println);
  
  }
  ```

  ![](http://fl.ljuuu.com/img/20210324105125.png)

- 通过id降序排序

  ```java
  @Test
  void test6(){
      //通过id降序排序
      QueryWrapper<User> wrapper = new QueryWrapper<>();
      wrapper.orderByDesc("id");
  
      List<User> users = userMapper.selectList(wrapper);
      users.forEach(System.out::println);
  
  
  }
  ```

  ![](http://fl.ljuuu.com/img/20210324105314.png)

### 4.3、代码自动生成器(牛逼)

**AutoGenerator 是 MyBatis-Plus 的代码生成器，通过 AutoGenerator 可以快速生成 Entity、Mapper、Mapper XML、Service、Controller 等各个模块的代码，极大的提升了开发效率。**

测试：

- 在test目录下新建一个代码生成类`FerronCode`

  ```java
  public class FerronCode {
      public static void main(String[] args) {
          //需要构建一个代码自动生成器 对象
          AutoGenerator mpg = new AutoGenerator();
          //配置策略
          //1.全局配置
          GlobalConfig gc = new GlobalConfig();
          String propertyPath = System.getProperty("user.dir");
          gc.setOutputDir(propertyPath+"/src/main/java");  //设置生成代码的路径
          gc.setAuthor("Ferron");
          gc.setOpen(false);  //打开资源管理器
          gc.setFileOverride(false);  //是否覆盖原来的代码
          gc.setServiceImplName("%sService");  //去掉Service的I前缀
          gc.setIdType(IdType.ID_WORKER);  //设置id策略
          gc.setDateType(DateType.ONLY_DATE);
          gc.setSwagger2(true);
  
          mpg.setGlobalConfig(gc);  //添加全局配置
  
          //2.设置数据源
          DataSourceConfig dsc = new DataSourceConfig();
          dsc.setUrl("jdbc:mysql://localhost:3306/db01?useSSL=false&useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC");
          dsc.setDriverName("com.mysql.jdbc.Driver");
          dsc.setUsername("root");
          dsc.setPassword("sanfashi");
          dsc.setDbType(DbType.MYSQL);  //数据库
          mpg.setDataSource(dsc);  //设置数据源
  
          //3.包的配置
          PackageConfig pc = new PackageConfig();
          pc.setModuleName("blog");
          pc.setParent("com.ferron");
          pc.setEntity("pojo");
          pc.setMapper("mapper");
          pc.setService("service");
          pc.setController("controller");
          mpg.setPackageInfo(pc);  //设置包的配置
  
          // 4.策略配置
          StrategyConfig strategy = new StrategyConfig();
          strategy.setInclude("user");  //设置要映射的表名
          strategy.setNaming(NamingStrategy.underline_to_camel);
          strategy.setColumnNaming(NamingStrategy.underline_to_camel);
          strategy.setSuperEntityClass("你自己的父类实体,没有就不用设置!");
          strategy.setEntityLombokModel(true);
          strategy.setRestControllerStyle(true);
          //逻辑删除的字段
          strategy.setLogicDeleteFieldName("deleted");
          //自动填充配置
          TableFill gmtCreate = new TableFill("gmt_create", FieldFill.INSERT);
          TableFill gmtModified = new TableFill("gmt_modified", FieldFill.INSERT_UPDATE);
          ArrayList<TableFill> tableFills = new ArrayList<>();
          tableFills.add(gmtCreate);
          tableFills.add(gmtModified);
          strategy.setTableFillList(tableFills);
          //乐观锁
          strategy.setVersionFieldName("version");
  
          mpg.setStrategy(strategy);  //设置策略
          
          //执行
          mpg.execute();
  
      }
  }
  ```

- 这代码可以生成`user`表的对应包，如果需要生成别的表，**就改一下`strategy.setInclude("user")`的表名就行**，参数也可以传List

