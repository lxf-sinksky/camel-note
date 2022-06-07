# JMX组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-27*
*相关连接:[http://camel.apache.org/jmx.html](http://camel.apache.org/jmx.html)*

## 概述

JMX组件允许消费者订阅mbean的通知。 该组件支持直接通过Exchange传递Notification对象，或根据此项目中提供的模式将其序列化为XML。 这是一个消费者的唯一组件。 如果您尝试为其创建生产者，则会引发异常。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-jmx</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
jmx:platform?options 
```

远程mbean服务器url可以在最初的JMX方案之后提供，如下所示：
```
jmx:service:jmx:rmi:///jndi/rmi://localhost:1099/jmxrmi?options 
```

## URI参数

名称|必填|默认值|描述
----|----|----|----
format|no|xml|消息正文的格式。 “xml”或“raw”。 如果xml，通知被序列化为xml。 如果是raw，那么原始的java对象被设置为body。
user|no|暂无|用于进行远程连接的凭证。
password|no|暂无|用于进行远程连接的凭证。
objectDomain|yes|暂无|连接到的mbean的域。
objectName|no|暂无|连接到的mbean的名称键。此值与传递的对象属性相互斥。
notificationFilter|no|暂无|对实现NotificationFilter的 bean 的引用。应使用#ref语法引用注册表bean。
handback|no|暂无|接收到通知时，向监听器回传的值。 这个值将被放在消息头中，并带有“jmx.handback”。
testConnectionOnStartup|no|true|Camel 2.11如果为true，则在启动时无法建立JMX连接时，使用者将抛出异常。 如果为false，则使用者将尝试每x秒建立一个JMX连接，直到建立连接 - 其中“x”是配置的reconnectDelay。
reconnectOnConnectionFailure|no|false|camel 2.11如果为true，则当发生任何连接失败时，使用者将尝试重新连接到JMX服务器。 使用者将尝试每“x”秒重新建立JMX连接，直到建立连接 - 其中“x”是配置的reconnectDelay。
reconnectDelay|no|10seconds|camel 2.11重试创建初始连接之前等待的秒数。

## 示例

### 常用 Blueprint DSL 配置

```xml
<route>
   <from uri="jmx:jmx:service:jmx:rmi:///jndi/rmi://localhost:1099/jmxrmi?objectDomain=org.apache.camel&objectName=CamelContext"/>
   <to uri="class://com.shudi.databridge.test.DataSetProcessor"/>
</route>
```

### 监控类型消费者

监视器样式消费者仅支持本地mbean服务器。

#### 监视类型的URI选项

属性|类型|可应用于|描述
----|----|----|----
monitorType|enum|all|计数器，仪表，字符串之一。
observedAttribute|string|all|被观察的属性。
granualityPeriod|long|all|粒度周期（以毫秒为单位）。根据JMX,默认值是10秒。
initThreshold|number|counter|初始阈值。
offset|number|counter|偏移值。
modulus|number|counter|模数值。
differenceMode|boolean|counter,gauge|如果报告差异，则为真，对于实际值为假。
notifyHigh|boolean|gauge|高级通知开/关。
notifyLow|boolean|gauge|低级通知开/关。
highThreshold|number|gauge|报告高级通知的阈值。
lowThreshold|number|gauge|报告低级通知的阈值。
notifyDiffer|boolean|string|如果字符串不同，则返回true。
notifyMatch|boolean|string|如果字符串匹配，则返回true。
stringToCompare|string|string|字符串与属性值进行比较。

#### 监控类型 Blueprint DSL 配置

```xml
<route>
   <from uri="jmx:jmx:service:jmx:rmi:///jndi/rmi://localhost:1099/jmxrmi?objectDomain=myDomain&objectName=simpleBean&monitorType=counter&observedAttribute=MonitorNumber&initThreshold=1&granularityPeriod=500"/>
   <to uri="class://com.shudi.databridge.test.DataSetProcessor"/>
</route>
```

### 补充

关于JMX扩展知识链接：[http://camel.apache.org/camel-jmx.html](http://camel.apache.org/camel-jmx.html)


