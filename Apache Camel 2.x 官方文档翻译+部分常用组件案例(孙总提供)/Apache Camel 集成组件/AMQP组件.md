# AMQP组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-12-12*
*相关连接:[http://camel.apache.org/amqp.html](http://camel.apache.org/amqp.html)*

## 概述

AMQP组件使用Apache Qpid项目的JMS客户端API来支持AMQP协议。
AMQP，即Advanced Message Queuing Protocol,一个提供统一消息服务的应用层标准高级消息队列协议,是应用层协议的一个开放标准,为面向消息的中间件设计。基于此协议的客户端与消息中间件可传递消息，并不受客户端/中间件不同产品，不同的开发语言等条件的限制。Erlang中的实现有RabbitMQ等。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-amqp</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
amqp:[queue:|topic:]destinationName[?options]
```

## URI参数

AMQP组件从JMS组件继承。适用JMS组件所有参数，详情请查看JMS组件文档。

## 示例

### 搭建代理服务(ActiveMQ)

搭建ActiveMQ服务请查看ActiveMQ组件文档

启动ActiveMQ服务

![](https://i.imgur.com/tp5YZdY.png)

### Blueprint DSL 配置

```xml
<bean id="amqp" class="org.apache.camel.component.amqp.AMQPComponent">
      <!--配置连接工厂-->
      <property name="connectionFactory">
           <bean class="org.apache.qpid.jms.JmsConnectionFactory">
	            <property name="remoteURI" value="amqp://localhost:5672"/>
				<property name="username" value="admin"/>
				<property name="password" value="admin"/>
           </bean>
      </property> 
</bean>
	
<camelContext xmlns="http://camel.apache.org/schema/blueprint">
      <!--生产者--> 
	  <route>
		   <from uri="timer://amqpoff?delay=5s&amp;period=20s"/>
		   <setBody>
			    <constant>今天是2017-12-13日，测试AMQP组件</constant>
		   </setBody>
		   <to uri="amqp://amqpTest"/>
	  </route>
	  <!--消费者-->
	  <route>
		   <from uri="amqp://amqpTest"/>
		   <to uri="class://com.shudi.databridge.test.DataSetProcessor"/>
	  </route>
</camelContext>	
```

### 示例运行

![](https://i.imgur.com/LWyc6xN.png)

### 示例分析

AMQP组件实现了在ActiveMQ服务中基于AMQP协议的异步消息传递。
AMQP的主要特性：
1. 独立于平台的底层消息传递协议。
2. 消费者驱动消息传递。
3. 跨语言和平台的互用性。
4. 有5种交换类型direct，fanout，topic，headers，system。
5. 面向缓存的。
6. 可实现高性能。
7. 支持长周期消息传递。
8. 支持经典的消息队列，循环，存储和转发。
9. 支持事务（跨消息队列）。
10. 支持分布式事务（XA，X/OPEN，MS DTC）。
11. 使用SASL和TLS确保安全性。
12. 支持代理安全服务器。
13. 元数据可以控制消息流。
14. 可扩展。

上述的特性能够使AMQP协议广泛的运用到消息传递中。
在集成环境使用AMQP组件，需要更多的学习AMQP协议相关的知识。

### 知识扩展

>JMS、AMQP和MQTT主要特性链接:[https://www.cnblogs.com/zhangyu1024/p/6138166.html](https://www.cnblogs.com/zhangyu1024/p/6138166.html)
AMQP协议简介链接：[http://blog.csdn.net/mx472756841/article/details/50815895](http://blog.csdn.net/mx472756841/article/details/50815895)
activeMQ支持5种协议链接：[http://haoran-10.iteye.com/blog/2259240](http://haoran-10.iteye.com/blog/2259240)
