# Netty4组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-12-22*
*相关连接:[http://camel.apache.org/netty4.html](http://camel.apache.org/netty4.html)*

## 概述

netty4组件是基于Netty项目版本4的socket通信组件。
Netty是一个NIO客户端服务器框架，可以快速简单地开发协议服务器和客户端等网络应用程序。
Netty极大地简化和简化了TCP和UDP Socket服务器等网络编程。
这个骆驼组件支持生产者和消费者端点。Netty组件有几个选项，并允许对许多TCP/UDP通信参数（缓冲区大小，keepAlive，tcpNoDelay等）进行细粒度的控制，并促进Camel路由上的In-Only和In-Out通信。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-netty4</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
netty4:tcp://localhost:port[?options]
netty4:udp://remotehost:port[?options]
```

## URI参数

名称|默认值|描述
----|----|----
keepAlive|true|设置以确保socket不会因不活动而关闭。
tcpNoDelay|true|改进TCP协议性能的设置,不延迟。
backlog|暂无|允许为netty消费者（服务器）配置积压工作。 请注意，待办事项只是取决于操作系统的最佳选择。 将此选项设置为200,500或1000等值可告知TCP堆栈“接受”队列的时间长短。 如果此选项未配置，则积压取决于操作系统设置。
broadcast|false|设置在UDP上选择多点发送。
connectTimeout|10000|等待socket连接可用的时间，单位为毫秒。
reuseAddress|true|设置以便socket复用。
sync|true|设置为单向或请求-响应端点。
synchronous|false|默认情况下，使用异步路由引擎。 设置为true以强制同步处理。
ssl|false|设置指定SSL加密是否应用于此端点。
sslClientCertHeaders|false|当启用并在SSL模式下, netty消费者将丰富的骆驼消息, 标题具有有关客户端证书的信息, 如主题名称、颁发者名称、序列号和有效日期范围。
sendBufferSize|65536 bytes|在出站通信期间要使用的TCP/UDP缓冲区大小。大小为字节。
receiveBufferSize|65536 bytes|在入站通信期间使用的TCP/UDP缓冲区大小。大小为字节。
option.XXX|null|允许使用“option”来配置额外的netty选项。 作为前缀。 例如“option.child.keepAlive = false”来设置netty选项“child.keepAlive = false”。 请参阅Netty文档以了解可以使用的可能选项。
corePoolSize|10|组件启动时分配的线程数量。 默认为10.注意：这个选项从Camel 2.9.2开始被移除。 因为我们依赖于Nettys的默认设置。
maxPoolSize|100|可分配给此端点的最大线程数。默认为100。注意:此选项从camel 2.9.2 开始删除。因为我们依赖Nettys默认设置。
disconnect|false|在使用后是否要断开(关闭)与Netty通道的连接。可用于消费者和生产者。
lazyChannelCreation|true|如果在启动Camel生产者时，远程服务器没有启动并运行，可以延迟创建通道以避免异常。
transferExchange|false|仅用于TCP。 您可以通过wire而不是仅仅身体来转移交换。 以下字段被传送：正文，正文，错误正文，正文标题，正文标题，错误标题，交换属性，交换异常。 这要求对象是可序列化的。 骆驼将排除任何不可序列化的对象，并将其记录在WARN级别。
allowSerializedHeaders|false|camel 2.18仅当transferExchange为true时用于TCP。In/Out头文件和交换属性中的序列化对象被传送。
disconnectOnNoReply|true|如果启用了同步，那么这个选项指定NettyConsumer是否应该断开没有回复的连接。
noReplyLogLevel|WARN|如果启用同步，则此选项指定NettyConsumer在记录日志时使用哪个日志记录级别，而没有回复发送。 值是：FATAL, ERROR, INFO, DEBUG, OFF。
serverExceptionCaughtLogLevel|WARN|如果服务器（NettyConsumer）捕获一个异常，那么它使用这个日志记录级别进行记录。
serverClosedChannelExceptionCaughtLogLevel|DEBUG|如果服务器（NettyConsumer）捕获java.nio.channels.ClosedChannelException，那么使用这个日志记录级别来记录它。 这用于避免记录关闭的通道异常，因为客户端可以突然断开连接，然后在Netty服务器中引发一系列关闭的异常。
allowDefaultCodec|true|netty组件安装一个默认的编解码器，如果两者都是，编码器/deocder是空的，而textline是假的。将默认值设置为false，可以防止netty组件在过滤器链中的第一个元素安装默认的编解码器。
textline|false|仅用于TCP。 如果未指定编解码器，则可以使用此标记指示基于文本行的编解码器; 如果未指定或值为false，则通过TCP假定对象序列化。
delimiter|LINE|用于文本行编解码器的分隔符。 可能的值是LINE和NULL。
decoderMaxLineLength|1024|为textline编解码器使用的最大行长度。
autoAppendDelimiter|true|在使用文本编解码器发送时是否自动添加缺少的结束分隔符。
encoding|null|用于textline编解码器的编码(字符集名称)。如果没有提供,骆驼将使用JVM默认字符集。
workerCount|null|当netty在nio模式下工作时，它使用来自Netty的默认workerCount参数，即cpu_core_threads * 2。 用户可以使用此操作覆盖Netty的默认workerCount。
sslContextParameters|null|使用一个org.apache.camel.util.jsse SSL配置。SSLContextParameters实例。请参阅使用JSSE配置实用工具。
receiveBufferSizePredictor|null|配置缓冲区大小预测器。查看Jetty文档和这个邮件线程的详细信息。
requestTimeout|0|允许在调用远程服务器时使用netty生产者的超时。默认情况下不使用超时。该值以毫秒为单位, 因此如30000为30秒。requestTimeout正在使用netty的ReadTimeoutHandler以触发超时。骆驼 2.16,2.15.3还可以通过设置CamelNettyRequestTimeout 标头来重写此设置。
needClientAuth|false|在使用SSL时，配置服务器是否需要客户机身份验证。
usingExecutorService|true|是否使用executorService来处理骆驼路由内的消息,executorService可以从NettyComponent设置。
maximumPoolSize|16|有序线程池的核心池大小（如果正在使用）。注意：你可以从Camel 2.15,2.14.1开始在NettyComponent级别设置它。
producerPoolEnabled|true|生产者。是否启用了生产者池。要点:不要关闭它，因为处理并发和可靠的请求/应答需要池。
producerPoolMaxActive|-1|生产者。在给定的时间内设置可以由池分配的对象的数量(向客户端进行检查，或者空闲等待签出)。用负的值来表示没有限制。
producerPoolMinIdle|0|只有生产者。 在evictor线程（如果处于活动状态）产生新对象之前，设置生产者池中允许的最小实例数。
producerPoolMaxIdle|100|只有生产者。 设置池中“空闲”实例的数量上限。
producerPoolMinEvictableIdle|300000|只有生产者。 设置对象可能在池中空闲的最小时间量（以毫为单位），然后才有资格由空闲对象evictor逐出。
bootstrapConfiguration|null|仅限消费者。允许使用org.apache.camel.component.netty4.NettyServerBootstrapConfiguration实例配置netty ServerBootstrap 选项。这可用于为多个使用者重用相同的配置, 以便更方便地对其配置进行调整。
bossGroup|null|使用显式的io.netty.channel.EventLoopGroup作为指挥线程池。 例如与多个消费者共享一个线程池。 默认情况下，每个消费者都有自己的拥有1个核心线程的指挥池。
workerGroup|null|将显式io.netty.channel.EventLoopGroup用作工作者线程池。 例如与多个消费者或生产者共享一个线程池。 默认情况下，每个消费者或生产者都有自己的工作池，其中有2个CPU计数核心线程。
channelGroup|null|Camel 2.17例如使用显式的io.netty.channel.group.ChannelGroup将消息扩展到多个通道。
networkInterface|null|只有消费者。当使用UDP时，这个选项可以被用来指定一个网络接口的名称，比如eth0来加入一个多点发送组。
clientInitializerFactory|null|camel 2.15:使用一个自定义客户端初始化器工厂来控制通道中的管道。更多细节请参见下面的内容。
serverInitializerFactory|null|camel 2.15：使用自定义服务器初始化工厂来控制通道中的管道。 请参阅下面的更多细节。
clientPipelineFactory|null|已弃用：请改用clientInitializerFactory。
serverPipelineFactory|null|已弃用：请改用serverInitializerFactory。
udpConnectionlessSending|false|camel 2.15：只有生产者。 这个选项支持连接较少的UDP发送，这是发送和忽略操作。 如果没有人在接收端口上侦听，则连接的udp send将收到PortUnreachableException。
clientMode|false|camel 2.15：只有消费者。 如果clientMode为true，则netty使用者将把该地址连接为TCP客户端。
reconnect|true|camel 2.16：仅消费者。 仅在消费者的clientMode中使用，消费者将尝试自动断开连接。
reconnectInterval|10000|camel 2.16：仅消费者。 如果重新连接且启用了clientMode，则使用此选项。 以毫秒为单位的间隔尝试重新连接。
useByteBuf|false|camel 2.16:生产者。如果是真的，那么netty的生产者会在发送消息之前将消息体转换为ByteBuf。
udpByteArrayCodec|false|camel 2.16:当使用UDP协议时，将该选项转换为true，将数据作为字节数组发送，而不是默认的对象序列化编解码器。
reuseChannel|false|camel 2.17：只有生产者。 该选项允许制作者在处理Exchange的生命周期中重用相同的Netty通道。 如果您需要在骆驼路由中多次呼叫服务器并且想要使用相同的网络连接，则这是可用的。 当使用这个时，通道不会返回到连接池，直到Exchange完成; 如果disconnect选项设置为true，则断开连接。重复使用的通道作为与NettyConstants＃NETTY_CHANNEL密钥的交换属性存储在交换中，允许您在路由期间获取频道并使用它。
nativeTransport|false|camel 2.18：是否使用本地运输，而不是NIO。 本地传输利用主机操作系统，仅在某些平台上受支持。 您需要为正在使用的主机操作系统添加netty JAR。 详情请参阅：http：//netty.io/wiki/native-transports.html

## 基于注册表选项
编解码器处理程序和SSL密钥库可以在注册表中注册，例如在Spring XML文件中。

名称|描述
----|----
passphrase|用于加密/解密使用SSH发送的有效负载的密码设置。
keyStoreFormat|用于有效负载加密的密钥库格式。如果未设置, 则默认为 "JKS"。
securityProvider|安全提供程序用于有效负载加密。如果未设置, 则默认为"SunX509"。
keyStoreFile|已弃用:用于加密的客户端证书密钥库。
trustStoreFile|已弃用:用于加密的服务器端证书密钥库。
keyStoreResource|camel 2.11.1:用于加密的客户端证书密钥库。默认情况下从类路径加载, 但可以使用"classpath:"、 "file:"或"http:"来加载来自不同系统的资源。
trustStoreResource|camel 2.11.1:用于加密的服务器端证书密钥库。默认情况下从类路径加载, 但可以使用"classpath:"、 "file:"或"http:"来加载来自不同系统的资源。
sslHandler|引用可以用来返回SSL处理程序的类。
encoder|自定义的ChannelHandler类，可用于执行出站有效负载的特殊编组。 必须io.netty.channel.ChannelInboundHandlerAdapter。
encoders|要使用的编码器列表。 您可以使用由逗号分隔的值的字符串，并在注册表中查找值。 只要记住用＃前缀的值。
decoder|一个自定义的ChannelHandler类，可用于执行入站负载的特殊编组。 必须覆盖io.netty.channel.ChannelOutboundHandlerAdapter。
decoders|要使用的编码器列表。 您可以使用由逗号分隔的值的字符串，并在注册表中查找值。 只要记住用＃前缀的值。

## 示例

### 示例一：基于TCP协议的服务端

#### DataSetProcessor类
处理器

```java
import java.util.Map.Entry;
import org.apache.camel.Exchange;
import org.apache.camel.Processor;

public class DataSetProcessor implements Processor {
	@Override
	public void process(Exchange exchange) throws Exception {
		System.out.println("Netty4组件测试,客户端请求数据--->"+"\n"+"Body:"+exchange.getIn().getBody());
		System.out.println("----------------------------------------------------------------------");
		for (Entry<String,Object> map : exchange.getIn().getHeaders().entrySet()) {
			System.out.println("Header:"+"key--"+map.getKey()+",value--"+map.getValue());
		}
		System.out.println("----------------------------------------------------------------------");
        //响应数据
		exchange.getOut().setBody(new String("服务器响应数据：你好，我是服务器!!!"));
	}
}
```

#### Blueprint DSL 配置

```xml
<route>
	<from uri="netty4:tcp://localhost:5150"/> 
    <!--打印交换中的body,header-->
    <!--获取客户端请求数据，设置响应数据-->
	<to uri="class://com.shudi.databridge.test.DataSetProcessor"/>  	  
</route>	
```

### 示例二：基于TCP协议的客户端

#### Blueprint DSL 配置

```xml
<route>
	<from uri="timer://foo?period=50s"/> 
    <setBody>
        <constant>Hello Apache Camel 12-25</constant>
    </setBody>
    <to uri="netty4:tcp://localhost:5150"/>
    <!--打印交换中的body,header-->
    <!--获取服务端响应数据-->
	<to uri="log:Test"/>  	  
</route>	
```

### 示例运行
先运行服务端示例一，再运行客户端示例二

![](https://i.imgur.com/PSBupJm.png)

![](https://i.imgur.com/UYx84tI.png)

### 示例分析

Netty4组件提供在路由中进行socket通讯的功能，当然也可以和外部的socket进行交互。(注意：这里的socket通讯是基于Java NIO)
Java NIO详情链接：[http://www.iteye.com/magazines/132-Java-NIO](http://www.iteye.com/magazines/132-Java-NIO)
Socket通讯的底层协议为TCP和UDP，udp在路由中的用法和tcp类似。
TCP和UDP的区别：
1. 基于连接与无连接；
2. 对系统资源的要求（TCP较多，UDP少）；
3. UDP程序结构较简单；
4. 流模式与数据报模式；
5. TCP保证数据正确性，UDP可能丢包，TCP保证数据顺序，UDP不保证。