## 微服务概述

### 微服务和微服务架构简介

**微服务**：

微服务强调的是服务的大小，它关注的是某一个点，是具体解决一个问题/提供落地对应服务的一个服务应用，狭义地说，就是相当于我们springboot项目中一个个小的module

**微服务架构：**

简而言之，微服务架构风格，就像是把一个单独的应用程序开发为一套小服务，每个小服务运行在自己的进程中，并使用轻量级机制通信，通常是 HTTP  API。这些服务围绕业务能力来构建，并通过完全自动化部署机制来独立部署。这些服务使用不同的编程语言书写，以及不同数据存储技术，并保持最低限度的集中式管理。

![2015-05-10_212121](D:%5CApplication%5CTypora%5Cimage%5C102138253292559.png)

### 微服务优缺点

**优点：**

- 单一职责原则
- 每个服务足够内聚，代码易于理解，能聚焦一个指定的业务功能或业务需要
- 开发简单，开发效率高
- 松耦合，是有功能意义的服务，开发和部署阶段都是独立的
- 能使用不同的语言开发
- 易于第三方集成，微服务允许容易且灵活的方式集成自动部署，通过持续集成工具如jenkins，Hudson，bamboo
- 微服务知识业务逻辑的代码，不会和Html，css或其他界面混合
- 每个微服务都有自己的存储能力，可以有自己的数据库，也可以有统一数据库

**缺点：**

- 开发人员要处理分布式系统的复杂性
- 多服务运维难度，运维压力大
- 系统部署依赖
- 服务间通信成本
- 数据一致性
- 系统集成测试
- 性能监控

### 技术栈

| 条目             | 落地技术                                     |
| ---------------- | -------------------------------------------- |
| 服务开发         | springboot，spring，springMVC                |
| 服务配置与管理   | Archaius、Diamond                            |
| 服务注册与发现   | Eureka、Consul、Zookeeper                    |
| 服务调用         | Rest、RPC、gRPC                              |
| 服务熔断         | Hystrix、Envoy                               |
| 负载均衡         | Ribbon、NGINX                                |
| 服务接口调用     | Feign                                        |
| 消息队列         | Kafka、RabbitMQ，ActiveMQ                    |
| 服务配置中心管理 | SpringcloudConfig、ActiveMQ                  |
| 服务路由         | Zuul、Nacos                                  |
| 服务监控         | Zabbix、Nagios、Metrics、Specatator          |
| 全链路追踪       | Zipkin、Brave、Dapper                        |
| 服务部署         | Docker、OpenStack、k8s                       |
| 数据流操作开发包 | SpringCloud Stream(封装redis，Rabbit，kafka) |
| 事件消息中线     | Springcloud Bus                              |

### 微服务存在问题

1. 微服务架构中有很多服务组件，不同的用户怎么调用不同的服务
2. 微服务架构中不同的服务之间怎么进行互相调用
3. 微服务架构中怎么对这些不同的服务进行治理
4. 微服务架构中某个服务出问题了，怎么解决

### 现有技术

- Spring Cloud NetFlix 一站式解决方案
  - api网关，zuul组件
  - Feign  ---HttpClient---Http通信方式，同步，阻塞
  - 服务注册与发现：Eureka
  - 熔断机制：Hystrix
- Apache Bubbo Zookeeper 半自动，需要整合
  - api：第三方组件
  - bubbo：专业的rpc通信框架
  - zookeeper
  - 熔断：第三方组件，借助Hystrix
- Spring Cloud Alibaba 最新的一站式解决方案！更简单

## SpringCloud概述

### SpringCloud是什么

SpringCloud并不是一门技术，而是一门生态。

![](D:%5CApplication%5CTypora%5Cimage%5Csrc=http---img2020.cnblogs.com-blog-851491-202004-851491-20200419205320561-767718392.png&refer=http---img2020.cnblogs.jpg)

### SpringCloud和SpringBoot的关系

- SpringBoot专注于快速开发单个个体微服务 -jar
- SpringCloud是关注全局的微服务协调整理治理框架，它将SpringBoot开发的一个个单体微服务整合并管理起来，为各个微服务之间提供：配置管理，服务发现，短路器，路由，微代理，事件总线，全局锁，决策竞选，分布式会话等等集成服务
- SpringBoot可以离开pringCloud独立使用，但是SpringCloud离不开SpringBoot，属于依赖关系
- **SpringBoot专注于快速、方便的开发单个个体微服务，SpringCloud关注全局的服务治理框架**

### 网站架构

![](D:%5CApplication%5CTypora%5Cimage%5C20210322143632.png)

**Dubbo和SpringCloud最大的区别：SpringCloud抛弃了Dubbo的RPC通信，采用的是基于HTTP的REST方式**

**解决的问题域不一样：Dubbo的定位是一款RPC框架，SpringCloud的目标是微服务架构的一站式解决方案**

几个参考网站：

- https://www.springcloud.cc/spring-cloud-netflix.html
- 中文API文档：https://www.springcloud.cc/spring-cloud-dalston.html

## Rest学习环境搭建

### 服务提供者

- 建立一个新的Maven空项目作为父工程，删除src目录

- 编写父工程`pom.xml`文件

  ```xml
  <!--打包方式-->
  <packaging>pom</packaging>
  
  <properties>
      <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
      <maven.compiler.source>1.8</maven.compiler.source>
      <maven.compiler.target>1.8</maven.compiler.target>
      <junit.version>4.12</junit.version>
      <lombok.version>1.18.12</lombok.version>
  </properties>
  
  <dependencyManagement>
      <dependencies>
          <!--springcloud依赖-->
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-dependencies</artifactId>
              <version>Greenwich.SR1</version>
              <type>pom</type>
              <scope>import</scope>
          </dependency>
          <!--springboot依赖-->
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-dependencies</artifactId>
              <version>2.1.4.RELEASE</version>
              <type>pom</type>
              <scope>import</scope>
          </dependency>
          <!--数据库-->
          <dependency>
              <groupId>mysql</groupId>
              <artifactId>mysql-connector-java</artifactId>
              <version>5.1.47</version>
          </dependency>
          <dependency>
              <groupId>com.alibaba</groupId>
              <artifactId>druid</artifactId>
              <version>1.1.10</version>
          </dependency>
          <dependency>
              <groupId>org.mybatis.spring.boot</groupId>
              <artifactId>mybatis-spring-boot-starter</artifactId>
              <version>1.3.2</version>
          </dependency>
          <!-- 日志和测试 -->
          <dependency>
              <groupId>junit</groupId>
              <artifactId>junit</artifactId>
              <version>${junit.version}</version>
          </dependency>
          <dependency>
              <groupId>org.projectlombok</groupId>
              <artifactId>lombok</artifactId>
              <version>${lombok.version}</version>
          </dependency>
          <dependency>
              <groupId>log4j</groupId>
              <artifactId>log4j</artifactId>
              <version>1.2.17</version>
          </dependency>
          <dependency>
              <groupId>ch.qos.logback</groupId>
              <artifactId>logback-core</artifactId>
              <version>1.2.3</version>
          </dependency>
      </dependencies>
  </dependencyManagement>
  
  <build>
      <resources>
          <resource>
              <directory>src/main/resources</directory>
              <includes>
                  <include>**/*.properties</include>
                  <include>**/*.xml</include>
                  <--!重要-->
                  <include>**/*.yml</include>  
              </includes>
              <filtering>false</filtering>
          </resource>
          <resource>
              <directory>src/main/java</directory>
              <includes>
                  <include>**/*.properties</include>
                  <include>**/*.xml</include>
                  <include>**/*.yml</include>
              </includes>
              <filtering>false</filtering>
          </resource>
      </resources>
  </build>
  ```

- 创建一个`springcloud-api`子工程

- 导入子工程需要的依赖

  ```xml
  <!--当前module所需要的依赖-->
  <dependencies>
      <dependency>
          <groupId>org.projectlombok</groupId>
          <artifactId>lombok</artifactId>
      </dependency>
  </dependencies>
  ```

- 创建一个`mysql`数据库，idea连接

- 创建对应的实体类

  ```java
  @Data
  @NoArgsConstructor
  @Accessors(chain = true) //链式写法
  /*
  链式写法：
  Dept dept = new Dept();
  dept.setDeptNo(11).setDname('ferron')
   */
  public class Dept implements Serializable {  //实体类  orm 类表关系映射
  
      private Long deptno;
      private String dname;
      private String db_source;
  
      public Dept(String dname){
          this.dname=dname;
      }
  
  
  }
  ```

- 创建一个服务的提供者`springcloud-provider-dept`的module

- 导入依赖

  ```xml
  <dependencies>
      <!--我们需要拿到实体类，所以要配置api module-->
      <dependency>
          <groupId>com.ferron</groupId>
          <artifactId>springcloud-api</artifactId>
          <version>1.0-SNAPSHOT</version>
      </dependency>
      <!--junit-->
      <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
      </dependency>
      <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
      </dependency>
      <dependency>
          <groupId>com.alibaba</groupId>
          <artifactId>druid</artifactId>
      </dependency>
      <dependency>
          <groupId>ch.qos.logback</groupId>
          <artifactId>logback-core</artifactId>
      </dependency>
      <dependency>
          <groupId>org.mybatis.spring.boot</groupId>
          <artifactId>mybatis-spring-boot-starter</artifactId>
      </dependency>
      <!--test-->
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-test</artifactId>
      </dependency>
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
      </dependency>
      <!--jetty-->
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-jetty</artifactId>
      </dependency>
      <!--热部署-->
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-devtools</artifactId>
      </dependency>
  
  </dependencies>
  ```

- 编写对应的`application.xml`

  ```yml
  server:
    port: 8001
  #mybatis配置
  mybatis:
    type-aliases-package: com.ferron.springcloud.pojo
    config-location: classpath:mybatis/mybatis-config.xml
    mapper-locations: classpath:mybatis/mapper/*.xml
  
  #spring配置
  spring:
    application:
      name: springcloud-provider-dept
    datasource:
      type: com.alibaba.druid.pool.DruidDataSource
      driver-class-name: org.gjt.mm.mysql.Driver
      url: jdbc:mysql://localhost:3306/db01?useSSL=false&useUnicode=true&characterEncoding=UTF-8&serverTimezone=UTC
      username: root
      password: sanfashi
  
  ```

- 编写`mybatis-config.xml`

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE configuration
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-config.dtd">
  <!--configuration核心配置文件-->
  <configuration>
      <settings>
          <setting name="cacheEnabled" value="true"/>
      </settings>
  </configuration>
  ```

- 编写dao层

  DeptDao

  ```java
  @Mapper
  @Repository
  public interface DeptDao {
  
      public boolean addDept(Dept dept);
  
      public Dept queryById(Long id);
  
      public List<Dept> queryAll();
  
  }
  ```

  DeptMapper.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  
  <mapper namespace="com.ferron.dao.DeptDao">
  
      <insert id="addDept" parameterType="Dept">
          insert into dept (dname,db_source) values (#{dname}, DATABASE());
      </insert>
  
      <select id="queryById" resultType="Dept" parameterType="Long">
          select * from dept where deptno = #{deptno}
      </select>
  
      <select id="queryAll" resultType="Dept">
          select * from dept
      </select>
  
  </mapper>
  ```

- 编写service层

  DeptService

  ```java
  public interface DeptSevice {
  
      public boolean addDept(Dept dept);
  
      public Dept queryById(Long id);
  
      public List<Dept> queryAll();
  
  }
  ```

  DeptServiceImpl

  ```java
  @Service
  public class DeptServiceImpl implements DeptSevice {
  
      @Autowired
      DepDao deptDao;
  
      @Override
      public boolean addDept(Dept dept) {
          return deptDao.addDept(dept);
      }
  
      @Override
      public Dept queryById(Long id) {
          return deptDao.queryById(id);
      }
  
      @Override
      public List<Dept> queryAll() {
          return deptDao.queryAll();
      }
  }
  ```

- 编写controller，**这里的controller只提供REST服务**！

  ```java
  /提供Restful服务！
  @RestController
  public class DeptController {
  
      @Autowired
      private DeptSevice deptSevice;
  
      @PostMapping("/dept/add")
      public boolean addDept(Dept dept){
          return deptSevice.addDept(dept);
      }
  
      @GetMapping("/dept/get/{id}")
      public Dept get(@PathVariable("id") Long id){
          return deptSevice.queryById(id);
      }
  
      @GetMapping("/dept/list")
      public List<Dept> queryAll(){
          return deptSevice.queryAll();
      }
  
  }
  ```


### 服务消费者

- 新建一个`springcloud-provider-dept`的module

- 导入相关依赖

  ```xml
  <dependency>
      <groupId>com.ferron</groupId>
      <artifactId>springcloud-api</artifactId>
      <version>1.0-SNAPSHOT</version>
  </dependency>
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  ```

- 编写对应的`application.yml`

  ```yml
  server:
    port: 80
  ```

- 用户是没有`service`层的，用户需要调用服务，则需要得到`RestTemplate`这个对象，这时候我们首先需要写一个配置类

  ```java
  @Configuration
  public class ConfigBean {
      
      @Bean
      public RestTemplate getRestTemplate(){
          return new RestTemplate();
      }
  
  }
  ```

- 写对应的消费者接口

  ```java
  @RestController
  public class DeptConsumerController {
  
      //消费者：不应该有service层
      //支持RestFul风格调用
      //RestTemplate 供我们直接调用，说白了就是提供多种便捷访问远程http服务的方法，
      //RestTemplate三个参数（url,实体：Map，Class<T> responseType)
      @Autowired
      RestTemplate restTemplate;
  
      private static final String REST_URL_PREFIX = "http://localhost:8001";
  
      //根据id获得对象
      @RequestMapping("/consumer/dept/get/{id}")
      public Dept get(@PathVariable("id") Long id){
          //提供服务者只能通过get方法获得，我们这里也写get
          return restTemplate.getForObject(REST_URL_PREFIX+"/dept/get/"+id,Dept.class);
      }
  
      @RequestMapping("/consumer/dept/add")
      public boolean add(Dept dept){
          return restTemplate.postForObject(REST_URL_PREFIX+"/dept/add",dept,Boolean.class);
      }
      
      @RequestMapping("/consumer/dept/list")
      public List<Dept> list(){
          return restTemplate.getForObject(REST_URL_PREFIX+"/dept/list",List.class);
      }
  
  }
  ```


## Eureka服务注册与发现

### 什么是Eureka

- 遵循AP原则
- Eureka是Netflix的一个字模块，也是核心模块之一。Eureka是一个机遇REST的服务，用于定位服务，以实现远端中间层服务发现和故障转移，服务注册与发现对于微服务来说是非常重要的，有了服务发现与注册，只需要使用服务的标识符，就可以访问到服务，而不需要修改服务调用的配置文件了，功能类似于Bubbo的注册中心，比如Zookeeper

### 原理讲解

- Eureka的基本架构
  - SpringCloud封装了NetFilx的Eureka模块来实现服务注册与发现
  - Eureka采用了C-S的架构设计，EurekaServer作为服务注册功能的服务器，就是注册中心
  - 而系统中其他的微服务。使用Eureka的客户端连接到EurekaServer并维持心跳连接。这样系统的维护人员可以通过EureServer来监控系统中各个微服务是否正常运行，SpringCloud的一些其他模块(比如Zuul)就可以通过EurekaServer来发现系统中的其他微服务，并执行相关逻辑。
  - ![](D:%5CApplication%5CTypora%5Cimage%5C20210323142043.png)

### 三大角色

- EurekaServer：提供服务的注册与发现。类似zookeeper
- Service Provider：将自身服务注册到Eureka中，使消费方能够找到
- Service Consumer：服务消费方从Eureka中获取注册服务列表，从而找到消费服务

### 编写注册中心

- 新建一个Maven项目，导入**服务端**依赖

  ```xml
  <!-- eureka服务端依赖 -->
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-eureka-server</artifactId>
      <version>1.4.6.RELEASE</version>
  </dependency>
  ```

- 编写`application.yml`

  ```yml
  server:
    port: 7001
  
  eureka:
    instance:
      hostname: localhost  #Eureka服务端的实例名称
    client:
      register-with-eureka: false  #表示是否向eureka注册中心注册自己
      fetch-registry: false  #fetch-registry如果为false，则表示自己为注册中心
      service-url: 
        defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka  #监控页面
  ```

- 使用`@EnableEurekaServer`注解，开启注册中心

  ```java
  @SpringBootApplication
  @EnableEurekaServer
  public class EurekaService {
      public static void main(String[] args) {
          SpringApplication.run(EurekaService.class,args);
      }
  }
  
  ```

- 打开注册中心页面

  ![](D:%5CApplication%5CTypora%5Cimage%5C20210323144332.png)

### 注册服务提供者(provider-dept)

- 返回`springcloud-provider-dept`项目，导入对应的eureka依赖

  ```xml
  <!-- Eureka启动器依赖 -->
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-eureka</artifactId>
      <version>1.4.6.RELEASE</version>
  </dependency>
  ```

- 编写相关的Eureka配置

  ```yml
  #Eureka的配置
  eureka:
    client:
      service-url: 
        defaultZone: http://localhost:7001/eureka/
  ```

- 在主启动类中增加`@EnableEurekaClient`注解：**这个注解会在服务启动后自动注册到Eureka中**





