---
title: SpringBoot学习笔记(一)
tags: SpringBoot
categories: 学习笔记
description: SpringBoot第一部分学习笔记，主要介绍了SpringBoot的作用、SpringBoot自动装配原理、以及编写了第一个SpringBoot程序
---

## 1、导言

javaweb：独立开发MVC三层框架的网站：原始

ssm：框架：简化了我们的开发流程，配置也开始较为复杂

**war：tomcat运行**

spring再简化：SpringBoot-jar：内嵌tomcat；微服务架构。

服务越来越多：springcloud

![image-20210123092039559](http://fl.ljuuu.com/image-20210123092039559.png)

## 2、是什么

### 2.1、什么是Spring

- spring是为了解决企业级应用开发的复杂性而创建的，简化开发
- 基于POJO的轻量级和最小侵入性编程
- 通过IOC，DI和面向接口实现松耦合
- AOP和惯例进行声明式编程
- 通过切面和模板减少样式代码

### 2.2、什么是SpringBoot

- 约定大于配置！
- **开箱即用**，提供各种默认配置来简化项目配置
- 内嵌式容器简化Web项目
- 没有冗余代码生成和XML配置的要求

### 2.3、微服务

- 微服务是一种架构风格，它要求我们在开发一个应用的时候，这个应用必须构建一系列小服务的组合；可以通过http的方式进行互通。

  ![image](http://dubbo.apache.org/imgs/user/dubbo-architecture-roadmap.jpg)

## 3、第一个SpringBoot程序

1. 两种创建方式：

   1. 官方网站创建
   2. IDEA直接创建

2. 创建之后

   点击启动，就跑起来了。

   ```java
   //程序的主入口
   @SpringBootApplication
   public class HelloworldApplication {
   
       public static void main(String[] args) {
           SpringApplication.run(HelloworldApplication.class, args);
       }
   
   }
   ```

3. 项目结构

   1. 

      **不能写错！约定大于配置！**

      ![image-20210123101250351](http://fl.ljuuu.com/image-20210123101250351.png)

      

   

4. 第一个接口

   ```java
   //自动装配：
   @RestController
   public class HelloController {
   
       //接口：http://localhost:8080/hello
       @RequestMapping("/hello")
       public String hello() {
   
           //调用业务，接收前端的参数
   
           return "hello,world";
       }
   }
   ```

5. pom.xml

   - 有一个父项目

     ```xml
     <parent>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-parent</artifactId>
         <version>2.4.2</version>
         <relativePath/> <!-- lookup parent from repository -->
     </parent>
     ```

   - 引入web依赖：tomcat，dispatchServlet，xml......

     注：所有的springboot依赖都是以**spring-boot-starter**开头的

     ```xml
     <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-web</artifactId>
     </dependency>
     ```

   - 单元测试依赖

     ```xml
     <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-test</artifactId>
         <scope>test</scope>
     </dependency>
     ```

   - 打jar包插件

     ```xml
     <build>
         <plugins>
             <plugin>
                 <groupId>org.springframework.boot</groupId>
                 <artifactId>spring-boot-maven-plugin</artifactId>
             </plugin>
         </plugins>
     </build>
     ```

6. 打jar包：微服务核心！

   1. 使用命令package打jar包

      ![image-20210123102548338](http://fl.ljuuu.com/image-20210123102548338.png)

   2. 出现BUILD SUCCESS时，打开target目录，找到jar包

      ![image-20210123102732460](http://fl.ljuuu.com/image-20210123102732460.png)

   3. 运行jar包，在本地就可直接访问。

### 3.1、改端口号

在application.properties中加入：

```properties
server.port=8081
```

### 3.2、自定义banner

1. 找一个好的banner
2. 在resources新建一个banner.txt
3. 重启

## 4、自动装配原理

### 4.1、pom.xml

- spring-boot-dependencies：核心依赖在父工程中
- 我们在写或者引入一些springboot依赖的时候，不需要指定版本，就因为有这些版本仓库

### 4.2、启动器

- ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-....</artifactId>
  </dependency>
  ```

- 启动器：说白了就是SpringBoot的启动场景

- 比如spring-boot-starter-web，他就会帮我们自动导入web环境所有的依赖

- springboot会将所有的功能场景，变成一个个启动器

- 如果我们要使用什么功能，就只需要找到对应的启动器就可以了-----> `starter`

### 4.3、主程序

- ```java
  //程序的主入口
  @SpringBootApplication
  public class HelloworldApplication {
  
      public static void main(String[] args) {
          SpringApplication.run(HelloworldApplication.class, args);
      }
  
  }
  ```

- 注解

  - `@SpringBootApplication`：标注这个类是一个springboot的应用
  - `@SpringBootConfiguration`：SpringBoot的配置
  - `@EnableAutoConfiguration`：自动装配

- 结论：springboot所有的自动配置都是在启动的时候扫描并加载：`spring.factories`所有的自动配置类都在这里面，但是不一定生效，要判断条件是否成立，只要导入了对应的`starter`，就有对应的启动器，有了启动器，我们自动装配就会生效，然后自动装配成功！

- 自动装配过程：

  1. springboot在启动的时候，从类路径下/META-INF/`spring.factories`获取指定值
  2. 将这些自动配置的类导入容器，自动配置就会生效，帮我们自动配置
  3. 以前我们需要自动配置的东西，现在springboot帮我们做了
  4. 整合javaEE，解决方法和自动配置的东西都在`spring-boot-autoconfigure-2.4.2.jar`这个包下
  5. 它会把所有需要的组件，以类名的方式返回，这些组件就会被添加到容器
  6. 容器中也会存在非常多的xxxxAutoConfiguration的文件(@bean)，就是这些类给容器中导入了这个场景需要的所有组件，并自动配置。 @Configration，JavaConfig！
  7. 有了自动配置类，就免去了我们手动配置配置文件。

- run方法：

  - `SpringApplication`对象作用：

    - 推断应用的类型是普通项目还是Web项目
    - 查找并加载所有可用初始化器，设置到`initializers`属性中
    - 找出所有的应用程序监听器，设置到`listeners`属性中
    - 推断并设置main方法的定义类，找到运行的主类

  - 执行流程

    ![run执行流程](https://img-blog.csdnimg.cn/2020033020092699.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1lfaGFueGlvbmc=,size_16,color_FFFFFF,t_70#pic_center)

## 5、SpringBoot配置

### 5.1、配置文件

- springboot使用一个全局的配置文件，配置文件名称是固定的，application.properties/application.yaml
- 作用：修改springboot自动配置的默认值

### 5.2、YAML

- 可以注入到我们的配置类中

- **对空格的要求极为严格**

- 普通kv键值对

  ```yaml
  name: ferron
  ```

- 对象

  ```yaml
  student:
  	name: ferron
  	age: 3
  	
  student:{name: ferron,age: 3}
  ```

- 数组

  ```yaml
  pets:
  	- cat
  	- dog
  	- pig
  	
  pets: [cat,dog,pig]
  ```


### 5.3、回忆：给一个实体类赋值

- 写一个实体类，需要在实体类上面加一个`@Component`注解，来注入

  ```java
  @Component
  public class Dog {
      @Value("旺财")
      private String name;
      @Value("8")
      private Integer age;
  }
  ```

- 在具体的属性上使用`@Value`注解来注入具体的值

- 在测试方法上使用`@Autowired`注解来自动装配

  ```java
  @SpringBootTest
  class Spring02ConfigApplicationTests {
  
      @Autowired
      private Dog dog;
  
      @Test
      void contextLoads() {
          System.out.println(dog);
      }
  
  }
  ```

### 5.4、使用YAML赋值

- 编写YAML文件

  ```yaml
  person:
    name: ferron
    age: 3
    happy: false
    birth: 2019/11/02
    maps: {k1: v1,k2: v2}
    list:
      - code
      - music
      - girl
    dog:
      name: 旺财
      age: 3
  ```

- 在想要注入的类前面加上`ConfigurationProperties`注解

- @Component
  @ConfigurationProperties(prefix = "person")
  public class Person {

      private String name;
      private Integer age;
      private Boolean happy;
      private Date birth;
      private Map<String,Object> maps;
      private List<Object> lists;
      private Dog dog;

  }

### 5.5、使用Properties文件赋值

- 使用`@PropertySource(value="classpath:...")`注解
- 在想要测试的属性上增加`@Value("#value")`注解注入

### 5.6、YAML文件好处

- 松散绑定：实体类中使用驼峰命名，而yaml文件中使用-分割，同样可以实现注入

- JSR303数据校验：

  - 添加对应依赖

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
    ```

  - 在对应的类上使用`@Validated`注解

  - 在对应的属性上增加对应的注解

    - 例如：邮箱：`@Email(message="邮箱格式错误")`

    - ```xml
      @NotNull(message="名字不能为空")
      @Max(value=120,message="年龄不能大于120")
      @Email(message="邮箱格式错误")
      
      空检查
      @Null  验证对象是否为null
      @NotNull 验证对象是否不为null，无法查验长度为0的字符串
      @NotBlank 检查约束字符串是不是Null还有被Trim的长度是否大于0，只对字符串，且会去掉前后空格
      @NotEmpty 检查约束元素是否为Null或是Empty
      
      Boolean检查
      @AssertTrue 验证Boolean对象是否为true
      @AssertFalse 验证Boolean对象是否为false
      
      长度检查
      @Size(min=, max=) 验证对象(Array, Collection, Map, String)长度是否在给定的范围之内
      @Length(min=, max=) 验证字符串是否在给定的范围之内
      
      日期检查
      @Past:  验证Date和Calendar是否在当前时间之前
      @Future:  验证Date和Calendar是否在当前时间之后
      @Pattern:  验证String对象是否符合对应正则表达式
      
      ```

  - 对应注解存放位置

    ![image-20210124100159038](http://fl.ljuuu.com/image-20210124100159038.png)

### 5.7、多环境配置和配置文件位置

1. 配置文件存放位置以及优先级

   1. file:./config/
   2. file:./
   3. classpath:/config/
   4. classpath:/

2. 多环境配置

   加application-后面的元素即可。

   ```yaml
   server:
     port: 8081
   #springboot的多环境配置：可以选择激活哪一个配置文件
   spring:
     profiles:
       active: dev
   ---
   server:
     port: 8082
   spring:
     profiles: test
   
   ---
   server:
     port: 8083
   spring:
     profiles: dev
   ```

### 5.8、自动配置再理解（重要）

- 在配置文件中能配置的东西，都有一个固有的规律。xxxAutoConfiguration：默认值  xxxProperties和配置文件绑定，我们就可以使用自定义的配置了。
- 过程：
  1. springboot刚开始帮我们自动装配，装配完之后默认值会存在xxxProperties.java这个类中
  2. 每个xxxProperties.java类都绑定了配置application.yaml这个配置文件
  3. 我们需要改变默认值，就在application.yaml中自定义值就可以了。
- 在配置文件中写入`debug: true`，可查看哪些配置已生效，哪些配置未生效