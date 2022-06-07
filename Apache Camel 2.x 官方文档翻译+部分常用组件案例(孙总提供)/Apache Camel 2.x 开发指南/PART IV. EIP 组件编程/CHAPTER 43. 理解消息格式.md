# 第43章. 理解消息格式
*karezflow 产品组
整理：孙勇
版本：0.1*

在开始使用Apache Camel编程之前，应该清楚的了解消息和消息交换是如何建模的。这是因为Apache Camel可以处理许多消息格式，基本消息类型设计为抽象格式。Apache Camel提供API访问和转换消息体和消息头的底层数据格式。

## 43.1. 交换
交换对象是封装接收到的消息和存储关联元数据（包括交换属性）的包装器。另外，如果当前消息调度到一个生产者端点，交换提供一个临时位置保存回复（Out消息）。在Apache Camel中，交换的一个重要特性是支持消息的懒创建。这可以在不需要显示访问消息的情况下显著优化路由。

![](./images/ExchangeObjectPassingthroughaRoute.png)

上图显示一个交换对象通过一个路由。在路由上下文，交换对象作为参数提供给`Processor.process()`方法。这意味着交换对象可以直接访问源端点、目标端点以及其间的所有处理器。

### Exchange 接口
**org.apache.camel.Exchange** 接口定义方法访问*In*和*Out*消息。
```java
// Access the In message
Message getIn();
void setIn(Message in);

// Access the Out message (if any)
Message getOut();
void setOut(Message out);
boolean hasOut();

// Access the exchange ID
String getExchangeId();
void setExchangeId(String id);
```

### 懒创建消息
Apache Camel支持懒创建*In*、*Out*和*Fault*消息。这意味着直到试图访问消息时创建（例如，调用 **getIn()** 或 **getOut()**）。懒消息创建继承 **org.apache.camel.impl.DefaultExchange** 类。

如果调用无参访问器（**getIn()** 或 **getOut()**），或者使用布尔值为true参数（**getIn(true))** 或 **getOut(true))**）调用访问器，如果未存在则默认方法创建一个新的消息实例。

如果使用布尔值为false参数（**getIn(false))** 或 **getOut(false))**）调用访问器，默认方法则返回当前消息。

### 懒创建交换IDs
Apache Camel支持懒创建交换IDs。可以在任意交换上调用`getExchangeId()`获得交换实例的唯一标识，该标识只在实际调用时生成。**DefaultExchange.getExchangeId()** 方法委托注册在 **CamelContext** 上的UUID生成器完成ID生成。

## 43.2. 消息
消息对象使用下列抽象模型表现消息：
- 消息体
- 消息头
- 消息附件

消息体和消息头可以是任意类型（它们定义的类型是 **Object**），消息附件定义类型是 **javax.activation.DataHandler**，可以包含任意的MIME类型。如果需要获得消息类容的具体标识，可以使用类型转换机制转换消息体和消息头。转换可能会使用序列化和反序列化机制。

Apache Camel的一个重要特性是支持赖创建消息体和消息头。在某些情况下，这意味着一个消息不需要在通过路由时完全解析。

### Message 接口
**org.apache.camel.Message** 接口定义方法访问消息体、消息头和消息附件。如下：
```java
// Access the message body
Object getBody();
<T> T getBody(Class<T> type);
void setBody(Object body);
<T> void setBody(Object body, Class<T> type);

// Access message headers
Object getHeader(String name);
<T> T getHeader(String name, Class<T> type);
void setHeader(String name, Object value);
Object removeHeader(String name);
Map<String, Object> getHeaders();
void setHeaders(Map<String, Object> headers);

// Access message attachments
javax.activation.DataHandler getAttachment(String id);
java.util.Map<String, javax.activation.DataHandler> getAttachments();
java.util.Set<String> getAttachmentNames();
void addAttachment(String id, javax.activation.DataHandler content)

// Access the message ID
String getMessageId();
void setMessageId(String messageId);
```
### 懒创建消息体、消息头和附件
Apache Camel 支持懒创建消息体、消息头和附件。这意味着消息体、消息头和附件对象直到需要时才创建。

例如，考虑下列路由从*In*消息访问 **foo** 消息头：

```java
from("SourceURL")
  .filter(header("foo")
  .isEqualTo("bar"))
  .to("TargetURL");
```

在这个路由，如果我们假设*SourceURL*引用的组件支持懒创建，*In*消息头在 **header("foo")** 调用执行前不用解析。在那个点上，底层消息实现头部解析和填充头部映射。在 **to("TargetURL")** 调用前，也就是路由未结束前，消息体不会被解析。在那个点上， 消息体被转换为格式要求并写入到目标端点。

通过等待直到在消息体、消息头和附件填充前最后一个可能的时刻，可以确保避免不必要的类型转换。在某些情况下，可以完全避免解析。例如，如果路由不包含消息头的明确引用，则消息可以在路由传输而无需解析。

在实践中，是否实现懒创建取决于底层组件的实现。一般来说，懒创建对于创建消息体很有价值，创建消息头和消息附件是昂贵的。

### 懒创建消息IDs
Apache Camel 支持懒创建消息IDs。就是说，一个消息ID的生成只当实际调用`getMessageId()`方法时生成。**DefaultExchange.getExchangeId()** 方法委托在** CamelContext**  注册的UUID生成器生成ID。

一些端点实现调用`getMessageId()`方法，如果端点实现一个需要唯一消息ID的协议。特别是JMS消息通常包含一个唯一消息ID的头，所以JMS组件自动调用`getMessageId()`方法来获取消息ID（这是由JMS端点`messageIdEnabled`选项控制）。

### 初始消息格式
*In*消息的初始格式由源端点决定，*Out*消息的初始格式由目标端点决定。如果底层组件支持懒创建，消息保持未解析直到应用程序显式访问该消息。大部分Apache Camel组件以相对原始的形式创建消息体。例如，使用诸如`byte[]`、`ByteBuffer`、`InputStream` 或`OutputStream`类型。这确保创建初始消息所需的开销是最小的。需要更详细的消息格式组件通常依赖于类型转化器和编组处理器。

### 类型转换器
初始消息的格式无关紧要，因为可以使用内置转换器轻松地将消息从一种格式转换为另一种格式。Apache Camel API中有各种方法公开类型转换功能。例如，**convertBodyTo(Class type)** 方法可以插入到转换In消息体的路由中，如下所示：

```java
from("SourceURL").convertBodyTo(String.class).to("TargetURL");
```

In消息体转换为**java.lang.String**。下列例子显示在In消息体结尾处添加一个字符串：

```java
from("SourceURL")
  .setBody(bodyAs(String.class).append("My Special Signature"))
  .to("TargetURL");
```

消息体在结尾处添加字符串前先转换为字符串。如果不需要明确转换消息体，可以：

```java
from("SourceURL")
  .setBody(body().append("My Special Signature"))
  .to("TargetURL");
```

`append()`方法在添加前自动将消息体转换为字符串。

### 消息类型转换方法
**org.apache.camel.Message** 接口公开一些明确的类型转换方法：
- **getBody(Class<T> type)**——返回类型为 **T** 的消息体。
- **getHeader(String name, Class<T> type)**——返回类型为 **T** 的消息头。

### 转换至XML
除了简单类型（如byte []，ByteBuffer，字符串等）之间的转换，内置转换器还支持转换为XML格式。例如，可以将消息体转换为org.w3c.dom.Document 类型。这种转换比简单转换更昂贵，因为它涉及解析整个消息并创建一个节点树来表示XML文档结构。可以转换为以下XML文档类型：
- org.w3c.dom.Document
- javax.xml.transform.sax.SAXSource

XML类型转换比起简单转换适用性窄。因为不是每个消息体都符合XML结构，类型转换可能失败。另一方面，路由器处理XML消息类型的场景很多。

### 序列化和反序列化
序列化涉及将高级格式转换为低级格式，反序列化涉及将低级格式转换为高级格式。以下两个处理器用于在路由中执行序列化和反序列化：
- marshal()
- unmarshal()

例如，要从文件读取序列化Java对象并将其反序列化成Java对象，可以使用下个示例进行路由定义：
```java
from("file://tmp/appfiles/serialized")
  .unmarshal()
  .serialization()
  .<FurtherProcessing>
  .to("TargetURL");
```
### 最终消息格式
当输入消息达到路由的末尾时，目标端点必须能够将消息正文转换为可写入物理端点的格式。同样的规则也适用于返回源端点的输出消息。该转换通常使用Apache Camel类型转换器隐含执行。通常，这涉及到一种低级格式到另一个低级格式。例如，从byte[]数组转换为InputStream类型。

## 43.3. 内置类型转换器
通常，类型转换器是易用调用的函数。如`Message.getBody(Class<T> type)`或者`Message.getHeader(String name, Class<T> type)`。也可以直接调用主类型转换器。例如，将给定值转换为字符串：

```java
org.apache.camel.TypeConverter tc =exchange.getContext().getTypeConverter();
String str_value = tc.convertTo(String.class, value);
```
### 基本类型转换器
Apache Camel提供内置转换器可以执行下列基本类型的转换：

- **java.io.File**
- **String**
- **byte[]** 和 **java.nio.ByteBuffer**
- **java.io.InputStream** 和 **java.io.OutputStream**
- **java.io.Reader** 和 **java.io.Writer**
- **java.io.BufferedReader** 和 **java.io.BufferedWriter**
- **java.io.StringReader**

但是，不是所有类型都是内置可转换的。内置转换器主要侧重于提供文件和字符串类型的转换。文件类型可以转换为上述类型，除了**Reader**, **Writer**, 和 **StringReader**。字符串类型可以转换为**File**, **byte[]**, **ByteBuffer**, **InputStream**, 或者 **StringReader**。从字符串转换为文件的工作方式则将字符串解释为文件名。**String**, **byte[]**,和 **ByteBuffer** 是内置可转换的。

>**注意：** 设置当前交换对象的 **Exchange.CHARSET_NAME** 属性，可以显式指定从 **byte[]** 转换为 **String** 和 **String** 转换为 **byte[]** 的字符集。例如，`exchange.setProperty("Exchange.CHARSET_NAME", "UTF-8")`。

### 集合类型转化器
Apache Camel 提供内置转换器可以执行下列集合类型的转换：
- **Object[]**
- **java.util.Set**
- **java.util.List**

支持上述集合类型之间的所有转换。

### Map类型转换器
Apache Camel 提供内置转换器执行下列Map类型的转换：
- java.util.Map
- java.util.HashMap
- java.util.Hashtable
- java.util.Properties

上述map类型也可以转换为 **java.util.Set** 类型，其中集合元素为 **MapEntry<K,V>** 类型。

### DOM类型转换器
可以指向下列DOM类型的转换：
- **org.w3c.dom.Document**从 byte[], String, java.io.File, 和 java.io.InputStream 类型转换。
- **org.w3c.dom.Node**
- **javax.xml.transform.dom.DOMSource**—从String类型转换。
- **javax.xml.transform.Source**—从byte[]和String类型转换。

支持上述Dom类型之间的转换。

### SAX类型转换器
可以对** javax.xml.transform.sax.SAXSource** 类型执行转换, 它支持 sax 事件驱动的 xml 解析器 (有关详细信息, 请参见 sax 网站)。可以从以下类型转换为 saxsource:
- String
- InputStream
- Source
- StreamSource
- DOMSource

### 枚举类型转换器
Apache Camel 提供了一个类型转换器，用于将字符产转换为枚举类型。其中，字符串值从指定的枚举类型转换为匹配的枚举常量。转换消息正文时很少需要此类型转换器，但是经常在内部使用它来选择特定的选项。例如，在设置日志记录级别选项时，以下info值转换为枚举常量：
```xml
<to uri="log:foo?level=INFO"/>
```

这是因为枚举类型转换器不区分大小写，下列用法也可以：
```xml
<to uri="log:foo?level=info"/>
<to uri="log:foo?level=INfo"/>
<to uri="log:foo?level=InFo"/>
```

### 自定义类型转换器
Apache Camel 允许使用自定义的类型转换。详见第45章：类型转换。

## 43.4. 内置UUID生成器
Apache Camel 允许在 **CamelContext** 中注册一个UUID生成器。然后，每当Apache Camel需要生成唯一的Id时就会使用这个UUID生成器。特别是，`Exchange.getExchangeId()` 和 `Message.getMessageId()` 方法调用注册的UUID生成器返回Id。

### 提供的UUID生成器
可以配置使用下列UUID生成器：
- **org.apache.camel.impl.ActiveMQUuidGenerator**—(默认) 生成与Apache ActiveMQ  相同风格的Id。此实现可能不适用所有应用程序，因为它使用了一些在云计算环境中禁止的JDK APIs（Google App Engine ）。
- **org.apache.camel.impl.SimpleUuidGenerator**—实现一个简单计数器ID，开始于1。基于 **java.util.concurrent.atomic.AtomicLong** 类型，因此是线程安全。
- **org.apache.camel.impl.JavaUuidGenerator**—基于 **java.util.UUID** 类型。由于 **java.util.UUID** 是同步的，这可能会影响某些高度并发系统的性能。

### 自定义UUID生成器
继承 **org.apache.camel.spi.UuidGenerator** 接口实现一个自定义UUID生成器。必须实现`generateUuid()`方法并返回一个唯一的ID字符串。

```java
package org.apache.camel.spi;
/**
* Generator to generate UUID strings.
*/
public interface UuidGenerator {
  String generateUuid();
}
```

### 使用Java指定UUID生成器
在当前 **CamelContext** 对象调用`setUuidGenerator()`方法替换默认UUID生成器，如下：
```java
getContext().setUuidGenerator(new org.apache.camel.impl.SimpleUuidGenerator());
```

>**注意：** 应在任何路由激活之前，在启动过程中调用`setUuidGenerator()`方法。

### 使用Spring指定UUID生成器
使用spring bean元素创建UUID生成器实例替换默认的UUID生成器。在创建 **camelContext** 实例时，它会自动查找spring注册，搜索实现  **org.apache.camel.spi.UuidGenerator** 的bean。如下所示：

```xml
<beans ...>
  <bean id="simpleUuidGenerator" class="org.apache.camel.impl.SimpleUuidGenerator" />

  <camelContext id="camel" xmlns="http://camel.apache.org/schema/spring">
    ...
  </camelContext>
  ...
</beans>
```