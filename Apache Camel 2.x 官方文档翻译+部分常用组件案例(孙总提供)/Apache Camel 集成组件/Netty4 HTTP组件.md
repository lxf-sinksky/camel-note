# Netty4 HTTP组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-12-25*
*相关连接:[http://camel.apache.org/netty4-http.html](http://camel.apache.org/netty4-http.html)*

## 概述

Netty4-HTTP组件是Netty4组件的一个扩展，用于使用Netty4进行HTTP传输。
Netty是基于流的，这意味着它接收到的输入被作为一个流提交给Camel。您将只能读取一次流的内容。如果发现邮件正文显示为空的情况，或者需要多次访问数据（例如：进行多点发送或重新传送错误处理）您应该使用流缓存或将消息正文转换为可安全重复读取多次的字符串。

>注意:Netty4 http 使用io.netty.handler.codec.http.HttpObjectAggregator将整个流读入内存, 以生成整个完整的http消息。但生成的消息仍然是基于流的消息, 可读一次。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-netty4-http</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
netty4-http:http://localhost:port[?options]
```

## URI参数
该组件继承了Netty4的所有选项。请查看Netty4组件文档。注意，Netty4的一些选项在使用这个Netty4 HTTP组件时是不适用的，比如与UDP传输相关的选项。

名称|默认值|描述
----|----|----
chunkedMaxContentLength|1mb|以字节为单位的值,Netty HTTP服务器上接收到的每个分块帧的最大内容长度。
compression|false|如果客户端支持HTTP头，则允许在Netty HTTP服务器上使用gzip/deflate进行压缩。
headerFilterStrategy|暂无|使用自定义org.apache.camel.spi.HeaderFilterStrategy来过滤标题。
httpMethodRestrict|暂无|在Netty HTTP使用者上禁用HTTP方法。 您可以指定多个逗号分隔。
mapHeaders|true|如果启用此选项，则在从Netty到Camel消息的绑定期间，标题也将被映射（例如，也作为标题添加到骆驼消息中）。 您可以关闭此选项以禁用此选项。 仍然可以使用方法getHttpRequest（）从org.apache.camel.component.netty4.http.NettyHttpMessage消息中访问标头，该方法返回Netty HTTP请求io.netty.handler.codec.http.HttpRequest实例。
matchOnUriPrefix|false|如果找不到完全匹配，那么camel应该通过匹配URI前缀来尝试找到目标消费者。 请参阅下面的更多细节。
nettyHttpBinding|暂无|使用自定义org.apache.camel.component.netty4.http.NettyHttpBinding绑定到Netty和Camel Message API。
bridgeEndpoint|false|如果该选项为true，则生产者将忽略Exchange.HTTP_URI头，并使用该端点的URI进行请求。 您也可以将throwExceptionOnFailure设置为false，让生产者发回所有的错误响应。
throwExceptionOnFailure|true|选项以禁用在远程服务器响应失败时引发HttpOperationFailedException 。这使您可以获取HTTP状态代码的所有响应regardles。
traceEnabled|false|指定是否为此Netty HTTP使用者启用HTTP TRACE。 默认情况下TRACE被关闭。
transferException|false|如果已启用，并且客户端上的Exchange处理失败，并且引发的异常是作为application / x-java-serialized-object内容类型在响应中发回的。 在生产者方面，异常将被反序列化并抛出，而不是HttpOperationFailedException。 引起的异常需要被序列化。
urlDecodeHeaders|暂无|如果启用此选项，则在从Netty到Camel Message的绑定过程中，标题值将被URL解码。
nettySharedHttpServer|null|使用共享的Netty4 HTTP服务器。 有关更多详细信息，请参阅Netty HTTP Server示例。
disableStreamCache|false|确定是否缓存来自Netty HttpRequest＃getContent（）的原始输入流（Camel将读取流缓存到基于轻量内存的Stream缓存）。 默认情况下，Camel会缓存Netty输入流以支持多次读取，以确保Camel可以从流中检索所有数据。 但是，当您需要访问原始流时，您可以将该选项设置为true，例如将它直接流到一个文件或其他持久化存储中。 请注意，如果启用此选项，则无法多次读取Netty流，并且需要手动重置Netty原始流上的阅读器索引。
securityConfiguration|null|仅消费者。引用org.apache.camel.component.netty4.http.NettyHttpSecurityConfiguration来配置安全的web资源。
send503whenSuspended|true|仅消费者。当消费者被暂停时是否发回HTTP状态码503。 如果该选项为false，则当消费者被挂起时Netty Acceptor被解除绑定，所以客户端不能再连接。
maxHeaderSize|8192|camel 2.15.3：仅消费者。 所有标题的最大长度。 如果每个标题长度的总和超过这个值，则会引发io.netty.handler.codec.TooLongFrameException异常。
okStatusCodeRange|200-299|camel 2.16：被认为是成功回应的状态码。范围必须定义为从200到299。
useRelativePath|false|Camel 2.16：仅生产者：是否在请求行中使用路径（/myapp）或默认的绝对URI（http://0.0.0.0:8080/myapp）
cookieHandler|null|Camel 2.19：仅生产者：配置cookie处理程序以维护HTTP会话。

### NettyHttpSecurityConfiguration具有以下选项

名称|默认值|描述
----|----|----
authenticate|true|是否启用认证。 可以用来快速关闭此功能。
constraint|Basic|约束的支持。目前只有Basic的实现和支持。
realm|null|JAAS 安全领域的名称。此选项是必需的。
securityConstraint|null|允许插入安全约束映射器，您可以在其中定义ACL到Web资源。
securityAuthenticator|null|允许插入执行身份验证的身份验证器。 如果没有配置，则默认使用org.apache.camel.component.netty4.http.JAASSecurityAuthenticator。
loginDeniedLoggingLevel|DEBUG|登录尝试失败时使用的记录级别，可以查看登录失败的更多细节。
roleClassName|null|指定包含用户角色的Principal实现的FQN类名称。 如果没有指定，那么Netty4 HTTP组件默认情况下假定一个Principal是基于角色的，如果它的FQN类名在其类名中具有小写字角色的话。 您可以指定多个以逗号分隔的类名称。

## Message Headers

名称|类型|描述
----|----|----
CamelHttpMethod|String|允许控制使用什么HTTP方法, 如GET,POST,TRACE etc等。该类型也可以是io.netty.handler.codec.http.HttpMethod实例。
CamelHttpQuery|String|允许提供URI查询参数作为覆盖端点配置的字符串值。 使用＆符号分隔多个参数。 例如：foo=bar＆beer=yes。
CamelHttpPath|String|允许提供URI上下文路径和查询参数作为覆盖端点配置的字符串值。 这允许重复使用相同的生产者调用相同的远程http服务器，但使用动态上下文路径和查询参数。
Content-Type|String|设置HTTP正文的内容类型。 例如：text/plain;字符集=“UTF-8”。
CamelHttpResponseCode|int|允许设置要使用的HTTP状态代码。默认情况下,200用于成功,500用于失败。


## 示例

### 示例一：使用Netty4-HTTP组件消费者发布HTTP服务

#### DataSetProcessor类
处理器，接收请求，设置响应数据

```java
import java.util.Map.Entry;
import org.apache.camel.Exchange;
import org.apache.camel.Processor;
import org.apache.camel.component.netty4.http.NettyChannelBufferStreamCache;

public class DataSetProcessor implements Processor {

	@Override
	public void process(Exchange exchange) throws Exception {
	    //HTTP请求数据包以NettyChannelBufferStreamCache缓存实例在交换中传输
		NettyChannelBufferStreamCache nettyBuf = exchange.getIn().getBody(NettyChannelBufferStreamCache.class);
		String body;
        //判断请求数据包是否为空
		if (nettyBuf.length() == 0) {
			body = null;
		} else {
			byte[] req = new byte[(int) nettyBuf.length()];
			nettyBuf.read(req);
			body = new String(req);
		}
        //输出打印body,header
		System.out.println("Netty4 HTTP组件测试,客户端请求输入流--->" + "\n" + "Body:" + body);
		System.out.println("----------------------------------------------------------------------");
		for (Entry<String, Object> map : exchange.getIn().getHeaders().entrySet()) {
			System.out.println("Header:" + "key--" + map.getKey() + ",value--" + map.getValue());
		}
		System.out.println("----------------------------------------------------------------------");
		exchange.getOut().setBody("服务器响应数据：你好，我是Netty4 HTTP服务器!!!");
	}
}
```

#### Blueprint DSL 配置

```xml
<route>
	<from uri="netty4-http:http://localhost:8080"/> 
    <!--打印交换中的body,header-->
    <!--获取客户端请求数据，设置响应数据-->
	<to uri="class://com.shudi.databridge.test.DataSetProcessor"/>  	  
</route>
```

#### 示例运行

使用SoapUI访问HTTP服务
![](https://i.imgur.com/I9BwXKI.png)

Netty4 HTTP服务器运行日志
![](https://i.imgur.com/v83l2Cq.png)

### 示例二：使用Netty4-HTTP组件生产者访问HTTP服务

#### Blueprint DSL 配置

```xml
<route>
	<from uri="timer://foo?period=50s"/> 
    <setHeader headerName="CamelHttpQuery">
         <constant>id=123456&name=Hunter</constant>
    </setBody>
    <setBody>
         <constant>Hello Apache Camel ! ! !</constant>
    </setBody>
    <to uri="netty4-http:http://localhost:8080"/>  
	<to uri="log:Test"/>  	  
</route>
```

#### 示例运行

Netty4 HTTP服务器运行日志（使用示例一）
![](https://i.imgur.com/ry0ssZU.png)

Netty4 HTTP客户端运行日志
![](https://i.imgur.com/LZ2wwhG.png)

### 示例分析

Netty4-HTTP组件在路由中提供了发布和访问HTTP服务的机制。
Netty4-HTTP组件完全继承与Netty4组件，因为HTTP是基于TCP协议的，所以Netty4组件的部分功能得到了重用（UDP协议除外）。
Netty4-HTTP组件中数据传输是依赖于Java NIO方式。