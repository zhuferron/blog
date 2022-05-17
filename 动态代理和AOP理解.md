## 1、代理模式理解

我们传统的业务开发中会编写以下的代码。

UserService接口

```java
public interface UserService {

    public void select();

    public void add();

    public void delete();

}
```

UserServiceImpl实现类

```java
public class UserServiceImpl implements UserService {

    @Override
    public void select() {
        System.out.println("处理查询业务逻辑");
    }

    @Override
    public void add() {
        System.out.println("处理新增业务逻辑");
    }

    @Override
    public void delete() {
        System.out.println("处理删除业务逻辑");
    }
}
```

测试，得到输出结果：处理新增业务逻辑

```java
UserService userService = new UserServiceImpl();
userService.add();
```

然而现在，我们有一个需求，我们需要在执行业务方法之前或之后输出一些日志信息，这时候我们应该怎么办呢？很简单，直接UserServiceImpl的具体实现代码即可。我们只需要在处理业务逻辑的代码前后加入对应的输出日志代码即可。

吗？

答案明显是错误的。因为本文的例子中只有三个实现方法，真实项目中可能有许多的业务实现代码，我们统一的去改会极大地降低开发效率，而且也会造成代码的高耦合。解决这个问题的办法就是使用代理的设计模式。编写一个代理类，代理实现类的所有方法，然后在代理类中编写对应的日志输出代码。

代理类：

```java
public class UserServiceProxy implements InvocationHandler {

    private UserService userService;

    public UserServiceProxy(UserService userService) {
        this.userService = userService;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("执行业务逻辑前日志输出");
        method.invoke(userService, args);
        System.out.println("执行业务逻辑后日志输出");
        return userService;
    }
}
```

测试：

```java
//创建被代理对象
UserService userService = new UserServiceImpl();
//得到代理类
InvocationHandler userServiceProxy = new UserServiceProxy(userService);
//静态代理生成新对象
ClassLoader loader = userServiceProxy.getClass().getClassLoader();
Class<?>[] interfaces = userService.getClass().getInterfaces();
UserService proxyInstance = (UserService) Proxy.newProxyInstance(loader, interfaces, userServiceProxy);
proxyInstance.add();
```

首先创建被代理对象然后得到代理类这个很好理解。得到代理类之后需要使用Proxy类来帮我们创建一个被代理后的实例对象，因为业务代码是由实例对象操作而不是由代理类操作，因此我们光得到代理类是不够的，需要进而得到被代理的实例对象。

得到实例对象的方法是`Proxy.newProxyInstance()`，接收三个参数

- ClassLoader loader：loader the class loader to define the proxy class，也就是代理类的类加载器，我们可以用`userServiceProxy.getClass().getClassLoader()`得到。
- Class<?>[] interfaces：interfaces the list of interfaces for the proxy class to implement，代理类需要实现的接口，也就是实例对象接口，我们可以用`userService.getClass().getInterfaces()`得到。
- InvocationHandler h：h the invocation handler to dispatch method invocations to，代理类。

得到被代理后的实例对象后，我们调用add方法，输出结果为

```
执行业务逻辑前日志输出
处理新增业务逻辑
执行业务逻辑后日志输出
```

## 2、动态代理

以上的内容是一个静态代理的例子，然后上面的例子存在一个很严重的问题。就是我们需要对于每个业务接口都编写一个代理类，对于UserService接口，我们需要编写一个具体的UserServiceProxy代理类。

于是我们就会想？能不能写一个万能的代理实现类呢？答案当然是可以的，其实只需要把上面的那个例子稍微改一下。这种方法就叫做动态代理。

万能动态代理类：

```java
public class ProxyInvocationHandler implements InvocationHandler {

    //被代理对象或接口
    private Object object;

    public void setObject(Object object) {
        this.object = object;
    }

    public Object getObject() {

        ClassLoader loader = this.getClass().getClassLoader();
        Class<?>[] interfaces = object.getClass().getInterfaces();
        Object o = Proxy.newProxyInstance(loader, interfaces, this);
        return o;

    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("执行业务逻辑前日志输出");
        Object proxyClass = method.invoke(object, args);
        System.out.println("执行业务逻辑后日志输出");
        return proxyClass;
    }
}
```

测试

```java
//创建被代理对象
UserService userService = new UserServiceImpl();
//万能代理类
ProxyInvocationHandler invocationHandler = new ProxyInvocationHandler();
invocationHandler.setObject(userService);
UserService userService1 = (UserService) invocationHandler.getObject();
userService1.add();
```

输出

```
执行业务逻辑前日志输出
处理新增业务逻辑
执行业务逻辑后日志输出
```



