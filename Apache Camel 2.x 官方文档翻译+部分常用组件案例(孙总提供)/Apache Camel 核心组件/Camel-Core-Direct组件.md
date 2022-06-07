# Direct组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-02*
*相关连接:[http://camel.apache.org/direct.html](http://camel.apache.org/direct.html)*

## 概述

Direct组件在生产者发送消息交换时提供对任何使用者的直接、同步调用。此端点可用于连接相同骆驼上下文中的现有路由。

### 补充：

异步调用，当生产者发送消息交换时, [SEDA](http://camel.apache.org/seda.html)组件提供对任何使用者的异步调用。
连接到其他的骆驼上下文，[VM](http://camel.apache.org/vm.html)组件在骆驼上下文之间提供连接, 只要它们在同一JVM中运行。

## URI

```
direct:someName[?options]
```

其中，someName可以是唯一标识端点的字符串。

## URI参数
名称|默认值|描述
----|----|----
allowMultipleConsumers|true|如果设置为 false，则在端点上启动第二个使用者时，将引发IllegalStateException 。(已废弃)
block|false|如果将消息发送到没有活动消费者的直接端点，则该生产者将阻塞等待消费者活动的超时毫秒数。
timeout|30000|在启用时阻塞的超时值（以毫秒为单位）
failIfNoConsumers|true|表示当发送到没有活动用户的直接端点时，生产者是否应该抛出异常

## 示例

### 示例分析

从下面的示例可以看出：通过Direct组件可以连接同一上下文环境中的所有路由进行消息传递。
所以说，当我们在复杂的集成环境中，可以使用Direct组件对路由进行编排，从而增强路由线路的灵活性、复用性。


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

	  <!--Direct测试组件测试dsl-->
	  <camelContext xmlns="http://camel.apache.org/schema/blueprint" id="测试">
	
              <!--路由一:产生数据集,并连接到direct组件端点-->
              <route id="DataSet测试">
                   <from uri="dataset:simpleDataSet"/>
				   <to uri="direct:test?failIfNoConsumers=false"/>
              </route>   
			  
              <!--路由二:连接direct组件端点(这里的someName要和上面的someName一致)，通过处理器打印数据-->
              <route id="Direct测试">
                   <from uri="direct:test"/>
				   <process ref="dataSetProcessor"/>
              </route> 		  
	   </camelContext>	  	  
</blueprint>
```

### 示例运行
![](./images/TIM截图20171102114356.png)

