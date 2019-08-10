# ElasticSearch

- [elasticsearch](#elasticsearch)
  - [elasticsearch介绍](#elasticsearch介绍)
    - [elasticsearch是什么](#elasticsearch是什么)
    - [elasticsearch具备功能](#elasticsearch具备功能)
    - [elasticsearch的特点](#elasticsearch的特点)
    - [elasticsearch使用场景](#elasticsearch使用场景)
  - [elasticsearch的核心概念](#elasticsearch的核心概念)
    - [<strong>NearRealtime</strong>](#nearrealtime)
    - [<strong>Cluster</strong>](#cluster)
    - [<strong>Node</strong>](#node)
    - [<strong>Document&amp;&amp;field</strong>](#documentfield)
    - [<strong>Index</strong>](#index)
    - [<strong>Type</strong>](#type)
    - [<strong>shard</strong>](#shard)
    - [replica](#replica)
  - [elasticsearch vs 数据库](#elasticsearch-vs-数据库)
    - [面向文档的搜索分析引擎 vs java面向对象时的操作数据库](#面向文档的搜索分析引擎-vs-java面向对象时的操作数据库)
      - [elasticsearch](#elasticsearch)
  - [elasticsearch的安装和启动](#elasticsearch的安装和启动)
  - [elasticsearch集群管理](#elasticsearch集群管理)
    - [<strong>快速检查集群的健康状况</strong>](#快速检查集群的健康状况)
      - [green、yellow、red健康状态?](#greenyellowred健康状态)
      - [<strong>为什么刚创建现在会处于一个yellow状态</strong> ?](#为什么刚创建现在会处于一个yellow状态-)
    - [<strong>快速查看集群中有哪些索引</strong>](#快速查看集群中有哪些索引)
    - [<strong>简单的索引操作</strong>](#简单的索引操作)
  - [elasticsearch对document的CRUD](#elasticsearch对document的crud)
    - [增](#增)
    - [查询](#查询)
    - [更新](#更新)
    - [删除](#删除)
  - [<strong>elasticsearch的多种搜索方式</strong>](#elasticsearch的多种搜索方式)
    - [query String query](#query-string-query)
    - [full-text search](#full-text-search)
    - [phrase search(短语搜索_精确)](#phrase-search短语搜索_精确)
    - [highlight search(高亮搜索结果)](#highlight-search高亮搜索结果)
  - [聚合分析(group by avg sort等)](#聚合分析group-byavgsort等)
  - [第四个分析需求 计算每个tag下商品的平均价格,并且按照平均价格排序](#第四个分析需求-计算每个tag下商品的平均价格并且按照平均价格排序)
  - [第五个分析需求 按照指定的价格范围区间进行分组,然后在每组内再按照tag进行分组,最后再计算每组的平均价格](#第五个分析需求-按照指定的价格范围区间进行分组然后在每组内再按照tag进行分组最后再计算每组的平均价格)
  - [elasticsearch 基础分布式介绍](#elasticsearch-基础分布式介绍)
  - [shard&amp;replica机制](#shardreplica机制)
  - [Shard数调优](#shard数调优)
  - [Elasticsearch的document 全量替换,强制创建,删除机制](#elasticsearch的document-全量替换强制创建删除机制)
    - [全量替换](#全量替换)
    - [强制创建](#强制创建)
    - [删除机制](#删除机制)
  - [elasticsearch 中document自动创建id](#elasticsearch-中document自动创建id)
  - [elasticsearch基于乐观锁的并发修改特性(基于_version)](#elasticsearch基于乐观锁的并发修改特性基于_version)
  - [elasticsearch可以基于自己维护的version进行并发控制](#elasticsearch可以基于自己维护的version进行并发控制)
    - [_version(es内部维护的版本号)和 version_type(外部维护的版本号)的区别](#_versiones内部维护的版本号和-version_type外部维护的版本号的区别)
  - [elasticsearch内置脚本(script)支持](#elasticsearch内置脚本script支持)
    - [外部脚本 groovy](#外部脚本-groovy)
    - [upsert操作](#upsert操作)



# 									elasticsearch

## elasticsearch介绍

### elasticsearch是什么

​	1.分布式的搜索引擎和数据分析引擎.

​	2.全文检索，结构化检索，数据分析

​	3.对海量数据进行近实时（秒级别）处理

+ 分布式:Es自动可以将海量数据分散到多台服务器去存储和检索。
+ 海量数据的处理：分布式解决了海量数据的处理。
+ vs Lunce

### elasticsearch具备功能

​	1.自动维护数据到多个节点的索引的建立，还有搜索请求分布到多个节点的执行。

​	2.自动维护数据的冗余副本，保证一些机器宕机了，不会丢失数据.

​	3.封装了更多高级的功能，给我们提供更多的高级支持，让我们快速的开发应用，开发更高级的应用；复杂的搜索功能，聚合分析的功能。基于地理位置的搜索（距离我当前位置1公里以内的超市）

### elasticsearch的特点

​	1.   可以作为一个大型分布式集群（数百台服务器）技术，处理PB级数据，服务大公司。也可以运行在单机上，服务小公司。

​	2.   Es不是什么新技术，主要是将全文检索、数据分析和分布式技术合并在了一起，才形成了独一无二的Es。

​	3.   对用户而言，是开箱即用的。

​	4.   提供了数据库不能提供的很多功能。

### elasticsearch使用场景

+ 维基百科 es实现

+ 某些新闻网站： 用户行为日志数据（点赞，评论等），社交网络数据，数据分析等
+ Github 搜索上千亿行代码
+ 商品价格监控网站，

## elasticsearch的核心概念

### **NearRealtime** 

​	**NRT** 近实时 秒级（写入到可以搜索到写入的数据时间毫秒级）。 

### **Cluster** 

​	**集群，**包含多个节点，每个节点属于哪个集群是通过一个配置（集群名称，默认是elasticsearch）来决定的，对于中小型应用来说，刚开始一个集群就一个节点很正常 

### **Node**

​	**节点**，集群中的一个节点，节点也有一个名称（默认是随机分配的），节点名称很重要（在执行运维管理操作的时候），默认节点会去加入一个名称为“elasticsearch”的集群，如果直接启动一堆节点，那么它们会自动组成一个elasticsearch集群，当然一个节点也可以组成一个elasticsearch集群 

### **Document&&field**

​	**文档,&&字段**

​	document是es中的最小数据单元，一个document可以是一条客户数据，一条商品分类数据，一条订单数据，通常用JSON数据结构表示，每个index下的type中，都可以去存储多个document。一个document里面有多个field，每个field就是一个数据字段。如下：有5个field。

~~~json
{
  "product_id": "1",
  "product_name": "高露洁牙膏",
  "product_desc": "高效美白",
  "category_id": "2",
  "category_name": "日化用品"
}

~~~



### **Index**

​	**索引**，包含一堆有相似结构的文档数据，比如可以有一个客户索引，商品分类索引，订单索引，索引有一个名称。一个index包含很多document，一个index就代表了一类类似的或者相同的document。比如说建立一个product index，商品索引，里面可能就存放了所有的商品数据，所有的商品document。 

### **Type**

​	**类型**，每个索引里都可以有一个或多个type，type是index中的一个逻辑数据分类，一个type下的document，都有相同的field，比如博客系统，有一个索引，可以定义用户数据type，博客数据type，评论数据type。商品index，里面存放了所有的商品数据，商品document.

但是商品分很多种类，每个种类的document的field可能不太一样，比如说电器商品，可能还包含一些诸如售后时间范围这样的特殊field；生鲜商品，**还包含一些诸如生鲜保质期之类的特殊field**

type，日化商品type，电器商品type，生鲜商品type

日化商品type：product_id，product_name，product_desc，category_id，category_name

电器商品type：product_id，product_name，product_desc，category_id，category_name，service_period

生鲜商品type：product_id，product_name，product_desc，category_id，category_name，eat_period

每一个type里面，都会包含一堆document

~~~json
{
  "product_id": "2",
  "product_name": "长虹电视机",
  "product_desc": "4k高清",
  "category_id": "3",
  "category_name": "电器",
  "service_period": "1年"
}

{
  "product_id": "3",
  "product_name": "基围虾",
  "product_desc": "纯天然，冰岛产",
  "category_id": "4",
  "category_name": "生鲜",
  "eat_period": "7天"
}

~~~

### **shard**

​	**单台机器无法存储大量数据，es可以将一个索引中的数据切分为多个shard，分布在多台服务器上存储。有了shard就可以横向扩展，存储更多数据，让搜索和分析等操作分布到多台服务器上去执行，提升吞吐量和性能（吞吐量如何测试）。每个shard都是一个lucene index。** 

### replica

​	**任何一个服务器随时可能故障或宕机，此时shard可能就会丢失，因此可以为每个shard创建多个replica副本。replica可以在shard故障时提供备用服务，保证数据不丢失，多个replica还可以提升搜索操作的吞吐量和性能。primary shard（建立索引时一次设置，设置后不能修改，不设置数量默认5个），replica shard（随时修改数量，默认1个），默认每个索引10个shard，5个primary shard，5个replica shard，最小的高可用配置，是2台服务器。** 

## elasticsearch vs 数据库

###elasticsearch和mysql的对比

| elasticsearch | 数据库 |
| ------------- | ------ |
| index         | 库     |
| type          | 表     |
| document      | 行     |
| field         | 字段   |

### 面向文档的搜索分析引擎 vs java面向对象时的操作数据库

　**对比可以明白es的document数据格式和数据库的关系型数据格式的区别**

####java	

+ 应用系统的数据结构都是面向对象的，复杂的 .
+ 对象数据存储到数据库中，只能拆解开来，变为扁平的多张表，每次查询的时候还得还原回对象格式，相当麻烦
+ java代码操作数据库一般这样写

~~~java
public class Employee {

  private String email;
  private String firstName;
  private String lastName;
  private EmployeeInfo info;
  private Date joinDate;

}

private class EmployeeInfo {
  
  private String bio; // 性格
  private Integer age;
  private String[] interests; // 兴趣爱好

}

EmployeeInfo info = new EmployeeInfo();
info.setBio("curious and modest");
info.setAge(30);
info.setInterests(new String[]{"bike", "climb"});

Employee employee = new Employee();
employee.setEmail("zhangsan@sina.com");
employee.setFirstName("san");
employee.setLastName("zhang");
employee.setInfo(info);
employee.setJoinDate(new Date());
~~~

​	employee对象：里面包含了Employee类自己的属性，还有一个EmployeeInfo对象

​	两张表：employee表，employee_info表，将employee对象的数据重新拆开来，变成Employee数据和		EmployeeInfo数据

​	employee表：email，first_name，last_name，join_date，4个字段

​	employee_info表：bio，age，interests，3个字段；此外还有一个外键字段，比如employee_id，关联着	employee表

#### elasticsearch

+ ES是面向文档的，文档中存储的数据结构，与面向对象的数据结构是一样的，基于这种文档数据结构，es可以提供复杂的索引，全文检索，分析聚合等功能.
+ es的document用json数据格式来表达.

~~~
{
    "email":      "zhangsan@sina.com",
    "first_name": "san",
    "last_name": "zhang",
    "info": {
        "bio":         "curious and modest",
        "age":         30,
        "interests": [ "bike", "climb" ]
    },
    "join_date": "2017/01/01"
}
~~~



## elasticsearch的安装和启动

**1、安装JDK，elasticsearch5.2.0至少需要1.8.0_73以上版本（检查jdk版本）**

2、下载和解压缩Elasticsearch安装包（先对windows版本测）

3、启动Elasticsearch：bin\elasticsearch.bat，es本身特点之一就是开箱即用，如果是中小型应用，数据量少，操作不是很复杂，直接启动就可以用了

**4**、检查ES是否启动成功：http://localhost:9200/?pretty**

​	打开链接返回的json数据

name: node名称

cluster_name: 集群名称（默认的集群名称就是elasticsearch）

version.number: 5.2.0，es版本号

```json
{
  "name" : "4onsTYV",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "nKZ9VK_vQdSQ1J0Dx9gx1Q",
  "version" : {
    "number" : "5.2.0",
    "build_hash" : "24e05b9",
    "build_date" : "2017-01-24T19:52:35.800Z",
    "build_snapshot" : false,
    "lucene_version" : "6.4.0"
  },
  "tagline" : "You Know, for Search"

}
```

 

5、修改集群名称：elasticsearch.yml

6、下载和解压缩Kibana安装包，使用里面的开发界面，去操作elasticsearch，作为学习es知识点的一个主要的界面入口

7、启动Kibana：bin\kibana.bat

**8**、**进入Kibana界面 http://localhost:5601**  选择Dev Tools

9、在Dev Tools 的控制台输入 GET _cluster/health　查看集群的健康状况

## elasticsearch集群管理

### **快速检查集群的健康状况** 

​		**es提供了一套api，叫做cat api，可以查看es中各种各样的数据**

​		**GET /_cat/health?v**

#### green、yellow、red健康状态?

​	**green**：每个索引的primary shard和replica shard都是active状态的

​	**yellow**：每个索引的primary shard都是active状态的，但是部分replica shard不是active状态，处于不可用的状态

​	**red**：不是所有索引的primary shard都是active状态的，部分索引有数据丢失了

#### **为什么刚创建现在会处于一个yellow状态** ?

​	我们现在就一个笔记本电脑，就启动了一个es进程，相当于就只有一个node。现在es中有一个index，就是kibana自己内置建立的index。由于默认的配置是给每个index分配5个primary shard和5个replica shard，而且primary shard和replica shard不能在同一台机器上（为了容错）。现在kibana自己建立的index是1个primary shard和1个replica shard。当前就一个node，所以只有1个primary shard被分配了和启动了，但是一个replica shard没有第二台机器去启动。

​	做一个小实验：此时只要启动第二个es进程，就会在es集群中有2个node，然后那1个replica shard就会自动分配过去，然后cluster status就会变成green状态。（**一台机器会自动分配，不同机器如何分配replica shard?**）

### **快速查看集群中有哪些索引** 

​	GET /_cat/indices?v

### **简单的索引操作**

​	增加索引: PUT /index_name?pretty

​	删除索引: DELETE/index_name?pretty

## elasticsearch对document的CRUD

*举例:*

### 增

​	格式:  PUT /index_name/type_name/id

​		{

​			"json数据"

​		}

~~~json
PUT /ecommerce/product/1
{
    "name" : "gaolujie yagao",
    "desc" :  "gaoxiao meibai",
    "price" :  30,
    "producer" :      "gaolujie producer",
    "tags": [ "meibai", "fangzhu" ]
}

{
  "_index": "ecommerce",
  "_type": "product",
  "_id": "1",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "created": true
}

PUT /ecommerce/product/2
{
    "name" : "jiajieshi yagao",
    "desc" :  "youxiao fangzhu",
    "price" :  25,
    "producer" :      "jiajieshi producer",
    "tags": [ "fangzhu" ]
}
~~~



​	**es会自动建立index和type，不需要提前创建，而且es默认会对document每个field都建立倒排索引，让其可以被搜索** 

### 查询

​	GET /index_name/type_name/id

*GET /ecommerce/product/1* 返回数据为:

```json
{
  "_index": "ecommerce",
  "_type": "product",
  "_id": "1",
  "_version": 1,
  "found": true,
  "_source": {
    "name": "gaolujie yagao",
    "desc": "gaoxiao meibai",
    "price": 30,
    "producer": "gaolujie producer",
    "tags": [
      "meibai",
      "fangzhu"
    ]
  }
}

```



### 更新

####传递所有字段

+ PUT命令 PUT 所有字段


  ~~~json
  PUT /ecommerce/product/1
  {
      "name" : "jiaqiangban gaolujie yagao",
      "desc" :  "gaoxiao meibai",
      "price" :  30,
      "producer" :      "gaolujie producer",
      "tags": [ "meibai", "fangzhu" ]
  } 
  ~~~

+ **替换方式有一个不好，即使必须带上所有的field，才能去进行信息的修改** 

     (1）应用程序先发起一个get请求，获取到document，展示到前台界面，供用户查看和修改

    （2）用户在前台界面修改数据，发送到后台
    （3）后台代码，会将用户修改的数据在内存中进行执行，然后封装好修改后的全量数据
    （4）然后发送PUT请求，到es中，进行全量替换
    （5）es将老的document标记为deleted，然后重新创建一个新的document

####partial update

用POST命令进行修改 拼接参数  **_update**   ,称为partial update  

```json
post /index/type/id/_update 
{
   "doc": {
      "要修改的少数几个field即可，不需要全量的数据"
   }
}

```



~~~json
POST /ecommerce/product/1/_update
{
"doc": {
    "name": "jiaqiangban gaolujie yagao"
  }
}

~~~

### 删除
DELETE /ecommerce/product/1

## **elasticsearch的多种搜索方式**

### query String query

​	**适用于临时的在命令行使用一些工具，比如curl，快速的发出请求，来检索想要的信息；但是如果查询请求很复杂，是很难去构建的**

**在生产环境中，几乎很少使用query string search**

​	搜索命令: **GET /index/type/_search**

```json
{
  "took": 2,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "failed": 0
  },
  "hits": {
    "total": 4,
    "max_score": 1,
    "hits": [
      {
        "_index": "ecommerce",
        "_type": "product",
        "_id": "2",
        "_score": 1,
        "_source": {
          "name": "jiajieshi yagao",
          "desc": "youxiao fangzhu",
          "price": 25,
          "producer": "jiajieshi producer",
          "tags": [
            "fangzhu"
          ]
        }
      },
      {
        "_index": "ecommerce",
        "_type": "product",
        "_id": "4",
        "_score": 1,
        "_source": {
          "name": "special yagao",
          "desc": "special meibai",
          "price": 40,
          "producer": "special producer",
          "tags": [
            "special"
          ]
        }
      },
      {
        "_index": "ecommerce",
        "_type": "product",
        "_id": "1",
        "_score": 1,
        "_source": {
          "name": "gaolujie yagao",
          "desc": "gaoxiao meibai",
          "price": 30,
          "producer": "gaolujie producer",
          "tags": [
            "meibai",
            "fangzhu"
          ]
        }
      },
      {
        "_index": "ecommerce",
        "_type": "product",
        "_id": "3",
        "_score": 1,
        "_source": {
          "name": "zhonghua yagao",
          "desc": "meibai",
          "price": 40,
          "producer": "zhonghua producer",
          "tags": [
            "meibai"
          ]
        }
      }
    ]
  }
}

```

​	field意义:

​		 **took**:搜索耗时(毫秒)

​		**timed_out**：搜索是否超时

​    		 **_shards**：数据拆成了5个分片，所以对于搜索请求，会打到所有的primary shard（或者是它的某个replica shard也可以）

​		**hits.total**：查询结果的数量。

​		**hits.max_score:**  **score**的含义，就是document对于一个search的相关度的匹配分数，越相关，就越匹配，分数也高

​		**hits.hits**：包含了匹配搜索的document的详细数据

###query DSL

​	**DSL：Domain Specified Language，特定领域的语言**.

​	**更加适合生产环境的使用，可以构建复杂的查询**

​	http request body：请求体，可以用json的格式来构建查询语法，比较方便，**可以构建各种复杂的语法**，比query string search强大多了

 语法命令: 		

+ 查询所有的 query中的 match_all命令

```json
GET /index_name/type_name/_search
	{
		"query":
			{
                "match_all":{}
			}
	}

```

+ 查询某个字段包含某个数据,如name中包含yagao的.query中的match命令

  ```json
  GET /ecommerce/product/_search
  {
      "query":{
          "match":{
              "name":"yagao"
          }
      }
  }
  ```

+ 查询某个字段包含某个数据,并且按照一定条件排序.

  ```json
  GET /ecommerce/product/_search
  {
      "query":{
          "match":{
              "name":"yagao"
          }
      },
      "sort":[
          {
              "price":"desc"
          }
      ]
  }
  ```


+ 分页查询		每页显示一条数据的查询  此命令是查询第三页页数据.

  ``` json
  GET /ecommerce/product/_search
  {
  	"query":{
  		"match_all":{}
  	},
  	"from":2,		##第三页
  	"size":1		##每页1条
  }
  ```

+ 指定查询某些字段,不需要的不查询

  ```
  GET /ecommerce/product/_search
  {
      "queru":{
          "match_all":{}
      },
      "source":["name","price"]
  }
  ```


###query filter

​	过滤查询

```
GET /ecommerce/product/_search
{
    "query":{
        "bool":{ 				##可以封装多个查询条件
            "must":{
                "match":{
                    "name":"yagao"
                }
            },
            "filter":{
                "range":{
                    "price":{
                        "gt":"25" 		##gt api
                    }
                }
            }
        }
    }
}
```



### full-text search

​	**全文检索**  全文检索会将输入的搜索串拆解开来，去倒排索引里面去一一匹配，只要能匹配上任意一个拆解后的单词，就可以作为结果返回

```json
GET /ecommerce/product/_search
{
	"query" : {
		"match" : {
			"producer" : "producer yagao"
		}
	}
}
```



### phrase search(短语搜索_精确)

​	phrase  search 跟全文检索相反,要求输入的搜索串，必须在指定的字段文本中，完全包含一模一样的，才可以算匹配，才能作为结果返回

```json
GET /ecommerce/product/_search
{
    "query":{
        "match_phrase":{
            "producer":"yagao producer"
        }
    }
}
```



### highlight search(高亮搜索结果)

​	指定搜索结果高亮

```
GET /ecommerce/product/_search
{
    "query":{
        "match":{
            "producer":"producer"
        }
    },
    "highlight":{
        "fields":{
            "producer":{}
        }
    }
}
```

###聚合分析(group by+avg+sort等)

​	嵌套聚合,下钻分析,聚合分析

###第一个分析需求：计算每个tag下的商品数量

```json
GET /ecommerce/product/_search
{
    "size":0  					## 不用返回对哪些数据聚合
    "aggs":{
        "group_by_tags":{      ## group_by_tags 可以随意命名
            "terms":{          ## 按照指定的field 进行分组
            	"field":"tags"
        	 }
        }
    }
}
```



返回结果的原因 fielddata默认为false 需要设置为true才能将fielddata 以正排索引 加载到内存中.

![fielddata=true](D:\学习资料\es\笔记\fielddata=true.png)

####设置fielddata 属性为 true

```json
PUT /ecommerce/_mapping/product
{
  "properties": {
    "tags": {
      "type": "text",
      "fielddata": true
    }
  }
}
```



###第二个分析需求 对名称中包含yagao的商品，计算每个tag下的商品数量

​	先搜索后分析

```json
GET /ecommerce/product/_search
{
    "size":0  					## 不用返回对哪些数据聚合
    "query":{
    	"match"{
    		"name":"yagao"
		}
	 },
    "aggs":{
        "group_by_tags":{      ## group_by_tags 可以随意命名
            "terms":{          ## 按照指定的field 进行分组
            	"field":"tags"
        	 }
        }
    }
}
```

###第三个分析需求 先分组,再算每组的平均值,计算每个tag下商品的平均价格

```json
GET /ecommerce/product/_search
{ 	
	"size":0,
	"aggs":{
        "group_by_tags":{      ## group_by_tags 可以随意命名
            "terms":{          ## 按照指定的field 进行分组
            	"field":"tags"
        	 },
        	 "aggs":{
                 ""avg_price"{
                     "avg":{
                         "field":"price"
                     }
                 }
        	 }
        }
    }
}
```



### 第四个分析需求 计算每个tag下商品的平均价格,并且按照平均价格排序

```json
GET /ecommerce/product/_search
{
    "size":0,
 	"aggs":{
        "group_by_tags":{
            "terms":{
                "field":"tags"
                "order":{
                    "avg_price":"desc"
                }
            },
          "aggs":{
              "avg_price":{
                  "avg":{
                     "field":"price"
                  }
              }
          }
        }
 	}
}
```

### 第五个分析需求 按照指定的价格范围区间进行分组,然后在每组内再按照tag进行分组,最后再计算每组的平均价格

```json
GET /ecommerce/product/_search
{
	"size":0,
    "aggs":{
        "group_by_price":{
            "range":{
                "field":"price",
                "ranges":[
                    {
                       "from":0,
                       "to":20
                    },
                    {
                       "from":20,
                        "to":40
                    },
                    {
                       "from":40,
                        "to":60
                    }
                ]
            },
            "aggs":{
                "group_by_tags":{
                    "terms":{
                        "field":"tags"
                    }
                },
                "aggs":{
                    "avg_price":{
                        "avg":{
                            "field":"price"
                        }
                    }
                }
            }
        }
    }
}
```



## elasticsearch 基础分布式介绍

1、Elasticsearch对复杂分布式机制的透明隐藏特性

​	Elasticsearch是一套分布式的系统，分布式是为了应对大数据量
​	隐藏了复杂的分布式机制

​	分片机制:创建的document自动插入到集群中去,不用关心数据怎么进行分片的,数据会分配到哪一个shard.

​	cluster discovery 集群发现  内网中新开启elasticsearch进程,会自动发现集群并加入,同时会接受数据.

​	shard负载均衡   elasticsearch 会自动均匀的将shard分配到节点上去,保持每个节点的负载均衡.

​	shard副本,请求路由,集群扩容,shard重分配,这些都由elasticsearch自动维护.

2、Elasticsearch的垂直扩容与水平扩容

​	垂直扩容：采购更强大的服务器，成本非常高昂，而且会有瓶颈.

​	水平扩容：业界经常采用的方案，采购越来越多的普通服务器，性能比较一般，但是很多普通服务器组织在一起，就能构成强大的计算和存储能力

3、增减或减少节点时的数据rebalance

4、master节点

​	master节点:创建或删除索引,增加或删除节点.

5、节点对等的分布式架构

（1）节点对等，每个节点都能接收所有的请求
（2）自动请求路由
（3）响应收集

## shard&replica机制

（1）index包含多个或者1个shard

​	1个index (一个库) 里可能有1个或多个shard,看机器的存储能力,数据量少时一台机器1个shard(无replica shard也可以)就能存储.数据量大时,多台机器多个shard,做负载均衡.'

​	如: 1个index里有5T数据,一台服务器只能存储1T数据,可以配置5台服务器,可以设置5个shard,每台服务器1个shard,1T数据.(也可10台机器做容错和负载均衡)

（2）每个shard都是一个最小工作单元，承载部分数据，lucene实例，完整的建立索引和处理请求的能力
（3）增减节点时，shard会自动在nodes中负载均衡
（4）primary shard和replica shard，每个document肯定只存在于某一个primary shard以及其对应的replica shard中，不可能存在于多个primary shard
（5）replica shard是primary shard的副本，负责容错，以及承担读请求负载
（6）**primary shard的数量在创建索引的时候就固定了，replica shard的数量可以随时修改**
（7）**primary shard的默认数量是5，replica默认是1(每个primary shard 默认1个replicashard)，默认有10个shard，5个primary shard，5个replica shard**
（8）primary shard不能和自己的replica shard放在同一个节点上（否则节点宕机，primary shard和副本都丢失，起不到容错的作用），但是可以和其他primary shard的replica shard放在同一个节点上.

## Shard数调优

https://www.cnblogs.com/richaaaard/p/5231905.html

##Elasticsearch容错机制：master选举，replica容错，数据恢复

https://blog.csdn.net/zx711166/article/details/81980929

##Elasticsearch的核心元数据

###1、_index元数据

（1）代表一个document存放在哪个index中
（2）类似的数据放在一个索引，非类似的数据放不同索引：product index（包含了所有的商品），sales index（包含了所有的商品销售数据），inventory index（包含了所有库存相关的数据）。如果你把比如product，sales，human resource（employee），全都放在一个大的index里面，比如说company index，不合适的。

####**非类似的数据放不同index的目的:**

​	**增加系统的高可用**.假如某电商网站,销售数据如果和商品数据放到1个index里(销售数据和商品数据量都很大),当有一个需求:分析销售数据(假如这个需求查询和分析需要5分钟),此时用户在搜索商品,那么此时会造成这个index阻塞(查询和分析需求并发),查询速度变慢,那么用户体验很差.因此不同类型的数据放不同的index可以提高系统的可用性.(每个shard都是一个lunce,1个index有多个shard或者1个shard,如果shard执行的需求过多会造成一定的阻塞).

​		

（3）index中包含了很多类似的document：类似是什么意思，其实指的就是说，这些document的fields很大一部分是相同的，你说你放了3个document，每个document的fields都完全不一样，这就不是类似了，就不太适合放到一个index里面去了。
（4）索引名称必须是小写的，不能用下划线开头，不能包含逗号：product，website，blog

###2、_type元数据

（1）代表document属于index中的哪个类别（type）
（2）一个索引通常会划分为多个type，逻辑上对index中有些许不同的几类数据进行分类：因为一批相同的数据，可能有很多相同的fields，但是还是可能会有一些轻微的不同，可能会有少数fields是不一样的，举个例子，就比如说，商品，可能划分为电子商品，生鲜商品，日化商品，等等。
（3）type名称可以是大写或者小写，但是同时不能用下划线开头，不能包含逗号

###3、_id元数据

（1）代表document的唯一标识，与index和type一起，可以唯一标识和定位一个document
（2）我们可以手动指定document的id（put /index/type/id），也可以不指定，由es自动为我们创建一个id

## Elasticsearch的document 全量替换,强制创建,删除机制

### 全量替换

​	全量替换: PUT命令 PUT 所有字段.语法和创建document一样,如果document id 不存在就会创建,如果id存在,就会全量替换,替换document的json字符串.

​	被替换的document不会被立即物理删除,会被标记为deleted状态.当建立越来越多的同一id的document或者不同id的document,es会在合适的时机将标记为deleted的document删除.

### 强制创建

强制创建: PUT 命令.

对同一个document id的document只想创建,不想全量替换. op_type=create   _create

```PUT /index/type/id?op_type=create，PUT /index/type/id/_create
PUT /index/type/id?op_type=create，PUT /index/type/id/_create
```

### 删除机制

**删除机制:lazy delete**

删除: DELETE /index/type/id 

**lazy delete:不会立即删除,会被标记为deleted,当数据越来越多的时候进行删除.**

## elasticsearch 中document自动创建id

根据应用情况来说，是否满足手动指定document id的前提：

+ 从某些其他的系统中，导入一些数据到es时，可用原系统的数据的唯一标识，作为es中document的id。

+ 系统主要的数据存储就是es一种,可以采取让es自动生成id的方式。POST命令

+ 自动生成的id，长度为20个字符，URL安全，base64编码，GUID，分布式系统并行生成时不可能会发生冲突

  ```json
  POST /index/type
  {
  	"json字符串"    
  }
  ```


## elasticsearch基于乐观锁的并发修改特性(基于_version)

特性:并发时 先修改后到,后修改先到,会把先修改的忽略掉. 如果先修改的先到,后修改后到,正常修改最后修改的(无论谁先到,底层知道谁先谁后修改.-基于版本号)

+ partial update内置乐观锁并发控制

+ retry策略是如何尝试修改的,  ``POST /index/type/id/_update?retry_on_conflc=5`

  ​	如果并发时会

+ 

## elasticsearch可以基于自己维护的version进行并发控制

 	**elasticsearch提供了一个功能,可以不依靠elasticsearch内部的版本号(_version)进行并发控制,可以基于自己维护的一个版本号来进行并发控制.**

​	假如自己的数据在mysql里也有一份，然后应用系统本身就维护了一个版本号，无论是什么自己生成的，程序控制的。这个时候，你进行乐观锁并发控制的时候，可能并不是想要用es内部的_version来进行控制，而是用你自己维护的那个version来进行控制。

?version=1&version_type=external

### _version(es内部维护的版本号)和 version_type(外部维护的版本号)的区别

​	**version_type=external，唯一的区别在于，_version，只有当你提供的version与es中的_version一模一样的时候，才可以进行修改，只要不一样，就报错；当version_type=external的时候，只有当你提供的version比es中的_version大的时候，才能完成修改**



## elasticsearch内置脚本(script)支持

###内置脚本 script

​	例如:

```json
##添加一测试数据
PUT /test_index/test_type/1
{
  "name":"love",
  "num":1,
  "tags":[]
}

##执行内置脚本
POST /test_index/test_type/1/_update
{
    "script":"ctx._source.num++"
}
```

​	**如果脚本复杂可以封装在groovy外部脚本里**

### 外部脚本 groovy

​	在elasticsearch的 安装根目录\config\scripts   在scripts文件夹里创建  .groovy的文件.在里面 写上脚本语言.

​	例如:在scripts文件夹里创建名称为 test-add-tags.groovy的文件,里面写上`` ctx_source.tags=new_tag``

​	然后在kibana Dev Tools 中输入

```json
POST /test_index/test_type/1/_update
{
  "script": {
    "lang": "groovy",
    "file": "test-add-tags",  ##注意名字 对应文件创建的名字不要有空格
    "params": {
      "new_tag":"add_tag"  	 ##field为 new_tag 要和文件中命名相同
    }
  }
}
```

### upsert操作

​	如果指定的document不存在，就执行upsert中的初始化操作；如果指定的document存在，就执行doc或者script指定的partial update操作

注意事项:``can't provide both script and doc``**script和doc不能一块使用**

```json
POST test_index/test_type/1/_update
{
 # "doc": {
 #   "name":"hanhan"     # script和doc不能一块使用
 # }
 # , 
  "script": "ctx._source.num++",
    
  "upsert": {
      "name":"love",
      "num":1,
      "tags":[]
  }

}
```

​	
=======
ElasticSearch
=================


## elasticsearch介绍

### elasticsearch是什么

​	1.分布式的搜索引擎和数据分析引擎.

​	2.全文检索，结构化检索，数据分析

​	3.对海量数据进行近实时（秒级别）处理

+ 分布式:Es自动可以将海量数据分散到多台服务器去存储和检索。
+ 海量数据的处理：分布式解决了海量数据的处理。
+ vs Lunce

### elasticsearch具备功能

​	1.自动维护数据到多个节点的索引的建立，还有搜索请求分布到多个节点的执行。

​	2.自动维护数据的冗余副本，保证一些机器宕机了，不会丢失数据.

​	3.封装了更多高级的功能，给我们提供更多的高级支持，让我们快速的开发应用，开发更高级的应用；复杂的搜索功能，聚合分析的功能。基于地理位置的搜索（距离我当前位置1公里以内的超市）

### elasticsearch的特点

​	1.   可以作为一个大型分布式集群（数百台服务器）技术，处理PB级数据，服务大公司。也可以运行在单机上，服务小公司。

​	2.   Es不是什么新技术，主要是将全文检索、数据分析和分布式技术合并在了一起，才形成了独一无二的Es。

​	3.   对用户而言，是开箱即用的。

​	4.   提供了数据库不能提供的很多功能。

### elasticsearch使用场景

+ 维基百科 es实现

+ 某些新闻网站： 用户行为日志数据（点赞，评论等），社交网络数据，数据分析等
+ Github 搜索上千亿行代码
+ 商品价格监控网站，

## elasticsearch的核心概念

### **NearRealtime** 

​	**NRT** 近实时 秒级（写入到可以搜索到写入的数据时间毫秒级）。 

### **Cluster** 

​	**集群，**包含多个节点，每个节点属于哪个集群是通过一个配置（集群名称，默认是elasticsearch）来决定的，对于中小型应用来说，刚开始一个集群就一个节点很正常 

### **Node**

​	**节点**，集群中的一个节点，节点也有一个名称（默认是随机分配的），节点名称很重要（在执行运维管理操作的时候），默认节点会去加入一个名称为“elasticsearch”的集群，如果直接启动一堆节点，那么它们会自动组成一个elasticsearch集群，当然一个节点也可以组成一个elasticsearch集群 

### **Document&&field**

​	**文档,&&字段**

​	document是es中的最小数据单元，一个document可以是一条客户数据，一条商品分类数据，一条订单数据，通常用JSON数据结构表示，每个index下的type中，都可以去存储多个document。一个document里面有多个field，每个field就是一个数据字段。如下：有5个field。

~~~json
{
  "product_id": "1",
  "product_name": "高露洁牙膏",
  "product_desc": "高效美白",
  "category_id": "2",
  "category_name": "日化用品"
}

~~~



### **Index**

​	**索引**，包含一堆有相似结构的文档数据，比如可以有一个客户索引，商品分类索引，订单索引，索引有一个名称。一个index包含很多document，一个index就代表了一类类似的或者相同的document。比如说建立一个product index，商品索引，里面可能就存放了所有的商品数据，所有的商品document。 

### **Type**

​	**类型**，每个索引里都可以有一个或多个type，type是index中的一个逻辑数据分类，一个type下的document，都有相同的field，比如博客系统，有一个索引，可以定义用户数据type，博客数据type，评论数据type。商品index，里面存放了所有的商品数据，商品document.

但是商品分很多种类，每个种类的document的field可能不太一样，比如说电器商品，可能还包含一些诸如售后时间范围这样的特殊field；生鲜商品，**还包含一些诸如生鲜保质期之类的特殊field**

type，日化商品type，电器商品type，生鲜商品type

日化商品type：product_id，product_name，product_desc，category_id，category_name

电器商品type：product_id，product_name，product_desc，category_id，category_name，service_period

生鲜商品type：product_id，product_name，product_desc，category_id，category_name，eat_period

每一个type里面，都会包含一堆document

~~~json
{
  "product_id": "2",
  "product_name": "长虹电视机",
  "product_desc": "4k高清",
  "category_id": "3",
  "category_name": "电器",
  "service_period": "1年"
}

{
  "product_id": "3",
  "product_name": "基围虾",
  "product_desc": "纯天然，冰岛产",
  "category_id": "4",
  "category_name": "生鲜",
  "eat_period": "7天"
}

~~~

### **shard**

​	**单台机器无法存储大量数据，es可以将一个索引中的数据切分为多个shard，分布在多台服务器上存储。有了shard就可以横向扩展，存储更多数据，让搜索和分析等操作分布到多台服务器上去执行，提升吞吐量和性能（吞吐量如何测试）。每个shard都是一个lucene index。** 

### replica

​	**任何一个服务器随时可能故障或宕机，此时shard可能就会丢失，因此可以为每个shard创建多个replica副本。replica可以在shard故障时提供备用服务，保证数据不丢失，多个replica还可以提升搜索操作的吞吐量和性能。primary shard（建立索引时一次设置，设置后不能修改，不设置数量默认5个），replica shard（随时修改数量，默认1个），默认每个索引10个shard，5个primary shard，5个replica shard，最小的高可用配置，是2台服务器。** 

## elasticsearch vs 数据库

###elasticsearch和mysql的对比

| elasticsearch | 数据库 |
| ------------- | ------ |
| index         | 库     |
| type          | 表     |
| document      | 行     |
| field         | 字段   |

### 面向文档的搜索分析引擎 vs java面向对象时的操作数据库

　**对比可以明白es的document数据格式和数据库的关系型数据格式的区别**

####java	

+ 应用系统的数据结构都是面向对象的，复杂的 .
+ 对象数据存储到数据库中，只能拆解开来，变为扁平的多张表，每次查询的时候还得还原回对象格式，相当麻烦
+ java代码操作数据库一般这样写

~~~java
public class Employee {

  private String email;
  private String firstName;
  private String lastName;
  private EmployeeInfo info;
  private Date joinDate;

}

private class EmployeeInfo {
  
  private String bio; // 性格
  private Integer age;
  private String[] interests; // 兴趣爱好

}

EmployeeInfo info = new EmployeeInfo();
info.setBio("curious and modest");
info.setAge(30);
info.setInterests(new String[]{"bike", "climb"});

Employee employee = new Employee();
employee.setEmail("zhangsan@sina.com");
employee.setFirstName("san");
employee.setLastName("zhang");
employee.setInfo(info);
employee.setJoinDate(new Date());
~~~

​	employee对象：里面包含了Employee类自己的属性，还有一个EmployeeInfo对象

​	两张表：employee表，employee_info表，将employee对象的数据重新拆开来，变成Employee数据和		EmployeeInfo数据

​	employee表：email，first_name，last_name，join_date，4个字段

​	employee_info表：bio，age，interests，3个字段；此外还有一个外键字段，比如employee_id，关联着	employee表

#### elasticsearch

+ ES是面向文档的，文档中存储的数据结构，与面向对象的数据结构是一样的，基于这种文档数据结构，es可以提供复杂的索引，全文检索，分析聚合等功能.
+ es的document用json数据格式来表达.

~~~
{
    "email":      "zhangsan@sina.com",
    "first_name": "san",
    "last_name": "zhang",
    "info": {
        "bio":         "curious and modest",
        "age":         30,
        "interests": [ "bike", "climb" ]
    },
    "join_date": "2017/01/01"
}
~~~



## elasticsearch的安装和启动

**1、安装JDK，elasticsearch5.2.0至少需要1.8.0_73以上版本（检查jdk版本）**

2、下载和解压缩Elasticsearch安装包（先对windows版本测）

3、启动Elasticsearch：bin\elasticsearch.bat，es本身特点之一就是开箱即用，如果是中小型应用，数据量少，操作不是很复杂，直接启动就可以用了

**4**、检查ES是否启动成功：http://localhost:9200/?pretty**

​	打开链接返回的json数据

name: node名称

cluster_name: 集群名称（默认的集群名称就是elasticsearch）

version.number: 5.2.0，es版本号

```json
{
  "name" : "4onsTYV",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "nKZ9VK_vQdSQ1J0Dx9gx1Q",
  "version" : {
    "number" : "5.2.0",
    "build_hash" : "24e05b9",
    "build_date" : "2017-01-24T19:52:35.800Z",
    "build_snapshot" : false,
    "lucene_version" : "6.4.0"
  },
  "tagline" : "You Know, for Search"

}
```

 

5、修改集群名称：elasticsearch.yml

6、下载和解压缩Kibana安装包，使用里面的开发界面，去操作elasticsearch，作为学习es知识点的一个主要的界面入口

7、启动Kibana：bin\kibana.bat

**8**、**进入Kibana界面 http://localhost:5601**  选择Dev Tools

9、在Dev Tools 的控制台输入 GET _cluster/health　查看集群的健康状况

## elasticsearch集群管理

### **快速检查集群的健康状况** 

​		**es提供了一套api，叫做cat api，可以查看es中各种各样的数据**

​		**GET /_cat/health?v**

#### green、yellow、red健康状态?

​	**green**：每个索引的primary shard和replica shard都是active状态的

​	**yellow**：每个索引的primary shard都是active状态的，但是部分replica shard不是active状态，处于不可用的状态

​	**red**：不是所有索引的primary shard都是active状态的，部分索引有数据丢失了

#### **为什么刚创建现在会处于一个yellow状态** ?

​	我们现在就一个笔记本电脑，就启动了一个es进程，相当于就只有一个node。现在es中有一个index，就是kibana自己内置建立的index。由于默认的配置是给每个index分配5个primary shard和5个replica shard，而且primary shard和replica shard不能在同一台机器上（为了容错）。现在kibana自己建立的index是1个primary shard和1个replica shard。当前就一个node，所以只有1个primary shard被分配了和启动了，但是一个replica shard没有第二台机器去启动。

​	做一个小实验：此时只要启动第二个es进程，就会在es集群中有2个node，然后那1个replica shard就会自动分配过去，然后cluster status就会变成green状态。（**一台机器会自动分配，不同机器如何分配replica shard?**）

### **快速查看集群中有哪些索引** 

​	GET /_cat/indices?v

### **简单的索引操作**

​	增加索引: PUT /index_name?pretty

​	删除索引: DELETE/index_name?pretty

## elasticsearch对document的CRUD

*举例:*

### 增

​	格式:  PUT /index_name/type_name/id

​		{

​			"json数据"

​		}

~~~json
PUT /ecommerce/product/1
{
    "name" : "gaolujie yagao",
    "desc" :  "gaoxiao meibai",
    "price" :  30,
    "producer" :      "gaolujie producer",
    "tags": [ "meibai", "fangzhu" ]
}

{
  "_index": "ecommerce",
  "_type": "product",
  "_id": "1",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "created": true
}

PUT /ecommerce/product/2
{
    "name" : "jiajieshi yagao",
    "desc" :  "youxiao fangzhu",
    "price" :  25,
    "producer" :      "jiajieshi producer",
    "tags": [ "fangzhu" ]
}
~~~



​	**es会自动建立index和type，不需要提前创建，而且es默认会对document每个field都建立倒排索引，让其可以被搜索** 

### 查询

​	GET /index_name/type_name/id

*GET /ecommerce/product/1* 返回数据为:

```json
{
  "_index": "ecommerce",
  "_type": "product",
  "_id": "1",
  "_version": 1,
  "found": true,
  "_source": {
    "name": "gaolujie yagao",
    "desc": "gaoxiao meibai",
    "price": 30,
    "producer": "gaolujie producer",
    "tags": [
      "meibai",
      "fangzhu"
    ]
  }
}

```



### 更新

####传递所有字段

+ PUT命令 PUT 所有字段


  ~~~json
  PUT /ecommerce/product/1
  {
      "name" : "jiaqiangban gaolujie yagao",
      "desc" :  "gaoxiao meibai",
      "price" :  30,
      "producer" :      "gaolujie producer",
      "tags": [ "meibai", "fangzhu" ]
  } 
  ~~~

+ **替换方式有一个不好，即使必须带上所有的field，才能去进行信息的修改** 

     (1）应用程序先发起一个get请求，获取到document，展示到前台界面，供用户查看和修改

    （2）用户在前台界面修改数据，发送到后台
    （3）后台代码，会将用户修改的数据在内存中进行执行，然后封装好修改后的全量数据
    （4）然后发送PUT请求，到es中，进行全量替换
    （5）es将老的document标记为deleted，然后重新创建一个新的document

####partial update

用POST命令进行修改 拼接参数  **_update**   ,称为partial update  

```json
post /index/type/id/_update 
{
   "doc": {
      "要修改的少数几个field即可，不需要全量的数据"
   }
}

```



~~~json
POST /ecommerce/product/1/_update
{
"doc": {
    "name": "jiaqiangban gaolujie yagao"
  }
}

~~~

### 删除
DELETE /ecommerce/product/1

## **elasticsearch的多种搜索方式**

### query String query

​	**适用于临时的在命令行使用一些工具，比如curl，快速的发出请求，来检索想要的信息；但是如果查询请求很复杂，是很难去构建的**

**在生产环境中，几乎很少使用query string search**

​	搜索命令: **GET /index/type/_search**

```json
{
  "took": 2,
  "timed_out": false,
  "_shards": {
    "total": 5,
    "successful": 5,
    "failed": 0
  },
  "hits": {
    "total": 4,
    "max_score": 1,
    "hits": [
      {
        "_index": "ecommerce",
        "_type": "product",
        "_id": "2",
        "_score": 1,
        "_source": {
          "name": "jiajieshi yagao",
          "desc": "youxiao fangzhu",
          "price": 25,
          "producer": "jiajieshi producer",
          "tags": [
            "fangzhu"
          ]
        }
      },
      {
        "_index": "ecommerce",
        "_type": "product",
        "_id": "4",
        "_score": 1,
        "_source": {
          "name": "special yagao",
          "desc": "special meibai",
          "price": 40,
          "producer": "special producer",
          "tags": [
            "special"
          ]
        }
      },
      {
        "_index": "ecommerce",
        "_type": "product",
        "_id": "1",
        "_score": 1,
        "_source": {
          "name": "gaolujie yagao",
          "desc": "gaoxiao meibai",
          "price": 30,
          "producer": "gaolujie producer",
          "tags": [
            "meibai",
            "fangzhu"
          ]
        }
      },
      {
        "_index": "ecommerce",
        "_type": "product",
        "_id": "3",
        "_score": 1,
        "_source": {
          "name": "zhonghua yagao",
          "desc": "meibai",
          "price": 40,
          "producer": "zhonghua producer",
          "tags": [
            "meibai"
          ]
        }
      }
    ]
  }
}

```

​	field意义:

​		 **took**:搜索耗时(毫秒)

​		**timed_out**：搜索是否超时

​    		 **_shards**：数据拆成了5个分片，所以对于搜索请求，会打到所有的primary shard（或者是它的某个replica shard也可以）

​		**hits.total**：查询结果的数量。

​		**hits.max_score:**  **score**的含义，就是document对于一个search的相关度的匹配分数，越相关，就越匹配，分数也高

​		**hits.hits**：包含了匹配搜索的document的详细数据

###query DSL

​	**DSL：Domain Specified Language，特定领域的语言**.

​	**更加适合生产环境的使用，可以构建复杂的查询**

​	http request body：请求体，可以用json的格式来构建查询语法，比较方便，**可以构建各种复杂的语法**，比query string search强大多了

 语法命令: 		

+ 查询所有的 query中的 match_all命令

```json
GET /index_name/type_name/_search
	{
		"query":
			{
                "match_all":{}
			}
	}

```

+ 查询某个字段包含某个数据,如name中包含yagao的.query中的match命令

  ```json
  GET /ecommerce/product/_search
  {
      "query":{
          "match":{
              "name":"yagao"
          }
      }
  }
  ```

+ 查询某个字段包含某个数据,并且按照一定条件排序.

  ```json
  GET /ecommerce/product/_search
  {
      "query":{
          "match":{
              "name":"yagao"
          }
      },
      "sort":[
          {
              "price":"desc"
          }
      ]
  }
  ```


+ 分页查询		每页显示一条数据的查询  此命令是查询第三页页数据.

  ``` json
  GET /ecommerce/product/_search
  {
  	"query":{
  		"match_all":{}
  	},
  	"from":2,		##第三页
  	"size":1		##每页1条
  }
  ```

+ 指定查询某些字段,不需要的不查询

  ```
  GET /ecommerce/product/_search
  {
      "queru":{
          "match_all":{}
      },
      "source":["name","price"]
  }
  ```


###query filter

​	过滤查询

```
GET /ecommerce/product/_search
{
    "query":{
        "bool":{ 				##可以封装多个查询条件
            "must":{
                "match":{
                    "name":"yagao"
                }
            },
            "filter":{
                "range":{
                    "price":{
                        "gt":"25" 		##gt api
                    }
                }
            }
        }
    }
}
```



### full-text search

​	**全文检索**  全文检索会将输入的搜索串拆解开来，去倒排索引里面去一一匹配，只要能匹配上任意一个拆解后的单词，就可以作为结果返回

```json
GET /ecommerce/product/_search
{
	"query" : {
		"match" : {
			"producer" : "producer yagao"
		}
	}
}
```



### phrase search(短语搜索_精确)

​	phrase  search 跟全文检索相反,要求输入的搜索串，必须在指定的字段文本中，完全包含一模一样的，才可以算匹配，才能作为结果返回

```json
GET /ecommerce/product/_search
{
    "query":{
        "match_phrase":{
            "producer":"yagao producer"
        }
    }
}
```



### highlight search(高亮搜索结果)

​	指定搜索结果高亮

```
GET /ecommerce/product/_search
{
    "query":{
        "match":{
            "producer":"producer"
        }
    },
    "highlight":{
        "fields":{
            "producer":{}
        }
    }
}
```

# 聚合分析(group by+avg+sort等)

​	嵌套聚合,下钻分析,聚合分析

###第一个分析需求：计算每个tag下的商品数量

```json
GET /ecommerce/product/_search
{
    "size":0  					## 不用返回对哪些数据聚合
    "aggs":{
        "group_by_tags":{      ## group_by_tags 可以随意命名
            "terms":{          ## 按照指定的field 进行分组
            	"field":"tags"
        	 }
        }
    }
}
```



返回结果的原因 fielddata默认为false 需要设置为true才能将fielddata 以正排索引 加载到内存中.

![fielddata=true](D:\学习资料\es\笔记\fielddata=true.png)

####设置fielddata 属性为 true

```json
PUT /ecommerce/_mapping/product
{
  "properties": {
    "tags": {
      "type": "text",
      "fielddata": true
    }
  }
}
```



###第二个分析需求 对名称中包含yagao的商品，计算每个tag下的商品数量

​	先搜索后分析

```json
GET /ecommerce/product/_search
{
    "size":0  					## 不用返回对哪些数据聚合
    "query":{
    	"match"{
    		"name":"yagao"
		}
	 },
    "aggs":{
        "group_by_tags":{      ## group_by_tags 可以随意命名
            "terms":{          ## 按照指定的field 进行分组
            	"field":"tags"
        	 }
        }
    }
}
```

###第三个分析需求 先分组,再算每组的平均值,计算每个tag下商品的平均价格

```json
GET /ecommerce/product/_search
{ 	
	"size":0,
	"aggs":{
        "group_by_tags":{      ## group_by_tags 可以随意命名
            "terms":{          ## 按照指定的field 进行分组
            	"field":"tags"
        	 },
        	 "aggs":{
                 ""avg_price"{
                     "avg":{
                         "field":"price"
                     }
                 }
        	 }
        }
    }
}
```



### 第四个分析需求 计算每个tag下商品的平均价格,并且按照平均价格排序

```json
GET /ecommerce/product/_search
{
    "size":0,
 	"aggs":{
        "group_by_tags":{
            "terms":{
                "field":"tags"
                "order":{
                    "avg_price":"desc"
                }
            },
          "aggs":{
              "avg_price":{
                  "avg":{
                     "field":"price"
                  }
              }
          }
        }
 	}
}
```

### 第五个分析需求 按照指定的价格范围区间进行分组,然后在每组内再按照tag进行分组,最后再计算每组的平均价格

```json
GET /ecommerce/product/_search
{
	"size":0,
    "aggs":{
        "group_by_price":{
            "range":{
                "field":"price",
                "ranges":[
                    {
                       "from":0,
                       "to":20
                    },
                    {
                       "from":20,
                        "to":40
                    },
                    {
                       "from":40,
                        "to":60
                    }
                ]
            },
            "aggs":{
                "group_by_tags":{
                    "terms":{
                        "field":"tags"
                    }
                },
                "aggs":{
                    "avg_price":{
                        "avg":{
                            "field":"price"
                        }
                    }
                }
            }
        }
    }
}
```



## elasticsearch 基础分布式介绍

1、Elasticsearch对复杂分布式机制的透明隐藏特性

​	Elasticsearch是一套分布式的系统，分布式是为了应对大数据量
​	隐藏了复杂的分布式机制

​	分片机制:创建的document自动插入到集群中去,不用关心数据怎么进行分片的,数据会分配到哪一个shard.

​	cluster discovery 集群发现  内网中新开启elasticsearch进程,会自动发现集群并加入,同时会接受数据.

​	shard负载均衡   elasticsearch 会自动均匀的将shard分配到节点上去,保持每个节点的负载均衡.

​	shard副本,请求路由,集群扩容,shard重分配,这些都由elasticsearch自动维护.

2、Elasticsearch的垂直扩容与水平扩容

​	垂直扩容：采购更强大的服务器，成本非常高昂，而且会有瓶颈.

​	水平扩容：业界经常采用的方案，采购越来越多的普通服务器，性能比较一般，但是很多普通服务器组织在一起，就能构成强大的计算和存储能力

3、增减或减少节点时的数据rebalance

4、master节点

​	master节点:创建或删除索引,增加或删除节点.

5、节点对等的分布式架构

（1）节点对等，每个节点都能接收所有的请求
（2）自动请求路由
（3）响应收集

## shard&replica机制

（1）index包含多个或者1个shard

​	1个index (一个库) 里可能有1个或多个shard,看机器的存储能力,数据量少时一台机器1个shard(无replica shard也可以)就能存储.数据量大时,多台机器多个shard,做负载均衡.'

​	如: 1个index里有5T数据,一台服务器只能存储1T数据,可以配置5台服务器,可以设置5个shard,每台服务器1个shard,1T数据.(也可10台机器做容错和负载均衡)

（2）每个shard都是一个最小工作单元，承载部分数据，lucene实例，完整的建立索引和处理请求的能力
（3）增减节点时，shard会自动在nodes中负载均衡
（4）primary shard和replica shard，每个document肯定只存在于某一个primary shard以及其对应的replica shard中，不可能存在于多个primary shard
（5）replica shard是primary shard的副本，负责容错，以及承担读请求负载
（6）**primary shard的数量在创建索引的时候就固定了，replica shard的数量可以随时修改**
（7）**primary shard的默认数量是5，replica默认是1(每个primary shard 默认1个replicashard)，默认有10个shard，5个primary shard，5个replica shard**
（8）primary shard不能和自己的replica shard放在同一个节点上（否则节点宕机，primary shard和副本都丢失，起不到容错的作用），但是可以和其他primary shard的replica shard放在同一个节点上.

## Shard数调优

https://www.cnblogs.com/richaaaard/p/5231905.html

##Elasticsearch容错机制：master选举，replica容错，数据恢复

https://blog.csdn.net/zx711166/article/details/81980929

##Elasticsearch的核心元数据

###1、_index元数据

（1）代表一个document存放在哪个index中
（2）类似的数据放在一个索引，非类似的数据放不同索引：product index（包含了所有的商品），sales index（包含了所有的商品销售数据），inventory index（包含了所有库存相关的数据）。如果你把比如product，sales，human resource（employee），全都放在一个大的index里面，比如说company index，不合适的。

####**非类似的数据放不同index的目的:**

​	**增加系统的高可用**.假如某电商网站,销售数据如果和商品数据放到1个index里(销售数据和商品数据量都很大),当有一个需求:分析销售数据(假如这个需求查询和分析需要5分钟),此时用户在搜索商品,那么此时会造成这个index阻塞(查询和分析需求并发),查询速度变慢,那么用户体验很差.因此不同类型的数据放不同的index可以提高系统的可用性.(每个shard都是一个lunce,1个index有多个shard或者1个shard,如果shard执行的需求过多会造成一定的阻塞).

​		

（3）index中包含了很多类似的document：类似是什么意思，其实指的就是说，这些document的fields很大一部分是相同的，你说你放了3个document，每个document的fields都完全不一样，这就不是类似了，就不太适合放到一个index里面去了。
（4）索引名称必须是小写的，不能用下划线开头，不能包含逗号：product，website，blog

###2、_type元数据

（1）代表document属于index中的哪个类别（type）
（2）一个索引通常会划分为多个type，逻辑上对index中有些许不同的几类数据进行分类：因为一批相同的数据，可能有很多相同的fields，但是还是可能会有一些轻微的不同，可能会有少数fields是不一样的，举个例子，就比如说，商品，可能划分为电子商品，生鲜商品，日化商品，等等。
（3）type名称可以是大写或者小写，但是同时不能用下划线开头，不能包含逗号

###3、_id元数据

（1）代表document的唯一标识，与index和type一起，可以唯一标识和定位一个document
（2）我们可以手动指定document的id（put /index/type/id），也可以不指定，由es自动为我们创建一个id

## Elasticsearch的document 全量替换,强制创建,删除机制

### 全量替换

​	全量替换: PUT命令 PUT 所有字段.语法和创建document一样,如果document id 不存在就会创建,如果id存在,就会全量替换,替换document的json字符串.

​	被替换的document不会被立即物理删除,会被标记为deleted状态.当建立越来越多的同一id的document或者不同id的document,es会在合适的时机将标记为deleted的document删除.

### 强制创建

强制创建: PUT 命令.

对同一个document id的document只想创建,不想全量替换. op_type=create   _create

```PUT /index/type/id?op_type=create，PUT /index/type/id/_create
PUT /index/type/id?op_type=create，PUT /index/type/id/_create
```

### 删除机制

**删除机制:lazy delete**

删除: DELETE /index/type/id 

**lazy delete:不会立即删除,会被标记为deleted,当数据越来越多的时候进行删除.**

## elasticsearch 中document自动创建id

根据应用情况来说，是否满足手动指定document id的前提：

+ 从某些其他的系统中，导入一些数据到es时，可用原系统的数据的唯一标识，作为es中document的id。

+ 系统主要的数据存储就是es一种,可以采取让es自动生成id的方式。POST命令

+ 自动生成的id，长度为20个字符，URL安全，base64编码，GUID，分布式系统并行生成时不可能会发生冲突

  ```json
  POST /index/type
  {
  	"json字符串"    
  }
  ```


## elasticsearch基于乐观锁的并发修改特性(基于_version)

特性:并发时 先修改后到,后修改先到,会把先修改的忽略掉. 如果先修改的先到,后修改后到,正常修改最后修改的(无论谁先到,底层知道谁先谁后修改.-基于版本号)

+ partial update内置乐观锁并发控制

+ retry策略是如何尝试修改的,  ``POST /index/type/id/_update?retry_on_conflc=5`

  ​	如果并发时会

+ 

## elasticsearch可以基于自己维护的version进行并发控制

 	**elasticsearch提供了一个功能,可以不依靠elasticsearch内部的版本号(_version)进行并发控制,可以基于自己维护的一个版本号来进行并发控制.**

​	假如自己的数据在mysql里也有一份，然后应用系统本身就维护了一个版本号，无论是什么自己生成的，程序控制的。这个时候，你进行乐观锁并发控制的时候，可能并不是想要用es内部的_version来进行控制，而是用你自己维护的那个version来进行控制。

?version=1&version_type=external

### _version(es内部维护的版本号)和 version_type(外部维护的版本号)的区别

​	**version_type=external，唯一的区别在于，_version，只有当你提供的version与es中的_version一模一样的时候，才可以进行修改，只要不一样，就报错；当version_type=external的时候，只有当你提供的version比es中的_version大的时候，才能完成修改**



## elasticsearch内置脚本(script)支持

###内置脚本 script

​	例如:

```json
##添加一测试数据
PUT /test_index/test_type/1
{
  "name":"love",
  "num":1,
  "tags":[]
}

##执行内置脚本
POST /test_index/test_type/1/_update
{
    "script":"ctx._source.num++"
}
```

​	**如果脚本复杂可以封装在groovy外部脚本里**

### 外部脚本 groovy

​	在elasticsearch的 安装根目录\config\scripts   在scripts文件夹里创建  .groovy的文件.在里面 写上脚本语言.

​	例如:在scripts文件夹里创建名称为 test-add-tags.groovy的文件,里面写上`` ctx_source.tags=new_tag``

​	然后在kibana Dev Tools 中输入

```json
POST /test_index/test_type/1/_update
{
  "script": {
    "lang": "groovy",
    "file": "test-add-tags",  ##注意名字 对应文件创建的名字不要有空格
    "params": {
      "new_tag":"add_tag"  	 ##field为 new_tag 要和文件中命名相同
    }
  }
}
```

### upsert操作

​	如果指定的document不存在，就执行upsert中的初始化操作；如果指定的document存在，就执行doc或者script指定的partial update操作

注意事项:``can't provide both script and doc``**script和doc不能一块使用**

```json
POST test_index/test_type/1/_update
{
 # "doc": {
 #   "name":"hanhan"     # script和doc不能一块使用
 # }
 # , 
  "script": "ctx._source.num++",
    
  "upsert": {
      "name":"love",
      "num":1,
      "tags":[]
  }

}
```

​	