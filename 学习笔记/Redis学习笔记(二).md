---
title: Redis学习笔记(二)
tags: Redis
categories: 学习笔记
description: Redis第二部分学习笔记，主要介绍了Redis中五种基本数据类型和三种特殊数据类型的使用、以及SpringBoot中如何集成Redis等内容
---

## 4、数据类型

### 4.1、分类

五种基本数据类型：

- String
- Hash
- List
- Set
- Zset

三种特殊类型：

- Geospatal 地理位置
- Bitmap
- Hyperloglog 计数

### 4.2、Redis-key

- `exists name `：判断name属性是否存在，1位存在，0位不存在

- `set name 1`：设置name为1

- `get name` ：取出name

- `keys *`：查看所有的keys

- `type name`：查看name类型

### 4.3、String

基础命令：

- `append key "hello" `：在key后面追加一个hello，若key不存在则相当于set key
- `strlen key`：返回key的长度
- `incr key ` ：每次key的值自动加1
- `decr key`： 每次key的值自动减1
- `incrby key 10`：每次key的值自动加10
- `decrby key 10`：每次key的值自动减10
- `getrange key 0 3`：得到key的0-3字符
- `setrange key 1 xx`：替换key中从指定位置开始的字符串
- `setex(set with expire) key 30 "hello" `：设置key为hello，过期时间为30s
- `setnx(set if not exists) key "hello"`：如果存在，设key为hello
- `mset k1 v1 k2 v2 k3 v3`：设置k1为v1，k2为v2，k3为v3
- `mget k1 k2 k3`：取出k1，k2，k3

高阶命令：

- **`set user:1 {name:ferron,age:3}`：设置1号user，name为ferron，age为3**
- **`set user:1:name ferron user:2:age 2`：同上**
- `getset key "hello"`：先得到key的值，然后再将key设为hello，key不存在为nil

使用场景：value除了是我们的字符串还可以是我们的数字

- 计数器
- 统计多单位的数量 uid:001:follow 0
- 粉丝数
- 对象缓存存储！

### 4.4、List

基本的数据类型，列表。

在Redis里面，我们可以把list变成栈，队列，阻塞队列；Redis不区分大小写命令

基本命名：

- `lpush list one`：将“one”插入一个list的头部
- `lrange list 0 -1`：取出list中的全部元素
- `rpush list four`：将"four"插入一个list的尾部
- `lpop list`：将list的尾部第一个元素移出
- `rpop list`：将list的头部第一个元素移出
- `lindex list 1`：将list的索引为1的元素取出
- `llen list`：查看list的长度
- `lrem list 1 one `：移除一个one，可移除多个
- `ltrim list 1 2`：截取1和2元素，使list只剩下1和2两个元素

高阶命令：

- `rpoplpush list list2  `：将list的尾部元素移到list2的头部
- `lset list 0 item`：将list中指定下标的值替换成item，list不存在会报错
- `linsert list before/after "world" "other" `：在list中在world的前面/后面插入other

小结：

- 实际上是一个链表，before Node after，left，right都可以插值
- 如果key不存在，创建新的链表
- 如果key存在，新增内容
- 如果移除了所有值，空链表，表示不存在
- 在两边插入或改动值，效率最高，中间元素，相对来说效率较低

消息排队！消息队列(Lpush Rpop)，栈(Lpush Lpop)

### 4.5、Set

set是无序不重复集合

- `sadd myset "hello"`：将hello添加到myset中
- `smembers myset`：查看myset中元素
- `sismember myset world`：查看world是否在myset中
- `scard myset`：查看myset中的元素个数
- `srem myset "hello"`：移除myset中的hello元素
- `srandmember myset`：在myset中随机抽取一个元素
- `spop myset`：随机删除myset中的元素
- `smove myset myset2 "hello"`：将hello从myset移动到myset2中
- `sdiff myset myset2`：取myset和myset2的差集
- `sinter myset myset2`：取myset和myset2的交集
- `sunion myset myset2`：取myset和myset2的并集

### 4.6、Hash

Map集合：key-Map集合，本质和string没有太大区别

- `hset myhash name ferron`：将myhash中的name设为ferron
- `hget myhash name`：取出myhash中的name
- `hmset myhash name ferron age 1`：将myhash中的name设为ferron，age设为1
- `hmget myhash name age`：取出myhash中的name和age
- `hgetall myhash`：取出myhash中的所有键值对
- `hdel myhash name`：删除myhash指定的key，对应的value也没了
- `hlen myhash `：得到myhash中的键值对数量
- `hexists myhash name`：判断myhash中的name是否存在
- `hkeys myhash`：获得myhash中所有的keys
- `hvals myhash`：获得myhash中所有的values
- `hincrby myhash age 1`：将myhash中的age自增1

应用：

hash可以存一个user的数据，将user:1为名字，设置其key为name，age.......

hash更适合对象的存储，string更适合字符串的存储

### 4.7、Zset

有序集合，在set的基础上增加了一个值

- `zadd myzset 1 one`：在myzset中的插入one元素，其score为1
- `zrange myset 0 -1`：查看myzset中所有的元素
- `zrangebyscore  myzset -inf +inf`：给myzset从小到大排序输出
- `zrevrange  myzset 0 -1`：给myzset从大到小排序输出
- `zrem myzset one`：将myzset中的one移除
- `zcard myzset`：获取myzset中的元素个数
- `zcount myzset 1 2`：获取1-2之间的成员数量

应用：

班级成绩表，工资表，带权重的数据

### 4.8、Geospatial（Geo）

可以查询一些测试数据：https://jingweidu.bmcx.com/

- `geoadd china:city 116.40 39.90 beijing`：在china:city中添加北京的经纬度
- `geopos china:city beijing`：从china:city中获取指定的beijing的经纬度
- `geodist china:city beijing shanghai km `：返回北京和上海的直线距离
  - **m** 表示单位为米。
  - **km** 表示单位为千米。
  - **mi** 表示单位为英里。
  - **ft** 表示单位为英尺。
- `georadius china:city 110 30 1000 km withdist  `：返回经纬度为110和30，**半径**为1000km内的所有城市
- `georadius china:city 110 30 1000 km withcoord `：返回经纬度为110和30，**直线距离**为1000km内的所有城市
- `georadiusbymember china:city beijing 1000 km`：返回北京周围半径1000km的城市
- `geohash`：返回一个或多个元素的geohash值(将经纬度转换成字符串)

**注意：geo基于zset实现的，所有zset的命令也适用于geo，例如删除等等......**

### 4.9、hyperloglog(用作计数统计)

基数：两个集合中不重复的元素

A{1,3,5,7,8} B{1,3,5,7,8}

网页的UV：一个人访问一个网站多次，但是还是算作一个人

传统的方式：set保存用户的id，然后就可以统计set的元素数量作为标准判断

- `pfadd mykey a b c d e f g`：将abcdefg存入mykey
- `pdcount mykey`：计算mykey中的值
- `pdmerge mykey3 mykey1 mykey2`：将mykey1和mykey2合并成mykey3

优点：一个mykey最多只有12kb的内存，占用内存小

### 4.10、Bitmaps(位存储)

统计疫情感染人数：0 0 1 0 1

Bitmaps位图，也是一种数据结构，都是操作二进制位来进行计算，只有0和1两个状态

![image-20210301155405110](http://fl.ljuuu.com/image-20210301155405110.png)

将0-6表示一周，则表示每天的打卡情况

- `setbit mybit 0 1`：将mybit中的0设为1
- `getbit mybit 0`：取出mybit中的0
- `bitcount mybit`：统计mybit中1的个数

## 5、Redis深入

### 5.1、事务

MySQL：ACID

Redis事务本质：一组命令的集合，一个事务中的所有命令都会被序列化，在事务执行过程中，会按照顺序执行！

一次性，顺序性，排他性！

**Redis单条命令是保证原子性，但是事务不保证原子性**

Redis事务也没有隔离级别的概念，所有的命令在事务中，并没有直接背执行，只有发起执行命令的时候才会被执行，Exec

事务：

- 开启事务（multi）
- 命令入队（......）
- 执行事务（exec）/放弃事务（discard）

编译型异常：事务中所有的命令都不会被执行

运行时异常：事务中其他命令也可以正常执行

### 5.2、Jedis

Jedis是Redis官方推荐的java开发工具，是java操作Redis的中间件

- 导入依赖

  ```xml
  <!-- https://mvnrepository.com/artifact/redis.clients/jedis -->
  <dependency>
      <groupId>redis.clients</groupId>
      <artifactId>jedis</artifactId>
      <version>3.5.1</version>
  </dependency>
  <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>fastjson</artifactId>
      <version>1.2.62</version>
  </dependency>
  ```

- 编码测试

  - 连接数据库
  - 编写指令

### 5.3、springboot整合

springboot操作数据：spring-data！

springdata也是和springboot齐名的项目

说明：在springboot2.x之后，原来的jedis被替换成了lettuce

jedis：采用的直连，多个线程操作的话，是不安全的，如果想要避免不安全的，使用jedis pool连接池

lettuce：采用netty，实例可以再多个线程进行共享，不存在线程不安全的情况。

- 创建一个新的springboot项目，然后添加redis依赖

- 配置连接

  ```properties
  #redis连接配置
  spring.redis.host=47.115.82.253
  spring.redis.port=6379
  ```

- 测试

  ```java
  @Autowired
  private RedisTemplate redisTemplate;
  
  
  @Test
  void contextLoads() {
  
      //redisTemplate 操作不同的数据类型
      //opsForValue 操作字符串 类似字符串string
      //opsForList 操作list.......
  
      //除了基本的操作，我们常用的方法都可以直接通过redisTemplate
  
      RedisConnection connection = redisTemplate.getConnectionFactory().getConnection();
      System.out.println(connection.ping());
  
  
  }
  ```

### 5.4、自定义RedisTemplate

- 定义一个未序列化的pojo类

  ```java
  @Component
  @AllArgsConstructor
  @NoArgsConstructor
  @Data
  public class User {
      private String name;
      private int age;
  }
  ```

- 测试，将这个对象传入redis中，发现其报未序列化的错误

- 将这个对象序列化，发现才可以正常输出

  ```java
  @Test
  public void test() throws JsonProcessingException {
  
      //真实的开发一般都使用json来传递对象
      User ferron = new User("朱飞龙", 3);
      String jsonUser = new ObjectMapper().writeValueAsString(ferron);
      redisTemplate.opsForValue().set("user",jsonUser);
      System.out.println(redisTemplate.opsForValue().get("user"));
  
  
  }
  ```

- 最好的就是User类在定义的时候就直接序列化，这时候序列化之后测试时就不用序列化了

  ```java
  @Component
  @AllArgsConstructor
  @NoArgsConstructor
  @Data
  public class User implements Serializable {
      private String name;
      private int age;
  }
  ```

- 自定义一个RedisTemplate

  ```java
  @Configuration
  public class RedisConfig {
  
      //编写我们自己的redisTemplate
      @Bean
      public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory){
  
          RedisTemplate<String,Object> template = new RedisTemplate<String, Object>();
          template.setConnectionFactory(factory);
          Jackson2JsonRedisSerializer<Object> objectJackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
          ObjectMapper om = new ObjectMapper();
          om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
          om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
          objectJackson2JsonRedisSerializer.setObjectMapper(om);
          StringRedisSerializer stringRedisSerializer = new StringRedisSerializer();
  
          //key采用string的序列化方式
          template.setKeySerializer(stringRedisSerializer);
          //hash的key也采用string的序列化方式
          template.setHashKeySerializer(stringRedisSerializer);
          //value序列化方式采用jackson
          template.setValueSerializer(objectJackson2JsonRedisSerializer);
          //hash序列化采用jackson
          template.setValueSerializer(objectJackson2JsonRedisSerializer);
  
          return template;
  
      }
  
  }
  ```

- 使用自定义的RedisTemplate之后，查看redis中的数据

  ![image-20210301194502526](http://fl.ljuuu.com/image-20210301194502526.png)

