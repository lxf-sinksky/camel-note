# HTTP4组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-22*
*相关连接:[http://camel.apache.org/http4.html](http://camel.apache.org/http4.html)*

## 概述

HTTP4组件提供基于HTTP的端点以使用外部HTTP资源（作为使用HTTP调用外部服务器的客户端）。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-http4</artifactId>
    <version>x.x.x</version>
</dependency>
```
camel-http4 vs camel-http
camel-http4 使用apache HttpClient 4.x而camel-http 使用apache HttpClient 3.x

## URI
```
http4:hostname[:port][/resourceUri][?param1=value1][&param2=value2] 
```
默认情况下, 将使用端口80作为HTTP和443以进行HTTPS。

## HttpComponent 选项

名称|默认值|描述
----|----|----
maxTotalConnections|200|最大连接数。
connectionsPerRoute|20|每个路由的最大连接数。
cookieStore|null|camel 2.11.2/2.12.0:使用自定义 org.apache.http.client.CookieStore。默认情况下, 使用 org.apache.http.impl.client.BasicCookieStore , 它是内存中唯一的 cookie 存储区。注意: 如果 bridgeEndpoint=true , 则 cookie 存储被强制为 NOOP cookie 存储, 因为 cookie 不应该存储, 因为我们只是作为代理。
httpClientConfigurer|null|对注册表中的 org.apache.camel.component.http.HttpClientConfigurer 的引用。
clientConnectionManager|null|使用自定义 org.apache.http.conn.ClientConnectionManager。
httpBinding|null|使用自定义 org.apache.camel.component.http.HttpBinding。
httpContext|null|在执行请求时使用自定义 org.apache.http.protocol.HttpContext。
sslContextParameters|null|骆驼 2.8:使用自定义 org.apache.camel.util.jsse.SSLContextParameters。请参见使用JSSE配置实用程序。注意:每个 HttpComponent只支持一个org.apache.camel.util.jsse.SSLContextParameters的实例。如果需要两个或多个实例, 则应在每个实例上创建一个新的HttpComponent。
x509HostnameVerifier|BrowserCompatHostnameVerifier|您可以在注册表中引用不同的 org.apache.http.conn.ssl.X509HostnameVerifier实例, 如org.apache.http.conn.ssl.StrictHostnameVerifier 或 org.apache.http.conn.ssl.AllowAllHostnameVerifier。
connectionTimeToLive|-1|camel 2.11.0:连接的有效时间, 时间单位为毫秒, 默认值始终保持为活动状态。
allowJavaSerializedObject|false|camel 2.16.1/2.15.5:当请求使用context-type=application/x-java-serialized-object时, 是否允许java序列化。默认情况下, 这是关闭的。如果您启用此功能, 则请注意, java将反序列化请求中的传入数据, 这可能会带来潜在的安全风险。

## HttpEndpoint 选项

名称|默认值|描述
----|----|----
throwExceptionOnFailure|true|选项可以禁止在远程服务器响应失败的情况下抛出HttpOperationFailedException。 这使您可以获取所有的响应，而不管HTTP状态码如何。
bridgeEndpoint|false|如果为true，则HttpProducer将忽略Exchange.HTTP_URI标头，并使用该端点的URI进行请求。如果throwExcpetionOnFailure = false，则HttpProducer将返回所有错误响应给调用者。此外，如果为true，那么如果内容编码是gzip，则HttpProducer和CamelServlet将跳过gzip处理。
clearExpiredCookies|true|camel 2.11.2/2.12.0：是否在发送HTTP请求之前清除过期的cookie。 这可以确保cookie存储不会因添加新的cookie而不断增长，这些新的cookie在过期时会被删除。
cookieStore|null|camel 2.11.2/2.12.0:使用自定义org.apache.http.client.CookieStore。默认情况下, 使用 org.apache.http.impl.client.BasicCookieStore , 它是内存中唯一的cookie存储区。注意: 如果 bridgeEndpoint=true , 则 cookie 存储被强制为 NOOP cookie 存储, 因为 cookie 不应该存储, 因为我们只是作为代理。
disableStreamCache|false|DefaultHttpBinding会将请求输入流复制到流缓存中，并将其放入消息体中，如果此选项为false以支持多次读取，则DefaultHttpBinding将直接在消息体中设置请求输入流。从Camel 2.17开始：生产者支持这个选项，默认情况下允许直接使用响应流而不是流缓存。
headerFilterStrategy|null|对注册表中 org.apache.camel.spi.HeaderFilterStrategy 的实例的引用。它将用于在新创建 HttpEndpoint上应用自定义headerFilterStrategy。
httpBindingRef|null|已弃用, 将在camel 3.0中删除:对注册表中的org.apache.camel.component.http.HttpBinding 的引用。改用 httpBinding选项。
httpClientConfigurer|null|对注册表中的org.apache.camel.component.http.HttpClientConfigurer的引用。
httpContext|null|camel 2.9.2:在执行请求时使用自定义org.apache.http.protocol.HttpContext。
httpClient.XXX|null|在BasicHttpParams上设置选项。 例如httpClient.soTimeout = 5000将把SO_TIMEOUT设置为5秒。 查看以下参数bean的setter方法以获取完整的引用：AuthParamBean，ClientParamBean，ConnConnectionParamBean，ConnRouteParamBean CookieSpecParamBean，HttpConnectionParamBean和HttpProtocolParamBean。从Camel 2.13.0开始：httpClient被更改为配置HttpClientBuilder和RequestConfig.Builder，请查看API文档以获得完整的参考。 例如，由于此版本使用httpClient.socketTimeout = 5000来将套接字超时设置为5秒。
clientConnectionManager|null|使用自定义 org.apache.http.conn.ClientConnectionManager。
transferException|false|如果为true，客户端上的Exchange处理失败，并且引发的异常作为application/x-java-serialized-object内容类型（例如使用Jetty或SERVLET Camel组件）在响应中发回。在生产者方面，异常将被反序列化并抛出，而不是HttpOperationFailedException。 引起的异常需要被序列化。
sslContextParameters|null|骆驼 2.11.1:对注册表中的org.apache.camel.util.jsse.SSLContextParameters的引用。
x509HostnameVerifier|BrowserCompatHostnameVerifier|骆驼 2.7:您可以在注册表中引用不同的 org.apache.http.conn.ssl.X509HostnameVerifier实例, 如org.apache.http.conn.ssl.StrictHostnameVerifier或 org.apache.http.conn.ssl.AllowAllHostnameVerifier。
urlRewrite|null|camel 2.11：仅生产者引用自定义org.apache.camel.component.http4.UrlRewrite,它允许您在桥接/代理端点时重写URL。有关更多详细信息，请参阅UrlRewrite和如何使用Camel作为客户端和服务器之间的HTTP代理。
maxTotalConnections|null|camel 2.14：连接管理器所具有的最大连接数。如果这个选项没有设置，骆驼将使用该组件的设置。
connectionsPerRoute|null|camel 2.14: 每个路由的最大连接数。如果未设置此选项, 则骆驼将改用该组件的设置。
authenticationPreemptive|false|camel 2.11.3/2.12.2:如果此选项为true, camel-http4将向服务器发送抢占式基本身份验证。
eagerCheckContentAvailable|false|camel 2.16：仅限消费者,如果内容长度标头为0或不存在，是否希望检查HTTP请求是否有内容。 这可以在HTTP客户端不发送流式数据的情况下打开。
copyHeaders|true|camel 2.16：如果这个选项是true，那么根据复制策略，IN交换头将被复制到OUT交换头。 将其设置为false，只允许包含来自HTTP响应的头（不传播IN headers）。
okStatusCodeRange|200-299|camel 2.16:被视为成功响应的状态代码。这些值是包含的。
ignoreResponseBody|false|camel 2.16：如果这个选项是true，HTTP生产者不会读取响应主体并缓存输入流。
useSystemProperties|false|camel 2.18：如果这个选项是true，HTTP客户端将使用系统属性来配置的一些参数。
mapHttpMessageBody|true|camel 2.18：如果这个选项是真的，那么IN交换体将被映射到HTTP正文。 将其设置为false将避免HTTP映射。
mapHttpMessageHeaders|true|camel 2.18：如果这个选项是真的，那么IN交换体将被映射到HTTP标头。 将其设置为false将避免HTTP标头映射。
mapHttpMessageFormUrlEncodedBody|true|camel 2.18:如果此选项为 true, 则 exchange 窗体的 exchange 表单编码正文IN的将映射到 HTTP。将此设置为 false 将避免 HTTP 表单编码的正文映射。
connectionClose|false|camel 2.18:如果此选项为true,生产者将添加一个关闭header的连接到HTTP请求。
cookieHandler|null|camel 2.19:配置cookie处理程序以维护HTTP会话。

## 设置基本的身份验证和代理

从camel 2.8.0以后:

名称|默认值|描述
----|----|----
authDomain|null|用于身份验证的域名。
authHost|null|用于身份验的证主机名。
authPassword|null|用于身份验证的密码。
authUsername|null|身份验证的用户名。
proxyAuthHost|null|代理主机名。
proxyAuthPort|null|代理端口号。
proxyAuthScheme|null|代理方案, 将回退并在未配置的端点使用该方案。
proxyAuthUsername|null|代理身份验证的用户名。
proxyAuthPassword|null|代理身份验证的密码。
proxyAuthDomain|null|代理域名。
proxyAuthNtHost|null|代理Nt主机名。

## Message Headers

名称|类型|描述
----|----|----
Exchange.CONTENT_ENCODING|String|HTTP 内容编码。设置在IN和OUT消息中，以提供内容编码, 如gzip。
Exchange.CONTENT_TYPE|String|HTTP 内容类型。设置在IN和OUT消息中, 以提供内容类型, 如text/html。
Exchange.HTTP_CHARACTER_ENCODING|String|字符编码。
Exchange.HTTP_PATH|String|请求URI的路径。标头将用于使用 HTTP_URI生成请求URI。
Exchange.HTTP_QUERY|String|URI 参数。将重写直接在端点上设置的现有URI参数。
Exchange.HTTP_RESPONSE_CODE|int|来自外部服务器的HTTP响应代码。为OK的200。
Exchange.HTTP_RESPONSE_TEXT|String|来自外部服务器的HTTP响应文本。
Exchange.HTTP_URI|String|要调用的URI。此选项的值将覆盖直接在端点上设置的现有URI。它与camel端点URI不同, 您可以在其中配置端点选项, 如安全性等。此标头不支持它,它只是HTTP服务器的URI。

### 使用系统属性

当useSystemProperties=true camel-http4客户端可以使用以下系统属性:
- java.home
- javax.net.ssl.trustStoreType
- javax.net.ssl.trustStore
- javax.net.ssl.trustStoreProvider
- javax.net.ssl.trustStorePassword
- javax.net.ssl.keyStore
- javax.net.ssl.keyStoreProvider
- javax.net.ssl.keyStorePassword
- javax.net.ssl.keyStoreType
- http.proxyHost
- http.proxyPort
- http.nonProxyHosts
- http.keepAlive
- http.maxConnections
- ssl.KeyManagerFactory.algorithm
- ssl.TrustManagerFactory.algorithm

## 使用GET或POST进行调用

使用下面的算法来确定是否应该使用GET或POST HTTP方法:
1. 使用header中提供的方法。
2. 如果在标题中提供了查询字符串，则为GET。
3. 如果端点配置了查询字符串，则为GET。
4. 如果有数据要发送(正文不为空)。则为POST。
5. 否则, 为GET。

## 示例

### 示例一

#### 引用Rest组件发布服务

这里测试HTTP组件需要发布一个HTTP服务，所以引用Rest组件发布服务(关于HTTP服务发布，这里不过多讲解，详细请查看Rest组件文档)。

#### Blueprint DSL 配置
```xml
<route>
    <from uri="timer://foo?period=5s"/>
    <!--访问HTTP服务-->
	<to uri="http4:192.168.1.190:3387/esb-servlet-rest-blueprint/rest/user/123"/>
    <!--打印输出-->
    <to uri="class://com.shudi.databridge.test.DataSetProcessor"/>
</route> 
```

#### 示例一运行

![](https://i.imgur.com/MzAeJx4.png)

#### 示例一分析

在示例一中，定时器触发任务，然后使用http4组件访问已经发布的HTTP服务，获得HTTP服务返回的数据并打印。
这是http4组件的简单调用，我们将请求地址和参数全部写进了URI中，请求方法是由htt4组件自动匹配的（详见上面的使用GET或POST进行调用）。

### 示例二

#### Blueprint DSL 配置
```xml
<route>
     <from uri="timer://foo?period=5s" />  
     <!-- 指定提交方式 -->  
     <setHeader headerName="CamelHttpMethod">  
        <constant>GET</constant>  
     </setHeader>  
     <!-- 指定请求参数。注意：这里参数指定不适合rest风格的URI(所以本示例无法引用)
     <setHeader headerName="CamelHttpQuery">  
        <constant>account=hongshu&client_id=111111&response_type=code</constant>  
     </setHeader>
     --> 
     <!-- 指定请求地址 -->  
     <setHeader headerName="CamelHttpUri">  
        <constant>http://192.168.1.190:3387/esb-servlet-rest-blueprint/rest/user/456</constant>  
     </setHeader>  
     <!-- 指定请求的编码方式 -->  
     <setProperty propertyName="CamelCharsetName">  
        <constant>UTF-8</constant>  
     </setProperty>  
     <!-- 执行(调用完成后，body是inputStream，转换成string即可) -->  
     <to uri="http4://http" />  
     <!-- 转换成string -->  
     <convertBodyTo type="java.lang.String" charset="UTF-8" />  
     <to uri="class://com.shudi.databridge.test.DataSetProcessor" /> 
</route> 
```

#### 示例二运行

![](https://i.imgur.com/9aGyFjj.png)

#### 示例二分析

示例二中，将请求方法，参数，请求地址，请求编码格式等设置在Header中。
当路由运行到http4端点时，http4组件会根据Header中属性来替换http4://http端点的属性(bridgeEndpoint=true时,则HttpProducer将忽略CamelHttpUri标头，并使用该端点的URI进行请求)。
示例二相对于示例一写法复杂，但是示例二对htt4组件的运用更为灵活，比较适合复杂的集成环境。

### 补充：使用sslContextParameters
```xml
<camel:sslContextParameters id="sslContextParameters">
    <camel:keyManagers keyPassword="keyPassword">
        <camel:keyStore resource="/users/home/server/keystore.jks" password="keystorePassword"/>
    </camel:keyManagers>
</camel:sslContextParameters>
<to uri="https4://127.0.0.1/mail/?sslContextParametersRef=sslContextParameters"/>
```




