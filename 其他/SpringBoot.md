# 微服务阶段

javaweb：独立开发MVC三层框架的网站：原始

ssm：框架：简化了我们的开发流程，配置也开始较为复杂

**war：tomcat运行**

spring再简化：SpringBoot-jar：内嵌tomcat；微服务架构。

服务越来越多：springcloud

![image-20210123092039559](D:\Application\Typora\Typora\image\image-20210123092039559.png)

## 1、是什么

#### 1.1、什么是Spring

- spring是为了解决企业级应用开发的复杂性而创建的，简化开发
- 基于POJO的轻量级和最小侵入性编程
- 通过IOC，DI和面向接口实现松耦合
- AOP和惯例进行声明式编程
- 通过切面和模板减少样式代码

#### 1.2、什么是SpringBoot

- 约定大于配置！
- **开箱即用**，提供各种默认配置来简化项目配置
- 内嵌式容器简化Web项目
- 没有冗余代码生成和XML配置的要求

#### 1.3、微服务

- 微服务是一种架构风格，它要求我们在开发一个应用的时候，这个应用必须构建一系列小服务的组合；可以通过http的方式进行互通。

  ![image](http://dubbo.apache.org/imgs/user/dubbo-architecture-roadmap.jpg)

## 2、第一个SpringBoot程序

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

      ![image-20210123101250351](D:\Application\Typora\Typora\image\image-20210123101250351.png)

      

   

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

      ![image-20210123102548338](D:\Application\Typora\Typora\image\image-20210123102548338.png)

   2. 出现BUILD SUCCESS时，打开target目录，找到jar包

      ![image-20210123102732460](D:\Application\Typora\Typora\image\image-20210123102732460.png)

   3. 运行jar包，在本地就可直接访问。

#### 改端口号

在application.properties中加入：

```properties
server.port=8081
```

#### 自定义banner

1. 找一个好的banner
2. 在resources新建一个banner.txt
3. 重启

## 3、自动装配原理

#### 3.1、pom.xml

- spring-boot-dependencies：核心依赖在父工程中
- 我们在写或者引入一些springboot依赖的时候，不需要指定版本，就因为有这些版本仓库

#### 3.2、启动器

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

#### 3.3、主程序

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

## 4、SpringBoot配置

#### 4.1、配置文件

- springboot使用一个全局的配置文件，配置文件名称是固定的，application.properties/application.yaml
- 作用：修改springboot自动配置的默认值

#### 4.2、YAML

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


#### 回忆：给一个实体类赋值

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

#### 使用YAML赋值

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

#### 使用Properties文件赋值

- 使用`@PropertySource(value="classpath:...")`注解
- 在想要测试的属性上增加`@Value("#value")`注解注入

#### YAML文件好处

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

    ![image-20210124100159038](D:\Application\Typora\Typora\image\image-20210124100159038.png)

#### 多环境配置和配置文件位置

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

#### 自动配置再理解（重要）

- 在配置文件中能配置的东西，都有一个固有的规律。xxxAutoConfiguration：默认值  xxxProperties和配置文件绑定，我们就可以使用自定义的配置了。
- 过程：
  1. springboot刚开始帮我们自动装配，装配完之后默认值会存在xxxProperties.java这个类中
  2. 每个xxxProperties.java类都绑定了配置application.yaml这个配置文件
  3. 我们需要改变默认值，就在application.yaml中自定义值就可以了。
- 在配置文件中写入`debug: true`，可查看哪些配置已生效，哪些配置未生效

## 5、springboot web开发

思考：springboot到底帮我们配置了什么？我们能不能修改？能不能拓展？

- xxxxAutoConfiguartion  向容器中自动配置组件
- xxxxProperties  自动配置类，装配配置文件中自定义的一些内容

要解决的问题：

- 导入静态资源
- 首页
- jsp，模板引擎Themeleaf
- 装配拓展SpringMVC
- 增删改查
- 拦截器
- 国际化

#### 5.1、静态资源

1. 在springboot中，我们可以使用以下方式处理静态资源

   - webjars  `localhost:8080/webjars/`
   - 文件夹：`localhost:8080/`
     - `classpath:/META-INF/resources`
     - `classpath:/resorces/`
     - `classpath:/static/`
     - `classpath:/public/`

2. 优先级：

   1. resources
   2. static：默认存放
   3. public

3. 自定义资源存放

   ```yml
   spring:
   	mvc:
   		static-path-pattern: /hello/
   ```

#### 5.2、首页定制

**注：在template目录下的所有页面，只能通过controller来跳转**

在静态资源目录中增加index.html作为首页即可

#### 5.3、模板引擎

1. 引入themeleaf

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-thymeleaf</artifactId>
   </dependency>
   ```

2. 在想要使用的html页面中加入以下代码，直接使用即可

   ```html
   <html xmlns:th="http://www.thymeleaf.org">
   ```
   
   **注意：所有的页面一定是在template目录下，并且后缀都是.html**

#### 5.4、Themeleaf语法

##### 5.4.1、简单表达式

| 语法 | 名称                           | 描述           | 作用                   |
| :--: | ------------------------------ | -------------- | ---------------------- |
| ${…} | Variable Expressions           | 变量表达式     | 取出上下文变量的值     |
| *{…} | Selection Variable Expressions | 选择变量表达式 | 取出选择的对象的属性值 |
| #{…} | Message Expressions            | 消息表达式     | 使文字消息国际化，I18N |
| @{…} | Link URL Expressions           | 链接表达式     | 用于表示各种超链接地址 |
| ~{…} | Fragment Expressions           | 片段表达式     | 引用一段公共的代码片段 |

##### 5.4.2、.....

#### 5.5、MVC自动配置

原来的springmvc配置是在spring-mvc.xml中配置，在springboot中他帮我们默认配置了，如果想要拓展的话，只需要写一个同来拓展的类即可！

1. 编写拓展类：添加为配置类`@Configuration`，实现`WebMvcConfigurer`接口

   ```java
   //拓展SpringMVC配置
   @Configuration
   public class MyMvcConfig implements WebMvcConfigurer {
       
   }
   ```

2. 重写对应的方法

   ```java
   //视图跳转
   @Override
   public void addViewControllers(ViewControllerRegistry registry) {
       registry.addViewController("/ferron").setViewName("test");
   
   }
   ```

**总结：在springboot中，有非常多的xxxxConfiguration(用来拓展的类，不是自动配置类)会帮助我们进行拓展配置！**

## 6、项目

#### 6.1、准备

1. 导入html资源

2. 建立pojo类

   ```java
   //部门表
   @Data
   @AllArgsConstructor
   @NoArgsConstructor
   public class Department {
   
       private Integer id;
       private String name;
   
   }
   ```

   ```java
   @Data
   @NoArgsConstructor
   public class Employee {
   
       private Integer id;
       private String lastName;
       private String email;
       private Integer gender;//0:女 1：男
       private Department department;
       private Date birth;
   
       public Employee(Integer id, String lastName, String email, Integer gender, Department department) {
           this.id = id;
           this.lastName = lastName;
           this.email = email;
           this.gender = gender;
           this.department = department;
           this.birth = new Date();
       }
   }
   ```

3. 编写底层dao代码，注意是使用原始方法实现，不使用数据库

   ```java
   //部门dao
   @Repository
   public class DepartmentDao {
   
       //模拟数据库中的数据
       private static Map<Integer, Department> departments = null;
   
       static {
           departments.put(101,new Department(101,"教学部"));
           departments.put(102,new Department(102,"市场部"));
           departments.put(103,new Department(103,"教研部"));
           departments.put(104,new Department(104,"运营部"));
           departments.put(105,new Department(105,"后勤部"));
       }
   
       //获得所有部门信息
       public Collection<Department> getDepartments(){
           return departments.values();
       }
   
       //根据id得到部门
       public Department getDepartmentById(Integer id){
           return departments.get(id);
       }
   
   }
   
   ```

   ```java
   //员工dao
   @Repository
   public class EmployeeDao {
   
       //模拟数据库中的数据
       private static Map<Integer, Employee> employees = null;
       //员工有所属的部门
       @Autowired
       private DepartmentDao departmentDao;
   
       static {
           employees.put(1001,new Employee(1001,"Ferron1","21355@qq.com",0,new Department(101,"教学部")));
           employees.put(1002,new Employee(1002,"Ferron2","21355@qq.com",1,new Department(102,"市场部")));
           employees.put(1003,new Employee(1003,"Ferron3","21355@qq.com",0,new Department(103,"教研部")));
           employees.put(1004,new Employee(1004,"Ferron4","21355@qq.com",1,new Department(104,"运营部")));
           employees.put(1005,new Employee(1005,"Ferron5","21355@qq.com",0,new Department(105,"后勤部")));
       }
   
       //主键自增
       private static Integer initId = 1006;
       //增加一个员工
       public void save(Employee employee){
           if(employee.getId() == null){
               employee.setId(initId++);
           }
   
           employee.setDepartment(departmentDao.getDepartmentById(employee.getDepartment().getId()));
           employees.put(employee.getId(),employee);
       }
   
       //查询全部员工
       public Collection<Employee> getAll(){
           return employees.values();
       }
   
       //通过id查询员工
       public void delete(Integer id){
           employees.remove(id);
       }
   }
   ```

#### 6.2、首页实现

1. 在首页实现跳转，最好将首页跳转放在拓展配置类里面实现
2. 将html中的改成themeleaf支持的格式

#### 6.3、国际化

1. 将idea编码设为utf-8

2. 在resources目录下新建i18n目录，在目录下新建并编写login.properties, login_en_US.properties, login_zh_CN.properties等三个文件

3. 在application.yml文件中配置

   ```yml
   spring.messages.basename=i18n.login
   ```

4. 在对应的html页面中使用`#{}`取出对应的内容

5. 编写对应的按钮

   ```html
   <a class="btn btn-sm" th:href="@{/index.html(l='zh_CN')}">中文</a>
   <a class="btn btn-sm" th:href="@{/index.html(l='en_US')}">English</a>
   ```

6. 自己重新编写一个本地化解析器

   ```java
   public class MyLocaleResolver implements LocaleResolver {
       @Override
       public Locale resolveLocale(HttpServletRequest httpServletRequest) {
   
           String language = httpServletRequest.getParameter("l");
   
           Locale locale = Locale.getDefault();  //如果没有就用默认的
   
           //如果请求的连接携带了国际化的参数
           if(!StringUtils.isEmpty(language)){
               //zh_CN
               String[] split = language.split("_");
               //国家，地区
               new Locale(split[0],split[1]);
           }
   
           return locale;
       }
   
       @Override
       public void setLocale(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Locale locale) {
   
       }
   }
   
   ```

7. 在拓展定义类中配置使用`@Bean`注解注入

   ```java
   //自定义自定义的国际化组件就生效了
   @Bean
   public LocaleResolver localeResolver(){
       return new MyLocaleResolver();
   }
   ```

8. **实质：点击切换按钮的时候进入网页时携带了对应的参数，以此来实现切换语言**

#### 6.4、登录功能实现

1. 将对应的表单绑定上接口，在对应的输入框中使用`name`属性，来实现在后台绑定。

   ```html
   <input type="text" name="username" class="form-control" th:placeholder="#{login.username}" required="" autofocus="">
   <input type="password" name="password" class="form-control" th:placeholder="#{login.password}" required="">
   ```

2. 编写对应的接口，实现传参，注意要使用`@RequestParam`注解赋值

   ```java
   @Controller
   public class LoginController {
       @RequestMapping("/user/login")
       public String login(@RequestParam("username") String username,
                           @RequestParam("password") String password,
                           Model model){
           //业务
           if(StringUtils.isEmpty(username) && "123456".equals(password)){
               return "dashboard";
           }else{
               model.addAttribute("msg","用户名或者密码错误");
               return "index";
           }
       }
   
   }
   ```

3. 当密码错误时，输出密码错误。【注意：只有密码错误才显示，其他情况不显示】

   ```html
   <!--如果msg的值为空，则不显示消息-->
         <p style="color: red" th:text="${msg}" th:if="${not #strings.isEmpty(msg)}"></p>
   ```

4. 重新配置请求url

   ```java
   registry.addViewController("/main.html").setViewName("dashboard");
   ```

5. 重新定义跳转

   ```java
   return "redirect:/main.html";
   ```

   注意：此时一个很严重的问题，就是main.html可以直接访问，因此要设置拦截器

#### 6.5、登录拦截器

1. 重写登录接口，在登录成功时设置session

   ```java
   @RequestMapping("/user/login")
   public String login(@RequestParam("username") String username,
                       @RequestParam("password") String password,
                       Model model,
                       HttpSession session){
       //业务
       if(!StringUtils.isEmpty(username) &&"123456".equals(password)){
           session.setAttribute("loginUser",username);
           return "redirect:/main.html";
       }else{
           model.addAttribute("msg","用户名或者密码错误");
           return "index";
       }
   }
   ```

2. 编写自定义拦截器

   ```java
   public class LoginHandlerInterceptor implements HandlerInterceptor {
       @Override
       public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
   
           //取出session
           Object loginUser = request.getSession().getAttribute("loginUser");
   
           //登录成功之后，应该存在用户的session
           if(loginUser == null){
               //没有登录
               request.setAttribute("msg","没有权限，请先登录");
               request.getRequestDispatcher("/index.html").forward(request,response);
               return false;
   
           }else{
               return true;
           }
           
       }
   }
   ```

3. 在拓展配置中重写对应方法

   ```java
   @Override
   public void addInterceptors(InterceptorRegistry registry) {
       registry.addInterceptor(new LoginHandlerInterceptor())
           .addPathPatterns("/**")
           .excludePathPatterns("/index.html","/","/user/login","/css/*","/js/**","/img/**");
   }
   ```

#### 6.6、员工列表展示

1. 提取公共页面

2. 编写对应的接口

   ```java
   @Autowired
   EmployeeDao employeeDao;
   
   //查询全部员工
   @RequestMapping("/emps")
   public String list(Model model){
   
       Collection<Employee> employees = employeeDao.getAll();
       model.addAttribute("emps",employees);
       return "emp/list";
   
   }
   ```

3. 编写前端

   ```html
   <table class="table table-striped table-sm">
       <thead>
           <tr>
               <th>id</th>
               <th>lastName</th>
               <th>email</th>
               <th>gender</th>
               <th>department</th>
               <th>birth</th>
               <th>操作</th>
           </tr>
       </thead>
       <tbody>
           <tr th:each="emp:${emps}">
               <td th:text="${emp.getId()}"></td>
               <td th:text="${emp.getLastName()}"></td>
               <td th:text="${emp.getEmail()}"></td>
               <td th:text="${emp.getGender()==0?'女':'男'}"></td>
               <td th:text="${emp.department.getName()}"></td>
               <td th:text="${#dates.format(emp.getBirth(),'yyyy-MM-dd HH:mm:ss')}"></td>
               <td>
                   <button class="btn btn-sm btn-primary">编辑</button>
                   <button class="btn btn-sm btn-danger">删除</button>
               </td>
           </tr>
       </tbody>
   </table>
   ```


#### 6.7、添加员工

1. 按钮提交

   ```html
   <h2><a class="btn btn-sm btn-success" th:href="@{/emp}">添加员工</a></h2>
   ```

2. 跳转添加页面

   ```html
   <form th:action="@{/emp}" method="post">
       <div class="form-group">
           <label>LastName</label>
           <input type="text" name="lastName" class="form-control" placeholder="ferrron">
       </div>
       <div class="form-group">
           <label>email</label>
           <input type="text" name="email" class="form-control" placeholder="314442@qq.com">
       </div>
       <div class="form-group">
           <label>Gender</label>
           <div class="form-check form-check-inline">
               <input class="form-check-input" type="radio" name="gender" value="1">
               <label class="form-check-label">男</label>
           </div>
           <div class="form-check form-check-inline">
               <input class="form-check-input" type="radio" name="gender" value="0">
               <label class="form-check-label">女</label>
           </div>
       </div>
       <div class="form-group">
           <label>department</label>
           <select class="form-control" name="department.id">
               <!--我们在controller接收的是一个Employee，所以我们需要提交的是其中的一个属性-->
               <option th:each="dept:${departments}" th:text="${dept.getName()}" th:value="${dept.getId()}"></option>
           </select>
       </div>
       <div class="form-group">
           <label>Birth</label>
           <input type="text" name="birth" class="form-control" placeholder="2000-01-01">
       </div>
       <button type="submit" class="btn btn-primary">添加</button>
   </form>
   ```

3. 添加员工成功

   注意：使用Restful风格

   ```java
   @GetMapping("/emp")
   public String toAddpage(Model model){
   
       //查出所有部门的信息
       Collection<Department> departments = departmentDao.getDepartments();
       model.addAttribute("departments",departments);
   
       return "emp/add";
   }
   
   @PostMapping("/emp")
   public String addEmp(Employee employee){
       //添加的操作
   
       employeeDao.save(employee); //保存员工
   
   
       return "redirect:/emps";
   }
   ```

4. 返回首页

#### 6.8、修改员工信息

1. 添加按钮

   ```html
   <a class="btn btn-sm btn-primary" th:href="@{/emp/}+${emp.getId()}">编辑</a>
   ```

2. 去员工页面接口

   ```java
   //去员工的修改页面
   @GetMapping("/emp/{id}")
   public String toUpdateEmp(@PathVariable("id") Integer id, Model model){
   
       Employee employeeById = employeeDao.getEmployeeById(id);
       model.addAttribute("emp",employeeById);
   
       //查找部门信息
       Collection<Department> departments = departmentDao.getDepartments();
       model.addAttribute("departments",departments);
   
       return "emp/update";
   }
   ```

3. 更改页面

   ```html
   <form th:action="@{/updateEmp}" method="post">
       <!--隐藏域-->
       <input type="hidden" name="id" th:value="${emp.getId()}">
       <div class="form-group">
           <label>LastName</label>
           <input type="text" name="lastName" class="form-control" th:value="${emp.getLastName()}">
       </div>
       <div class="form-group">
           <label>email</label>
           <input type="text" name="email" class="form-control" placeholder="314442@qq.com" th:value="${emp.getEmail()}">
       </div>
       <div class="form-group">
           <label>Gender</label>
           <div class="form-check form-check-inline">
               <input th:checked="${emp.getGender()==1}" class="form-check-input" type="radio" name="gender" value="1">
               <label class="form-check-label">男</label>
           </div>
           <div class="form-check form-check-inline">
               <input th:checked="${emp.getGender()==0}"class="form-check-input" type="radio" name="gender" value="0">
               <label class="form-check-label">女</label>
           </div>
       </div>
       <div class="form-group">
           <label>department</label>
           <select class="form-control" name="department.id">
               <!--我们在controller接收的是一个Employee，所以我们需要提交的是其中的一个属性-->
               <option th:selected="${dept.getId()==emp.getDepartment().getId()}" th:each="dept:${departments}" th:text="${dept.getName()}" th:value="${dept.getId()}"></option>
           </select>
       </div>
       <div class="form-group">
           <label>Birth</label>
           <input type="text" name="birth" class="form-control" placeholder="2000-01-01" th:value="${#dates.format(emp.getBirth(),'yyyy-MM-dd HH:mm')}">
       </div>
       <button type="submit" class="btn btn-primary">修改</button>
   </form>
   ```

4. 更改接口

   ```java
   //员工信息修改
   @PostMapping("/updateEmp")
   public String updateEmp(Employee employee){
       employeeDao.save(employee);
       return "redirect:/emps";
   }
   ```

#### 6.9、员工修改以及404处理

员工修改：

1. 删除按钮
2. RestFul风格传参，删除

404：

1. 在templates目录下建立error文件夹
2. 在error文件夹下建立`404.html`，当发生404错误时自动进入

注销

#### 6.10、如何写一个网站

##### 6.10.1、前端：

- 模板：别人写好的，我们拿来改
- 框架：组件：需要自己手动组合拼
  - 栅格系统
  - 导航栏
  - 侧边栏

##### 6.10.2、网站开发步骤

1. 前端搞定：页面长寿面样子
2. 设计数据库（数据库设计）
3. 前端让他能够自动运行，独立化工程
4. 数据接口如果对象：json
5. 前后端联调测试

##### 6.10.3、必备

- 有一套自己熟悉的后台模板
- 前端界面：至少自己能够通过前端框架，组合出来一个网站页面
  - index
  - about
  - blog
  - post
  - user
- 让网站独立运行

## 7、Spring Data

#### 7.1、原生态实现数据库

1. 编写数据库配置文件

   ```yml
   spring:
   	datasource:
   		url: jdbc:mysql://localhost:3306/mybatis?useSSL=false&useUnicode=truecharacterEncoding=UTF-8&serverTimezone=UTC
   		password: sanfashi
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


#### 7.2、整合Druid数据源

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

#### 7.3、整合Mybatis

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

## 8、SpringSecurity

#### 8.1、概述

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

#### 8.2、环境搭建

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

#### 8.3、用户认证和授权

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

#### 8.4、用户注销及权限控制

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

#### 8.5、定制登录页面以及记住我

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


## 9、Shiro

#### 9.1、概述

- shiro是一个java的安全(权限)框架
- 可以完成认证，授权，加密，会话管理，web集成，缓存等等

#### 9.2、三大对象

- Subject 用户
- SecurityManager 管理所有用户
- Realm 连接数据

#### 9.3、环境搭建

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

#### 9.4、登录拦截

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

#### 9.5、用户认证

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

#### 9.6、整合mybatis

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

#### 9.7、shiro授权实现

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

#### 9.8、shiro和thymeleaf整合

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


## 10、swagger

#### 10.1、概述

前后端分离：Vue+springboot

后端时代：前端只用管理静态页面；html==>后端，模板引擎：jsp==>后端是主力

前后端分离时代：

- 后端：后端控制层，服务层，数据访问层【后端工程】
- 前端：前端控制层，视图层【前端工程】
  - 伪造后端数据，json。已经存在了，不需要后端，前端工程依然能跑起来
- 前后端如何交互？==>API
- 前后端相对独立，松耦合；
- 前后端甚至可以部署在不同的服务器上



产生问题：

- 前后端集成联调，前端人员和后端人员无法做到，即使协商，今早解决。

解决方案：

- 首先制定schema计划的提纲，实时更新最新的API，降低集成的风险
- 前后端分离：
  - 前端测试后端接口：postman
  - 后端提供接口，需要实时更新最新的消息及改动

**swagger**

- 号称世界上最流行的api框架
- restful api文档在线自动生成工具=>api文档与api同步更新
- 直接运行，可以在线测试api接口

在项目中使用swagger需要springbox

- swagger2
- ui

#### 10.2、springboot集成

- 新建一个项目

- 导入相关依赖

  ```xml
  <!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
  <dependency>
      <groupId>io.springfox</groupId>
      <artifactId>springfox-swagger2</artifactId>
      <version>2.9.2</version>
  </dependency>
  <!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
  <dependency>
      <groupId>io.springfox</groupId>
      <artifactId>springfox-swagger-ui</artifactId>
      <version>2.9.2</version>
  </dependency>
  ```

- 配置swagger

  ```java
  @Configuration
  @EnableSwagger2           //开启Swagger2
  public class SwaggerConfig {
      
  
  }
  ```

- 访问`/swagger-ui`

  ![image-20210130102158388](D:\Application\Typora\Typora\image\image-20210130102158388.png)

#### 10.3、配置swagger

swagger的bean实例Docket

```java
@Configuration
@EnableSwagger2           //开启Swagger2
public class SwaggerConfig {

    //配置了Swagger的Docket的bean实例
    @Bean
    public Docket docket(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo());
    }

    //配置Swagger信息=apiInfo
    public ApiInfo apiInfo(){
        //自定义swagger网站信息
        return new ApiInfo(
                "Ferron的Swagger文档",
                "Api Documentation",
                "1.0",
                "urn:tos", DEFAULT_CONTACT,
                "Apache 2.0",
                "http://www.apache.org/licenses/LICENSE-2.0",
                new ArrayList());
    }
}
```

#### 10.4、Swagger配置扫描接口

**Docket.select()**

配置扫描的接口：

```java
@Bean
public Docket docket(){
    return new Docket(DocumentationType.SWAGGER_2)
        .apiInfo(apiInfo())
        .select()
        //RequestHandlerSelectors 配置要扫描接口的方式
        //basePackage()：指定要扫描接口的包
        //any():扫描全部
        //none()：全部不扫描
        //withClassAnnotation:扫描类上的注解，参数是一个注解的反射对象
        //withMethodAnnotation:扫描方法上的注解
        .apis(RequestHandlerSelectors.basePackage("com.ferron.controller"))
        //过滤什么路径：过滤只有ferron的请求
        .paths(PathSelectors.ant("/ferron/**"))
        .build();  //bulid 工厂模式
}
```

配置是否启动Swagger

```java
@Bean
public Docket docket(){
    return new Docket(DocumentationType.SWAGGER_2)
        .apiInfo(apiInfo())
        .enable(false) //不启用swagger
}
```

我只希望在生产环境中使用，在发布的时候不使用

- 设置两套环境

#### 10.5、配置API分组

上面分组框的分组

```java
//配置了Swagger的Docket的bean实例
@Bean
public Docket docket(){

    return new Docket(DocumentationType.SWAGGER_2)
        .apiInfo(apiInfo())
        .groupName("ferron")
        .select()
        .apis(RequestHandlerSelectors.basePackage("com.ferron.controller"))
        .paths(PathSelectors.ant("/ferron/**"))
        .build();
}
```

如何配置多个组：

在SwaggerConfig类中创建多个返回Docket的不同方法，创建多个分组。

```java
@Bean
public Docket docket1(){
    return new Docket(DocumentationType.SWAGGER_2).groupName("A");
}

@Bean
public Docket docket2(){
    return new Docket(DocumentationType.SWAGGER_2).groupName("B");
}

@Bean
public Docket docket3(){
    return new Docket(DocumentationType.SWAGGER_2).groupName("C");
}
```

#### 10.6、实体类配置

**Model：**

- 创建pojo类，新建User实体类

- 写一个请求，返回值为实体类

  ```java
  //只要我们的请求返回值中存在实体类，model中就会有实体类
  @PostMapping(value = "/user")
  public User user(){
      return new User();
  }
  ```

- 使用`@ApiModel`给实体类加注释，使用`@ApiModelProperty`给实体类字段加注释

  ```java
  @ApiModel("用户实体类")
  public class User {
  
      @ApiModelProperty("用户名")
      public String username;
      @ApiModelProperty("密码")
      public String password;
  
  }
  ```

#### 10.7、接口文档配置

- 不能给整个控制器加注释

- 给控制器里面的某个接口加注释

  ```java
  @ApiOperation("Hello控制类")
  @GetMapping(value = "/hello2")
  public String hello2(@ApiParam("用户名") String username){
      return "hello"+username;
  }
  ```

- 给某个接口里面的参数加注释

  ```java
  @ApiOperation("Hello控制类")
  @GetMapping(value = "/hello2")
  public String hello2(@ApiParam("用户名") String username){
      return "hello"+username;
  }
  ```

#### 10.8、总结

- 在线测试
- 实时更新
- 【注意点】在正式发布的时候，关闭Swagger！

## 11、任务

#### 11.1、异步任务

**使用多线程体验：**

service层：

```java
@Service
public class AsyncService {

    public void hello(){
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("消息正在处理");
    }    
}
```

Controller层

```java
@RestController
public class AsycController {

    @Autowired
    AsyncService asyncService;

    @RequestMapping("/hello")
    public String hello(){
        asyncService.hello();
        return "OK";
    }
}
```

**使用异步任务来完成：**

Service层：

```java
@Service
public class AsyncService {

    //这个一个异步任务
    @Async
    public void hello(){
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("消息正在处理");
    }

}
```

Controller层

```java
@RestController
public class AsycController {

    @Autowired
    AsyncService asyncService;

    @RequestMapping("/hello")
    public String hello(){
        asyncService.hello();
        return "OK";
    }
}
```

**注：要在主类中开启异步任务注解**

```java
//开启异步注解功能
@EnableAsync
@SpringBootApplication
public class SpringBoot08TestApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBoot08TestApplication.class, args);
    }

}
```

#### 11.2、邮件任务

- 导入相关依赖

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-mail</artifactId>
  </dependency>
  ```

- 在application.properties中配置

  ```properties
  spring.mail.username=2963624004@qq.com
  spring.mail.password=wsklzxzjitzadgca
  spring.mail.host=smtp.qq.com
  spring.mail.properties.mail.smtp.ssl.enable=true
  ```

- 测试

  ```java
  @SpringBootTest
  class SpringBoot08TestApplicationTests {
  
      @Autowired
      JavaMailSenderImpl mailSender;
  
      @Test
      void contextLoads() {
  
          //一个简单的邮件
          SimpleMailMessage mailMessage = new SimpleMailMessage();
          mailMessage.setSubject("你好");
          mailMessage.setText("谢谢你");
          mailMessage.setTo("2963624004@qq.com");
          mailMessage.setFrom("2963624004@qq.com");
  
          mailSender.send(mailMessage);
      }
  
  }
  ```

- 一个复杂的邮件发送

  ```java
  @Test
  void contextLoads2() throws MessagingException {
  
      //一个复杂的邮件
      MimeMessage mimeMessage = mailSender.createMimeMessage();
      //组装
      MimeMessageHelper helper = new MimeMessageHelper(mimeMessage,true);
  
      helper.setSubject("你好plus");
      //发送一个html段落
      helper.setText("<p style='color:red'>这个一个段落</p>",true);
      //附件
      helper.addAttachment("1.jpg",new File("C:\\Users\\朱小龙\\Desktop\\1.jpg"));
  
      helper.setTo("2963624004@qq.com");
      helper.setFrom("2963624004@qq.com");
  
      mailSender.send(mimeMessage);
  
  
  }
  ```

- **封装成一个方法**

#### 11.3、定时任务

- 开启定时任务的注解

  ```java
  @EnableScheduling 
  ```

- 写一个定时任务【重点：cron表达式】

  ```java
  @Service
  public class ScheduledService {
  
      //在一个特定的时间执行这个方法
      //cron表达式
      //秒 分 时 日 月 周几
      @Scheduled(cron = "0 23 12 * * ?")
      public void hello(){
          System.out.println("Hello,你被执行了");
      }
  
  }
  ```


## 12、分布式 Dubbo Zookeeper

#### 12.1、分布式

分布式系统是若干个独立计算机的集合，但是对于用户来说就像一个系统

- 服务器内部交互：http，RPC
- 最开始服务只是一个单机，当单机处理不了时，再考虑分布式

![image](https://dubbo.apache.org/imgs/user/dubbo-architecture-roadmap.jpg)

#### 12.2、RPC

- Remote Procedure Call是远程过程调用，是一种进程间通信方式
- 它是一种技术的思想，而不是规范
- 它允许程序调用另一个地址空间(通常是共享网络的另一台机器上)的过程或函数，而不用程序员显式编码这个远程调用的细节
- 即程序员无论是调用本地的还是远程的函数，本质上编写的调用代码基本相同
- 本地过程调用就是类比同一台电脑A和B两个函数，A调用B；远程过程调用就类比A和B函数在不同的电脑中，互相调用
- RPC两个核心模块
  - 通讯
  - 序列化：数据传输需要转换【反序列化】

#### 12.3、Dubbo

- Dubbo是一款高性能、轻量级的开源Java RPC框架
- 面向接口的远程方法调用
- 智能容错和负载均衡
- 服务自动注册和发现

![dubbo-architucture](https://dubbo.apache.org/imgs/user/dubbo-architecture.jpg)

#### 12.4、Zookeeper：管理Hadoop Hive..

- Zookeeper是 Apache Hadoop 的子项目，是一个树型的目录服务
- 支持变更推送，适合作为 Dubbo 服务的注册中心，工业强度较高，可用于生产环境，并推荐使用

#### 12.5、Zookeeper安装

- 使用镜像下载Zookeeper，并解压【注意：要下载带有bin的】
- 启动bin目录下的`zkServer.cmd`，注意加入`pause`排错
- 进入config目录加入`zoo.cfg`文件，再启动。
- 保持服务端启动，启动用户端`zkCli.cmd`，连接成功

#### 12.6、Dubbo-admin安装

- Dubbo本身不是一个服务软件，实质上就是一个jar包，能够帮你的java程序连接到Zookeeper，并且利用Zookeeper消费，提供服务
- 为了让用户更好的管理监控众多的dubbo服务，官方提供了一个可视化的监控程序dubbo-admin

安装：

- 下载：https://github.com/apache/dubbo-admin/tree/master

- 解压并且进入目录：

  修改dubbo-admin\src\main\resources\application.properties指定zookeeper地址

  ```properties
  server.port=7001
  spring.velocity.cache=false
  spring.velocity.charset=UTF-8
  spring.velocity.layout-url=/templates/default.vm
  spring.messages.fallback-to-system-locale=false
  spring.messages.basename=i18n/message
  spring.root.password=root
  spring.guest.password=guest
  #注册中心的地址
  dubbo.registry.address=zookeeper://127.0.0.1:2181
  ```

- 在项目目录下`D:\Work\JAVA\dubbo-admin-master\dubbo-admin`打包`dubbo-admin`

  ```xshell
  mvn clean package -Dmaven.test.skip=true
  ```

- 在target目录下执行该jar包【注意：zookeeper需要在运行中】

  ```xshell
  java -jar xxx.jar
  ```

- 或者直接在idea中打开dubbo-admin项目，运行，账户密码需要在`application.properties`查看，进入dubbo-admin

  ![image-20210131132922027](D:\Application\Typora\Typora\image\image-20210131132922027.png)

- 注意：

  - zookeeper：注册中心
  - dubbo-admin：是一个监控管理后台，也可以没有。可以查看我们注册了哪些服务，哪些服务被消费了
  - dubbo：jar包

#### 12.7、整合dubbo+zookeeper

- 新建两个module，一个是provider，一个是consumer

- provider中编写对应的service接口和具体实现类

  ```java
  package com.ferron.service;
  
  public interface TicketService {
  
      public String getTicket();
  
  }
  ```

  ```java
  package com.ferron.service;
  
  public class TicketServiceImpl implements TicketService {
      @Override
      public String getTicket() {
          return "Ferron";
      }
  }
  ```

- provider导入相关依赖

  ```xml
  <!-- https://mvnrepository.com/artifact/org.apache.dubbo/dubbo-spring-boot-starter -->
  <dependency>
      <groupId>org.apache.dubbo</groupId>
      <artifactId>dubbo-spring-boot-starter</artifactId>
      <version>2.7.8</version>
  </dependency>
  <!-- https://mvnrepository.com/artifact/com.github.sgroschupf/zkclient -->
  <dependency>
      <groupId>com.github.sgroschupf</groupId>
      <artifactId>zkclient</artifactId>
      <version>0.1</version>
  </dependency>
  <!--日志会冲突-->
  <!--引入zookeeper-->
  <dependency>
      <groupId>org.apache.curator</groupId>
      <artifactId>curator-framework</artifactId>
      <version>2.12.0</version>
  </dependency>
  <dependency>
      <groupId>org.apache.curator</groupId>
      <artifactId>curator-recipes</artifactId>
      <version>2.12.0</version>
  </dependency>
  <dependency>
      <groupId>org.apache.zookeeper</groupId>
      <artifactId>zookeeper</artifactId>
      <version>3.4.14</version>
      <!--排除这个slf4j-log4j12-->
      <exclusions>
          <exclusion>
              <groupId>org.slf4j</groupId>
              <artifactId>slf4j-log4j12</artifactId>
          </exclusion>
      </exclusions>
  </dependency>
  ```

- 编写provider的配置文件

  【注意：这里的注册中心地址需要看你下载的zookeeper的application.properties中的注册中心地址】

  ```properties
  server.port=8001
  
  #服务应用名字
  dubbo.application.name=provider-server
  #注册中心地址
  dubbo.registry.address=zookeeper://127.0.0.1:2181
  #哪些服务要被注册
  dubbo.scan.base-packages=com.ferron.service
  ```

- 将provider的服务注册

  ```java
  package com.ferron.service;
  
  import org.apache.dubbo.config.annotation.DubboService;
  import org.springframework.stereotype.Component;
  
  //服务注册与发现
  @DubboService
  @Component //使用了dubbo后尽量不要使用@Service注解
  public class TicketServiceImpl implements TicketService {
      @Override
      public String getTicket() {
          return "Ferron";
      }
  }
  ```

- 启动zookeeper之后，进入dubbo-admin查看，发现我们的提供者注册成功

  ![image-20210131162152044](D:\Application\Typora\Typora\image\image-20210131162152044.png)

- 到这里，服务者就算完成了，接下来进行消费者的配置，首先**导入依赖**

- 编写配置文件：名称及注册中心地址

  ```properties
  server.port=8002
  
  #消费者要去哪里拿，暴露自己的名字
  dubbo.application.name=consumer-server
  #注册中心地址
  dubbo.registry.address=zookeeper://127.0.0.1:2181
  
  ```

- 在consumer端编写实现类，从远程调用对应的实现类，但是前提是要本地有一样的接口

  【**注意：本地要有同样的包名以及同样的接口**】

  ```java
  package com.ferron.service;
  
  import org.apache.dubbo.config.annotation.DubboReference;
  import org.springframework.stereotype.Service;
  
  @Service  //使用spring的注解放到容器中
  public class UserService {
  
      //想拿到provider的ticket,要去注册中心拿到服务
      //引用， Pom坐标，可以定义路径相同的接口名
      @DubboReference
      TicketService ticketService;
  
      public void buyTicker(){
          String ticket = ticketService.getTicket();
          System.out.println("在注册中心拿到"+ticket);
      }
  
  }
  ```

- 测试

  ```java
  @Autowired
  UserService userService;
  
  
  @Test
  void contextLoads() {
  
      userService.buyTicker();
  
  }
  ```

#### 12.8、使用步骤

**前提：zookeeper服务一直开启**

1. 提供者提供服务
   1. 导入依赖
   2. 配置注册中心的地址，以及服务发现名，和要扫描的包
   3. 在想要被注册的服务上面，增加一个注解`DubboService`
2. 消费者如何消费
   1. 导入依赖
   2. 配置注册中心的地址，以及自己的服务名
   3. 从远程注入服务，使用`@Reference`

## 13、未来

#### 13.1、微服务架构问题

- 这么多服务，客户端怎么去访问
- 这么多服务，服务之间如何进行通信
- z这么多服务，如何治理
- 服务挂了，怎么办

#### 13.2、解决方案

- SpringCloud是一套生态，就是来解决以上分布式架构的四个问题
- SpringCloud是基于SpringBoot的
- SpringCloud一站式解决方案
  - Api网关，服务路由
  - HTTP,RPC框架，异步调用
  - 服务注册与发现，高可用
  - 熔断机制，服务降级









## X、知识点

#### X.1、路径问题

- 在resources目录下：classpath:static/...









#### X.2、创建springboot

1. 创建一个空的maven项目

2. pom.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
       <parent>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-parent</artifactId>
           <version>2.4.2</version>
           <relativePath/> <!-- lookup parent from repository -->
       </parent>
       <groupId>com.ferron</groupId>
       <artifactId>xxx</artifactId>
       <version>0.0.1-SNAPSHOT</version>
       <name>xxx</name>
       <description>Demo project for Spring Boot</description>
       <properties>
           <java.version>11</java.version>
       </properties>
       <dependencies>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
           </dependency>
   
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-test</artifactId>
               <scope>test</scope>
           </dependency>
       </dependencies>
   
       <build>
           <plugins>
               <plugin>
                   <groupId>org.springframework.boot</groupId>
                   <artifactId>spring-boot-maven-plugin</artifactId>
               </plugin>
           </plugins>
       </build>
   
   </project>
   ```

3. 补充main目录

   - com.ferron

   - com.ferron下主类

     ```java
     package com.ferron;
     
     import org.springframework.boot.SpringApplication;
     import org.springframework.boot.autoconfigure.SpringBootApplication;
     
     @SpringBootApplication
     public class ConsumerServerApplication {
     
         public static void main(String[]args)
         {
             SpringApplication.run(ConsumerServerApplication.class,args);
         }
     
     }
     ```

   - static

   - templates

   - application.properties

4. 补充test目录

   - com.ferron

   - com.ferron下的测试类

     ```java
     package com.ferron;
     
     import org.junit.jupiter.api.Test;
     import org.springframework.boot.test.context.SpringBootTest;
     
     @SpringBootTest
     public class ConsumerServerApplicationTests {
     
         @Test
         void contextLoads() {
         }
     }
     ```

     



