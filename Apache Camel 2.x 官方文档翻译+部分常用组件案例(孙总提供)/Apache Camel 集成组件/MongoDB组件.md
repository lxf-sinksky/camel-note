# MongoDB组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-12-19*
*相关连接:[http://camel.apache.org/mongodb.html](http://camel.apache.org/mongodb.html)*

## 概述

MongoDB是一种非常流行的NoSQL解决方案，而Camel MongoDB组件将骆驼与MongoDB集成在一起，允许您与MongoDB集合进行交互，既可以作为生产者(在集合上执行操作)，也可以作为消费者(从MongoDB集合中使用文档)。

基本上，NoSQL解决方案与传统的RDBMS(关系数据库管理系统)不同，因为它们不使用SQL作为查询语言，通常不提供类似于acid的事务行为或关系数据。MongoDB围绕文档的概念(不是office文档，而是在json/bson中定义的层次化数据)和集合。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-mongodb</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
mongodb:connectionBean?database=databaseName&collection=collectionName&operation=operationName[&moreOptions...]
```

## URI参数
MongoDB端点支持以下选项, 具体取决于它们是作为生产者还是作为消费者。

名称|默认值|描述|生产者|Tailable 游标消费者
----|----|----|----|----
database|none|必要的,这个端点将被绑定到的数据库的名称。除非启用了动态特性，并且设置了CamelMongoDbDatabase标头，否则所有操作都将被执行。|Yes|Yes
collection|none|必要的(除了getDbStats和命令操作之外),此端点将绑定到的集合的名称 (在指定的数据库中)。除非启用动态特性,并且设置了CamelMongoDbDatabase标头, 否则将对此数据库执行所有操作。|Yes|Yes
collectionIndex|none|Camel 2.12:在插入新集合时要创建一个可选的单一字段索引或复合索引。|Yes|No
operation|none|仅生产者：此端点将执行的操作的id。从以下内容中选择:查询操作: findById、 findOneByQuery、 findAll、count;写入操作:insert,save,update;删除操作:remove;其他操作: getDbStats、 getColStats、aggregate, command。|Yes|No
createCollection|true|确定是否在端点初始化期间在MongoDB数据库中自动创建集合（如果它尚不存在）。 如果这个选项是false，并且集合不存在，则会抛出一个初始化异常。|Yes|No
invokeGetLastError|false(行为可以从连接WriteConcern继承)|在骆驼2.16中删除:指示MongoDB Java驱动程序在每次调用之后调用getLastError（）。 MongoDB Java驱动程序2.7.2版中的默认行为是，只有网络错误才会导致操作失败，因为实际操作是在MongoDB服务器中异步执行的，而不保留客户端-以提高性能。 客户端可以通过在返回的WriteResult对象上显式调用getLastError（）或通过设置适当的WriteConcern来获得操作的真实结果。 如果后端操作尚未完成，客户端将阻塞，直到结果可用。 将此选项设置为true将使端点同步运行，并在基础操作失败时返回Exception。|Yes|No
writeConcern|none(驱动程序默认)|使用MongoDB的参数化值设置WriteConcern。请参阅WriteConcern.valueOf（String）。|Yes|No
writeConcernRef|none|设置注册表中存在的自定义WriteConcern。 指定bean名称。|Yes|No
readPreference|none|可用于camel 2.12.4、2.13.1 和 2.14.0:在连接上设置一个ReadPreference 。接受的值是由ReadPreference#valueOf()公共API支持的。目前, MongoDB-Java 驱动程序版本2.12.0 支持的值有:primary、 primaryPreferred、secondary、 secondaryPreferred和nearest的。|Yes|No
dynamicity|false|如果设置为true，则端点将检查传入消息的CamelMongoDbDatabase和CamelMongoDbCollection标头，如果存在任何标头，则将针对该特定操作覆盖目标集合和/或数据库。 如果不需要该功能，则默认设置为false以避免触发每个Exchange上的查找|Yes|No
writeResultAsHeader|false|可用于Camel 2.10.3和2.11：在写操作（保存，更新，插入等）中，不用用MongoDB返回的WriteResult对象替换主体，而是保持输入主体不变，并将WriteResult放在CamelMongoWriteResult标头（常量MongoDbConstants.WRITERESULT）。|Yes|No
outputType|DBObjectList for findAll DBObject for all other operations|适用于Camel 2.16：将生产者的输出转换为选择的类型：“DBObjectList”，“DBObject”或“DBCursor”：DBObjectList或DBCursor（可能对输出流有用）适用于findAll。DBObject适用于所有其他操作。|Yes|No
persistentTailTracking|false|为 Tailable 游标消费者启用或禁用持久跟踪。|No|Yes
persistentId|none|如果启用持续追踪，则为必需。 这个持续跟踪器的ID，将其记录与跟踪集合中的其余记录分开。|No|Yes
tailTrackingIncreasingField|none|如果启用持续追踪，则为必需。传入记录中的相关字段，它增加了属性，并将用于每次生成跟踪的跟踪光标。使用类型查询创建的游标将是(re):tailTrackIncreasingField> lastValue（其中lastValue可能从持久追踪中恢复）。 可以是Integer类型，Date类型，String类型等。注意：当前不支持点符号，所以这个字段应该在文档的顶层。|No|Yes
cursorRegenerationDelay|1000ms|在被MongoDB服务器(正常行为)结束之后，端点将等待多长时间来重新生成游标。|No|Yes
tailTrackDb|same as endpoint's|持久跟踪器将存储其运行时信息的数据库。|No|Yes
tailTrackCollection|camelTailTracking|持久性追踪器将在其上存储其运行时信息的集合。|No|Yes
tailTrackField|lastTrackingValue|持续跟踪器将存储最后一个跟踪值的字段。|No|Yes

## 示例

### 搭建MongoDB数据库环境

MongoDB安装教程链接：[http://www.runoob.com/mongodb/mongodb-window-install.html](http://www.runoob.com/mongodb/mongodb-window-install.html)

Robomongo图形化管理工具安装教程链接：[https://jingyan.baidu.com/article/9113f81b011ee72b3214c78d.html](https://jingyan.baidu.com/article/9113f81b011ee72b3214c78d.html)

#### 启动MongoDB

打开cmd命令窗口，从MongoDB目录的bin目录中执行mongod.exe文件，如下图所示。
![](https://i.imgur.com/BCb7wBI.png)

#### 启动Robomongo(连接MongoDB)

![](https://i.imgur.com/1ROMuVc.png)

### 示例一：查询数据

#### Blueprint DSL 配置

```xml
<!--连接实例connectionBean-->
<bean id="mongoClient" class="com.mongodb.MongoClient">
     <!--构造方法参数注入-->
	 <argument value="localhost:27017"/> 
</bean>
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	 <route>
		 <from uri="timer://foo?period=10s"/> 
         <!--mongoClient为引用的连接实例，database为数据库名，collection为集合名，operation=findAll指查询全部数据-->
		 <to uri="mongodb://mongoClient?database=changer&amp;collection=Test&amp;operation=findAll"/>
         <!--打印交换中的body,header-->
		 <to uri="class://com.shudi.databridge.test.DataSetProcessor"/>  	  
     </route>	
</camelContext>	
```

#### 示例运行

![](https://i.imgur.com/vmbRUy1.png)

### 示例二：添加数据

#### BeanProcessor类
处理器，添加正文内容

```java
package com.shudi.databridge.test;

import java.util.HashMap;
import java.util.Map;
import org.apache.camel.Exchange;
import org.apache.camel.Processor;
import org.bson.types.ObjectId;
import com.mongodb.BasicDBObject;

public class BeanProcessor implements Processor {
	
	@Override
	public void process(Exchange exchange){
        //ObjectId为数据标识，注意：参数必须为十六进制表示
        ObjectId objectId = new ObjectId("5a38d3d98eca542d3b9cea6c");
        //添加数据
		Map<String, Object> map = new HashMap<>();
		map.put("_id", objectId);
		map.put("name", "Apache Camel");
		map.put("age", "10");
		map.put("description", "Apache Camel是基于规则路由引擎的开源项目");
        //实例BasicDBObject，MongoDB组件会接收BasicDBObject实例作为数据源
		BasicDBObject basicDBObject = new BasicDBObject(map);
	    exchange.getIn().setBody(basicDBObject);  
	}
}
```

#### Blueprint DSL 配置

```xml
<!--连接实例connectionBean-->
<bean id="mongoClient" class="com.mongodb.MongoClient">
     <!--构造方法参数注入-->
	 <argument value="localhost:27017"/> 
</bean>
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	 <route>
		 <from uri="timer://foo?period=10s"/> 
         <!--添加正文数据-->
         <to uri="class://com.shudi.databridge.test.BeanProcessor"/> 
         <!--mongoClient为引用的连接实例，database为数据库名，collection为集合名，operation=save指保存数据操作-->
		 <to uri="mongodb://mongoClient?database=changer&amp;collection=Test&amp;operation=save"/>
         <!--打印交换中的body,header-->
		 <to uri="class://com.shudi.databridge.test.DataSetProcessor"/>  	  
     </route>	
</camelContext>	
```

#### 示例运行

![](https://i.imgur.com/J97UmcW.png)

![](https://i.imgur.com/CtBzHwq.png)

### 示例三：删除数据

#### BeanProcessor类
处理器，添加正文内容

```java
package com.shudi.databridge.test;

import java.util.HashMap;
import java.util.Map;
import org.apache.camel.Exchange;
import org.apache.camel.Processor;
import org.bson.types.ObjectId;
import com.mongodb.BasicDBObject;

public class BeanProcessor implements Processor {
	
	@Override
	public void process(Exchange exchange){
        //ObjectId为数据标识，注意：参数必须为十六进制表示
        ObjectId objectId = new ObjectId("5a38d3d98eca542d3b9cea6c");
        //添加数据
		Map<String, Object> map = new HashMap<>();
        //删除操作将会删除满足_id=ObjectId("5a38d3d98eca542d3b9cea6c")的数据
        //条件之间为 and 逻辑，当多个属性条件时，必须全部满足,才会删除相应数据。
		map.put("_id", objectId);
        //实例BasicDBObject，MongoDB组件会接收BasicDBObject实例作为数据源
		BasicDBObject basicDBObject = new BasicDBObject(map);
	    exchange.getIn().setBody(basicDBObject);  
	}
}
```

#### Blueprint DSL 配置

```xml
<!--连接实例connectionBean-->
<bean id="mongoClient" class="com.mongodb.MongoClient">
     <!--构造方法参数注入-->
	 <argument value="localhost:27017"/> 
</bean>
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	 <route>
		 <from uri="timer://foo?period=10s"/> 
         <!--添加正文数据-->
         <to uri="class://com.shudi.databridge.test.BeanProcessor"/> 
         <!--mongoClient为引用的连接实例，database为数据库名，collection为集合名，operation=remove指删除数据操作-->
		 <to uri="mongodb://mongoClient?database=changer&amp;collection=Test&amp;operation=remove"/>
         <!--打印交换中的body,header-->
		 <to uri="class://com.shudi.databridge.test.DataSetProcessor"/>  	  
     </route>	
</camelContext>	
```

>补充：MongoDB组件还支持消费者，消费者提供数据库游标的功能，解决重复访问数据效率问题。暂无示例

### 示例分析

MongoDB组件提供在路由中访问MongoDB数据库的能力。需注意数据源添加有自己的规则，必须遵循。


