# Direct-VM组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-02*
*相关连接:[http://camel.apache.org/direct-vm.html](http://camel.apache.org/direct-vm.html)*

## 概述

- 当生产者发送消息交换时，Direct-VM组件提供对 JVM 中任何用户的直接、同步调用(Direct组件的扩展)。
- 此端点可用于连接相同的骆驼上下文中的现有路由，以及同一JVM 中的其他骆驼上下文。
- 该组件与Direct组件不同，该组件支持跨CamelContext实例的通信，因此您可以使用此机制跨web应用程序进行通信。
- 在运行时，您可以通过停止现有的消费者，并启动新的消费者来替换前者。但是在任何给定的时间点，对于给定的端点，最多只能有一个活跃的消费者。

### 补充：

```
该组件还可以连接部署在不同OSGI包中的路由，您可以在这里看到。即使它们在不同的包中运行，骆驼的路线也会使用相同的线程。并使用事务-Tx来开发应用程序
```
![](./images/TIM截图20171102151734.png)

## URI
```
direct-vm:someName
```

其中，someName可以是唯一标识端点的字符串。

## URI参数

名称|默认值|描述
----|----|----
block|false|如果将消息发送到没有活动消费者的直接端点，则该生产者将阻塞等待消费者活动的超时毫秒数。
timeout|30000|在启用时阻塞的超时值（以毫秒为单位）
failIfNoConsumers|true|表示当发送到没有活动用户的直接端点时，生产者是否应该抛出异常

## 示例

### 示例分析
示例我们开启了二个上下文环境，并在环境中分别配置了路由。路由1向交换中添加了数据集，并连接到direct-vm组件端点。路由2连接了direct-vm组件端点，通过处理器将数据集打印。
示例验证了direct-vm组件可以去连接不同上下文环境中的所有路由（必须是在同一JVM中）。

### Blueprint DSL 配置

```xml
<blueprint xmlns="http://www.osgi.org/xmlns/blueprint/v1.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:cm="http://aries.apache.org/blueprint/xmlns/blueprint-cm/v1.0.0"
           xsi:schemaLocation="
           http://www.osgi.org/xmlns/blueprint/v1.0.0 http://www.osgi.org/xmlns/blueprint/v1.0.0/blueprint.xsd">
     
	  <!--配置数据集(这里引用的的是Camel DataSet组件中的测试类SimpleDataSet) -->
      <!--此处有疑问可查看DataSet组件文档-->
	  <bean id="simpleDataSet" class="org.apache.camel.component.dataset.SimpleDataSet">
            <property name="size" value="10"/>
      </bean>

      <!--自定义处理器（打印结果集）-->
	  <bean id="dataSetProcessor" class="com.shudi.databridge.test.DataSetProcessor"/>

	  <!--direct-vm组件测试环境1-->
	  <camelContext xmlns="http://camel.apache.org/schema/blueprint" id="测试1">	
              <route id="路由1">
                   <from uri="dataset:simpleDataSet"/>
				   <to uri="direct-vm:test?failIfNoConsumers=false"/>
              </route>                	  
	   </camelContext>
	   
      <!--direct-vm组件测试环境2-->
	  <camelContext xmlns="http://camel.apache.org/schema/blueprint" id="测试2">	
	          <route id="路由2">
                   <from uri="direct-vm:test"/>
				   <process ref="dataSetProcessor"/>
              </route> 	
	  </camelContext>
</blueprint>
```

### 示例运行
![](./images/TIM截图20171102114356.png)