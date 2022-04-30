## 1、Spring

#### 1.1、简介

给软件行业带来了春天

#### 1.2、优点

- spring是一个开源的免费的框架（容器）
- spring是一个轻量级的、非入侵式的框架
- 控制反转（IOC），面向切面编程（AOP）
- 支持事务的处理，对框架整合的支持

#### 1.3、组成

![img](D:\Application\Typora\Typora\image\`}T6QIHKB79MHE8$QUYCWA.png)

#### 1.4、拓展

- spring boot
  - 一个快速开发的脚手架
  - 基于springboot可以快速开发单个微服务
  - 约定大于配置
- spring cloud
  - 基于springboot实现的

因为现在大部分公司都在使用springboot进行开发。

弊端：配置十分复杂，人称“配置地狱”

## 2、IOC理论推导

原本实现一个业务：

1. UserDao接口
2. UserDaoImpl实现类
3. UserService业务接口
4. UserServiceImpl业务实现类

在我们之前的业务中，用户的需求可能会影响我们原来的代码，我们需要根据用户的需求去修改源代码！如果程序代码量十分大，修改代码成本会十分昂贵！



我们使用set接口实现，已经发生了革命性的变化！

```java
private UserDao userDao;

//利用set进行动态实现值的注入
public void setUserDao(UserDao userDao){
    this.userDao = userDao;
}
```

- 之前，程序是主动创建对象！控制权在程序员身上！
- 使用set注入后，程序不再具有主动性，而是变成了被动地接受对象。



这种思想，从本质上解决了问题，我们程序员不用再去管理对象的创建了。系统的耦合性大大降低，可以更专注地去实现业务！这是IOC的原型。

IOC：**控制反转，是一种设计思想，DI(依赖注入)是实现IOC的一种方法。**也有人认为DI是IOC的另一种说法。没有IOC的程序中，我们使用面向对象编程，对象的创建与对象间的依赖关系完全硬编码在程序中，对象的创建由程序自己控制，控制反转后将对象的创建转移给第三方，即获得依赖对象的方法反转了。

## 3、HelloSpring

1. 编写一个实体类对象

   ```java
   public class Hello {
   
       private String str;
   
       public String getStr() {
           return str;
       }
       //必须得有set方法，否则就报错
       public void setStr(String str) {
           this.str = str;
       }
   
       @Override
       public String toString() {
           return "Hello{" +
                   "str='" + str + '\'' +
                   '}';
       }
   }
   ```

2. 编写beans.xml配置文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd">
   
       <!--使用spring来创建对象，在spring这些都称为bean
   
       java创建对象写法：
       类型 变量名 = new 类型();
       Hello hello = new Hello();
   
       spring创建对象写法：
       id=变量名
       class=new的对象
       property相当于给对象中的属性设置一个值
       -->
       <bean id="hello" class="com.ferron.pojo.Hello">
           <property name="str" value="Spring"/>
       </bean>
   
   </beans>
   ```

3. 测试

   ```java
   //获取Spring的上下文对象,这里使用配置文件加载spring上下文对象。
   ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
   //我们的对象现在都在spring中管理，我们要使用，直接去里面取出来就可以了！
   Hello hello = (Hello) context.getBean("hello");
   System.out.print(hello.toString());
   ```

## 4、IOC创建对象的方式

#### 4.1使用无参构造创建对象

```xml
<bean id="user" class="com.ferron.pojo.User">
    <property name="name" value="朱小龙"/>
</bean>
```

#### 4.2使用有参构造创建对象

##### 4.2.1使用索引传参

```xml
<bean id="user" class="com.ferron.pojo.User">
    <constructor-arg index="0" value="朱飞龙"/>
</bean>
```

##### 4.2.2使用类型传参(不建议使用)

```xml
<bean id="user" class="com.ferron.pojo.User">
    <constructor-arg type="java.lang.String" value="朱飞龙"/>
</bean>
```

##### 4.2.3使用参数名传参

```xml
<bean id="user" class="com.ferron.pojo.User">
    <constructor-arg name="name" value="朱飞龙"/>
</bean>
```

总结：在配置文件加载的时候，容器中管理的对象就已经初始化了！

## 5、Spring配置

#### 5.1、别名

```xml
<!--使用别名-->
<alias name="user" alias="userAlias"/>
```

#### 5.2、Bean配置

```xml
<!--bean里面的配置：
id:bean的唯一标识符，也就是相当与我们的对象名
class:bean对象所对应的全限定名：包名+类型
name:也是别名,name更高级，可以取多个别名
-->
<bean id="userT" class="com.ferron.pojo.User">
</bean>
```

#### 5.3、Import

```xml
<!--用于团队开发，import别人的beans，合成applicationContext.xml-->
```

## 6、依赖注入

#### 6.1、构造器注入

参见第四节。

#### 6.2、Set方式注入【重点】

- 依赖注入：set注入
  - bean对象的创建依赖于容器
  - bean对象中的所有属性由容器来注入

【环境搭建】

1. 复杂类型

   ```java
   private String address;
   public String getAddress() {
       return address;
   }
   public void setAddress(String address) {
       this.address = address;
   }
   ```

2. 真实测试对象

   ```java
   public class student{
       private String name;
       private Address address;
       private String[] books;
       private List<String> hobbys;
       private Map<String,String> card;
       private Set<String> games;
       private String wife;
       private Properties info;
   }
   ```

3. beans.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd">
       <bean id="address" class="com.ferron.pojo.Address">
           <property name="address" value="广州"/>
       </bean>
   
       <bean id="student" class="com.ferron.pojo.Student">
           <!--第一种：普通值注入，value -->
           <property name="name" value="朱飞龙"/>
           <!--第二种：引用类型注入，首先要在外面使用bean标签将其注册，然后使用引用ref注入 -->
           <property name="address" ref="address"/>
           <!--第三种:数组注入,ref-->
           <property name="books">
               <array>
                   <value>红楼梦</value>
                   <value>西游记</value>
                   <value>水浒传</value>
                   <value>三国演义</value>
               </array>
           </property>
           <!--第四种:List注入-->
           <property name="hobbys">
               <list>
                   <value>听歌</value>
                   <value>敲代码</value>
                   <value>看电影</value>
               </list>
           </property>
   
           <!--第五种:Map注入-->
           <property name="card">
               <map>
                   <entry key="身份证" value="123456789"></entry>
                   <entry key="银行卡" value="147258369"></entry>
               </map>
           </property>
   
           <!--第六种Set注入-->
           <property name="games">
               <set>
                   <value>LOL</value>
                   <value>COC</value>
                   <value>BOB</value>
               </set>
           </property>
   
           <!--第七种null注入-->
           <property name="wife">
               <null/>
           </property>
   
           <!--第八种Properties注入-->
           <property name="info">
               <props>
                   <prop key="性别">男</prop>
                   <prop key="学号">456123789</prop>
                   <prop key="生日">20000727</prop>
               </props>
           </property>
   
       </bean>
   
   
   </beans>
   ```

4. 测试类

   ```java
   public class MyTest {
       public static void main(String[] args) {
           ApplicationContext Context = new ClassPathXmlApplicationContext("beans.xml");
           Student student = (Student)Context.getBean("student");
           System.out.println(student.toString());
           /*
           Student{
           name='朱飞龙',
           address=Address{address='广州'},
           books=[红楼梦, 西游记, 水浒传, 三国演义],
           hobbys=[听歌, 敲代码, 看电影],
           card={身份证=123456789, 银行卡=147258369},
           games=[LOL, COC, BOB],
           wife='null',
           info={生日=20000727,学号=456123789, 性别=男}
           }
   
            */
       }
   }
   ```

#### 6.3、拓展方式注入

c和p命名空间

#### 6.4、bean的作用域

| Scope                                                        | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [singleton](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-singleton) | (Default) Scopes a single bean definition to a single object instance for each Spring IoC container. |
| [prototype](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-prototype) | Scopes a single bean definition to any number of object instances. |
| [request](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-request) | Scopes a single bean definition to the lifecycle of a single HTTP request. That is, each HTTP request has its own instance of a bean created off the back of a single bean definition. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [session](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-session) | Scopes a single bean definition to the lifecycle of an HTTP `Session`. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [application](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes-application) | Scopes a single bean definition to the lifecycle of a `ServletContext`. Only valid in the context of a web-aware Spring `ApplicationContext`. |
| [websocket](https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#websocket-stomp-websocket-scope) | Scopes a single bean definition to the lifecycle of a `WebSocket`. Only valid in the context of a web-aware Spring `ApplicationContext`. |

```xml
<!--单例模式：只产生一个相同的对象-->
<bean id="address" class="com.ferron.pojo.Address" scope="singleton">
    <property name="address" value="广州"/>
</bean>
```

```xml
<!--原型模式：每次get都会产生一个新对象-->
<bean id="address" class="com.ferron.pojo.Address" scope="prototype">
    <property name="address" value="广州"/>
</bean>
```

## 7、Bean的自动装配

- 自动装配是spring满足bean依赖一种方式
- spring会在上下文中自动寻找，并自动给bean装配属性



在spring中有三种装配的方式

1. 在xml中显示的配置
2. 在java中显示的配置
3. 隐式地自动装配bean

#### 7.1、测试

环境搭建：一个人有两个宠物

#### 7.2、ByName自动装配

```
<bean id="cat" class="com.ferron.pojo.Cat"/>
<bean id="dog" class="com.ferron.pojo.Dog"/>

<!--byName：会自动在容器上下文中查找，和自己对象set方法后面的值对应的bean id！-->
<bean id="people" class="com.ferron.pojo.People" autowire="byName">
    <property name="name" value="朱飞龙"/>
</bean>
```

#### 7.3、ByType自动装配

```
<bean id="cat" class="com.ferron.pojo.Cat"/>
<bean id="dog222" class="com.ferron.pojo.Dog"/>

<!--byType：会自动在容器上下文中查找，和自己对象属性类型相同的bean！-->
<bean id="people" class="com.ferron.pojo.People" autowire="byType">
    <property name="name" value="朱飞龙"/>
</bean>
```

小结：

- byName的时候，需要保证所有bean的id唯一，并且这个bean需要和自动注入的属性的set方法的值一致
- byType的时候，需要保证所有bean的class唯一，并且这个bean需要和自动注入的属性的类型一致

#### 7.4、使用注解实现自动装配

要使用前须知：

1. 导入约束

2. 配置注解的支持

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           https://www.springframework.org/schema/context/spring-context.xsd">
   <!--加入注解依赖-->
       <context:annotation-config/>
   <!--开启注解支持-->
   ```



@Autowired

直接在属性上使用即可，也可以在set方式上使用！

使用Autowired我们可以不用编写set方法了，前提是你这个自动装配的属性在IOC(Spring)容器中存在，且符合ByName

```
使用@Nullable可以使name为空时不报错
```

**如果@Autowired自动装配的环境比较复杂，自动装配无法通过一个注解完成的时候，我们可以使用@Qualifier(value=)配合其使用，指定一个唯一的bean对象注入**

## 8、使用注解开发

Spring4之后，要使用注解开发，必须保证导入了aop的包

使用注解需要导入context约束，增加注解的支持

指定扫描注解的包，该包下的注解会生效【重点】

```xml
<!--指定要扫描的包，这个包下的注解就会生效-->
<context:component-scan base-package="com.ferron"/>
```

1. bean
2. 属性如何注入
3. 衍生的注解
4. 自动装配
5. 作用域

#### 注解大全

- @Autowired：
直接在属性和set方法上使用即可
使用Autowired我们可以不用编写set方法，前提是你这个自动装配的属性在IOC(spring)容器中存在，且符合命名byname
当required=false时，注入空值时也不会报错
- @Qualifier：
如果@Autowired自动装配的环境比较复杂，自动装配无法通过一个注解@Autowired完成的时候
我们可以使用@Qualifier(value="xxx")去配合@Autowired使用，制定一个唯一的bean对象注入
- @Resource：
也是一个注解，先通过name找再通过type找，都找不到才报错，也可以使用@Resource(name="xxx")

- @Nullable：
字段标记了这个注解，说明这个字段可以为null

- @Component(@Repository,@Service,@Controller)：
组件，放在类上，说明这个类被spring管理了，就是bean

- @Value：
给属性赋值，放在属性或者set方法上

- @Scope：
配置为单例模式或者原型模式

小结：xml和注解

- xml更加万能，适用于任何场合！维护简单方便
- 注解 不是自己类用不了，维护相对复杂
- xml和注解最佳实现：xml配置bean，注解注入属性。

## 9、使用java的方式配置Spring

使用java的方式配置spring：完全不使用spring的xml配置，全权交给java来做
这种纯java的配置方式，在springboot中随处可见

pojo类：

```java
//这里这个注解的意思，就是说明这个类被spring接管了，注册到了容器中
@Component
public class User {

    private String name;

    public String getName() {
        return name;
    }

    @Value("朱飞龙") //属性注入值
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```

配置类：

```java
//这个也会被spring容器托管，注册到容器中，因为他本来就是一个@Component
//@Configuration代表这是一个配置类，就和我们之前看的bean.xml是一样的
@Configuration
@ComponentScan("com.ferron.pojo") //扫描
@Import(FerronConfig2.class) //引入FerronConfig2，变成一个类
public class FerronConfig {

    //注册一个bean，就相当于我们之前写的一个bean
    //这个方法的名字，相当于bean标签中的id
    //这个方法的返回值，相当于bean标签中的class
    @Bean
    public User getUser(){
        return new User(); //就是返回要注入bean的对象
    }
    
}
```

测试类：

```java
public class MyTest {
    public static void main(String[] args) {
        //未使用xml配置文件去配置
        //如果完全适应了配置类去做，只能通过AnnotationConfig上下文来获取容器，通过配置类的class对象加载
        ApplicationContext context = new AnnotationConfigApplicationContext(FerronConfig.class);
        User getUser = (User)context.getBean("getUser");
        System.out.println(getUser.getName());

    }
}
```

## 10、代理模式

为什么要学习代理模式？因为这就是Spring AOP的底层 ！【Spring AOP 和Spring MVC】

- 静态代理
- 动态代理

#### 10.1、静态代理

角色分析：

- 抽象角色：一般会使用接口或者抽象类来解决
- 真实角色：被代理的角色
- 代理角色：代理真实角色，代理真实角色后，我们一般会做一些附属操作
- 客户：访问代理对象的人

代码步骤：

1. 接口

   ```java
   //租房
   public interface Rent {
   
       public void rent();
   }
   ```

2. 真实角色

   ```java
   //房东
   public class Host implements Rent{
   
       @Override
       public void rent() {
           System.out.println("房东要出租房子！");
       }
   }
   ```

3. 代理角色

   ```java
   //代理人
   public class Proxy implements Rent{
       //取入房东
       private Host host;
   
       public Proxy(){
   
       }
   
       public Proxy(Host host) {
           this.host = host;
       }
   
   
       @Override
       public void rent() {
           seeHouse();
           host.rent();
           hetong();
           fare();
   
       }
   
       //看房
       public void seeHouse(){
           System.out.println("中介带你看房");
       }
   
       //收中介费
       public void fare(){
           System.out.println("收中介费");
       }
   
       //签合同
       public void hetong(){
           System.out.println("签合同");
       }
   
   
   }
   ```

4. 客户端访问代理角色

   ```java
   public class Client {
       public static void main(String[] args) {
   
           //原本是这样实现，但现实中很难直接找到房东，因此要找中介
           //Host host = new Host();
           //host.rent();
   
           //存在一个房东
           Host host = new Host();
           //代理人,中介帮房东租房子，代理角色一般会有一些附属操作
           Proxy proxy = new Proxy(host);
           //你不用面对房东，直接找中介租房
           proxy.rent();
       }
   }
   ```

代理模式的好处：

1. 可以使真实角色的操作更加纯粹，不用去关注一些公共的业务
2. 公共业务就交给了代理角色，实现了业务的分工
3. 公共业务发生扩展的时候，方便集中管理

缺点：

1. 一个真实角色就会产生一个代理角色：代码量会翻倍，开发效率会变低

#### 10.2、加深理解

![img](file:///D:\Application\Typora\Typora\image{Z1X3E9)ZTCTSI51W6T@647W.png)

#### 10.3、动态代理

- 动态代理和静态代理角色一样
- 动态代理的代理类是动态生成的，不是我们直接写好的
- 动态代理分为两大类：基于接口的动态代理，基于类的动态代理
  - 基于接口---JDK动态代理
  - 基于类：cglib
  - java字节码实现：javasist

需要了解两个类：Proxy：代理，InvokationHandler：调用处理

万能动态代理类：

```java
//用这个类自动生成代理类
public class ProxyInvocationHandler implements InvocationHandler {

    //被代理的接口
    private Object target;

    public void setTarget(Object target) {
        this.target = target;
    }

    //生成得到代理对象
    public Object getProxy(){
        return Proxy.newProxyInstance(this.getClass().getClassLoader(), target.getClass().getInterfaces(),this);

    }

    //处理代理实例，并返回结果(生成代理类)
    @Override
    public Object invoke(Object o, Method method, Object[] args) throws Throwable {
        //反射是万能的！通过method的反射对象得到执行的方法名字
        log(method.getName());
        //动态代理的本质就是使用反射机制实现！
        Object result = method.invoke(target, args);
        return result;
    }

    public void log(String msg){
        System.out.println("执行了"+msg+"方法");
    }
    

}
```

测试：

```java
public class Client {
    public static void main(String[] args) {
        //真实角色
        UserServiceImpl userService = new UserServiceImpl();
        //代理角色：原来不存在，自己动态创建
        ProxyInvocationHandler pih = new ProxyInvocationHandler();
        //代理真实类(接口)
        pih.setTarget(userService);//设置要代理的对象
        //动态生成代理类
        UserService proxy = (UserService) pih.getProxy();
        //实现操作
        proxy.delete();

    }
}
```

好处：

- 一个动态代理类代理的是一个接口，一般就是对应的一类业务
- 一个动态代理类可以代理多个类，只要是实现了同一个接口即可

## 11、AOP

#### 11.1、什么是AOP

- 在软件业，AOP为Aspect Oriented Programming的缩写，意为：面向切面编程。
- 通过[预编译](https://baike.baidu.com/item/预编译/3191547)方式和运行期间动态代理实现程序功能的统一维护的一种技术。
- AOP是[OOP](https://baike.baidu.com/item/OOP)的延续，是软件开发中的一个热点，也是[Spring](https://baike.baidu.com/item/Spring)框架中的一个重要内容，是[函数式编程](https://baike.baidu.com/item/函数式编程/4035031)的一种衍生范型。
- 利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的[耦合度](https://baike.baidu.com/item/耦合度/2603938)降低，提高程序的可重用性，同时提高了开发的效率。

#### 11.2、使用Spring实现AOP

方式一：使用Spring的API接口

前置和后置日志类

```java
//接口：前置执行接口
public class Log implements MethodBeforeAdvice {

    //method:要执行的目标对象的方法
    //objects:参数
    //target:目标对象
    @Override
    public void before(Method method, Object[] objects, Object target) throws Throwable {
        System.out.println(target.getClass().getName()+"的"+method.getName()+"被执行了");
    }

}

//接口：后置执行接口
public class AfterLog implements AfterReturningAdvice {

    //returnValue:返回值
    @Override
    public void afterReturning(Object returnValue, Method method, Object[] objects, Object traget) throws Throwable {
        System.out.println("执行了"+method.getName()+"方法,返回结果为"+returnValue);
    }
}
```

beans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">
    <!--加上aop约束-->

    <!--注册bean-->
    <bean id="userService" class="com.ferron.service.UserServiceImpl"/>
    <bean id="log" class="com.ferron.log.Log"/>
    <bean id="afterLog" class="com.ferron.log.AfterLog"/>

    <!--方式一：使用原生spring api接口    -->
    <!--配置aop:需要导入aop的约束 -->
    <aop:config>
        <!--切入点:需要在哪里切入 expression:表达式 execution(要执行的位置! 最前面的*表示返回的类型(*任何类型)，.*表示该这个类的所有方法，(..)表示这些方法的所有参数)-->
        <aop:pointcut id="pointcut" expression="execution(* com.ferron.service.UserServiceImpl.*(..))"/>

        <!--执行环绕增加 advice-ref为beanid，pointcut-ref为切入点id-->
        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="afterLog" pointcut-ref="pointcut"/>

    </aop:config>
</beans>
```

测试类：

```java
public class MyTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        //动态代理的是接口,不写UserServiceImpl，而写UserService
        UserService userService = (UserService) context.getBean("userService");
        userService.add();
    }
}
```

方式二：自定义类实现

beans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">
    <!--加上aop约束-->

    <!--注册bean-->
    <bean id="userService" class="com.ferron.service.UserServiceImpl"/>
    <bean id="log" class="com.ferron.log.Log"/>
    <bean id="afterLog" class="com.ferron.log.AfterLog"/>
    
    <!--方式二：自定义类 -->
    <bean id="diy" class="com.ferron.diy.DiyPointCut"/>

    <aop:config>
        <!--自定义切面，ref 要引用的类        -->
        <aop:aspect ref="diy">
            <!--切入点-->
            <aop:pointcut id="point" expression="execution(* com.ferron.service.UserServiceImpl.*(..))"/>
            <!--通知-->
            <aop:before method="before" pointcut-ref="point"/>
            <aop:after method="after" pointcut-ref="point"/>
        </aop:aspect>
    </aop:config>
</beans>
```

方式三：使用注解实现

beans.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">
    <!--加上aop约束-->

    <!--注册bean-->
    <bean id="userService" class="com.ferron.service.UserServiceImpl"/>
    <bean id="log" class="com.ferron.log.Log"/>
    <bean id="afterLog" class="com.ferron.log.AfterLog"/>
    
    <!--方式三-->
    <bean id="annotationPointCut" class="com.ferron.diy.AnnotationPointCut"/>
    <!--开启注解支持  JDK(默认)  cglib-->
    <aop:aspectj-autoproxy/>

</beans>
```

注解实现类：

```java

@Aspect //标注这个类是一个切面
public class AnnotationPointCut {

    @Before("execution(* com.ferron.service.UserServiceImpl.*(..))")
    public void before(){
        System.out.println("=====方法执行前=====");
    }

    @After("execution(* com.ferron.service.UserServiceImpl.*(..))")
    public void after(){
        System.out.println("=====方法执行后=====");
    }

    //在环绕增强中，我们可以给定一个参数，代表我们要获取处理切入的点
    @Around("execution(* com.ferron.service.UserServiceImpl.*(..))")
    public void around(ProceedingJoinPoint jp) throws Throwable {
        System.out.println("环绕前");

//        Signature signature = jp.getSignature(); //获得签名
//        System.out.println("signature:"+signature);

        //执行方法
        Object proceed = jp.proceed();

        System.out.println("环绕后");

    }


}
```

## 12、整合Mybatis

步骤：

1. 导入相关jar包

   - junit

   - mybatis

   - mysql

   - springmvc

   - aop织入

   - spring-jdbc【new】

   - mybatis-spring【new】

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
     <!-- Spring连接数据库 -->
     <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-jdbc</artifactId>
         <version>5.2.12.RELEASE</version>
     </dependency>
     <!-- Spring依赖 -->
     <dependency>
         <groupId>org.springframework</groupId>
         <artifactId>spring-webmvc</artifactId>
         <version>5.2.9.RELEASE</version>
     </dependency>
     <!-- AOP附属依赖包 -->
     <dependency>
         <groupId>org.aspectj</groupId>
         <artifactId>aspectjweaver</artifactId>
         <version>1.9.4</version>
     </dependency>
     <!-- 整合mybatis和spring -->
     <dependency>
         <groupId>org.mybatis</groupId>
         <artifactId>mybatis-spring</artifactId>
         <version>2.0.6</version>
     </dependency>
     ```

2. 编写配置文件

3. 测试

#### 12.1、回忆mybatis

1. 编写实体类
2. 编写核心配置文件
3. 编写接口
4. 编写mapper.xml
5. 测试

#### 12.2、mybatis-spring

1. 编写数据源配置

   ```xml
   <!--DataSource：使用Spring的数据源替换Mybatis的配置 c3p0 dbcp druid
       这里使用spring提供的jdbc：org.springframework.jdbc.datasource-->
   <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
       <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
       <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
       <property name="username" value="root"/>
       <property name="password" value="sanfashi"/>
   </bean>
   ```

2. sqlSessionFacory

   ```xml
   <!--sqlSessionFactory-->
   <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
       <property name="dataSource" ref="dataSource"/>
       <!--绑定mybatis配置文件-->
       <property name="configLocation" value="classpath:mybatis-config.xml"/>
       <!--注册mapper-->
       <property name="mapperLocations" value="classpath:com/ferron/mapper/*.xml"/>
   </bean>
   ```

3. sqlSessionTemplate

   ```xml
   <!--SqlSessionTemplate：就是我们使用的sqlSession-->
   <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
       <!--只能使用构造器注入sqlSessionFacroty，因为它没有set方法-->
       <constructor-arg index="0" ref="sqlSessionFactory"/>
   </bean>
   ```

4. 需要给接口加实现类【】

   ```java
   package com.ferron.mapper;
   
   import com.ferron.pojo.User;
   import org.mybatis.spring.SqlSessionTemplate;
   
   import java.util.List;
   
   public class UserMapperImpl implements UserMapper {
   
       //我们的所有操作，都使用sqlSession来执行，在原来，现在都使用SqlSessionTemplate；
       private SqlSessionTemplate sqlSession;
   
       public void setSqlSession(SqlSessionTemplate sqlSession) {
           this.sqlSession = sqlSession;
       }
   
       @Override
       public List<User> selectUser() {
           UserMapper mapper = sqlSession.getMapper(UserMapper.class);
           return mapper.selectUser();
       }
   }
   ```

5. 将自己写的实现类，注入到spring中

   ```xml
   <bean id="userMapper" class="com.ferron.mapper.UserMapperImpl">
       <property name="sqlSession" ref="sqlSession"/>
   </bean>
   ```

6. 测试

   ```java
   @Test
   public void test() {
       ApplicationContext context = new ClassPathXmlApplicationContext("spring-dao.xml");
       UserMapper userMapper = context.getBean("userMapper", UserMapper.class);
       for (User user : userMapper.selectUser()) {
           System.out.println(user);
       }
   }
   ```

#### 12.3、整合

1. 写好pojo类，Mapper接口，Mapper.xml
2. 写好mybatis-config.xml
3. 编写spring-dao.xml【12.2】
4. 写实现类注入
5. 测试

## 13、声明式事务

#### 13.1、事务

- 把一组事务当成一个业务来做要么都成功，要么都失败
- 事务在项目开发中十分重要，涉及到数据一致性的问题



事务ACID原则

- 原子性
- 一致性
- 隔离性
- 持久性

#### 13.2、spring中的事务管理

- 声明式事务：AOP
- 编程式事务

步骤：

1. 编写applicationContext.xml

   ```xml
   <!--配置声明式事务-->
   <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
       <property name="dataSource" ref="dataSource"/>
   </bean>
   
   <!--结合AOP实现事务的织入-->
   <!--配置事务通知-->
   <tx:advice id="txAdvice" transaction-manager="transactionManager">
       <!--给哪些方法配置事务-->
       <!--配置事务的传播特性：new-->
       <tx:attributes>
           <tx:method name="add" propagation="REQUIRED"/>
           <tx:method name="delete"/>
           <tx:method name="update"/>
           <tx:method name="query" read-only="true"/>
           <tx:method name="*"/>
       </tx:attributes>
   </tx:advice>
   
   <!--配置事务切入-->
   <aop:config>
       <aop:pointcut id="txPonitCut" expression="execution(* com.ferron.mapper.*.*(..))"/>
       <aop:advisor advice-ref="txAdvice" pointcut-ref="txPonitCut"/>
   </aop:config>
   ```

2. 测试

为什么需要事务？

- 如果不配置事务，可能存在数据提交不一致的情况
- 如果步骤spring中配置声明式事务，我们就需要在代码中手动配置事务
- 事务中项目中十分重要！

## X、核心代码

```xml
<!-- Srping依赖 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.9.RELEASE</version>
</dependency>
<!-- AOP附属依赖包 -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
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
<!-- Spring连接数据库 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.12.RELEASE</version>
</dependency>
<!-- Spring依赖 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.2.9.RELEASE</version>
</dependency>
<!-- AOP附属依赖包 -->
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.4</version>
</dependency>
<!-- 整合mybatis和spring -->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>2.0.6</version>
</dependency>
```



ad