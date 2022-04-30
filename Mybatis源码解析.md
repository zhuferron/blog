## 0、前置资料

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

















