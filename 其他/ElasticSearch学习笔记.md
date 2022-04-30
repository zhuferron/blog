## ES概述

### 为什么需要

SQL：模糊查询(like %%)，如果在大数据前提下就十分慢，用了索引也没用

ElasticSearch：**分布式全文搜索**

大数据两个问题：存储+计算

ELK三剑客：ElasticSearch+Logstash+kibana

存储：**GFS(google file system)->NDFS(nutch distributed file system)**

计算：**MapReduce**

非关系型数据库：**BigTable(Google)->HBase**

**Hadoop:MapReduce+NDFS->HDFS**

### 概述

​	Elasticsearch是一个基于Lucene的搜索服务器。它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。Elasticsearch是用Java语言开发的，并作为Apache许可条款下的开放源码发布，是一种流行的企业级搜索引擎。Elasticsearch用于云计算中，能够达到实时搜索，稳定，可靠，快速，安装使用方便。官方客户端在Java、.NET（C#）、PHP、Python、Apache  Groovy、Ruby和许多其他语言中都是可用的。根据DB-Engines的排名显示，Elasticsearch是最受欢迎的企业搜索引擎，其次是Apache Solr，也是基于Lucene。

### Lucene

Luncene是一套信息检索工具包，只是一个jar包，不包含搜索引擎系统！

只包含：索引结构！读写索引的工具！排序，搜索规则...工具类

**ES是基于Luncene做一些封装和增强**

### ELK

E：Elasticsearch

L：Logstash，中央数据流引擎，用于从不同目标(文件/数据存储/MQ)收集不同格式的数据

K：Kiana，可以将es收集到的数据通过友好的页面展示出来，并且提供实时分析的功能

收集清洗数据-->搜索，存储-->kabana



### Elasticsearch VS Solr

- ES基本是开箱即用(解压就可以用)，非常简单，Solr安装稍微复杂一点
- Solr利用Zookeeper进行分布式管理，而ES自身带有分布式协调管理功能
- Solr支持更多格式的数据，例如JSON，XML，CSV，而ES只支持JSON格式
- Solr官方提供的功能更多，而ES本身更注重核心功能，高级功能多有第三方插件提供，例如图形化界面需要kabana友好支撑
- Solr查询快，但更新索引慢(插入删除慢)，用于电商等查询多应用
  - ES建立索引快(查询比Solr慢)，但实时性查询快，用于Facebook或新浪等搜索
  - Solr是传统搜索应用的有力解决方案，但ES更适用于新兴的实时搜索应用
- Solr比较成熟，ES学习成本较高

## ES安装

ES官网：https://www.elastic.co/

下载地址：https://www.elastic.co/cn/downloads/elasticsearch

镜像下载：https://mirrors.huaweicloud.com/elasticsearch/

windows下解压即可使用。

### elasticsearch目录

```
bin 启动文件
config 配置文件
	log4j2 日志配置文件
	jvm.options java虚拟机相关的配置
	elasticsearch.yml elasticsearch的配置文件，默认9200端口->跨域
lib 相关jar包
modules 功能模块
plugins 插件：ik分词器
```

- 启动bin目录下的`elasticsearch.bat`

- 访问`localhost:9200`，测试

  ![](http://fl.ljuuu.com/img/20210317182223.png)

### 安装ES可视化head

- 下载：https://github.com/mobz/elasticsearch-head

- 解压之后，安装

  ```bash
  npm install
  npm run start  //启动之后进入9100
  ```

  ![](http://fl.ljuuu.com/img/20210317183725.png)

- 发现需要**解决跨域问题**：

  - 进入elasticsearch的config目录，找到elasticsearch.yml，在末尾加入

    ```yml
    http.cors.enabled: true
    http.cors.allow-origin: "*"
    ```

  - 成功连接

    ![](http://fl.ljuuu.com/img/20210317184228.png)

初学：

ES->数据库

索引->库

文档->库中的数据

**以后的head就用来做展示工具，查询我们使用Kibana来做**

### 安装Kibana

注意：下载kibana对应的版本需要和es一致

- 下载完之后，解压也需要很多时间，是一个标准的工程

  - 好处：ELK基本上都是拆箱即用

- 打开bin目录，启动kibana

- 注意：启动的时候需要把ES打开。

- 开发工具(post，curl，head，谷歌浏览器插件测试)

  ![](http://fl.ljuuu.com/img/20210317190549.png)



​		我们之后都基于kibana写查询语句

- 汉化

  - 找到汉化补丁

    ![](http://fl.ljuuu.com/img/20210317191128.png)

  - 修改配置文件`kibana.yml`

    ```yml
    i18n.locale: "zh-CN"
    ```

    ​	![](http://fl.ljuuu.com/img/20210317190905.png)

### IK分词器

分词：将连续的字序列按照一定的规范重新组合成词序列的过程。

中文分词：使用IK分词器

IK提供了两个分词算法：ik_smart 和ik_max_word，其中ik_smart为最少切分，ik_max_word为最细粒度划分

- 安装

  1. 下载：https://github.com/medcl/elasticsearch-analysis-ik/releases/，要对应版本

  2. 重启es，发现ik插件被加载

     ![](http://fl.ljuuu.com/img/20210317202542.png)

- 使用Kibana测试

  - 使用ik_smart划分

    ```json
    GET _analyze
    {
      "analyzer": "ik_smart",
      "text": "中国共产党"
    }
    
    --->
    
    {
      "tokens" : [
        {
          "token" : "中国共产党",
          "start_offset" : 0,
          "end_offset" : 5,
          "type" : "CN_WORD",
          "position" : 0
        }
      ]
    }
    ```

  - 使用ik_max_word划分

    ```
    GET _analyze
    {
      "analyzer": "ik_max_word",
      "text": "中国共产党"
    }
    
    --->
    {
      "tokens" : [
        {
          "token" : "中国共产党",
          "start_offset" : 0,
          "end_offset" : 5,
          "type" : "CN_WORD",
          "position" : 0
        },
        {
          "token" : "中国",
          "start_offset" : 0,
          "end_offset" : 2,
          "type" : "CN_WORD",
          "position" : 1
        },
        {
          "token" : "国共",
          "start_offset" : 1,
          "end_offset" : 3,
          "type" : "CN_WORD",
          "position" : 2
        },
        {
          "token" : "共产党",
          "start_offset" : 2,
          "end_offset" : 5,
          "type" : "CN_WORD",
          "position" : 3
        },
        {
          "token" : "共产",
          "start_offset" : 2,
          "end_offset" : 4,
          "type" : "CN_WORD",
          "position" : 4
        },
        {
          "token" : "党",
          "start_offset" : 4,
          "end_offset" : 5,
          "type" : "CN_CHAR",
          "position" : 5
        }
      ]
    }
    
    ```

  - 最小粒度划分，穷尽文本的组合可能性

- 有些自己需要的词，怎么加到ik分词字典中呢？

  - 配置文件

    ![](http://fl.ljuuu.com/img/20210317204130.png)

  - ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE properties SYSTEM "http://java.sun.com/dtd/properties.dtd">
    <properties>
    	<comment>IK Analyzer 扩展配置</comment>
    	<!--用户可以在这里配置自己的扩展字典 -->
    	<entry key="ext_dict"></entry>
    	 <!--用户可以在这里配置自己的扩展停止词字典-->
    	<entry key="ext_stopwords"></entry>
    	<!--用户可以在这里配置远程扩展字典 -->
    	<!-- <entry key="remote_ext_dict">words_location</entry> -->
    	<!--用户可以在这里配置远程扩展停止词字典-->
    	<!-- <entry key="remote_ext_stopwords">words_location</entry> -->
    </properties>
    ```

  - 加入自己的dic

    ```xml
    <!--用户可以在这里配置自己的扩展字典 -->
    	<entry key="ext_dict">mydic.dic</entry>
    ```

  - 编辑自己的dic

  - 重启之后，编写测试

  - 以后，我们只要把需要的词**放到自己定义的dic文件中即可！**

## ES核心概念

**ES是面向文档的 关系型数据库和es的客观对比**

| 关系型数据库 | ES        |
| ------------ | --------- |
| 数据库       | 索引      |
| 表           | types     |
| 行           | documents |
| 字段         | fields    |

es(集群)中可以包含多个索引(数据库)，每个索引中可以包含多个类型(表)，每个类型又可以包含多个文档(行)，每个文档中又包含多个字段(列)

### 设计

**物理设计**：es在后台把每个索引划分成多个分片，每个分片都可以在集群中的不同服务器间迁移，一个也可以是一个集群

**逻辑设计**：一个索引中，包含多个文档，如果说文档1和文档2。当我们索引一篇文档时，可以通过这样的一个顺序找到它：索引-->类型-->文档ID，通过这个组合我们就能索引到某个具体的文档。注意：ID不必是整数，实际上它是个字符串

### 文档

```
user
1 zhangsan 18  //一行就是一个文档
2 lisi 12
3 ferron 5
```

ES是面向文档的，意味这索引和搜索数据的最小单位是文档，es中文档有几个重要属性

- 自我包含：一篇文档同时包含字段和对应的值，也就是同时包含key:value
- 可以是层次型的，一个文档中包含着文档，复杂的逻辑实体就是这样来的{就是一个json对象}
- 灵活的结构，文档不依赖预先定义的模式，我们知道关系型数据库中，要提前定义字段才能使用，在es中，对于字段是十分灵活的，有时候我们甚至可以忽略一个字段或者动态的添加一个字段

### 类型

就是一个字段的类型，可以设置也可以不设置。

### 索引

就是数据库！

索引是映射类型的容器，es中的索引是一个非常大的文档集合。索引存储了映射类型的字段和其他设置。然后它们被存储到了每个分片上。

实际上，一个分片就是一个Lucene索引，一个包含**倒排索引**的文件目录，倒排索引的结构使得es在不扫描全部文档的情况下，就能告诉你哪些文档包含特定的关键字

**倒排索引：**es中使用的是一种称为倒排索引的结构，采用Lucene倒排索作为底层。这种结果适用于快速的全文搜索，一个索引由文档中所有不重复的列表构成，对于每一个词，都有一个包含它的文档列表。例如：下面两个文档：

```
Study every day, good good up to forever  #文档1
To forever, Study every day, good good up  #文档2
```

为了创建倒排索引，我们首先将每个文档拆分成独立的词(称为词条或者tokens)，然后创建一个包含所有不重复的词条的排序列表，然后列出每个词条出现在哪个文档(0为不存在，1为存在)

| term    | doc_1 | doc_2 |
| ------- | ----- | ----- |
| Study   | 1     | 0     |
| To      | 0     | 0     |
| every   | 1     | 1     |
| forever | 1     | 1     |
| day     | 1     | 1     |
| study   | 0     | 1     |
| good    | 1     | 1     |
| every   | 1     | 1     |
| to      | 1     | 0     |
| up      | 1     | 1     |

现在，我们试图搜索to forever，只需要查看包含每个词条的文档

| term                | doc_1 | doc_2 |
| ------------------- | ----- | ----- |
| to                  | 1     | 0     |
| forever             | 1     | 1     |
| total(weight,socre) | 2     | 1     |

因此在es中，数据库就是倒排索引这种数据结构！因此es中数据库就是索引

然后在es中，倒排索引是被分片存储的，索引被分成多个分片，每一个分片就是一个Lucene的索引，所有也可以说一个es的索引就是多个Lucene索引组成的！

**使用倒排索引，完全过滤掉无关的数据，提高效率**，

## Restful风格说明

一种软件架构风格，而不是标准，只是提供了一组设计原则和约束条件。它主要用于客户端和服务器交互类的软件。基于这个风格设计的软件可以更简洁，更有层次，更易于实现缓存等机制

基本Restful风格命令说明：

| method |                     url地址                     |       描述       |
| :----: | :---------------------------------------------: | :--------------: |
|  PUT   |     localhost:8200/索引名称/类型名称/文档id     | 创建文档(指定id) |
|  POST  |        localhost:8200/索引名称/类型名称         | 创建文档(随机id) |
|  POST  | localhost:8200/索引名称/类型名称/文档id/_update |     修改文档     |
| DELETE |     localhost:8200/索引名称/类型名称/文档id     |     删除文档     |
|  GET   |     localhost:8200/索引名称/类型名称/文档id     |  通过id查询文档  |
|  POST  |    localhost:8200/索引名称/类型名称/_search     |   查询所有数据   |

ES中的数据类型：

1. 字符串类型：text，keyword
2. 数值类型：long，integer，short，byte，double，float，half float，scaled float
3. 日期类型：date
4. 布尔值类型：boolean
5. 二进制类型：binary
6. ......

### 索引基本操作

- 创建一个索引：完成自动创建索引和添加数据

  ```rest
  PUT /test1/type1/1
  {
    "name": "ferron",
    "age": 15
  }
  --->
  PUT /索引名/~类型名~/id{
  	请求体
  }
  ```

  ![](http://fl.ljuuu.com/img/20210319081800.png)

- 指定字段的类型

  ```
  PUT /test2
  {
    "mappings": {
      "properties": {
        "name": {
          "type": "text"
        },
        "age": {
          "type": "long"
        },
        "birthday": {
          "type": "date"
        }
      }
    }
  }
  --->
  创建对应的规则
  ```

  ![](http://fl.ljuuu.com/img/20210319083249.png)

- 获得信息

  ```
  GET test2
  --->
  获得test2的信息
  ```

- 使用默认类型`_doc`创建索引库

  ```
  PUT /test3/_doc/1
  {
    "name": "ferron",
    "age": "15",
    "birthday": "2000-07-27"
  }
  ```

  ![](http://fl.ljuuu.com/img/20210319083705.png)

- 拓展：通过命令了解es索引情况，`_cat`命令

  ```
  GET _cat/indices?v
  ```

- 修改索引一：使用`PUT`命令覆盖（**这种方法必须得加上其他属性**）

  ```
  PUT /test3/_doc/1
  {
    "name": "ferron123",
    "age": "15",
    "birthday": "2000-07-27"
  }
  ```

  ![](http://fl.ljuuu.com/img/20210319084130.png)

- 修改索引二：

  ```
  POST /test3/_doc/1/_update
  {
    "doc":{
      "name": "ferron123789"
    }
  }
  --->
  POST /索引名/类型名/id/_update
  {
    "doc":{
      字段名:字段值
    }
  }
  ```

  ![](http://fl.ljuuu.com/img/20210319084611.png)

- 删除索引

  ```
  DELETE 索引名
  DELETE 索引名/类型名/id  --->删除文档
  ```

### 文档基本操作(重点)

### 基本操作

- 创建数据

  ```
  PUT /test/user/1
  {
    "name":"ferron1",
    "age": 20,
    "desc":"一天没课浑身轻松",
    "tags":["天才","直男"]
  }
  PUT /test/user/2
  {
    "name":"ferron2",
    "age": 15,
    "desc":"一天没课浑身轻松",
    "tags":["天才","直男"]
  }
  PUT /test/user/3
  {
    "name":"ferron3",
    "age": 10,
    "desc":"一天没课浑身轻松",
    "tags":["天才","直男"]
  }
  
  ```

  ![](http://fl.ljuuu.com/img/20210319085523.png)

- 更新数据：使用`PUT`必须加上未修改的信息，否则就默认为空，使用`POST`命令后面加一个`_uodate`则可以实现某个属性具体的更改

  ```
  POST /test/user/2/_update
  {
    "doc":{
      "name":"ferron4"
    }
  }
  ```

  ![](http://fl.ljuuu.com/img/20210319085932.png)

- 查询数据(通过id)

  ```
  GET /test/user/1
  ```

- 查询数据(通过条件)

  通过name=ferron1的条件进行查询(**后面会使用模糊查询**)

  **注：ES中有两种数据类型分别为text和keyword，text可以通过IK分词器被分成多个keyword，模糊查询搜索的是keyword**

  ```
  GET /test/user/_search?q=name:ferron1
  ```

  ![](http://fl.ljuuu.com/img/20210319090819.png)

  这里的`_score`代表每个文档被搜索出来占的权重，权重越高，表示搜索越匹配

### 复杂操作(搜索)

- 关键字查询：查询name中含有keyword为一天的数据

  ```
  GET /test/user/_search
  {
    "query":{
      "match":{
        "desc":"一天"
      }
    }
  }
  ```

  ![](http://fl.ljuuu.com/img/20210319091701.png)

- 查询结果只返回部分属性，并且按照某个属性排序

  ```
  GET /test/user/_search
  {
    "query":{
      "match":{
        "desc":"一天"
      }
    },
    "_source": ["name","age"],
    "sort": [
      {
        "age": {
          "order": "desc"
        }
      }
    ]
  }
  ```

- 分页查询：以后从前端接收，格式就是/search/{current}/{pagesize}

  ```
  "from":0  //从第几个数据开始
  "size":1  //返回多少条数据(单页面大小)
  ```

- 布尔值查询：多条件精确查询

  must(and)：所有条件都要匹配

  ```
  GET /test/user/_search
  {
    "query":{
      "bool":{
        "must": [
          {
            "match": {
              "desc": "一天"
            }
          },
          {
            "match": {
              "age": "20"
            }
          }
        ]
      }
    }
  }
  ```

  ![](http://fl.ljuuu.com/img/20210319092756.png)

  should(or)：有一个满足即可

  ```
  GET /test/user/_search
  {
    "query":{
      "bool":{
        "should": [
          {
            "match": {
              "desc": "一天"
            }
          },
          {
            "match": {
              "age": "20"
            }
          }
        ]
      }
    }
  }
  ```

  ![](http://fl.ljuuu.com/img/20210319092825.png)

  must_not(not)

  ```
  GET /test/user/_search
  {
    "query":{
      "bool":{
        "must_not": [
          {
            "match": {
              "age": "20"
            }
          }
        ]
      }
    }
  }
  ```

  ![](http://fl.ljuuu.com/img/20210319092934.png)

  

- 过滤器：使用`filter`进行数据过滤

  - gt：大于
  - gte：大于等于
  - lt：小于
  - lte：小于等于

  ```
  GET /test/user/_search
  {
    "query":{
      "bool":{
        "must": [
          {
            "match": {
              "desc":"一天"
            }
          }
        ],
        "filter": {
          "range": {
            "age": {
              "gte": 11
            }
          }
        }
      }
    }
  }
  ```

  ![](http://fl.ljuuu.com/img/20210319093132.png)

- 多条件匹配查询：**多个条件通过空格隔开，只有满足一个条件就能匹配成功，score表示匹配的分值**

  ```
  GET /test/user/_search
  {
    "query":{
      "match": {
        "tags": "男 天才"
      }
    }
  }
  ```

  ![](http://fl.ljuuu.com/img/20210319093411.png)

- 精确查询：term查询是直接通过倒排索引指定的词条进行精确查找的

  关于分词：

  - term，会直接查询精确的
  - match，会使用分词器解析，先分析文档，然后通过分析的文档进行查询
  - **在分词器中，text会被分词器解析为多个keyword，而keyword不会被分词器解析**

- 高亮查询：搜索出来的结果定义为高亮

  ```
  GET /test/user/_search
  {
    "query":{
      "match": {
        "name": "ferron1"
      }
    },
    "highlight": {
      "fields": {
        "name":{}
      }
    }
  }
  ```

  ![](http://fl.ljuuu.com/img/20210319094423.png)

- 可以自定义样式，不使用默认的<em></em>

  ```
  GET /test/user/_search
  {
    "query":{
      "match": {
        "name": "ferron1"
      }
    },
    "highlight": {
      "pre_tags": "<p class='key' style='color:red'>",
      "post_tags": "</p>", 
      "fields": {
        "name":{}
      }
    }
  }
  ```

  ![](http://fl.ljuuu.com/img/20210319094631.png)

## ES集成Springboot

### 测试集成

- 找官网文档

  ![](http://fl.ljuuu.com/img/20210319145317.png)

  ![](http://fl.ljuuu.com/img/20210319145409.png)

- 在官方文档下面找到了原生的依赖

  ```xml
  <dependency>
      <groupId>org.elasticsearch.client</groupId>
      <artifactId>elasticsearch-rest-high-level-client</artifactId>
      <version>7.11.2</version>
  </dependency>
  ```

- 初始化

  ![](http://fl.ljuuu.com/img/20210319145719.png)

- 配置基本的项目

  **注意：一定要保证我们导入的依赖和我们的es版本一致，默认的不一致**

  ![](http://fl.ljuuu.com/img/20210319150830.png)

  ```xml
  <elasticsearch.version>7.6.1</elasticsearch.version>
  ```

- 注入bean，在springboot中就相当于写一个配置类

  ```java
  @Configuration
  public class ElasticSearchClientConfig {
  
      @Bean
      //相当于在xml中配置bean，因为我们需要这个对象，所以我们需要注入这个对象
      //<beans id=restHighLevelClient class=RestHighLevelClient><bean>
      public RestHighLevelClient restHighLevelClient(){
  
          //官网文档
          RestHighLevelClient client = new RestHighLevelClient(
                  RestClient.builder(
                          new HttpHost("localhost", 9200, "http"),
                          new HttpHost("localhost", 9201, "http")));
  
          return client;
  
      }
  
  }
  ```

- 分析es在springboot中的自动装配

  1. 找到autoconfigure

     ![](http://fl.ljuuu.com/img/20210319152116.png)

  2. 找到es相关的自动配置类

     ![](http://fl.ljuuu.com/img/20210319152435.png)

  3. 源码中提供三个对象，我们可以直接拿过来使用

     ![](http://fl.ljuuu.com/img/20210319153043.png)

### 索引API

- 首先注入对象，如果名字不一致则使用`Qulifier`指定名字

  ```java
  @Autowired
  @Qualifier("restHighLevelClient")
  private RestHighLevelClient client;
  ```

- 创建一个新的索引

  ```java
  //索引的创建
  @Test
  void test1() throws IOException {
      //1.创建索引的请求
      CreateIndexRequest request = new CreateIndexRequest("test1");
      //2.执行请求:使用客户端，请求后获得请求后的响应
      CreateIndexResponse createIndexResponse = client.indices().create(request, RequestOptions.DEFAULT);
      //3.打印响应
      System.out.println(createIndexResponse);
  }
  ```

  ![](http://fl.ljuuu.com/img/20210319154624.png)
  
- 判断一个索引是否存在

  ```java
  //获取索引
  @Test
  void test2() throws IOException {
      //判断test1是否存在
      GetIndexRequest request = new GetIndexRequest("test1");
      //执行请求
      boolean exists = client.indices().exists(request, RequestOptions.DEFAULT);
      //打印响应
      System.out.println(exists);
  
  }
  ```

- 删除一个索引

  ```java
  //删除索引
  @Test
  void test3() throws IOException {
      DeleteIndexRequest request = new DeleteIndexRequest("test1");
      AcknowledgedResponse delete = client.indices().delete(request, RequestOptions.DEFAULT);
      System.out.println(delete);
  }
  ```

### 文档API

- 首先创建一个`User`实体类

  ```java
  @Data
  @AllArgsConstructor
  @NoArgsConstructor
  @Component
  public class User {
      private String name;
      private int age;
  }
  ```

- 因为我们的数据需要通过json数据格式传输，所以我们得引入fastjson

  ```xml
  <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>fastjson</artifactId>
      <version>1.2.62</version>
  </dependency>
  ```

- 添加一个文档

  ```java
  //测试添加文档
  @Test
  void test1() throws IOException {
      //创建对象
      User user = new User("ferron", 20);
      //创建请求，若没有索引则用Create创建一个新索引，有则不用Create
      IndexRequest request = new IndexRequest("test");
      //规则： put /test/_doc/1
      request.id("1");
      request.timeout("1s");
  
      //将我们的数据放入请求：json数据格式
      request.source(JSON.toJSONString(user), XContentType.JSON);
      //客户端发送请求
      IndexResponse response = client.index(request, RequestOptions.DEFAULT);
      //打印结果
      System.out.println(response.toString());
      System.out.println(response.status());
  
  }
  ```

  ![](http://fl.ljuuu.com/img/20210319161438.png)

- 判断一个文档是否存在

  ```java
  //测试获取文档，判断是否存在：GET /test/_doc/1
  @Test
  void test2() throws IOException {
      //创建请求
      GetRequest request = new GetRequest("test","1");
      //不获取返回的_source的上下文
      request.fetchSourceContext(new FetchSourceContext(false));
      request.storedFields("_none_");
  
      boolean exists = client.exists(request, RequestOptions.DEFAULT);
      System.out.println(exists);
  
  }
  ```

- 获取文档信息

  ```java
  //获取文档信息 GET /test/_doc/1
  @Test
  void test3() throws IOException {
      //创建请求
      GetRequest request = new GetRequest("test","1");
  
      //得到响应结果
      GetResponse response = client.get(request, RequestOptions.DEFAULT);
  
      //得到响应结果之后，我们就可以获得到这个文档的全部信息了，只要调用对应的方法即可！
      System.out.println(response.getSourceAsString());
      System.out.println(response);
  }
  ```

- 修改文档信息

  ```java
  //更新文档信息
  @Test
  void test4() throws IOException {
  
      //创建更新对象
      User user = new User("ferron牛逼", 23);
  
      //创建请求
      UpdateRequest request = new UpdateRequest("test","1");
      request.doc(JSON.toJSONString(user),XContentType.JSON);
  
      //得到响应结果
      UpdateResponse response = client.update(request, RequestOptions.DEFAULT);
  
      //得到响应结果之后
      System.out.println(response);
  }
  ```

- 删除文档信息

  ```java
  //删除文档信息
  @Test
  void test5() throws IOException {
  
      //创建请求
      DeleteRequest request = new DeleteRequest("test", "3");
  
      //得到响应结果
      DeleteResponse response = client.delete(request, RequestOptions.DEFAULT);
  
      //得到响应结果之后
      System.out.println(response.status());
  }
  ```

- 大批量插入数据

  ```java
  //大批量插入数据
  @Test
  void test6() throws IOException {
  
      //大批量数据请求接口
      BulkRequest request = new BulkRequest();
      request.timeout("30s");
  
      //创建数据列表
      ArrayList<User> list = new ArrayList<>();
      list.add(new User("ferron01",10));
      list.add(new User("ferron02",10));
      list.add(new User("ferron03",10));
      list.add(new User("ferron04",10));
      list.add(new User("ferron05",10));
      list.add(new User("ferron06",10));
      list.add(new User("ferron07",10));
      list.add(new User("ferron08",10));
      list.add(new User("ferron09",10));
      list.add(new User("ferron10",10));
      list.add(new User("ferron11",10));
      list.add(new User("ferron12",10));
      list.add(new User("ferron13",10));
      list.add(new User("ferron14",10));
      list.add(new User("ferron15",10));
      list.add(new User("ferron16",10));
      list.add(new User("ferron17",10));
      list.add(new User("ferron18",10));
      list.add(new User("ferron19",10));
      list.add(new User("ferron20",10));
  
      //批处理请求
      for (int i = 0; i < list.size(); i++) {
          //批量更新和删除，就在这里更改就好了
          request.add(new IndexRequest("test").id(""+(i+1)).source(JSON.toJSONString(list.get(i)),XContentType.JSON));
      }
  
      //获得响应
      BulkResponse response = client.bulk(request, RequestOptions.DEFAULT);
  
      //打印结果
      System.out.println(response.hasFailures());
  
  
  }
  ```

- 查询

  ```java
  //查询
  @Test
  void test7() throws IOException {
      //获得请求
      SearchRequest request = new SearchRequest("test");
  
      //获得搜索条件构建器
      //sourceBuilder就是搜索条件构建器，里面就是我们之前写的query里面的值
      SearchSourceBuilder sourceBuilder = new SearchSourceBuilder();
  
      //获得查询构建器
      //精确查询
      TermQueryBuilder termQueryBuilder = QueryBuilders.termQuery("name", "ferron01");
      //匹配所有查询
      MatchAllQueryBuilder matchAllQueryBuilder = QueryBuilders.matchAllQuery();
  
      //将查询构建器放入搜索条件构建器
      sourceBuilder.query(matchAllQueryBuilder);
      //设置分页
      sourceBuilder.from();
      sourceBuilder.size();
      //设置查询响应时间
      sourceBuilder.timeout(new TimeValue(60, TimeUnit.SECONDS));
  
      //执行请求
      request.source(sourceBuilder);
  
      SearchResponse response = client.search(request, RequestOptions.DEFAULT);
      System.out.println(JSON.toJSONString(response.getHits()));
      System.out.println("===================");
      for (SearchHit hit : response.getHits().getHits()) {
          System.out.println(hit.getSourceAsMap());
      }
  
  
  }
  ```

  ![](http://fl.ljuuu.com/img/20210319171049.png)

## 实战

### 项目准备

- 新建一个项目，导入fastjson模块
- 导入对应的前端资料
- 写一个测试controller

### 爬取数据

- 通过python爬虫在淘宝爬取一些测试数据

  ```python
  url='https://uland.taobao.com/sem/tbsearch?refpid=mm_26632258_3504122_32538762&keyword=%E6%8A%98%E6%89%A3&clk1=baf5d2523927db20d74512441fe5c88a&upsId=baf5d2523927db20d74512441fe5c88a&spm=a2e0b.20350158.search.1&pid=mm_26632258_3504122_32538762&union_lens=recoveryid%3A201_11.88.44.78_7356863_1616155394109%3Bprepvid%3A201_11.88.44.78_7356863_1616155394109'
  
  
  browser = webdriver.Chrome()
  browser.execute_cdp_cmd("Page.addScriptToEvaluateOnNewDocument", {
      "source": """
                                  Object.defineProperty(navigator, 'webdriver', {
                                    get: () => Chrome
                                  })
                                """
  })
  browser.get(url)
  wait_start = WebDriverWait(browser, 10)
  wait_start.until(EC.presence_of_element_located((By.CLASS_NAME, 'submit')))
  browser.maximize_window()
  
  time.sleep(1)
  print("进入首页")
  
  list=[]
  for i in range(20):
      dic={}
      dic['price']=browser.find_element_by_xpath('//*[@id="mx_5"]/ul/li['+str(i+1)+']/a/div[2]/span[2]').text
      dic['img']=browser.find_element_by_xpath('//*[@id="mx_5"]/ul/li['+str(i+1)+']/a/img').get_attribute("src")
      dic['title']=browser.find_element_by_xpath('//*[@id="mx_5"]/ul/li['+str(i+1)+']/a/div[1]/span').text
      list.append(dic)
      if(i%10 == 0):
          js = "var q=document.documentElement.scrollTop=680"
          browser.execute_script(js)
  
  
  pd.DataFrame(list).to_excel(r"D:\ZFL\workplace\Idea_workplace\study\es-api\jd\src\main\resources\static\data\tb_data.xlsx")
  ```

### 业务编写

- 首先编写一个内容实体类

  ```java
  @Data
  @NoArgsConstructor
  @AllArgsConstructor
  @Component
  public class Content {
  
      private String title;
      private String price;
      private String img;
  
  }
  ```

- 编写读取数据的方法

  ```java
  //读取python爬取的数据
  public List<Content> getData(String filename) throws Exception {
      //创建workbook
      Workbook workbook=Workbook.getWorkbook(new File(filename));
      //获取第一个工作表sheet
      Sheet sheet=workbook.getSheet(0);
      //创建数据表
      ArrayList<Content> list = new ArrayList<>();
  
      //获取数据
      for (int i=0;i<sheet.getRows();i++){
          list.add(new Content(
              sheet.getCell(2,i).getContents()+"",
              sheet.getCell(0,i).getContents()+"",
              sheet.getCell(1,i).getContents()+""));
      }
      //关闭数据表
      workbook.close();
      return list;
  }
  ```

  









### 前后端分离





### 搜索高亮





