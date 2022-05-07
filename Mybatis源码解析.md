## 0、前言

- 笔者从现在开始准备开始更新部分java开发相关框架的源码分析学习，就先从myabtis开始吧。文章内容基于源码和官方文档。

- mybatis中文官方文档：https://mybatis.org/mybatis-3/zh/

## 1、从一个简单的例子开始

从官网的例子开始：从xml中构建SqlSessionFactory

```java
//resource路径
String resource = "org/mybatis/example/mybatis-config.xml";
//获得resource的文件流
InputStream inputStream = Resources.getResourceAsStream(resource);
//使用resource的文件流创建SqlSessionFactory
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
```

第三行代码最终走的是下面的方法

```java
//前文传入的inputStream，在这里已经封装成了mybatis中的Configuration config对象
public SqlSessionFactory build(Configuration config) {
    //返回的默认DefaultSqlSessionFactory，是SqlSessionFactory接口的一个实现类
    return new DefaultSqlSessionFactory(config);
}
```

我们可以简单看一下mybatis中的Configuration类的几个属性。

```java
//数据库的配置环境类
protected Environment environment;
//执行类型
protected ExecutorType defaultExecutorType = ExecutorType.SIMPLE;
//数据库id
protected String databaseId;
//重要：MapperRegistry
protected final MapperRegistry mapperRegistry = new MapperRegistry(this);
```

到此为止官方的创建SqlSessionFactory的三行代码我们解析完毕。我们可以知道：当调用完SqlSessionFactoryBuilder()的build方法后，程序会将xml文件封装成一个Configuration，然后返回一个SqlSessionFactory的默认实现类实例。

紧接着官方给出了下列的代码：

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  BlogMapper mapper = session.getMapper(BlogMapper.class);
  Blog blog = mapper.selectBlog(101);
}
```

我们首先了解一下三个session究竟是什么，再继续我们的源码学习。

- SqlSessionFactoryBuilder，说白了就是一个工具人，用于创建SqlSessionFactory，一旦SqlSessionFactory创建了，其就没有存在的意义了
- SqlSessionFactory：Session的工厂，可以类比成数据库的连接池，保存着一堆的Session。SqlSessionFactory的作用域应该是整个应用的周期，应用运行过程中其应该一直存活
- Session：可以类比成数据库的一次连接，相当于数据库的一次请求，用完需要关闭。

介绍完这些，我们就很清楚try括号里面的代码实际就是从SqlSessionFactory拿出一个Session。我们扒一下DefaultSqlSessionFactory实现类的实现代码。

```java
@Override
public SqlSession openSession() {
    return openSessionFromDataSource(configuration.getDefaultExecutorType(), null, false);
}

private SqlSession openSessionFromDataSource(ExecutorType execType, TransactionIsolationLevel level, boolean autoCommit) {
    Transaction tx = null;
    try {
        final Environment environment = configuration.getEnvironment();
        final TransactionFactory transactionFactory = getTransactionFactoryFromEnvironment(environment);
        tx = transactionFactory.newTransaction(environment.getDataSource(), level, autoCommit);
        final Executor executor = configuration.newExecutor(tx, execType);
        return new DefaultSqlSession(configuration, executor, autoCommit);
    } catch (Exception e) {
        closeTransaction(tx); // may have fetched a connection so lets call close()
        throw ExceptionFactory.wrapException("Error opening session.  Cause: " + e, e);
    } finally {
        ErrorContext.instance().reset();
    }
}
```

可以看到openSession()的最终结果就是将一堆乱七八遭的东西封装成了mybatis包的DefaultSqlSession类实例，并且返回。

我们简单扒一扒DefaultSqlSession类

```java
public class DefaultSqlSession implements SqlSession {
    public DefaultSqlSession(Configuration configuration, Executor executor, boolean autoCommit) {
        this.configuration = configuration;
        this.executor = executor;
        this.dirty = false;
        this.autoCommit = autoCommit;
    }  
}
```

其实看到第一行我们就知道了，mybatis中的DefaultSqlSession类是sql包中SqlSession接口的实现类！也就是实际上最终返回的其实是SqlSession接口的实现类。

接着往下走

```java
//DefaultSqlSession的getMapper()方法
@Override
public <T> T getMapper(Class<T> type) {
    return configuration.getMapper(type, this);
}

//Configuration的getMapper()方法
public <T> T getMapper(Class<T> type, SqlSession sqlSession) {
    return mapperRegistry.getMapper(type, sqlSession);
}

//MapperRegistry的getMapper()方法
public <T> T getMapper(Class<T> type, SqlSession sqlSession) {
    final MapperProxyFactory<T> mapperProxyFactory = (MapperProxyFactory<T>) knownMappers.get(type);
    if (mapperProxyFactory == null) {
        throw new BindingException("Type " + type + " is not known to the MapperRegistry.");
    }
    try {
        return mapperProxyFactory.newInstance(sqlSession);
    } catch (Exception e) {
        throw new BindingException("Error getting mapper instance. Cause: " + e, e);
    }
}
```

MapperRegistry的getMapper()方法大概就是接受一个Class的对象，然后在根据配置文件加载的knownMappers中去寻找存在的MapperProxyFactory，如果不存在，可能是未在mybatis-config.xml中配置相应的Mapper。

最终mapperProxyFactory.newInstance(sqlSession)执行过程如下。

```java

public T newInstance(SqlSession sqlSession) {
    final MapperProxy<T> mapperProxy = new MapperProxy<>(sqlSession, mapperInterface, methodCache);
    return newInstance(mapperProxy);
}

public class MapperProxy<T> implements InvocationHandler, Serializable {
    
}
```

可以看到，mybatis创建mapper对象的是通过MapperProxy这个对象实现的，MapperProxy是究竟是什么呢？往下看MapperProxy实现了InvocationHandler这个接口。懂了吧？本质就是动态代理！通过动态代理获得程序运行时传入的class，再创建对象的Mapper对象返回。

接下来生成的Mapper对象之后，调用接口里面的方法，就得执行对应的sql方法了。

## 2、mybatis的XML配置文件

#### 2.1、属性

还是从官方的文档入手，首先从属性为例子开始把。

下列代码是文档中为我们提供的一个设置某个属性的方法：导入`config.properties`文件后，既可以直接使用文件中已经命名好的属性值，也可以直接对value标签后写死属性值，各有各的优点。

```xml
<properties resource="org/mybatis/example/config.properties">
  <property name="username" value="dev_user"/>
  <property name="password" value="F2Fa3!33TYyg"/>
</properties>

<dataSource type="POOLED">
  <property name="driver" value="${driver}"/>
  <property name="url" value="${url}"/>
  <property name="username" value="${username}"/>
  <property name="password" value="${password}"/>
</dataSource>
```

当然，你也可以使用java代码在程序运行时写入配置。

```java
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, props);
// ... 或者 ...
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment, props);
```

从SqlSessionFactoryBuilder这个类中我们可以看到许多build的重载方法，不过核心就只有两个，就是接受Reader和接受InputStream两种build方法。

- Reader：用于字符流读取的公共父类
- InputStream：用于字节输入流的公共父类

```java
public SqlSessionFactory build(Reader reader) {
    return build(reader, null, null);
}

public SqlSessionFactory build(Reader reader, String environment) {
    return build(reader, environment, null);
}

public SqlSessionFactory build(Reader reader, Properties properties) {
    return build(reader, null, properties);
}

public SqlSessionFactory build(Reader reader, String environment, Properties properties) {
}

public SqlSessionFactory build(InputStream inputStream) {
    return build(inputStream, null, null);
}

public SqlSessionFactory build(InputStream inputStream, String environment) {
    return build(inputStream, environment, null);
}

public SqlSessionFactory build(InputStream inputStream, Properties properties) {
    return build(inputStream, null, properties);
}

public SqlSessionFactory build(InputStream inputStream, String environment, Properties properties) {
}
```

而按照官方文档的说法，以上三种属性加载的优先级为：

- 首先读取在 properties 元素体内指定的属性。
- 然后根据 properties 元素中的 resource 属性读取类路径下属性文件，或根据url 属性指定的路径读取属性文件，并覆盖之前读取过的同名属性。
- 最后读取作为方法参数传递的属性，并覆盖之前读取过的同名属性。

以上我们讲解了属性的配置，至于设置、类别别名我们便跳过。

#### 2.2、类型处理器

先了解一下resultMap



