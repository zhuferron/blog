---
title: SpringBoot学习笔记(三)
tags: SpringBoot
categories: 学习笔记
description: SpringBoot第三部分学习笔记，主要介绍了Spring Data(如何整合Mybatis)、以及两个安全框架SpringSecurity和Shiro的使用
---

## 8、Spring Data

### 8.1、原生态实现数据库

1. 编写数据库配置文件

   ```yml
   spring:
   	datasource:
   		url: jdbc:mysql://localhost:3306/mybatis?useSSL=false&useUnicode=truecharacterEncoding=UTF-8&serverTimezone=UTC
   		password: ******
   		username: root
   		driver-class-name: com.mysql.cj.jdbc.Driver
   ```

2. idea连接数据库，并且在test类中测试连接

   ```java
   @SpringBootTest
   class Springboot04DataApplicationTests {
   
       @Autowired
       DataSource dataSource;
   
       @Test
       void contextLoads() throws SQLException {
           //数据源：class com.zaxxer.hikari.HikariDataSource
           System.out.println(dataSource.getClass());
           //获得数据库连接
           Connection connection = dataSource.getConnection();
           System.out.println(connection);
           // xxxx Template:springboot已经配置好的模板bean，拿来即用
           //关闭
           connection.close();
   
   
       }
   
   }
   ```

3. 测试增删改查

   ```java
   @RestController
   public class JDBCController {
   
       @Autowired
       JdbcTemplate jdbcTemplate;
   
       //查询数据库的所有信息
       //没有实体类，数据库的东西怎么获取？ Map
       @GetMapping("/userList")
       public List<Map<String,Object>> userList(){
           String sql = "select * from user";
           List<Map<String, Object>> maps = jdbcTemplate.queryForList(sql);
           return maps;
       }
   
       @GetMapping("/addUser")
       public String addUser(){
           String sql = "insert into mybatis.user(id,name,pwd) values (4,'ferron4','123456')";
           jdbcTemplate.update(sql);
           return "add-ok";
       }
   
       @GetMapping("/updateUser/{id}")
       public String updateUser (@PathVariable("id") int id){
           String sql = "update mybatis.user set name=?,pwd=? where id="+id;
   
           Object[] objects = new Object[2];
           objects[0] = "ferron9";
           objects[1] = "1235456";
           //预编译
           jdbcTemplate.update(sql,objects);
           return "update-ok";
       }
   
       @GetMapping("/deleteUser/{id}")
       public String deleteUser(@PathVariable("id") int id){
           String sql = "delete from mybatis.user where id=?";
           jdbcTemplate.update(sql,id);
           return "delete-ok";
       }
   
   
   
   }
   ```


### 8.2、整合Druid数据源

1. 导入依赖

   ```xml
   <!-- https://mvnrepository.com/artifact/com.alibaba/druid -->
   <dependency>
       <groupId>com.alibaba</groupId>
       <artifactId>druid</artifactId>
       <version>1.2.4</version>
   </dependency>
   ```

2. 在springboot配置文件中加入配置

   ```yml
   spring:
     datasource:
     	type: com.alibaba.druid.pool.DruidDataSource
   ```

3. 自定义DruidConfig配置类，进行数据源配置

   ```java
   @Configuration
   public class DruidConfig {
   
       //与application.yml文件绑定
       @ConfigurationProperties(prefix = "spring.datasource")
       @Bean
       public DataSource druidDataSource(){
           return new DruidDataSource();
       }
   
       //后台监控：相当于web.xml
       //因为springboot内置了servlet容器，所有没有web.xml，替代方法：ServletRegistrationBean
       @Bean
       public ServletRegistrationBean statViewServlet(){
           ServletRegistrationBean<StatViewServlet> bean = new ServletRegistrationBean<>(new StatViewServlet(), "/druid/*");
   
           //后台需要有人登陆，账号密码配置
           HashMap<String, String> initParameters = new HashMap<>();
           //增加配置
           initParameters.put("loginUsername","admin");  //登陆的key是固定的
           initParameters.put("loginPassword","sanfashi");
           //允许谁可以访问
           initParameters.put("allow","");
           //禁止谁能访问
           //initParameters.put("ferron","192.168.11.123");
           // 设置初始化参数
           bean.setInitParameters(initParameters);
   
           return bean;
   
       }
   
       //filter
       @Bean
       public FilterRegistrationBean webStatFilter(){
   
           FilterRegistrationBean<Filter> bean = new FilterRegistrationBean<>();
   
           bean.setFilter(new WebStatFilter());
   
           //可以过滤哪些请求
           HashMap<String, String> initParameters = new HashMap<>();
           //这些东西不进行统计
           initParameters.put("exclusions","*.js,*.css,/durid/*");
   
           return bean;
   
       }
   
   }
   ```

4. 在网站中请求/durid，可以登录进行sql监控

### 8.3、整合Mybatis

1. 导入依赖：mybatis-spring-boot-starter

   ```xml
   <!-- https://mvnrepository.com/artifact/org.mybatis.spring.boot/mybatis-spring-boot-starter -->
   <dependency>
       <groupId>org.mybatis.spring.boot</groupId>
       <artifactId>mybatis-spring-boot-starter</artifactId>
       <version>2.1.4</version>
   </dependency>
   ```

2. 数据库配置

   ```yml
   spring:
     datasource:
       username: root
       password: sanfashi
       url: jdbc:mysql://localhost:3306/mybatis?useSSL=false&useUnicode=truecharacterEncoding=UTF-8&serverTimezone=UTC
       driver-class-name: com.mysql.cj.jdbc.Driver
   ```

3. 建立实体类

   ```java
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   public class User {
   
       private int id;
       private String name;
       private String pwd;
   
   }
   ```

4. 写一个对应的mapper接口

   注意：要使用`@Mapper`和`@Repository`注解

   ```java
   //这个注解表示这个是mybatis的一个mapper类
   @Mapper
   @Repository
   public interface UserMapper {
   
       List<User> queryUserList();
   
       User queryById(int id);
   
       int addUser(User user);
   
       int updateUser(User user);
   
       int deleteUser(int id);
       
   }
   ```

5. 在`application.yml`文件中配置mybatis

   ```yml
   #整合mybatis
   mybatis:
     #别名
     type-aliases-package: com.ferron.pojo
     #绑定包下的mapper
     mapper-locations: classpath:mybatis/mapper/*.xml
     
   ```

6. 在resources目录下新建一个mybatis.mapper文件夹，在该文件夹下写对应的mapper.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <!--namespace绑定一个对应的Dao/Mapper接口-->
   <!--相当于实现了UserDao接口-->
   <mapper namespace="com.ferron.mapper.UserMapper">
       <select id="queryUserList" resultType="User">
           select * from user;
       </select>
       <select id="queryById" resultType="User">
           select * from user where id = #{id};
       </select>
       <insert id="addUser" parameterType="User">
           insert into user (id,name,pwd) values (#{id},#{name},#{pwd});
       </insert>
       <update id="updateUser" parameterType="User">
           update user set name=#{name},pwd=#{pwd} where id = #{id};
       </update>
       <delete id="deleteUser" parameterType="int">
           delete from user where id=#{id}
       </delete>
   
   </mapper>
   ```

7. 写好对应的service代码

8. 写controller，实现，测试

   ```java
   @RestController
   public class UserController {
   
       @Autowired
       private UserMapper userMapper;
   
       @GetMapping("/queryUserList")
       public List<User> queryUserList(){
           List<User> users = userMapper.queryUserList();
           return users;
       }
   
       @GetMapping("/addUser")
       public String addUser(){
           userMapper.addUser(new User(7,"ferron7","123456"));
           return "ok";
       }
   
       @GetMapping("/updateUser")
       public String updateUser(){
           userMapper.updateUser(new User(7,"ferron7","654321"));
           return "ok";
       }
   
       @GetMapping("/deleteUser")
       public String deleteUser(){
           userMapper.deleteUser(7);
           return "ok";
       }
   
   }
   ```

## 9、SpringSecurity

### 9.1、概述

在web开发中，安全第一位！过滤器，拦截器~

功能性需要：否

做网站：安全应该在什么时候考虑？ 设计之初

- 漏洞：隐私泄露
- 架构一旦确定
- shiro，springsecurity：认证，授权(vip1,vip2,vip3)

**思想：AOP**

几个重要的类：

- `WebSecurityConfigureAdapter`：自定义Security策略
- `AuthenticationManagerBuilder`：自定义认证策略
- `@EnableWebSecurity`：开启WebSecurity模式，开启某个功能：`@Enablexxx`

### 9.2、环境搭建

1. 编写对应的controller，测试网页能否正常访问

   ```java
   @Controller
   public class RouterController {
   
       @RequestMapping({"/","/index"})
       public String index(){
           return "index";
       }
   
       @RequestMapping("/tologin")
       public String toLogin(){
           return "views/login";
       }
   
       @RequestMapping("/level1/{id}")
       public String level1(@PathVariable("id") int id){
           return "views/level1/"+id;
       }
   
   
       @RequestMapping("/level2/{id}")
       public String level2(@PathVariable("id") int id){
           return "views/level2/"+id;
       }
   
   
       @RequestMapping("/level3/{id}")
       public String level3(@PathVariable("id") int id){
           return "views/level3/"+id;
       }
   }
   ```

2. 导入spring-security

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-security</artifactId>
   </dependency>
   ```

### 9.3、用户认证和授权

1. 新建config目录，并且建立`SecurityConfig`类

2. ``SecurityConfig`类继承`WebSecurityConfigurerAdapter`，并且在该类上面添加`@EnableWebSecurity`注解

   ```java
   //AOP：拦截器
   @EnableWebSecurity
   public class SecurityConfig extends WebSecurityConfigurerAdapter {
   
       //链式编程
       //授权
       @Override
       protected void configure(HttpSecurity http) throws Exception {
           //首页所有人可以访问，但是功能页只有对应有权限的人才能访问
           //请求授权的规则
           http.authorizeRequests()
                   .antMatchers("/").permitAll()
                   .antMatchers("/level1/**").hasRole("vip1")
                   .antMatchers("/level2/**").hasRole("vip2")
                   .antMatchers("/level3/**").hasRole("vip3");
   
           //没有权限默认会到登录页面:自带登录页面：
           http.formLogin();
   
       }
   
       //认证：spring2.1.x可以直接使用
       //密码编码：There was an unexpected error
       //在spring security5.0+新增了很多加密方式
       @Override
       protected void configure(AuthenticationManagerBuilder auth) throws Exception {
           //这些数据正常应该在数据库中读
           auth.inMemoryAuthentication().passwordEncoder(new BCryptPasswordEncoder())
                   .withUser("ferron").password(new BCryptPasswordEncoder().encode("123456")).roles("vip2","vip3")
                   .and()
                   .withUser("root").password(new BCryptPasswordEncoder().encode("123456")).roles("vip1","vip2","vip3")
                   .and()
                   .withUser("guest").password(new BCryptPasswordEncoder().encode("123456")).roles("vip1");
       }
   }
   ```

### 9.4、用户注销及权限控制

注销：

1. 新增前端页面

2. 链式编程，添加对应的注销功能

   ```java
   //开启了注销，并且注销成功后跳到首页
   http.logout().logoutSuccessUrl("/index");
   ```

权限控制：登录的用户，只显示出对应的功能

注意：springboot版本要在2.0.9以下或者**导入依赖时使用`thymeleaf-extras-springsecurity5`**

1. 添加security-thymeleaf整合的依赖

   ```xml
   <!-- https://mvnrepository.com/artifact/org.thymeleaf.extras/thymeleaf-extras-springsecurity4 -->
   <dependency>
       <groupId>org.thymeleaf.extras</groupId>
       <artifactId>thymeleaf-extras-springsecurity4</artifactId>
       <version>3.0.4.RELEASE</version>
   </dependency>
   
   ```

2. 编写对应的前端页面，加入命名空间`xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity4"`

   导航栏：

   ```html
   <!--登录注销-->
   <div class="right menu">
       <!--如果未登录：显示登录按钮-->
       <div sec:authorize="!isAuthenticated()">
           <a class="item" th:href="@{/toLogin}">
               <i class="address card icon"></i> 登录
           </a>
       </div>
       <!--如果登录：显示用户名和注销按钮-->
       <div sec:authorize="isAuthenticated()">
           <!--注销-->
           <a class="item" th:href="@{/logout}">
               <i class="sign-out icon"></i> 注销
           </a>
       </div>
       <div sec:authorize="isAuthenticated()">
           <a class="item">
               用户名：<span sec:authentication="name"></span>
               角色：<span sec:authentication="authorities"></span>
           </a>
       </div>
       <!--已登录
   <a th:href="@{/usr/toUserCenter}">
   <i class="address card icon"></i> admin
   </a>
   -->
   </div>
   ```

   内容：

   ```html
   <!--根据用户的角色实现动态菜单-->
   <div class="column" sec:authorize="hasRole('vip1')">
       <div class="ui raised segment">
           <div class="ui">
               <div class="content">
                   <h5 class="content">Level 1</h5>
                   <hr>
                   <div><a th:href="@{/level1/1}"><i class="bullhorn icon"></i> Level-1-1</a></div>
                   <div><a th:href="@{/level1/2}"><i class="bullhorn icon"></i> Level-1-2</a></div>
                   <div><a th:href="@{/level1/3}"><i class="bullhorn icon"></i> Level-1-3</a></div>
               </div>
           </div>
       </div>
   </div>
   
   <div class="column" sec:authorize="hasRole('vip2')">
       <div class="ui raised segment">
           <div class="ui">
               <div class="content">
                   <h5 class="content">Level 2</h5>
                   <hr>
                   <div><a th:href="@{/level2/1}"><i class="bullhorn icon"></i> Level-2-1</a></div>
                   <div><a th:href="@{/level2/2}"><i class="bullhorn icon"></i> Level-2-2</a></div>
                   <div><a th:href="@{/level2/3}"><i class="bullhorn icon"></i> Level-2-3</a></div>
               </div>
           </div>
       </div>
   </div>
   
   <div class="column" sec:authorize="hasRole('vip3')">
       <div class="ui raised segment">
           <div class="ui">
               <div class="content">
                   <h5 class="content">Level 3</h5>
                   <hr>
                   <div><a th:href="@{/level3/1}"><i class="bullhorn icon"></i> Level-3-1</a></div>
                   <div><a th:href="@{/level3/2}"><i class="bullhorn icon"></i> Level-3-2</a></div>
                   <div><a th:href="@{/level3/3}"><i class="bullhorn icon"></i> Level-3-3</a></div>
               </div>
           </div>
       </div>
   </div>
   ```

3. 防止注销失败

   ```java
   //防止网站攻击：get post
   http.csrf().disable(); //关闭csrf功能 登出失败可能存在的原因
   ```

### 9.5、定制登录页面以及记住我

记住我：cookies实现，默认保存两周

```java
//开启记住我功能
http.rememberMe();
```

登录页定制：

1. 开启登录页定制功能

   ```java
   //没有权限默认会到登录页面:自带登录页面：
   http.formLogin().loginPage("/toLogin");
   ```


## 10、Shiro

### 10.1、概述

- shiro是一个java的安全(权限)框架
- 可以完成认证，授权，加密，会话管理，web集成，缓存等等

### 10.2、三大对象

- Subject 用户
- SecurityManager 管理所有用户
- Realm 连接数据

### 10.3、环境搭建

- 导入对应依赖

  ```xml
  <dependency>
      <groupId>org.apache.shiro</groupId>
      <artifactId>shiro-spring</artifactId>
      <version>1.7.0</version>
  </dependency>
  ```

- 建立config包，编写对应的配置类

- 创建UserRealm自定义类

  ```java
  //自定义的UserRealm
  public class UserRealm extends AuthorizingRealm {
      //授权
      @Override
      protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
          System.out.println("执行了授权doGetAuthorizationInfo");
          return null;
      }
  
      //认证
      @Override
      protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
          System.out.println("执行了认证doGetAuthorizationInfo");
          return null;
      }
  }
  ```

- 编写对应的ShiroConfig配置类

  ```java
  @Configuration
  public class ShiroConfig {
  
      //1.创建Realm对象
      @Bean
      public UserRealm userRealm(){
          return new UserRealm();
      }
  
      //2.获得DefaultWebSecurityManager
      @Bean(name = "securityManager")
      public DefaultWebSecurityManager getDefaultWebSecurityManager(@Qualifier("userRealm") UserRealm userRealm){
          DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
          //关联UserRealm
          securityManager.setRealm(userRealm);
          return securityManager;
      }
  
      //3.获得ShiroFilterFactoryBean
      @Bean
      public ShiroFilterFactoryBean getShiroFilterFactoryBean(@Qualifier("securityManager") DefaultWebSecurityManager defaultWebSecurityManager){
          ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();
          //设置安全管理器
          bean.setSecurityManager(defaultWebSecurityManager);
  
          return bean;
      }
  }
  ```

- 编写首页

  ```java
  <!DOCTYPE html>
  <html lang="en" xmlns:th="http://www.thymeleaf.org">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
  </head>
  <body>
  
  <h1>首页</h1>
  <p th:text="${msg}"> </p>
  
  <a th:href="@{/user/add}">add</a>
  <a th:href="@{/user/update}">update</a>
  </body>
  </html>
  ```

- 编写接口

  ```java
  @Controller
  public class MyController {
  
      @RequestMapping({"/","/index"})
      public String toIndex(Model model){
          model.addAttribute("msg","hello,shiro");
          return "index";
      }
  
      @RequestMapping("/user/add")
      public String add(){
          return "user/add";
      }
  
      @RequestMapping("/user/update")
      public String update(){
          return "user/update";
      }
  
  
  }
  ```

### 10.4、登录拦截

- 编写登录去登录页面

  ```html
  <h1>登录</h1>
  
  <form action="">
      <p>账户： <input type="text" name="username"></p>
      <p>密码： <input type="text" name="password"></p>
      <p><input type="submit"></p>
  </form>
  ```

- 去登录页面的请求

  ```java
  @RequestMapping("/toLogin")
  public String toLogin(){
      return "login";
  }
  ```

- 编写登录拦截

  ```java
  //添加shiro的内置过滤器
  /*
          * anon:无需认证就可以访问
          * authc:必须认证了才能访问
          * user:必须拥有 记住我 功能才可以访问
          * perms: 拥有对某个资源的权限才能访问
          * role: 拥有某个角色权限才可以访问
          * */
  
  //创建过滤器Map
  LinkedHashMap<String, String> filterMap = new LinkedHashMap<>();
  //添加对应的拦截
  filterMap.put("/user/add","authc");
  filterMap.put("/user/update","authc");
  
  bean.setFilterChainDefinitionMap(filterMap);
  
  //设置登录的请求
  bean.setLoginUrl("/toLogin");
  ```

### 10.5、用户认证

**Shiro的特点就是在`ShiroFilterFactoryBean`中进行请求过滤，在`Realm`中进行授权认证**

- 首先在提交按钮上绑定对应的请求，该请求给接口传递用户名和密码的参数，编写对应的接口

  ```java
  @RequestMapping("/login")
  public String login(String username, String password, Model model){
  
      //获取当前的用户
      Subject subject = SecurityUtils.getSubject();
      //封装用户的登录数据：封装好了之后全局存在
      UsernamePasswordToken token = new UsernamePasswordToken(username, password);
      //执行登录的方法，如果没有异常就说明ok
      try {
          subject.login(token);
          return "index";
      }catch (UnknownAccountException e){ //用户名不存在
          model.addAttribute("msg","用户名错误");
          return "login";
      }catch (IncorrectCredentialsException e){ //密码不存在
          model.addAttribute("msg","密码错误");
          return "login";
      }
  }
  ```

- 执行`subject.login(token)`之后，流程进入认证的`doGetAuthorizationInfo`方法

  ```java
  //认证
  @Override
  protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
      System.out.println("执行了认证doGetAuthorizationInfo");
  
      //用户名，密码=>数据库中取
      String username = "root";
      String password = "123456";
      //拿到token
      UsernamePasswordToken userToken = (UsernamePasswordToken) authenticationToken;
  
      if(!userToken.getUsername().equals(username)){
          return null; //自动抛出异常UnknownAccountException
      }
      //自动做密码认证，不需要我们来写！不对则返回IncorrectCredentialsException
      return new SimpleAuthenticationInfo("",password,"");
  }
  ```

### 10.6、整合mybatis

- 导入对应依赖

  ```xml
  <dependency>
      <groupId>org.apache.shiro</groupId>
      <artifactId>shiro-spring</artifactId>
      <version>1.7.0</version>
  </dependency>
  <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
  </dependency>
      <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.12</version>
  </dependency>
  <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.2.4</version>
  </dependency>
  <dependency>
      <groupId>org.mybatis.spring.boot</groupId>
      <artifactId>mybatis-spring-boot-starter</artifactId>
      <version>2.1.4</version>
  </dependency>
  ```

- 对应application.yml配置文件

  ```yml
  #数据源配置
  spring:
    datasource:
      url: jdbc:mysql://localhost:3306/mybatis?useSSL=false&useUnicode=truecharacterEncoding=UTF-8&serverTimezone=UTC
      password: sanfashi
      username: root
      driver-class-name: com.mysql.cj.jdbc.Driver
      type: com.alibaba.druid.pool.DruidDataSource
  #mybatis配置
  mybatis:
    mapper-locations: classpath:mapper/*.xml
    type-aliases-package: com.ferron.pojo
  ```

- 创建对应的包和实体类

- 在com.ferron目录下创建mapper包和UserMapper接口

  ```java
  @Repository
  @Mapper
  public interface UserMapper {
      
      public User queryUserByName(String name);
      
  }
  ```

- 在resources目录下创建mapper目录和编写UserMapper.xml文件

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper
          PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
          "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
  <!--namespace绑定一个对应的Dao/Mapper接口-->
  <!--相当于实现了UserDao接口-->
  <mapper namespace="com.ferron.mapper.UserMapper">
      <select id="queryUserByName" parameterType="String" resultType="User">
          select * from user where name=#{name};
      </select>
  
  </mapper>
  ```

- 编写业务层代码

  UserService：

  ```java
  public interface UserService {
      public User queryUserByName(String name);
  }
  ```

  UserServiceImpl：

  ```java
  @Service
  public class UserServiceImpl implements UserMapper{
  
      @Autowired
      UserMapper userMapper;
  
      @Override
      public User queryUserByName(String name) {
          return userMapper.queryUserByName(name);
      }
  }
  ```

- 更换认证的代码

  ```java
  //认证
  @Override
  protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
      System.out.println("执行了认证doGetAuthorizationInfo");
  
      //拿到token
      UsernamePasswordToken userToken = (UsernamePasswordToken) authenticationToken;
  
      //用户名，密码=>数据库中取
      User user = userService.queryUserByName(userToken.getUsername());
      //String username = "root";
      //String password = "123456";
      if(user == null){
          return null; //自动抛出异常UnknownAccountException
      }
      //自动做密码认证，不需要我们来写！不对则返回IncorrectCredentialsException
      return new SimpleAuthenticationInfo("",user.getPwd(),"");
  }
  ```

### 10.7、shiro授权实现

- 在数据库中对每个角色赋予不同的权限

- 添加不同的授权

  ```java
  filterMap.put("/user/add","perms[user:add]");
  filterMap.put("/user/update","perms[user:update]");
  ```

- 在认证时，返回用户

  ```java
  return new SimpleAuthenticationInfo(user,user.getPwd(),"");
  ```

- 给每个不同的用户授权

  ```java
  SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
  
  //拿到当前登录的这个对象
  Subject subject = SecurityUtils.getSubject();
  User principal = (User) subject.getPrincipal();   //拿到user对象
  
  //设置当前用户的权限
  info.addStringPermission(principal.getPerms());
  ```

### 10.8、shiro和thymeleaf整合

- 导入依赖

  ```java
  <!-- https://mvnrepository.com/artifact/com.github.theborakompanioni/thymeleaf-extras-shiro -->
  <dependency>
      <groupId>com.github.theborakompanioni</groupId>
      <artifactId>thymeleaf-extras-shiro</artifactId>
      <version>2.0.0</version>
  </dependency>
  ```

- 在配置类中返回`ShiroDialect`

  ```java
  //整合ShiroDialect 
  @Bean
  public ShiroDialect getShiroDialect(){
      return new ShiroDialect();
  }
  ```

- 导入命名空间

  ```java
  xmlns:shiro="http://www.thymelaef.org/thymeleaf-extra-shiro"
  ```

- 编写前端页面

  ```html
  <div shiro:notAuthenticated>
      <a th:href="@{/toLogin}">登录</a>
  </div>
  <div shiro:hasPermission="user:add">
      <a th:href="@{/user/add}">add</a>
  </div>
  <div shiro:hasPermission="user:update">
      <a th:href="@{/user/update}">update</a>
  </div>
  ```
