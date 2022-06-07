# MongoDB GridFS组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-12-21*
*相关连接:[http://camel.apache.org/mongodb-gridfs.html](http://camel.apache.org/mongodb-gridfs.html)*

## 概述

通过MongoDB的GridFS系统发送和接收文件。
GridFS是MongoDB规范用于存储和检索大文件，如图片，音频文件，视频文件等。这是一种文件系统用来存储文件，但数据存储于MongoDB集合中。GridFS可以更好的存储大于16M的文件。
GridFS的划分一个文件分成块存储数据每个块在一个单独的文件，每个最大尺寸255K。
GridFS默认使用两个集合fs.files和fs.chunks存储该文件的元数据和块。每组块标识其唯一的_id ObjectID字段。fs.files切断作为父文件。fs.chunks文档files_id字段链接块到其父文件。

MongoDB GridFS相关资料链接：[http://www.runoob.com/mongodb/mongodb-gridfs.html](http://www.runoob.com/mongodb/mongodb-gridfs.html)

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-mongodb-gridfs</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

camel版本小于2.19
```
gridfs:connectionBean?database=databaseName&bucket=bucketName[&moreOptions...]
```

camel版本大于,等于2.19
```
mongodb-gridfs:connectionBean?database=databaseName&bucket=bucketName[&moreOptions...]
```

## URI参数

名称|默认值|描述|生产者|消费者
----|----|----|----|----
database|none|必要的,这个端点将被绑定到的数据库的名称,所有操作都将针对这个数据库执行。|Yes|Yes
bucket|fs|数据库中的GridFS Bucket的名称。默认值GridFS.DEFAULT_BUCKET="fs")。|Yes|Yes
operation|create|此端点将执行的操作的id。从以下内容中选择:查询操作: findOne、 listAll、count。写入操作:create。删除操作:remove|Yes|No
query|none|结合查询策略参数，创建用于搜索新文件的查询。|No|Yes
queryStrategy|TimeStamp|用于查找新文件的策略。可以是下列之一:TimeStamp-处理消费者启动后上传的文件。PersistentTimestamp-像TimeStamp一样，但是最后一个使用的时间戳被保存到一个集合中，所以当消费者重新启动时，它可以恢复到停止的地方。FileAttribute-查找没有给出属性的文件。在处理之后，它会添加属性。TimestampAndFileAttribute-找到比TimeStamp更新的文件，并且缺少属性。PersistentTimestampAndFileAttribute|No|Yes
persistentTSCollection|camel-timestamps|使用持久性时间戳，这是时间戳存储的集合。|No|Yes
persistentTSObject|camel-timestamp|使用持久性时间戳时，这是时间戳对象的对象ID。 每个消费者可以有自己的时间戳ID存储在一个共同的集合。|No|Yes
fileAttributeName|camel-processed|使用FileAttribute时，这是使用的属性的名称。 当文件即将被处理时，属性被设置为“处理”，然后在文件处理完成时被设置为“完成”。|No|Yes
delay|500(ms)|用于新文件的轮询GridFS之间的延迟。|No|Yes
initialDelay|1000(ms)|第一次轮询之前的最初延时。|No|Yes

## 示例

### 搭建MongoDB数据库环境

MongoDB数据库，Robomongo图形化管理工具安装请参照MongoDB组件文档。

### 示例一：写入文件

#### Blueprint DSL 配置

```xml
<!--连接实例connectionBean-->
<bean id="mongoClient" class="com.mongodb.MongoClient">
     <!--构造方法参数注入-->
	 <argument value="localhost:27017"/> 
</bean>
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	 <route>
		 <from uri="file:H:/Data?noop=true&fileName=9527.mp3"/> 
         <!--mongoClient为引用的连接实例，database为数据库名，bucket为集合前缀名，operation=create指写入文件-->
		 <to uri="mongodb-gridfs:mongo?database=changer&bucket=fs&operation=create"/>
         <!--打印交换中的body,header-->
		 <to uri="class://com.shudi.databridge.test.DataSetProcessor"/>  	  
     </route>	
</camelContext>	
```

#### 示例运行

![](https://i.imgur.com/GbPGlLz.png)

![](https://i.imgur.com/QxoYS22.png)

![](https://i.imgur.com/W59IeuF.png)

### 示例二：定期轮询获取新文件

#### Blueprint DSL 配置

```xml
<!--连接实例connectionBean-->
<bean id="mongoClient" class="com.mongodb.MongoClient">
     <!--构造方法参数注入-->
	 <argument value="localhost:27017"/> 
</bean>
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	 <route>
		 <from uri="mongodb-gridfs:mongo?database=changer"/> 
         <!--打印交换中的body,header-->
		 <to uri="class://com.shudi.databridge.test.DataSetProcessor"/>  	  
     </route>	
</camelContext>	
```

#### 示例运行

执行示例一文件写入

示例二运行结果
![](https://i.imgur.com/2MQx26s.png)

### 示例分析

MongoDB GridFS组件提供在路由中访问MongoDB GridFS系统的功能，以满足MongoDB对大型文件操作。
消费者端点为监控端点，当文件的状态在GridFS系统发送变化时，该端点可以捕捉到变化的数据，并在路由中做后续的处理。






