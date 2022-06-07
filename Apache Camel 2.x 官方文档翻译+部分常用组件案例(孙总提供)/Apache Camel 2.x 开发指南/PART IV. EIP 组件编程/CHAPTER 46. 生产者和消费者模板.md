# CHAPTER 46. 生产者和消费者模板
*karezflow 产品组
整理：孙勇
版本：0.1*

Apache Camel中的生产者和消费者模板是通过Spring容器API的功能进行建模的，被称为**模板**。通过简化的易于使用的API访问资源。 在Apache Camel的情况下，生产者模板和消费者模板提供简化的接口，用于向生产者端点和消费者端点发送消息并从其接收消息。

## 46.1. 使用生产者模板
### 46.1.1. 生产者模板介绍
生产者模板支持各种不同的调用生产者端点的方法。有些方法支持请求消息的不同格式（作为Exchange对象，作为邮件正文，具有单个标题设置的消息体等）并且有方法来支持同步和异步的调用方式。总的来说，生产者模板方法可以分为以下几类：
- 同步调用
- 用处理器的同步调用
- 异步调用
- 用处理器的异步调用

#### 同步调用
调用端点的方法同步具有以下形式的名称：**sendSuffix()**和**requestSuffix()**。例如，使用默认消息交换模式（MEP）或明确指定的MEP调用端点的方法名为**send()**，**sendBody()**和**sendBodyAndHeader()**（其中这些方法分别发送Exchange对象，消息身体或邮件正文和标题值）。如果要强制MEP为InOut（请求/回复语义），可以调用**request()**，**requestBody()** 和 **requestBodyAndHeader()** 方法。

以下示例显示如何创建ProducerTemplate实例并将其用于将消息体发送到`activemq：MyQueue`端点。该示例还显示了如何使用**sendBodyAndHeader()** 发送消息体和消息头。

```java
import org.apache.camel.ProducerTemplate
import org.apache.camel.impl.DefaultProducerTemplate
...
ProducerTemplate template = context.createProducerTemplate();

// Send to a specific queue
template.sendBody("activemq:MyQueue", "<hello>world!</hello>");

// Send with a body and header
template.sendBodyAndHeader(
  "activemq:MyQueue",
  "<hello>world!</hello>",
  "CustomerRating", "Gold" );
```

#### 用处理器的同步调用
同步调用的一个特殊情况是您使用**Processor**参数而不是**Exchange**参数提供**send()** 方法。 在这种情况下，生产者模板隐含地要求指定的端点创建一个Exchange实例（通常，但默认情况下并不总是具有InOnly MEP）。 然后将此默认交换传递给处理器，该处理器初始化exchange对象的内容。

以下示例显示如何将由MyProcessor处理器初始化的交换发送到activemq：MyQueue端点。

```java
import org.apache.camel.ProducerTemplate
import org.apache.camel.impl.DefaultProducerTemplate
...
ProducerTemplate template = context.createProducerTemplate();

// Send to a specific queue, using a processor to initialize
template.send("activemq:MyQueue", new MyProcessor());
```

MyProcessor 类实现如下例所示。 除了设置In消息正文（如此处所示）之外，还可以初始化消息头和交换属性。

```java
import org.apache.camel.Processor;
import org.apache.camel.Exchange;
...
public class MyProcessor implements Processor {
  public MyProcessor() { }

  public void process(Exchange ex) {
    ex.getIn().setBody("<hello>world!</hello>");
  }
}
```

#### 异步调用
异步调用端点的方法具有asyncSendSuffix() 和asyncRequestSuffix() 形式的名称。例如，使用默认消息交换模式（MEP）或明确指定的MEP调用端点的方法名为asyncSend（）和asyncSendBody() （其中这些方法分别发送Exchange对象或消息体）。如果要强制MEP为InOut（请求/回复语义），则可以调用asyncRequestBody() ，asyncRequestBodyAndHeader() 和asyncRequestBodyAndHeaders（）方法。

以下示例显示如何将异步交换发送到direct：start端点。 asyncSend() 方法返回一个java.util.concurrent.Future对象，用于稍后检索调用结果。

```java
import java.util.concurrent.Future;
import org.apache.camel.Exchange;
import org.apache.camel.impl.DefaultExchange;
...
Exchange exchange = new DefaultExchange(context);
exchange.getIn().setBody("Hello");
Future<Exchange> future = template.asyncSend("direct:start", exchange);

// You can do other things, whilst waiting for the invocation to complete
...
// Now, retrieve the resulting exchange from the Future
Exchange result = future.get();
```

生产者模板还提供了异步发送消息体的方法（例如，使用asyncSendBody() 或asyncRequestBody() ）。在这种情况下，您可以使用以下帮助方法之一从Future对象中提取返回的消息正文：

```java
<T> T extractFutureBody(Future future, Class<T> type);
<T> T extractFutureBody(Future future, long timeout, TimeUnit unit,
Class<T> type) throws TimeoutException;
```

extractFutureBody() 方法的第一个版本将阻塞，直到调用完成并且回复消息可用。 extractFutureBody() 方法的第二个版本允许您指定超时。两种方法都有一个类型参数类型，它使用内置的类型转换器将返回的消息体转换为指定的类型。

以下示例显示如何使用asyncRequestBody() 方法将消息体发送到direct：start端点。然后使用阻塞extractFutureBody() 方法从Future对象检索回复消息体。

```java
Future<Object> future = template.asyncRequestBody("direct:start",
"Hello");
// You can do other things, whilst waiting for the invocation to complete
...
// Now, retrieve the reply message body as a String type
String result = template.extractFutureBody(future, String.class);
```
#### 异步调用与回调
在前面的异步示例中，请求消息在子线程中调度，而主线程检索和处理回复。生产者模板还提供了使用`asyncCallback()`，`asyncCallbackSendBody()`或`asyncCallbackRequestBody()`方法之一来处理子线程中的回复的选项。在这种情况下，您将提供一个回调对象（**org.apache.camel.impl.SynchronizationAdapter** 类型），一旦回复消息到达，它就会在子线程中自动调用。

同步回调接口定义如下：

```java
package org.apache.camel.spi;

import org.apache.camel.Exchange;

public interface Synchronization {
  void onComplete(Exchange exchange);
  void onFailure(Exchange exchange);
}
```

在接收到正常回复时调用`onComplete()`方法，并且在接收到故障消息回复时调用`onFailure()`方法。只有这些方法之一被调用，所以您必须覆盖它们，以确保处理所有类型的回复。

以下示例显示如何将交换发送到`direct：start`端点，其中由 **SynchronizationAdapter** 回调对象在子线程中处理回复消息。

```java
import java.util.concurrent.Future;
import java.util.concurrent.TimeUnit;
import org.apache.camel.Exchange;
import org.apache.camel.impl.DefaultExchange;
import org.apache.camel.impl.SynchronizationAdapter;
...
Exchange exchange = context.getEndpoint("direct:start").createExchange();
exchange.getIn().setBody("Hello");

Future<Exchange> future = template.asyncCallback("direct:start",
exchange, new SynchronizationAdapter() {
  @Override
  public void onComplete(Exchange exchange) {
  assertEquals("Hello World", exchange.getIn().getBody());
  }
});
```

其中 **SynchronizationAdapter** 类是默认的实现同步接口，您可以覆盖以提供自己的`onComplete()`和`onFailure()`回调方法的定义。

您仍然可以从主线程访问回复，因为`asyncCallback()`方法也返回一个 **Future** 对象，例如：

```java
// Retrieve the reply from the main thread, specifying a timeout
Exchange reply = future.get(10, TimeUnit.SECONDS);
```

### 46.1.2. 同步发送
同步发送方法是可以用于调用生产者端点的方法的集合，其中当前线程阻塞，直到方法调用完成并且已收到回复（如果有的话）。 这些方法与任何类型的消息交换协议兼容。

#### 发送交换
基本的`send()`方法是使用交换的消息交换模式（MEP）将Exchange对象的内容发送到端点的通用方法。 返回值是在生产者端点处理完毕后可能收到的交换（可能包含Out消息，具体取决于MEP）。

`send()`方法有三种发送交换器，可以通过以下方式之一指定目标端点：作为默认端点，作为端点URI或端点对象。

```java
Exchange send(Exchange exchange);
Exchange send(String endpointUri, Exchange exchange);
Exchange send(Endpoint endpoint, Exchange exchange);
```

#### 发送由处理器填充的交换
一般`send()`方法的一个简单变化是使用处理器来填充默认的交换，而不是明确提供交换对象（详见“处理器的同步调用”一节）。

用于发送由处理器填充的交换的`send()`方法可以通过以下方式之一指定目标端点：作为默认端点，作为端点URI或端点对象。 此外，您还可以通过提供模式参数来指定交易所的MEP，而不是接受默认值。

```java
Exchange send(Processor processor);
Exchange send(String endpointUri, Processor processor);
Exchange send(Endpoint endpoint, Processor processor);
Exchange send(String endpointUri,ExchangePattern pattern,Processor processor);
Exchange send(Endpoint endpoint,ExchangePattern pattern,Processor processor);
```

#### 发送消息体
如果您只关心要发送的消息正文的内容，则可以使用`sendBody()`方法将消息体作为参数提供，并让生产者模板将主体插入到默认交换对象中。

`sendBody()`方法可以通过以下方式之一指定目标端点：作为默认端点，作为端点URI或Endpoint对象。 此外，您还可以通过提供模式参数来指定交易所的MEP，而不是接受默认值。没有模式参数的方法返回void（即使在某些情况下调用可能会产生回复）; 并且具有模式参数的方法返回Out消息的主体（如果有的话）或In消息的主体（否则）。

```java
void sendBody(Object body);
void sendBody(String endpointUri, Object body);
void sendBody(Endpoint endpoint, Object body);
Object sendBody(String endpointUri,ExchangePattern pattern,Object body);
Object sendBody(Endpoint endpoint,ExchangePattern pattern,Object body);
```
#### 发送消息正文和标题
为了测试的目的，尝试单个头设置的效果通常是有趣的，而`sendBodyAndHeader()`方法对于这种头测试是有用的。 您将消息正文和标题设置提供给`sendBodyAndHeader()`的参数，并让生产者模板将主体和标题设置插入到默认的交换对象中。

`sendBodyAndHeader()`方法允许您以下列方式之一指定目标端点：作为默认端点，作为端点URI或端点对象。 此外，您还可以通过提供模式参数来指定交易所的MEP，而不是接受默认值。 没有模式参数的方法返回void（即使在某些情况下调用可能会产生回复）; 并且具有模式参数的方法返回Out消息的主体（如果有的话）或In消息的主体（否则）。

```java
void sendBodyAndHeader(
  Object body,
  String header,
  Object headerValue
);
void sendBodyAndHeader(
  String endpointUri,
  Object body,
  String header,
  Object headerValue
);
void sendBodyAndHeader(
  Endpoint endpoint,
  Object body,
  String header,
  Object headerValue
);
Object sendBodyAndHeader(
  String endpointUri,
  ExchangePattern pattern,
  Object body,
  String header,
  Object headerValue
);
Object sendBodyAndHeader(
  Endpoint endpoint,
  ExchangePattern pattern,
  Object body,
  String header,
  Object headerValue
);
```

`sendBodyAndHeaders()`方法与`sendBodyAndHeader()`方法类似，只不过只提供一个头设置，这些方法允许您指定头设置的完整哈希映射。

```java
void sendBodyAndHeaders(
  Object body,
  Map<String, Object> headers
);
void sendBodyAndHeaders(
  String endpointUri,
  Object body,
  Map<String, Object> headers
);
void sendBodyAndHeaders(
  Endpoint endpoint,
  Object body,
  Map<String, Object> headers
);
Object sendBodyAndHeaders(
  String endpointUri,
  ExchangePattern pattern,
  Object body,
  Map<String, Object> headers
);
Object sendBodyAndHeaders(
  Endpoint endpoint,
  ExchangePattern pattern,
  Object body,
  Map<String, Object> headers
);
```
#### 发送消息正文和交换属性
您可以尝试方法使用`sendBodyAndProperty()`该设置设置单个交换属性的效果。 您将消息正文和属性设置提供给`sendBodyAndProperty()`的参数，并让生产者模板负责将主体和交换属性插入到默认交换对象中。

`sendBodyAndProperty()`方法可以通过以下方式之一指定目标端点：作为默认端点，作为端点URI或端点对象。 此外，您还可以通过提供模式参数来指定交易所的MEP，而不是接受默认值。 没有模式参数的方法返回void（即使在某些情况下调用可能会产生回复）; 并且具有模式参数的方法返回Out消息的主体（如果有的话）或In消息的主体（否则）。

```java
void sendBodyAndHeaders(
  Object body,
  Map<String, Object> headers
);
void sendBodyAndHeaders(
  String endpointUri,
  Object body,
  Map<String, Object> headers
);
void sendBodyAndHeaders(
  Endpoint endpoint,
  Object body,
  Map<String, Object> headers
);
Object sendBodyAndHeaders(
  String endpointUri,
  ExchangePattern pattern,
  Object body,
  Map<String, Object> headers
);
Object sendBodyAndHeaders(
  Endpoint endpoint,
  ExchangePattern pattern,
  Object body,
  Map<String, Object> headers
);
```
### 46.1.3. 具有InOut模式的同步请求
同步请求方法与同步发送方法类似，只是请求方法强制将消息交换模式设为InOut（符合请求/应答语义）。 因此，如果您希望收到来自生产者端点的回复，则使用同步请求方法通常很方便。

#### 请求由处理器填充的交换
基本的`request()`方法是一种通用方法，它使用处理器填充默认交换，并强制将消息交换模式设置为InOut（以便调用符合请求/回复语义）。 返回值是您在生产者端点处理后获得的交换，其中Out消息包含回复消息。

用于发送由处理器填充的交换机的`request()`方法可以通过以下方式之一指定目标端点：作为端点URI或端点对象。

```java
Exchange request(String endpointUri, Processor processor);
Exchange request(Endpoint endpoint, Processor processor);
```

#### 请求消息正文
如果您仅在请求和回复中关注邮件正文的内容，可以使用`requestBody()`方法来提供请求消息正文作为参数，并让生产者模板将照片插入到 默认交换对象。

`requestBody()`方法允许您以下列方式之一指定目标端点：作为默认端点，作为端点URI或端点对象。 返回值是回复消息的主体（Out消息体），可以使用内置的类型转换器返回为plain对象或转换为特定类型T（参见第43.3节“内置类型”转换器”）。

```java
Object requestBody(Object body);
<T> T requestBody(Object body, Class<T> type);
Object requestBody(
  String endpointUri,
  Object body
);
<T> T requestBody(
  String endpointUri,
  Object body,
  Class<T> type
);
Object requestBody(
  Endpoint endpoint,
  Object body
);
<T> T requestBody(
  Endpoint endpoint,
  Object body,
  Class<T> type
);
```

#### 请求邮件正文和消息头
您可以尝试使用`requestBodyAndHeader()`方法设置单个头值的效果。 您将消息体和头设置作为参数提供给`requestBodyAndHeader()`，并让生产者模板处理将主体和交换属性插入到默认交换对象中。

`requestBodyAndHeader()`方法允许您以下列方式之一指定目标端点：作为端点URI或端点对象。 返回值是回复消息的主体（Out消息体），可以使用内置的类型转换器返回为plain对象或转换为特定类型T（参见第43.3节“内置类型”转换器”）。

```java
Object requestBodyAndHeader(
  String endpointUri,
  Object body,
  String header,
  Object headerValue
);
<T> T requestBodyAndHeader(
  String endpointUri,
  Object body,
  String header,
  Object headerValue,
  Class<T> type
);
Object requestBodyAndHeader(
  Endpoint endpoint,
  Object body,
  String header,
  Object headerValue
);
<T> T requestBodyAndHeader(
  Endpoint endpoint,
  Object body,
  String header,
  Object headerValue,
  Class<T> type
);
```

`requestBodyAndHeaders()`方法类似于`requestBodyAndHeader()`方法，不同的是，这些方法不是仅提供单个消息头设置，而是可以指定消息头设置的完整哈希映射。

```java
Object requestBodyAndHeaders(
  String endpointUri,
  Object body,
  Map<String, Object> headers
);
<T> T requestBodyAndHeaders(
  String endpointUri,
  Object body,
  Map<String, Object> headers,
  Class<T> type
);
Object requestBodyAndHeaders(
  Endpoint endpoint,
  Object body,
  Map<String, Object> headers
);
<T> T requestBodyAndHeaders(
  Endpoint endpoint,
  Object body,
  Map<String, Object> headers,
  Class<T> type
);
```

### 异步发送

生产者模板提供了用于异步调用生产者端点的各种方法，使得主线程在等待调用完成时不阻塞，并且可以在以后检索回复消息。 本节描述的异步发送方法与任何类型的消息交换协议兼容。

#### 发送交换

基本的`asyncSend()`方法使用 **Exchange** 参数，并使用指定交换的消息交换模式（MEP）异步调用端点。 返回值是一个 **java.util.concurrent.Future** 对象，它是可以用于在稍后时间收集回复消息的票据 - 有关如何从 **Future** 对象获取返回值的详细信息，请参见“ 异步调用“。

以下`asyncSend()`方法允许您以下列方式之一指定目标端点：作为端点URI或端点对象。

```java
Future<Exchange> asyncSend(String endpointUri, Exchange exchange);
Future<Exchange> asyncSend(Endpoint endpoint, Exchange exchange);
```
#### 发送由处理器填充的交换
一般`asyncSend()`方法的一个简单变化是使用处理器来填充默认的交换，而不是明确提供交换对象。

以下`asyncSend()`方法允许您以下列方式之一指定目标端点：作为端点URI或端点对象。

```java
Future<Exchange> asyncSend(String endpointUri, Processor processor);
Future<Exchange> asyncSend(Endpoint endpoint, Processor processor);
```

#### 发送消息体
如果您仅关心要发送的消息正文的内容，则可以使用`asyncSendBody()`方法异步发送消息体，并让生产者模板将主体插入默认的交换对象。

`asyncSendBody()`方法允许您以下列方式之一指定目标端点：作为端点URI或端点对象。

```java
Future<Object> asyncSendBody(String endpointUri, Object body);
Future<Object> asyncSendBody(Endpoint endpoint, Object body);
```

### 46.1.5. 具有InOut模式的异步请求
异步请求方法与异步发送方法类似，只是请求方法强制将消息交换模式设为InOut（符合请求/应答语义）。因此，如果您希望收到来自生产者端点的回复，则使用异步请求方法通常很方便。

#### 请求消息正文
如果您仅在请求和回复中关注邮件正文的内容，可以使用`requestBody()`方法提供请求消息正文作为参数，并让生产者模板将照片插入到默认交换对象。

`asyncRequestBody()`方法允许您以下列方式之一指定目标端点：作为端点URI或端点对象。可以从Future对象检索的返回值是回复消息（Out消息体）的主体，可以使用内置的类型转换器作为普通对象返回或转换为特定类型T（参见该部分称为“异步调用”）。

```java
Future<Object> asyncRequestBody(
  String endpointUri,
  Object body
);
<T> Future<T> asyncRequestBody(
  String endpointUri,
  Object body,
  Class<T> type
);
Future<Object> asyncRequestBody(
  Endpoint endpoint,
  Object body
);
<T> Future<T> asyncRequestBody(
  Endpoint endpoint,
  Object body,
  Class<T> type
);
```

#### 请求消息正文和消息头
您可以尝试使用`asyncRequestBodyAndHeader()`方法设置单个消息头的效果。 您将邮件正文和消息头设置作为参数提供给`asyncRequestBodyAndHeader()`，并让生产者模板保留插入正文并将属性交换到默认交换对象中。

`asyncRequestBodyAndHeader()`方法允许您以下列方式之一指定目标端点：作为端点URI或端点对象。 可以从Future对象检索的返回值是回复消息（Out消息体）的主体，可以使用内置的类型转换器作为普通对象返回或转换为特定类型T（参见 该部分称为“异步调用”）。

```java
Future<Object> asyncRequestBodyAndHeader(
  String endpointUri,
  Object body,
  String header,
  Object headerValue
);
<T> Future<T> asyncRequestBodyAndHeader(
  String endpointUri,
  Object body,
  String header,
  Object headerValue,
  Class<T> type
);
Future<Object> asyncRequestBodyAndHeader(
  Endpoint endpoint,
  Object body,
  String header,
  Object headerValue
);
<T> Future<T> asyncRequestBodyAndHeader(
  Endpoint endpoint,
  Object body,
  String header,
  Object headerValue,
  Class<T> type
);
```

`asyncRequestBodyAndHeaders()`方法与`asyncRequestBodyAndHeader()`方法类似，不同之处在于，它们不是仅提供一个消息头设置，而是允许您指定消息头设置的完整哈希映射。

```java
Future<Object> asyncRequestBodyAndHeaders(
  String endpointUri,
  Object body,
  Map<String, Object> headers
);
<T> Future<T> asyncRequestBodyAndHeaders(
  String endpointUri,
  Object body,
  Map<String, Object> headers,
  Class<T> type
);
Future<Object> asyncRequestBodyAndHeaders(
  Endpoint endpoint,
  Object body,
  Map<String, Object> headers
);
<T> Future<T> asyncRequestBodyAndHeaders(
  Endpoint endpoint,
  Object body,
  Map<String, Object> headers,
  Class<T> type
);
```

### 46.1.6. 使用回调异步发送
生产者模板还提供在用于调用生产者端点的同一子线程中处理回复消息的选项。在这种情况下，您提供一个回调对象，一旦接收到回复消息，该对象就会在子线程中自动调用。换句话说，使用回调方法的异步发送使您能够在主线程中启动一个调用，然后让生产者端点的所有关联处理调用，等待回复并处理回复 - 在子进程中异步发生。

#### 发送交换
基本的`asyncCallback()`方法使用 **Exchange** 参数，并使用指定交换机的消息交换模式（MEP）异步调用端点。此方法类似于交换的`asyncSend()`方法，除了它需要一个附加的**org.apache.camel.spi.Synchronization** 参数，它是一个具有两种方法的回调接口：`onComplete()`和`onFailure()`。有关如何使用同步回调的详细信息，请参阅“使用回调的异步调用”一节。

以下`asyncCallback()`方法允许您以下列方式之一指定目标端点：作为端点URI或端点对象。

```java
Future<Exchange> asyncCallback(
  String endpointUri,
  Exchange exchange,
  Synchronization onCompletion
);
Future<Exchange> asyncCallback(
  Endpoint endpoint,
  Exchange exchange,
  Synchronization onCompletion
);
```

#### 发送由处理器填充的交换
处理器的`asyncCallback()`方法调用处理器来填充默认交换，并强制将消息交换模式设置为InOut（以便调用符合请求/回复语义）。

以下`asyncCallback()`法允许您以下列方式之一指定目标端点：作为端点URI或端点对象。

```java
Future<Exchange> asyncCallback(
  String endpointUri,
  Processor processor,
  Synchronization onCompletion
);
Future<Exchange> asyncCallback(
  Endpoint endpoint,
  Processor processor,
  Synchronization onCompletion
);
```

#### 发送消息体
如果您仅关心要发送的消息正文的内容，则可以使用`asyncCallbackSendBody()`方法异步发送消息体，并让生产者模板将主体插入到默认的交换对象中。

`asyncCallbackSendBody()`方法允许您以下列方式之一指定目标端点：作为端点URI或端点对象。

```java
Future<Object> asyncCallbackSendBody(
  String endpointUri,
  Object body,
  Synchronization onCompletion
);
Future<Object> asyncCallbackSendBody(
  Endpoint endpoint,
  Object body,
  Synchronization onCompletion
);
```

#### 请求消息正文
如果您仅在请求和回复中关注消息正文的内容，可以使用`asyncCallbackRequestBody()`方法来提供请求消息正文作为参数，并让生产者模板照顾将身体插入到 默认交换对象

`asyncCallbackRequestBody()`方法允许您以下列方式之一指定目标端点：作为端点URI或端点对象。

```java
Future<Object> asyncCallbackRequestBody(
  String endpointUri,
  Object body,
  Synchronization onCompletion
);
Future<Object> asyncCallbackRequestBody(
  Endpoint endpoint,
  Object body,
  Synchronization onCompletion
);
```

## 46.2. 使用消费者模板
消费者模板提供了轮询消费者端点以便接收传入消息的方法。 您可以选择以交换对象的形式或以消息正文形式（消息体可以使用内置类型转换器将其转换为特定类型）的形式接收传入消息。

### 轮询交换的例子
您可以使用消费者模板轮询使用以下轮询方法之一的交换机的消费者端点：blocking receive（）; receive（）与超时; 或receiveNoWait（），它立即返回。 因为消费者端点代表服务，所以在尝试轮询交换之前，通过调用start（）来启动服务线程也很重要。

以下示例显示如何使用阻止receive（）方法从seda：foo consumer endpoint轮询交换：

```java
import org.apache.camel.ProducerTemplate;
import org.apache.camel.ConsumerTemplate;
import org.apache.camel.Exchange;
...
ProducerTemplate template = context.createProducerTemplate();
ConsumerTemplate consumer = context.createConsumerTemplate();
// Start the consumer service
consumer.start();
...
template.sendBody("seda:foo", "Hello");
Exchange out = consumer.receive("seda:foo");
...
// Stop the consumer service
consumer.stop();
```

消费者模板实例消费者使用CamelContext.createConsumerTemplate（）方法实例化，消费者服务线程通过调用ConsumerTemplate.start（）来启动。

### 轮询消息体的示例
您还可以使用以下方法之一轮询消息端点的消息：阻塞receiveBody（）; receiveBody（）与超时; 或receiveBodyNoWait（），它立即返回。 在前面的例子中，在尝试轮询交换机之前，通过调用start（）启动服务线程也很重要。

以下示例显示如何使用阻止receiveBody（）方法从seda：foo consumer endpoint轮询传入邮件正文：

```java
import org.apache.camel.ProducerTemplate;
import org.apache.camel.ConsumerTemplate;
...
ProducerTemplate template = context.createProducerTemplate();
ConsumerTemplate consumer = context.createConsumerTemplate();
// Start the consumer service
consumer.start();
...
template.sendBody("seda:foo", "Hello");
Object body = consumer.receiveBody("seda:foo");
...
// Stop the consumer service
consumer.stop();
```

### 轮询交换方法
从消费者端点进行轮询交换有三种基本方法：receive（）无限制地超时; receive（）具有指定的毫秒级的超时块; 而receiveNoWait（）是非阻塞的。 您可以将使用者端点指定为端点URI或端点实例。\

```java
Exchange receive(String endpointUri);
Exchange receive(String endpointUri, long timeout);
Exchange receiveNoWait(String endpointUri);
Exchange receive(Endpoint endpoint);
Exchange receive(Endpoint endpoint, long timeout);
Exchange receiveNoWait(Endpoint endpoint);
```

### 轮询消息体的方法
从消费者端点轮询邮件正文有三种基本方法：receiveBody（），无限期地阻塞; receiveBody（）具有指定的毫秒级的超时块; 并且receiveBodyNoWait（）是非阻塞的。 您可以将使用者端点指定为端点URI或端点实例。 此外，通过调用这些方法的模板形式，您可以使用内置类型转换器将返回的主体转换为特定类型T。

```java
Object receiveBody(String endpointUri);
Object receiveBody(String endpointUri, long timeout);
Object receiveBodyNoWait(String endpointUri);
Object receiveBody(Endpoint endpoint);
Object receiveBody(Endpoint endpoint, long timeout);
Object receiveBodyNoWait(Endpoint endpoint);

<T> T receiveBody(String endpointUri, Class<T> type);
<T> T receiveBody(String endpointUri, long timeout, Class<T> type);
<T> T receiveBodyNoWait(String endpointUri, Class<T> type);

<T> T receiveBody(Endpoint endpoint, Class<T> type);
<T> T receiveBody(Endpoint endpoint, long timeout, Class<T> type);
<T> T receiveBodyNoWait(Endpoint endpoint, Class<T> type);
```
