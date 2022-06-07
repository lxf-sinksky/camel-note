# Websocket组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-12-15*
*相关连接:[http://camel.apache.org/websocket.html](http://camel.apache.org/websocket.html)*

## 概述

Websocket组件提供端点与客户端使用websocket协议进行通信。该组件使用了实现IETF规范的Eclipse Jetty服务器(drafts and RFC 6455)。它支持协议ws://和wss://。使用wss://协议，SSLContextParameters必须定义。


使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-websocket</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
websocket://hostname[:port][/resourceUri][?options] 
```

## 组件参数
WebsocketComponent可以在使用之前配置，用于设置主机，充当websocket服务器。

名称|默认值|描述
----|----|----
host|0.0.0.0|主机名。
port|9292|端口号。
staticResources|null|静态资源（如index.html文件等）的路径。如果已经配置了此选项，则会在给定的主机名和端口上启动服务器，以便为静态资源（例如index.html文件）提供服务。 如果此选项尚未配置，则不启动服务器。
sslContextParameters|暂无|引用注册表中的org.apache.camel.util.jsse.SSLContextParameters。 此引用将覆盖组件级别的任何已配置的SSLContextParameters。 请参阅使用JSSE配置实用工具。
enableJmx|false|如果这个选项是正确的，那么将为这个端点启用Jetty JMX支持。更多细节请参见Jetty JMX支持。
sslKeyPassword|null|仅消费者:使用SSL时，密钥库的密码。
sslPassword|null|仅消费者: 使用SSL时的密码。
sslKeystore|null|仅消费者: 密钥库的路径。
minThreads|1|仅消费者: 设置服务器线程池中最小线程数的值。从camel 2.18：MinThreads/MaxThreads或threadPool字段是必需的，因为切换到Jetty 9。
maxThreads|1 + noCores * 2|仅消费者:设置服务器线程池中最大线程数的值。从camel 2.18：MinThreads/MaxThreads或threadPool字段是必需的，因为切换到Jetty 9。
threadPool|null|仅消费者: 用于服务器的自定义线程池。。从camel 2.18：MinThreads/MaxThreads或threadPool字段是必需的，因为切换到Jetty 9。

## 端点参数
可以在使用之前配置WebsocketEndpoint

名称|默认值|描述
----|----|----
sslContextParametersRef|暂无|已弃用, 将在camel 3.0 中删除:对注册表中的org.apache.camel.util.jsse.SSLContextParameters的引用。此引用将覆盖组件级别上任何配置的 SSLContextParameters。
sslContextParameters|暂无|camel 2.11.1:对注册表中的org.apache.camel.util.jsse.SSLContextParameters的引用。此引用将覆盖组件级别的任何已配置的SSLContextParameters。
sendToAll|null|仅生产者:发送到所有websocket订阅服务器。可用于在端点级别上配置, 而不必在消息上使用WebsocketConstants.SEND_TO_ALL标头。
staticResources|null|web资源或类路径的根目录。使用协议文件:或类路径:取决于您希望组件从文件系统或类路径加载资源。
bufferSize|null|camel 2.12.3:设置websocketServlet的缓冲区大小,它也是最大帧字节大小(默认值8192)。
maxIdleTime|null|camel 2.12.3：设置时间以ms为单位，websocketServlet创建的websocket在关闭之前最大空闲时间（默认是300000）。
maxTextMessageSize|null|Camel 2.12.3：可以用来设置websocketServlet创建的websocket在关闭之前可以接受的字符大小。
maxBinaryMessageSize|null|Camel 2.12.3：可以用来设置websocketServlet创建的websocket在关闭之前可以接受的字节大小。(默认值是-1或无限制）
minVersion|null|Camel 2.12.3：可以用来设置websocketServlet接受的最小协议版本。（默认13 - RFC6455版本）
sendTimeout|30000|camel 2.18：发送到websocket通道时超时。 默认的超时时间是30000（30秒）

## Message Headers
websocket组件使用2个头文件来指示将消息发送回单个/当前客户端或所有客户端。

Key|描述
----|----
WebsocketConstants.SEND_TO_ALL|将消息发送到当前连接的所有客户端。可以在端点上使用sendToAll选项, 而不是使用此标头。
WebsocketConstants.CONNECTION_KEY|将消息发送到具有给定连接键的客户端。

## 示例


#### 示例分析

#### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	     
		 <route>
              <!--定义websocket服务器，并指定URL-->
		      <from uri="websocket://192.168.1.149:8181/v3/replication"/> 
              <!--将服务器接收到的数据输出到控制台-->
			  <to uri="class://com.shudi.databridge.test.DataSetProcessor"/>
			  <choice>
                <when>
                   <simple>body='A'</simple>     
				   <setBody>
                      <constant>您选择了A</constant>
                   </setBody>
                </when>
                <when>
                   <simple>body='B'</simple>
				   <setBody>
                      <constant>您选择了B</constant>
                   </setBody>
                </when>
                <otherwise>
                   <setBody>
                      <constant>没有该操作</constant>
                   </setBody>
                </otherwise>
              </choice>
              <!--回复websocket客户端,因为在header中存在WebsocketConstants.CONNECTION_KEY,所以能够定位到握手的客户端-->
			  <to uri="websocket://192.168.1.149:8181/v3/replication"/>
		 </route>
		 
</camelContext>	
```

#### 示例本地运行

使用WebSocket在线测试客户端：[http://www.blue-zero.com/WebSocket/](http://www.blue-zero.com/WebSocket/) 

本地服务器运行状态

![](https://i.imgur.com/qGUIGUB.png)

客户端运行状态

![](https://i.imgur.com/YYAx4Hr.png)

#### 示例分析

WebSocket组件提供了与WebSocket客户端通信的机制，集成WebSocket和camel，让我们在浏览器中和路由交互更加便捷。


