# Zookeeper组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-27*
*相关连接:[http://camel.apache.org/zookeeper.html](http://camel.apache.org/zookeeper.html)*

## 概述

ZooKeeper组件允许与ZooKeeper集群进行交互，并向Camel公开以下功能：
- 在任何ZooKeeper创建模式下创建节点。
- 获取并设置任意集群节点的数据内容。(正在设置的数据必须转换为byte [])
- 创建并检索附加到特定节点的子节点的列表。
- 分布式RoutePolicy，它利用由ZooKeeper协调的Leader选举来确定是否应该处理交换。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-zookeeper</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
zookeeper://zookeeper-server[:port][/path][?options]
```

来自uri的路径指定将作为端点目标的ZooKeeper服务器（又名znode）中的节点。(例如：zookeeper://192.168.1.190:2181/node_3)

## URI参数

名称|默认值|描述
----|----|----
path|暂无|ZooKeeper服务器中的节点（又名znode）。
listChildren|false|是否应列出节点的子级。
repeat|false|应该对znode进行更改并“重复”处理。
backoff|5000|在返回一个错误重新尝试的时间间隔。
timeout|5000|超时之前等待连接的时间间隔。
create|false|如果端点在当前不存在, 是否创建该节点。
createMode|EPHEMERAL|应该用于新创建节点的创建模式。
sendEmptyMessageOnDelete|true|camel 2.10：删除一个znode后，应该把一个空的消息发送给消费者。

## 示例

### 搭建Zookeeper服务器

使用ZooKeeper组件的之前，需搭建ZooKeeper服务器以供远程访问。

Zookeeper安装和配置连接(配置单机模式就可以满足示例需求)：[http://coolxing.iteye.com/blog/1871009](http://coolxing.iteye.com/blog/1871009)

### 启动Zookeeper服务器，创建节点

安装配置完成后，启动Zookeeper服务器（注意：先启动zkServer.cmd,后启动zkCli.cmd,这里需要启动这两个服务）

![](https://i.imgur.com/mKdBsXT.png)

创建节点，zkCli.cmd窗口命令输入create /Test_1 123456（向节点/Test_1添加数据123456）
查看节点，zkCli.cmd窗口命令输入get /Test_1

如下图所示，则节点创建成功
![](https://i.imgur.com/vhRUShe.png)

ZooKeeper客户端zkCli.sh节点的增删改查链接:[https://www.cnblogs.com/sherrykid/p/5813148.html](https://www.cnblogs.com/sherrykid/p/5813148.html)

### 示例一：读取节点数据

#### DataSetProcessor类
打印数据

```java
package com.shudi.databridge.test;
import org.apache.camel.Exchange;
import org.apache.camel.Processor;

public class DataSetProcessor implements Processor {
	@Override
	public void process(Exchange exchange) throws Exception {
		 System.out.println("Zookeeper组件测试打印---->："+exchange.getIn().getBody(String.class));
	}
}

```

#### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 			
	    <route>
            <!--zookeeper端点，/Test_1为节点名称-->
            <from uri="zookeeper://192.168.1.190:2181/Test_1"/> 
            <!--打印正文数据，即zookeeper服务器中/Test_1节点的数据--> 
			<to uri="class://com.shudi.databridge.test.DataSetProcessor"/>
	    </route> 			
</camelContext>
```

#### 示例运行

![](https://i.imgur.com/m3d2Gse.png)

>提示：在databridge中运行示例会抛出`java.lang.NoClassDefFoundError: org/ietf/jgss/GSSException`异常，需将org.apache.zookeeper/zookeeper依赖版本改为3.4.7以上。

### 示例二：写入节点数据

#### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 			
	    <route>
            <from uri="timer://foo?period=30s"/> 
            <!--添加数据-->
            <setBody>
                 <constant>zookeeper998</constant>
            </setBody>
            <!--zookeeper端点,传入数据会覆盖/Test_1节点的原数据--> 
			<to uri="zookeeper://192.168.1.190:2181/Test_1"/>
	    </route> 			
</camelContext>
```

#### 示例运行

![](https://i.imgur.com/LbKPJAA.png)

### 补充

#### 通过URI配置创建持久节点

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 			
	    <route>
            <from uri="timer://foo?period=30s"/> 
            <!--省略向正文添加数据......-->
			<to uri="zookeeper://192.168.1.190:2181/somepath/somenode?create=true&createMode=PERSISTENT"/>
	    </route> 			
</camelContext>
```

#### 通过Header设置删除节点

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 			
	    <route>
           <from uri="direct:delete-znode" />
           <setHeader headerName="CamelZookeeperOperation">
           <constant>DELETE</constant>
           </setHeader>
           <to uri="zookeeper://192.168.1.190:2181/somepath/somenode" />
</route>		
</camelContext>
```

### 示例分析

Zookeeper组件提供了在路由中访问Zookeeper服务器操作节点的能力，解决了Camel和Zookeeper的集成问题。
想要更深入的了解Zookeeper组件，需要去学习ZooKeeper分布式协调技术。
ZooKeeper分布式协调技术相关链接：[https://www.cnblogs.com/wuxl360/p/5817471.html](https://www.cnblogs.com/wuxl360/p/5817471.html)
