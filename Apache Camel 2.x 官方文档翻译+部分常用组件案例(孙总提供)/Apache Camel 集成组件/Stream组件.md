# Stream组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-28*
*相关连接:[http://camel.apache.org/stream.html](http://camel.apache.org/stream.html)*

## 概述

Stream组件提供对System.in，System.out和System.err流的访问，并允许流式传输文件和URL。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-stream</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
stream:in[?options] 
stream:out[?options]
stream:err[?options] 
stream:header[?options] 
```

此外，在Apache camel 2.0中还支持文件和URL端点URI。

```
stream:file?fileName=/foo/bar.txt
stream:url[?options] 
```

如果指定了stream：header，则使用流标题来查找要写入的流。 此选项仅适用于流生产者（即不能出现在from()中）。

## URI参数

名称|默认值|描述
----|----|----
delay|0|在消耗或产生流之前，以毫秒为单位的初始延迟。
encoding|JVM Default|您可以配置编码（是一个字符集名称）以使用基于文本的流（例如，消息体是一个String对象）。 如果不提供，Camel使用JVM默认字符集。
promptMessage|null|消息提示,从流中读取时使用stream：in; 例如，您可以将其设置为Enter a command:。
promptDelay|0|显示消息提示之前的可选延迟（以毫秒为单位）。
initialPromptDelay|2000|显示消息提示之前的初始延迟（以毫秒为单位）。 这个延迟只发生一次。 可以在系统启动过程中使用，以避免消息提示正在写入，而其他日志记录已完成。
fileName|null|当使用stream：file URI格式时，这个选项指定要流入/流出的文件名。
url|null|当使用stream：url URI格式时，此选项指定要流入/流出的URL。 输入/输出流将使用JDK URLConnection工具打开。
scanStream|false|用于连续读取诸如unix tail命令的流。
retry|false|骆驼2.7：将重试打开文件，如果它被覆盖，有点像tail--retry。
scanStreamDelay|0|使用scanStream时，尝试读取之间的延迟时间（以毫秒为单位）。
groupLines|0|camel 2.5:在消费者中对X的行数进行分组。例如，对10行进行分组，因此只输出10行，而不是每行1个交换。
autoCloseCount|0|Camel 2.10.0：（2.9.3和2.8.6）在Producer端关闭流之前处理的消息数量。 切勿在默认情况下关闭流（仅在生产者停止时）。 如果发送更多消息，则重新打开该流以进行另一个autoCloseCount批处理。
closeOnDone|false|camel 2.11.0:此选项与拆分器组合使用并流到同一个文件中。这样做的目的是为了保持流的开放，并且只有当分割器完成时才关闭，以提高性能。请注意，这要求您只流到相同的文件，而不是两个或更多的文件，并且最后一个带有最后一个信息的分割消息被路由到流端点，这样它就可以获得关闭的信号。

## Message content

Stream组件支持String或byte[]来写入流。 只要将字符串或byte[]内容添加到message.in.body即可。 在二进制模式下发送到流：生产者的消息之后不再是换行符（相对于字符串消息）。 具有null主体的消息不会被附加到输出流。
特殊的stream:headerURI 用于自定义输出流。只需将java.io.OutputStream对象添加到键header中的message.in.header。

## 示例

### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	    <!--stream:in-->
	    <route>
            <from uri="stream:in?encoding=utf-8&amp;promptMessage=Enter a command:"/>
            <!--打印控制台输入的消息写入文件-->  	
			<to uri="file:H:/Data/"/>
			<log message="fang.${body}"/>
	    </route> 
 
		<!--stream:out-->
        <route>
            <from uri="timer://foo?period=10s"/>
            <!--向消息正文添加数据-->  	
			<to uri="class:com.shudi.databridge.test.BeanProcessor"/>
            <!--控制台打印出数据-->
            <to uri="stream:out"/>
			<log message="fang.${body}"/>
	    </route> 
		
        <!--stream:err-->
        <route>
            <from uri="timer://foo?period=10s"/>
            <!--向消息正文添加数据-->  	
			<to uri="class:com.shudi.databridge.test.BeanProcessor"/>
            <!--err适用于IDEA运行环境中，在databridge容器中没有预想效果-->
            <to uri="stream:err"/>
			<log message="fang.${body}"/>
	    </route> 

        <!--stream:header-->
        <route>
            <from uri="timer://foo?period=10s"/>
           	<setHeader headerName="stream">
                  <constant>123456</constant>
            </setHeader>
            <to uri="stream:header"/>
			<to uri="class:com.shudi.databridge.test.DataSetProcessor"/>
			<log message="fang.${body}"/>
	    </route> 

        <!--stream:file-->
        <route>
            <from uri="stream:file?fileName=H:/fxk/we.txt"/> 
            <!--打印文件内容--> 	
			<to uri="class:com.shudi.databridge.test.DataSetProcessor"/>		
			<log message="fang.${body}"/>
	    </route>

        <!--stream:url-->
        <route>
            <!--指定HTTP服务作为stream:url参数-->
            <from uri="stream:url?url=http://localhost:3387/esb-servlet-rest-blueprint/rest/user/123"/> 
            <!--打印响应内容--> 	
			<to uri="class:com.shudi.databridge.test.DataSetProcessor"/>		
			<log message="fang.${body}"/>
	    </route>


</camelContext>	
```

### 示例分析

Stream组件将流和路由集成到一起，并且提供了流的访问（in,out,err）。
Stream组件采用简单，安全的流传输方式传输数据。解决了对数据处理中筛选、映射、合并等问题。
当然，流还具有更多的特性可以运用到集成环境中，需要大家多去实践。








