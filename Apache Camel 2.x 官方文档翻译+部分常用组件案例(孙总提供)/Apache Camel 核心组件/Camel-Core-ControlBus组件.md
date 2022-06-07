# ControlBus组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-01*
*相关连接:[http://camel.apache.org/controlbus-component.html](http://camel.apache.org/controlbus-component.html)*

## 概述 
ControlBus组件提供了基于控制总线模式的camel应用程序的简单管理。例如, 通过向端点发送消息, 可以控制路由的生命周期, 或收集性能统计信息。

## URI 
```
controlbus:command[?options]
```

## URI参数

### Commands 

命令|描述
----|----
route|使用 routeId 和action参数来控制路由
language|允许您指定用于计算消息正文的语言。在消息正文中返回结果 (如果有)

### Options 

名称|默认值|描述
----|----|----
routeId|null|通过 id指定路由。特殊关键字current指示当前路由。
action|null|可选值：start,stop,suspend,resume,status,stats。启动或停止路由, 或将路由的状态作为输出在消息正文中获取。从骆驼 2.11.1: 使用suspend和resume来暂停或继续路由或stats以 XML 格式获取其 id 由提供的路由的性能统计信息. routeId 选项.如果未定义 routeId , 则将返回整个CamelContext的统计信息。
async|false|是否异步执行控制总线任务。**重要**:如果启用此选项, 则该任务的任何结果都不是在Exchange上设置的。只有同步执行任务时才可能发生这种情况。
loggingLevel|INFO|日志记录级别用于在任务完成时进行日志记录, 或者在处理任务时发生异常

## 示例

### 示例分析

1. controlbus组件可以端点发出指令，并控制路由的状态。
2. 可以通过language去指定消息正文的语言，关于语言相关链接[http://camel.apache.org/language.html](http://camel.apache.org/language.html)

### BeanProcessor类
用于打印当前状态

```java
package com.shudi.databridge.test;
import org.apache.camel.Exchange;
import org.apache.camel.Processor;

   public class BeanProcessor implements Processor {
	     @Override
	       public void process(Exchange exchange) throws Exception {
		          System.out.println("ControlBus组件测试。。。");   
	       }
    }
```

### Blueprint DSL配置

```xml
<blueprint xmlns="http://www.osgi.org/xmlns/blueprint/v1.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:cm="http://aries.apache.org/blueprint/xmlns/blueprint-cm/v1.0.0"
           xsi:schemaLocation="
           http://www.osgi.org/xmlns/blueprint/v1.0.0 http://www.osgi.org/xmlns/blueprint/v1.0.0/blueprint.xsd">

	  <bean id="beanProcessor" class="com.shudi.databridge.test.BeanProcessor"/> 
	  
	  <!--ControlBus组件测试dsl-->
	  <camelContext xmlns="http://camel.apache.org/schema/blueprint" id="测试">	
              <route id="ControlBus测试">
                   <from uri="timer://order?period=20s"/>
                   <process ref="beanProcessor"/> 
                   <!--停止当前路由-->
                   <to uri="controlbus://route?routeId=current&action=stop"/>
                   <!--延迟当前路由-->
                   <!--<to uri="controlbus://route?routeId=current&action=suspend"/>-->
                   ......
				   <process ref="beanProcessor"/> 
				   <log message="fang.${body}"/>
              </route>           
	   </camelContext>  
</blueprint>
```