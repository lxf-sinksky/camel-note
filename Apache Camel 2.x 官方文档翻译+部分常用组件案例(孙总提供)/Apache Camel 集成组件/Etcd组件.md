# Etcd组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-12-04*
*相关连接:[http://camel.apache.org/etcd.html](http://camel.apache.org/etcd.html)*

## 概述

Etcd是一种分布式的键值存储，它提供了一种可靠的方式来存储数据。

使用此组件需添加如下依赖(camel-etcd版本必须在2.17.0以上)：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-etcd</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
etcd:namespace[/path][?options]
```

其中，namespace表示etcd上下文，而etcd组件应该在其中运行，而path是一个可选属性来定义哪个节点受到了影响。

支持的命名空间有:
- keys
- watch
- stats

## URI参数

名称|默认值|描述
----|----|----
uris|http://localhost:2379,http://localhost:4001|定义组件应该连接到的uri。
sslContextParameters|null|使用自定义org.apache.camel.util.jsse.SSLContextParameters。
userName|null|用于基本身份验证的用户名。
password|null|用于基本身份验证的密码。
sendEmptyExchangeOnTimeout|false|要发送空消息，以防超时监视关键字(仅使用消费者)。
recursive|false|递归地应用一个动作。
timeToLive|null|将键的生命周期设置为毫秒。
timeout|null|设定一个动作可以完成的最大时间。

## Headers

名称|类型|描述
----|----|----
CamelEtcdAction|java.lang.String|要执行的操作,支持的值set, delete, deleteDir, get。
CamelEtcdNamespace|java.lang.String|交换是从etcd上下文生成/处理的。
CamelEtcdPath|java.lang.String|对于keyspace命名空间，它用于确定节点的动作，如果没有设置从URI端点使用的路径。对于统计和监视名称空间，它包含正在处理的节点的路径。
CamelEtcdTimeout|java.lang.Long|设置操作完成所用的最长时间。如果不存在, 则将timeout选项考虑在内。
CamelEtcdTtl|java.lang.Integer|以毫秒为单位设置键的使用寿命。 如果不存在，则考虑timeToLive选项。
CamelEtcdRecursive|java.lang.Boolean|递归地应用一个动作。 如果不存在，则考虑recursive选项。

## 示例

### 搭建Etcd服务器

Etcd下载地址：[https://github.com/coreos/etcd/releases/](https://github.com/coreos/etcd/releases/)

Etcd安装教程：[http://www.iigrowing.cn/etcd_shi_yong_ru_men.html](http://www.iigrowing.cn/etcd_shi_yong_ru_men.html)

>注：只需要单点安装即可满足示例

### 利用HTTP API 进行交互

使用SoapUI进行Etcd服务端交互测试

#### 增加节点数据

![](https://i.imgur.com/FiXyzin.png)

利用HTTP API还可以执行更多操作，详情请参照上面的Etcd安装教程。

### 运用组件访问Etcd

#### 示例一：获取节点数据

##### DataSetProcessor类
打印正文数据

```java
package com.shudi.databridge.test;

import org.apache.camel.Exchange;
import org.apache.camel.Processor;
import mousio.etcd4j.responses.EtcdKeysResponse;

public class DataSetProcessor implements Processor {

	@Override
	public void process(Exchange exchange) throws Exception {
        //Etcd组件会返回EtcdKeysResponse实例
		EtcdKeysResponse etcdKeysResponse = exchange.getIn().getBody(EtcdKeysResponse.class);
        //打印输出Key,Value
		System.out.println("Etcd组件测试--->"+"Key:"+etcdKeysResponse.getNode().getKey()+"--Value:"+etcdKeysResponse.getNode().value);
	}
}
```

##### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 			
	    <route>
            <from uri="timer://foo?period=30s"/> 
            <!--CamelEtcdAction为操作名-->
            <setHeader headerName="CamelEtcdAction">
                <constant>get</constant>
            </setHeader>
            <!--引用etcd组件生产者端点对键值进行操作--> 
			<to uri="etcd:keys/helloword?uris=http://localhost:2379"/>
            <to uri="class://com.shudi.databridge.test.DataSetProcessor"/>
	    </route> 			
</camelContext>
```
##### 示例运行

![](https://i.imgur.com/abl8TEg.png)

#### 示例二：监控节点

##### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 			
	    <route>
            <!--Etcd消费者端点，namespace必须为watch-->
            <from uri="etcd:watch/helloword?uris=http://localhost:2379"/> 
            <!--监控路由，打印输出--> 
			<to uri="class://com.shudi.databridge.test.DataSetProcessor"/>
	    </route> 			
</camelContext>
```

##### 示例运行

使用SoapUI修改节点数据

![](https://i.imgur.com/6OJp3ga.png)

查看控制台

![](https://i.imgur.com/k9KczVs.png)

#### 示例三：查看集群数据信息

etcd 还保存了集群的数据信息，包括节点之间的网络信息，操作的统计信息。
- /v2/stats/leader:会返回集群中 leader 的信息，以及 followers 的基本信息。
- /v2/stats/self:会返回当前节点的信息。
- /v2/state/store：会返回各种命令的统计信息。

##### DataSetProcessor类
打印正文数据

```java
package com.shudi.databridge.test;

import org.apache.camel.Exchange;
import org.apache.camel.Processor;
import mousio.etcd4j.responses.EtcdKeysResponse;

public class DataSetProcessor implements Processor {

	@Override
	public void process(Exchange exchange) throws Exception {
        //查询统计信息时Etcd组件会返回EtcdStoreStatsResponse实例
		EtcdStoreStatsResponse etcdStoreStatsResponse =  exchange.getIn().getBody(EtcdStoreStatsResponse.class);
        //打印输出set操作成功的次数
		System.out.println("Etcd组件测试--->setsSuccess:"+etcdStoreStatsResponse.setsSuccess());
	}
}
```


##### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 			
	    <route>
            <from uri="timer://foo?period=10s"/> 
            <!--当namespace=stats时，etcd端点可以为消费者或者生产者-->
            <to uri="etcd:stats/store?uris=http://localhost:2379"/>
            <!--打印输出统计信息--> 
			<to uri="class://com.shudi.databridge.test.DataSetProcessor"/>
	    </route> 			
</camelContext>
```

#### 示例运行

![](https://i.imgur.com/wuPZmAl.png)

### 示例分析

Etcd是一个高可用的Key/Value存储系统，主要用于分享配置和服务发现。etcd和ZooKeeper在功能上极为相似。
Etcd组件集成camel和etcd,使我们能在路由中熟练的操作etcd存储系统。

>补充：关于etcd存储系统详细介绍：[https://yq.aliyun.com/articles/11035](https://yq.aliyun.com/articles/11035)