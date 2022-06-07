# Quartz组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-10*
*相关连接:[http://camel.apache.org/quartz.html](http://camel.apache.org/quartz.html)*

## 概述

Quartz组件是一个定时调度器，通过Quartz可以快速完成任务调度的工作。

使用Quartz组件需添加如下依赖：
```
<dependency>
		<groupId>org.apache.camel</groupId>
		<artifactId>camel-quartz</artifactId>
		<version>2.16.5</version>
</dependency>
```

## URI

```
quartz://timerName?options
```
其中，timerName为调度器名称。

## URI参数

参数|默认|描述
----|----|----
cron|暂无|指定cron表达式(与trigger.*或job.*选项不兼容)。
trigger.repeatCount|0|SimpleTrigger: 计时器应该重复多少次？
trigger.repeatInterval|0|SimpleTrigger: 重复触发器之间的时间 (以毫秒为单位)。
job.name|null|设置工作名称。
job.XXX|null|使用XXX setter 名称设置工作选项。
trigger.XXX|null|使用XXX setter 名称设置触发器选项。
stateful|false|指上一个任务没有结束，不发起下一个调度任务。
fireNow|false|如果它是true，那么当使用simple触发器时，将触发触发器。
deleteJob|true|如果设置为 true, 则在路由停止时触发器会自动删除。否则, 如果设置为 false, 它将保留在计划程序中。当设置为 false 时, 它还意味着用户可以重用预先配置的触发器和骆驼 Uri。请确保名称匹配。请注意, 您不能同时将 deleteJob 和 pauseJob 设置为 true。
pauseJob|false|如果设置为 true, 则在路由停止时触发器自动暂停。否则, 如果设置为 false, 它将保留在计划程序中。当设置为 false 时, 它还意味着用户可以重用预先配置的触发器和骆驼 Uri。请确保名称匹配。请注意, 您不能同时将 deleteJob 和 pauseJob 设置为 true。
usingFixedCamelContextName|false| 如果为 true, 则为 JobDataMap使用 CamelContext 名称直接引用骆驼上下文。如果是false，JobDataMap将使用CamelContext管理名称，可以在部署时更改该名称。

## 配置Quartz属性文件

参数|默认|类型|描述
----|----|----|----
properties|null|Properties|可以配置java.util.Properties实例。
propertiesFile|null|String|从类路径加载的属性的文件名。

可以在Spring XML 中配置如下：
```
<bean id="quartz" class="org.apache.camel.component.quartz.QuartzComponent">
    <property name="propertiesFile" value="com/mycompany/myquartz.properties"/>
</bean>
```
## 启动Quartz调度器
Quartz组件提供了一个选项, 使Quartz计划程序可以延迟启动, 或者根本不自动启动。

参数|默认|类型|描述
----|----|----|----
startDelayedSeconds|0|int|在启动Quartz调度程序之前等待秒数。
autoStartScheduler|true|bolean|是否应自动启动计划程序。

可以在Spring XML 中配置如下：
```
<bean id="quartz" class="org.apache.camel.component.quartz.QuartzComponent">
    <property name="startDelayedSeconds" value="5"/>
</bean>
```

## 使用Cron触发器(Quartz组件端点中常用配置)

Quartz组件支持类似 Cron 的表达式, 用于以方便的格式指定计时器。但是Cron表达式比较复杂，只要熟练的运用了Cron表达式，Quartz组件就可以满足定时调度的各种场景。

在线Cron表达式生成器:[http://cron.qqe2.com/](http://cron.qqe2.com/)
Cron表达式详解：[https://www.cnblogs.com/linjiqin/archive/2013/07/08/3178452.html](https://www.cnblogs.com/linjiqin/archive/2013/07/08/3178452.html)


## 示例

### Blueprint DSL 配置
```xml
<blueprint xmlns="http://www.osgi.org/xmlns/blueprint/v1.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:cm="http://aries.apache.org/blueprint/xmlns/blueprint-cm/v1.0.0"
           xsi:schemaLocation="
           http://www.osgi.org/xmlns/blueprint/v1.0.0 http://www.osgi.org/xmlns/blueprint/v1.0.0/blueprint.xsd">
    
	<bean id="beanProcessor" class="com.shudi.databridge.test.BeanProcessor" />
	<bean id="dataSetProcessor" class="com.shudi.databridge.test.DataSetProcessor" />
	
	<camelContext xmlns="http://camel.apache.org/schema/blueprint">
	    
		<!--Quartz组件测试路-->
		<route>
		     <!--每5S执行一次-->
		     <from uri="quartz://dispatchTest?cron=0/5+*+*+*+*+?+*"/>
             <!--每天在2点到23点之间，每30分钟执行一次-->
             <!--<from uri="quartz://dispatchTest?cron=0+0/30+2-23+*+*+?+*"/>-->
			 <process ref="beanProcessor"/>
		     <process ref="dataSetProcessor"/>
		</route>
		
	</camelContext>

</blueprint>
```

### 示例分析

在Apache Camel中任务调度组件有Scheduler组件,Timer组件，Quartz组件。
相对于前两者，Quartz组件表现的更优秀，在比较复杂的集成环境中我们应优先选择Quartz组件作为任务调度组件。