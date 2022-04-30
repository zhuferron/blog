---
title: SpringBoot学习笔记(二)
tags: SpringBoot
categories: 学习笔记
description: SpringBoot第二部分学习笔记，主要介绍了使用SpringBoot怎么进行真实开发，以及一个简单的SpringBoot项目
---

## 6、springboot web开发

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

### 6.1、静态资源

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

### 6.2、首页定制

**注：在template目录下的所有页面，只能通过controller来跳转**

在静态资源目录中增加index.html作为首页即可

### 6.3、模板引擎

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

### 6.4、Themeleaf语法

#### 简单表达式

| 语法 | 名称                           | 描述           | 作用                   |
| :--: | ------------------------------ | -------------- | ---------------------- |
| ${…} | Variable Expressions           | 变量表达式     | 取出上下文变量的值     |
| *{…} | Selection Variable Expressions | 选择变量表达式 | 取出选择的对象的属性值 |
| #{…} | Message Expressions            | 消息表达式     | 使文字消息国际化，I18N |
| @{…} | Link URL Expressions           | 链接表达式     | 用于表示各种超链接地址 |
| ~{…} | Fragment Expressions           | 片段表达式     | 引用一段公共的代码片段 |

### 6.5、MVC自动配置

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

## 7、项目

### 7.1、准备

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

### 7.2、首页实现

1. 在首页实现跳转，最好将首页跳转放在拓展配置类里面实现
2. 将html中的改成themeleaf支持的格式

### 7.3、国际化

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

### 7.4、登录功能实现

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

### 7.5、登录拦截器

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

### 7.6、员工列表展示

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


### 7.7、添加员工

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

### 7.8、修改员工信息

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

### 7.9、员工修改以及404处理

员工修改：

1. 删除按钮
2. RestFul风格传参，删除

404：

1. 在templates目录下建立error文件夹
2. 在error文件夹下建立`404.html`，当发生404错误时自动进入

注销

### 7.10、如何写一个网站

#### 7.10.1、前端：

- 模板：别人写好的，我们拿来改
- 框架：组件：需要自己手动组合拼
  - 栅格系统
  - 导航栏
  - 侧边栏

#### 7.10.2、网站开发步骤

1. 前端搞定：页面长寿面样子
2. 设计数据库（数据库设计）
3. 前端让他能够自动运行，独立化工程
4. 数据接口如果对象：json
5. 前后端联调测试

#### 7.10.3、必备

- 有一套自己熟悉的后台模板
- 前端界面：至少自己能够通过前端框架，组合出来一个网站页面
  - index
  - about
  - blog
  - post
  - user
- 让网站独立运行