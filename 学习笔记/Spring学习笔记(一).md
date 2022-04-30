---
title: Spring学习笔记(一)
tags: Spring
categories: 学习笔记
description: Spring第一部分学习笔记，主要介绍了Spirng的概念，IOC理论和DI、以及创建了第一个Spirng程序
---


## 1、Spring

### 1.1、简介

给软件行业带来了春天

### 1.2、优点

- spring是一个开源的免费的框架（容器）
- spring是一个轻量级的、非入侵式的框架
- 控制反转（IOC），面向切面编程（AOP）
- 支持事务的处理，对框架整合的支持

### 1.3、组成

![img](http://fl.ljuuu.com/\`}T6QIHKB79MHE8$QUYCWA.png)





### 1.4、拓展

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

### 4.1、使用无参构造创建对象

```xml
<bean id="user" class="com.ferron.pojo.User">
    <property name="name" value="朱小龙"/>
</bean>
```

### 4.2、使用有参构造创建对象

#### 4.2.1、使用索引传参

```xml
<bean id="user" class="com.ferron.pojo.User">
    <constructor-arg index="0" value="朱飞龙"/>
</bean>
```

#### 4.2.2、使用类型传参(不建议使用)

```xml
<bean id="user" class="com.ferron.pojo.User">
    <constructor-arg type="java.lang.String" value="朱飞龙"/>
</bean>
```

#### 4.2.3、使用参数名传参

```xml
<bean id="user" class="com.ferron.pojo.User">
    <constructor-arg name="name" value="朱飞龙"/>
</bean>
```

总结：在配置文件加载的时候，容器中管理的对象就已经初始化了！

## 4、Spring配置

### 4.1、别名

```xml
<!--使用别名-->
<alias name="user" alias="userAlias"/>
```

### 4.2、Bean配置

```xml
<!--bean里面的配置：
id:bean的唯一标识符，也就是相当与我们的对象名
class:bean对象所对应的全限定名：包名+类型
name:也是别名,name更高级，可以取多个别名
-->
<bean id="userT" class="com.ferron.pojo.User">
</bean>
```

### 4.3、Import

```xml
<!--用于团队开发，import别人的beans，合成applicationContext.xml-->
```

## 5、依赖注入

### 5.1、构造器注入

参见第四节。

### 5.2、Set方式注入【重点】

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

### 5.3、拓展方式注入

c和p命名空间

### 5.4、bean的作用域

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
