# Jetty组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-12-18*
*相关连接:[http://camel.apache.org/jetty.html](http://camel.apache.org/jetty.html)*

## 概述

jetty组件提供基于HTTP的端点来消费和生成HTTP请求。 也就是说，Jetty组件的行为如同一个简单的Web服务器。 Jetty也可以用作HTTP客户端，这意味着您也可以将其与Camel一起用作生产者。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-jetty</artifactId>
    <version>x.x.x</version>
</dependency>
```

>补充：camel-jetty,camel-core依赖尽量使用最新版本。否则会出现java.lang.NoClassDefFoundError异常。

## URI

```
jetty:http://hostname[:port][/resourceUri][?options]
```

## URI参数

名称|默认值|描述
----|----|----
bridgeEndpoint|false|camel 2.1：如果选项是真的，HttpProducer将忽略Exchange.HTTP_URI头，并使用端点的URI的请求。 您也可以将throwExceptionOnFailure设置为false，让HttpProducer将所有错误响应发送回去。Camel 2.3：如果选项为true，如果Content-Encoding是gzip，则HttpProducer和CamelServlet将跳过gzip处理。考虑设置disableStreamCache = true来优化桥接。
chunked|true|camel 2.2：如果这个选项是false,Jetty Servlet将禁用HTTP流，并设置响应的Content-Length标头。
continuationTimeout|null|camel 2.6：当使用Jetty作为消费者（服务器）时，允许设置以毫秒为单位的超时。 默认情况下，Jetty使用30000.您可以使用<= 0的值永不过期。 如果发生超时，则请求将过期，Jetty将向客户端返回HTTP错误503。此选项仅在与异步路由引擎一起使用Jetty时使用。
cookieHandler|null|Camel 2.19：仅生产者,配置cookie处理程序以维护HTTP会话。
disableStreamCache|false|camel 2.3：确定来自Jetty的原始输入流是否被缓存（Camel将读取流内存/溢出到文件，流缓存）缓存。 默认情况下，Camel会缓存Jetty输入流以支持多次读取，以确保Camel可以从流中检索所有数据。 但是，例如，当您需要访问原始流时，可以将此选项设置为true，例如,将其直接流式传输到文件或其他持久性存储。DefaultHttpBinding会将请求输入流复制到流缓存中，并将其放入消息体中，如果此选项为false以支持多次读取流。 如果您使用Jetty桥接/代理端点，则考虑启用此选项以提高性能，以防万一您不需要多次读取消息负载。
enableCORS|false|camel 2.15：如果选项是true，Jetty服务器将设置支持CORS开箱的CrossOriginFilter。
enableJmx|false|camel 2.3：如果这个选项是true，这个端点将启用Jetty JMX支持。 请参阅Jetty JMX支持以获取更多详细信息
enablemulti-partFilter|true|camel 2.5：是否启用Jetty org.eclipse.jetty.servlets.multi-partFilter。在桥接端点时将此选项设置为false，以确保多部分请求也被代理/桥接。
filterInit.xxx|null|camel 2.17：过滤器InitParameters的配置。例如，设置filterInit.parameter = value时，可以在调用过滤器init（）方法时使用该参数。
filtersRef|null|camel 2.9：允许使用一个自定义的过滤器被列入一个列表，并可以在注册表中找到。
handlers|null|在您的注册表中指定逗号分隔的org.mortbay.jetty.Handler实例集（例如您的Spring ApplicationContext）。 这些处理程序被添加到Jetty Servlet上下文中（例如，添加安全性）。注意：不能使用具有不同Jetty端点的不同处理程序使用相同的端口号。 处理程序与端口号关联。 如果您需要不同的处理程序，则使用不同的端口号。
headerFilterStrategy|null|camel 2.11：引用注册表中的org.apache.camel.spi.HeaderFilterStrategy实例。 它将用于在新创建的HttpJettyEndpoint上应用自定义headerFilterStrategy。
httpBindingRef|null|引用注册表中的org.apache.camel.component.http.HttpBinding。 可以使用HttpBinding来定制如何为消费者编写响应。
httpClient.xxx|null|Jetty的HttpClient的配置。 例如，设置httpClient.idleTimeout = 30000可将空闲超时设置为30秒。 而且httpClient.timeout = 30000会将请求超时设置为30秒，以防万一长时间运行请求/响应调用时您希望超时。
httpClient|null|对由此端点创建的所有生产者使用共享的org.eclipse.jetty.client.HttpClient。 这个选项只能在特殊情况下使用。
httpClientMinThreads|null|Camel 2.11：仅生产者：为HttpClient线程池中最小线程数设置一个值。 此设置会覆盖在组件级别配置的任何设置。 请注意，必须配置最小和最大值。 如果没有设置它默认为在Jetty的线程池中使用的最小8线程。
httpClientMaxThreads|null|Camel 2.11：仅生产者：为HttpClient线程池中的最大线程数设置一个值。 此设置会覆盖在组件级别配置的任何设置。 请注意，必须配置最小和最大值。 如果没有设置，则默认为Jetty线程池中使用的最多16个线程。
httpMethodRestrict|null|camel 2.11：仅用于消费者：只用于在HttpMethod匹配的情况下使用，比如GET / POST / PUT等。camel 2.15：可以用逗号分隔多个方法。
jettyHttpBindingRef|null|Camel 2.6.0+：引用注册表中的org.apache.camel.component.jetty.JettyHttpBinding。 JettyHttpBinding可以用来定制如何为生产者写一个响应。
matchOnUriPrefix|false|如果未找到精确匹配, CamelServlet是否应尝试通过匹配URI前缀来查找目标使用者。
multi-partFilterRef|null|camel 2.6:允许使用自定义多过滤器。注意: 设置 multi-partFilterRef将强制enablemulti-partFilter 的值true.
okStatusCodeRange|200-299|Camel 2.16：Producer only被视为成功响应的状态码。 价值是包容性的。 范围必须被定义为从 - 到包含短划线。
optionsEnabled|false|camel 2.17:指定是否为这个Jetty消费者启用HTTP OPTIONS。 默认情况下，OPTIONS被关闭。
proxyHost|null|camel 2.11:仅生产者Jetty客户端将使用的HTTP代理主机URL。
proxyPort|null|camel 2.11:仅生产者Jetty客户端使用的 HTTP 代理端口。
responseBufferSize|null|camel 2.12:在 javax.servlet.ServletResponse 上使用自定义缓冲区大小.
sendDateHeader|false|camel 2.14：如果选项是true，Jetty服务器将发送date header到发送请求的客户端。注意：确保没有其他共享相同端口的Camel Jetty端点，否则此选项可能无法正常工作。
sendServerVersion|true|camel 2.13：如果选项是true，Jetty将发送带有Jetty版本信息的服务器header到发送请求的客户端。注意：确保没有其他共享相同端口的Camel Jetty端点，否则此选项可能无法正常工作。
sessionSupport|false|指定是否启用Jetty服务器端的会话管理器。
sslContextParameters|null|camel 2.17：引用注册表中的org.apache.camel.util.jsse.SSLContextParameters。 此引用将覆盖组件级别的任何已配置的SSLContextParameters。请参阅使用JSSE配置公用程序。
sslContextParametersRef|null|camel 2.8：已弃用,引用注册表中的org.apache.camel.util.jsse.SSLContextParameters。 此引用将覆盖组件级别的任何已配置的SSLContextParameters。请参阅使用JSSE配置实用程序。
throwExceptionOnFailure|true|选项可以禁止在远程服务器响应失败的情况下抛出HttpOperationFailedException。 这使您可以获取所有的响应，而不管HTTP状态码如何。
traceEnabled|false|指定是否为此Jetty使用者启用HTTP TRACE。 默认情况下TRACE被关闭。
transferException|false|Camel 2.6：如果启用，并且客户端上的Exchange处理失败，并且引发的异常是作为application/x-java-serialized-object内容类型在响应中发回的。在生产者方面，异常将被反序列化并抛出，而不是HttpOperationFailedException。 引起的异常需要被序列化。
urlRewrite|null|Camel 2.11：Producer only引用自定义的org.apache.camel.component.http.UrlRewrite，它允许您在桥接/代理端点时重写URL。请参阅UrlRewrite和如何使用Camel作为客户端和服务器之间的HTTP代理的更多详细信息。
useContinuation|true|camel 2.6：是否使用Jetty服务器的Jetty支持。

## 组件参数
JettyHttpComponent 提供了以下选项:

名称|默认值|描述
----|----|----
allowJavaSerializedObject|false|camel 2.16.1/2.15.5:在请求使用context-type=application/x-java-serialized-object时是否允许java序列化。当true时, 请注意, java将将来自请求的传入数据反序列化为 java, 这可能会带来潜在的安全风险。
enableJmx|false|camel 2.3:如果此选项为true, 则对此端点启用Jetty JMX支持。有关更多详细信息, 请参见Jetty JMX支持。
errorHandler|null|camel 2.15: 此选项用于设置Jetty服务器使用的 ErrorHandler 。
httpClient|null|已弃用:仅生产者: 要使用自定义HttpClient与Jetty生产者。注意: 从camel 2.11中删除了此选项。改为在端点上设置选项。
httpClientMaxThreads|null|仅生产者: 在HttpClient线程池中设置最大线程数的值。请注意, 必须配置最小和最大值。
httpClientMinThreads|null|仅生产者: 在HttpClient线程池中设置最小线程数的值。请注意, 必须配置最小和最大值。
httpClientThreadPool|null|已弃用:仅生产者:为客户端使用自定义线程池。注意: 此选项已从camel 2.11中删除。
maxThreads|null|camel 2.5仅消费者: 为服务器线程池中的最大线程数设置值。请注意, 必须配置最小和最大值。
minThreads|null|camel 2.5仅消费者: 为服务器线程池中的最小线程数设置值。请注意, 必须配置最小和最大值。
proxyHost|null|camel 2.12.2/2.11.3使用HTTP代理主机URL。
proxyPort|null|camel 2.12.2/2.11.3:使用HTTP代理端口。
socketConnectors|null|camel 2.5仅消费者:包含每个端口号特定HTTP连接器的映射。使用与sslSocketConnectors相同的原则, 因此请参阅部分SSL支持以了解更多详细信息。
socketConnectorProperties|null|camel 2.5仅消费者。包含一般HTTP连接器属性的映射。使用与sslSocketConnectorProperties相同的原则, 因此请参阅部分SSL支持以了解更多详细信息。
sslContextParameters|null|camel 2.8:在组件级别配置自定义SSL/TLS配置选项。有关详细信息, 请参阅使用JSSE配置实用程序。
sslKeyPassword|null|仅消费者: 使用SSL时密钥库的密码。
sslKeystore|null|仅消费者: 密钥库的路径。
sslPassword|null|仅消费者: 使用SSL时的密码。
sslSocketConnectors|null|camel 2.3仅消费者:包含每个端口号特定SSL连接器的映射。有关详细信息, 请参阅SSL支持部分。
sslSocketConnectorProperties|null|camel 2.5仅消费者。包含常规SSL连接器属性的映射。有关详细信息, 请参阅SSL支持部分。
requestBufferSize|null|camel 2.11.2:允许在Jetty连接器上配置请求缓冲区大小的自定义值。
requestHeaderSize|null|camel 2.11.2:允许在Jetty连接器上配置请求header大小的自定义值。
responseBufferSize|null|camel 2.11.2:允许在Jetty连接器上配置响应缓冲区大小的自定义值。
responseHeaderSize|null|camel 2.11.2:允许在Jetty连接器上配置响应header大小的自定义值。
threadPool|null|camel 2.5仅消费者: 用于服务器的自定义线程池。此选项只应在特殊情况下使用。

## 示例

### 示例一：Jetty组件消费者
在路由中发布http服务

#### BeanProcessor类
处理器

```java
package com.shudi.databridge.test;

import java.util.Map.Entry;
import javax.servlet.http.HttpServletRequest;
import org.apache.camel.Exchange;
import org.apache.camel.Processor;
import org.apache.camel.http.common.HttpMessage;

public class BeanProcessor implements Processor {
	
	@Override
	public void process(Exchange exchange){
		    System.out.println("===============================================");  
            //打印消息Header参数
			for (Entry<String,Object> map : exchange.getIn().getHeaders().entrySet()) {
				System.out.println("Header:"+"key--"+map.getKey()+",value--"+map.getValue());
			} 
			System.out.println("================================================");
            //获取Request实例
	        HttpServletRequest req = exchange.getIn(HttpMessage.class).getRequest(); 
            //获取请求参数 
	        String name = req.getParameter("name");  
	        System.out.println("请求参数name打印："+name);  
            //set响应消息
	        exchange.getOut().setBody("Hello "+name+"!!!");  

	}
	
}
```

#### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 	
	 <route>
          <!--jetty消费者，发布http服务-->
          <from uri="jetty:http://localhost:8080"/>
          <!--处理器打印header参数，并设置响应消息-->
          <to uri="class:com.shudi.databridge.test.BeanProcessor"/>
	 </route> 	
</camelContext>	
```

#### 示例一运行

浏览器访问http://localhost:8080/?name=Hunter
![](https://i.imgur.com/NgMFYp5.png)

控制台信息
![](https://i.imgur.com/Ivb4CaS.png)


### 示例二：Jetty组件生产者
在路由中请求http服务

#### DataSetProcessor类
处理器

```java
package com.shudi.databridge.test;

import java.util.Map.Entry;
import org.apache.camel.Exchange;
import org.apache.camel.Processor;
import com.eds.esb.rest.User;

public class DataSetProcessor implements Processor {

	@Override
	public void process(Exchange exchange) throws Exception {
		System.out.println("Jetty组件测试--->"+"\n"+"Body:"+exchange.getIn().getBody(String.class));
		System.out.println("-------------------------------------------");
		for (Entry<String,Object> map : exchange.getIn().getHeaders().entrySet()) {
			System.out.println("Header:"+"key--"+map.getKey()+",value--"+map.getValue());
		}
		System.out.println("-------------------------------------------");
	}
}

```

#### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 	
	 <route>
          <from uri="timer://foo?period=20s"/>
          <!--设置请求参数,多个参数使用"&"分割-->
          <setHeader headerName="CamelHttpQuery">
               <constant>name=KAKA</constant>
          </setHeader>
          <!--jetty生产者端点，请求http服务(需保持示例一处于运行状态)-->
          <to uri="jetty:http://localhost:8080"/>
          <!--获取响应消息并打印-->
          <to uri="class:com.shudi.databridge.test.DataSetProcessor"/>
	 </route> 	
</camelContext>	
```

#### 示例二运行

控制台信息
![](https://i.imgur.com/b9vrDp1.png)

### 示例分析

Jetty是一个提供HHTP服务器、HTTP客户端和javax.servlet容器的开源项目。
Jetty组件集成了Jetty和Camel,提供了更便捷的服务发布和访问机制。
Jetty组件在集成环境中使前后端的数据交互更为直接。








