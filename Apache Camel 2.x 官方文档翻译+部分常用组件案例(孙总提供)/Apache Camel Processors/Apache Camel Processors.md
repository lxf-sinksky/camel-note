   <span id="top"></span>
# Apache Camel Processors

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-14*
*相关连接:暂无*

>提示：页面跳转样式需使用html格式浏览本文档

## 概述
Apache Camel 路由引擎是按一定规则组合的。这里将大家介绍camel的处理器，以及使用规则。

## 处理器列表

序号|名称|描述
----|----|----
1|[aop](#aop)|在 DSL 中有一个 AOP 概念, 允许您在路由交换时进行一些自定义之前或之后的处理。
2|[aggregate](#aggregate)|聚合器接收消息流，并识别消息然后将其聚合为一个单独的消息。
3|[bean](#bean)|用于引用注册表中的实例，并可以调用其方法来处理交换。
4|[doTry](#doTry)|表明路由中发生异常的范围。
5|[doCatch](#doTry)|捕捉异常。
6|[doFinally](#doTry)|无论异常是否存在都需要运行的区域。
7|[onException](#onException)|onException子句是一种捕获的机制，一旦您定义了一个onException子句，它就会捕获在路由中任何位置发生的异常。这与Java try/catch机制形成了鲜明的对比，在java机制中，只有特定的代码片段被显式地封装在try块中，才会捕捉到异常。
8|[choice](#choice)|根据交换内容选择特定的子路由。
9|[when](#choice)|配合choice使用，作为选择的子句。
10|[otherwise](#choice)|配合choice-when使用，如果when子句都不成立，则进入otherwise子句。
11|[convertBodyTo](#convertBodyTo)|将消息中的消息体转换为指定的类型。
12|[delay](#delay)|将交换延迟传播到路由的后一部分。
13|[dynamicRouter](#dynamicRouter)|动态路由器模式,使您能够通过一系列处理步骤连续发送消息。在设计时，步骤的顺序是未知的，消息应通过端点列表在运行时动态计算。 每当消息从一个端点返回时，动态路由器回调一个bean来发现路由中的下一个端点。
14|[enrich](#enrich)|内容扩充器，将当前的交换与指定的端点URI所请求的数据相结合。
15|[pollEnrich](#pollEnrich)|内容扩充器,将当前交换与从指定的消费端点URI轮询的数据组合在一起。
16|[filter](#filter)|消息过滤器，使用断言表达式来过滤传入的交换。
17|[idempotentConsumer](#idempotentConsumer)|过滤器，实现一个抑制重复消息的策略。
18|[inOnly-inOut](#inOnly-inOut)|inOnly为事件消息（ A one-way message）。inOut为请求响应消息（request-response message）。
19|[intercept](#intercept)|拦截器,在路由交换路由时拦截每个处理步骤(这个拦截器在消息路由时被不断调用)。
20|[interceptFrom](#interceptFrom)|拦截器，拦截路由中传入的交换（这个拦截器只被调用一次）。
21|[interceptSendToEndpoint](#interceptSendToEndpoint)|拦截器，当交换即将被发送到给定的端点时截获(这个拦截器只被调用一次)。
22|[loadBalance](#loadBalance)|在一组端点上实现负载均衡。
23|[log](#log)|将消息记录到控制台。
24|[loop](#loop)|循环模式使您可以多次处理消息。 它主要用于测试。
25|[marshal-unmarshal](#marshal-unmarshal)|marshal编组，使用指定的数据格式将消息体从高级格式转换为低级或二进制格式。unmarshal解组，使用指定的数据格式将消息体从低级或二进制格式转换为高级格式。
26|[multicast](#multicast)|多点发送。当向多个端点发送相同交换的副本。
27|[onCompletion](#onCompletion)|OnCompletion用于定义在工作单元完成时要执行的操作。工作单元是一个包含整个交换的骆驼概念。
28|[pipeline](#pipeline)|发送交换到一系列端点，其中一个端点的输出成为下一个端点的输入。
29|[to](#to)|将交换发送到一个或多个端点。
30|[toD](#toD)|toD允许发送消息到动态计算的端点表达式上。
31|[process](#process)|用于加工处理端点之间的交换。
32|[recipientList](#recipientList)|将交换发送到在运行时计算的收件人列表。
33|[removeHeader](#removeHeader)|从交换消息中删除指定的header。
34|[removeHeaders](#removeHeaders)|从交换的输入消息中删除与指定模式匹配的头部。 该模式可以有形式，前缀* - 在这种情况下，它匹配每个名称以前缀开头 - 否则，它被解释为一个规则表达。
35|[removeProperties](#removeProperties)|从交换中删除与指定模式匹配的属性。第一个字符串是模式（参见上面的removeHeaders（）)。后续字符串指定例外 - 这些属性依然存在。
36|[removeProperty](#removeProperty)|从交换中删除指定的属性。
37|[resequence](#resequence)|根据指定的比较器对传入的交换进行重新排序。 支持批处理模式和流模式。
38|[rollback](#rollback)|(事务)只标记回滚的当前事务(默认情况下也会抛出异常)。
39|[route](#route)|标记一段路由。
40|[routingSlip](#routingSlip)|根据从交换标题中提取的端点URI的列表来动态构建路由管道。
41|[sample](#sample)|创建采样器，允许您从路线上的运输中提取交换样本。
42|[script](#script)|script处理器，它以表达式的形式执行脚本，并且不改变消息体。
43|[setBody](#setBody)|设置交换消息的消息体。
44|[setExchangePattern](#setExchangePattern)|将当前交换模式设置为指定的值。
45|[setFaultBody](#setFaultBody)|添加一个处理器，在错误消息上设置正文。
46|[setHeader](#setHeader)|设置交换消息的消息头。
47|[setOutHeader](#setOutHeader)|在交换消息中设置输出的消息头。
48|[setProperty](#setProperty)|设置指定的交换属性。
49|[sort](#sort)|对In消息体的内容进行排序（可以选择指定自定义比较器的位置）。
50|[split](#split)|将当前的交换划分为一个交换序列，其中每个分割交换都包含原始消息体的一个片段。
51|[stop](#stop)|停止路由当前交换并将其标记为已完成
52|[threads](#threads)|创建一个线程池，用于并发处理路由的后一部分
53|[throttle](#throttle)|将流量限制在指定的级别(每秒交换),确保生产者端点不会过载。
54|[throwException](#throwException)|抛出指定的Java异常。
55|[transacted](#transacted)|创建一个包含路由后一部分的Spring事务范围。
56|[transform](#transform)|转换消息，将In消息头信息复制到Out消息头，并将Out消息体设置为指定的值。
57|[validate](#validate)|使用断言表达式来测试当前消息是否有效。 如果断言返回false，则抛出PredicateValidationException异常
58|[whenSkipSendToEndpoint](#whenSkipSendToEndpoint)|暂无
59|[wireTap](#wireTap)|窃听节点,将当前交换的副本发送到指定的线路URI。
60|[restBinding](#restBinding)|暂无
61|[policy](#policy)|暂无
62|[xpath](#xpath)|Camel支持XPath，允许在DSL或Xml配置中使用表达式或谓语。 例如，您可以使用XPath在消息过滤器中创建谓语，或者将其作为收件人列表的表达式。
63|[constant](#constant)|常量表达式语言实际上只是将常量字符串指定为表达式的一种方式。
64|[simple](#simple)|Simple语言是Apache Camel专门为访问和操作交换对象的各个部分而开发的语言。 这种语言并不像最初创建时那么简单，它现在具有一套全面的逻辑运算符和连词。
65|[cm:property-placeholder](#cm:property-placeholder)|用于在OSGi blueprint文件的上下文中用于访问在OSGi配置管理服务中定义的属性。
66|[endpoint](#endpoint)|在上下文环境中配置端点URI,使冗长的URI更容易管理。
67|[toF](#toF)|使用字符串格式将交换发送到一个端点,也就是说，端点URI字符串可以在C printf()函数的样式中嵌入替换。(注意：toF处理器在XML DSL中是不适用的,只能在Java DSL中使用)
68|[errorHandler](#errorHandler)|errorHandler处理器提供了与onException处理器类似的功能，只是此机制不能区分不同的异常类型。errorHandler处理器是Apache Camel提供的原始异常处理机制，在执行onException子句之前可用。
69|[tokenize](#tokenize)|标记器语言是camel内核中的一种内置语言，最常用于Splitter EIP使用基于标记的策略来分割消息。标记器语言旨在使用指定的分隔符模式标记文本文档。 它也可以用来标记XML文档。 对于真正意义上的XML标记，推荐使用XMLTokenizer语言，因为它提供了一种专门用于XML文档的更快，更高效的标记。 
70|[jxpath](#jxpath)|JXPath语言使您能够使用Apache Commons JXPath语言调用Java bean。 JXPath语言与XPath具有相似的语法，但不是从XML文档中选择元素或属性节点，而是在Java bean的对象图上调用方法。



<a href="#top" style="position: fixed; top: 50px; right: 80px; font-size:20px; text-decoration: none;">跳转至顶部</a>

## 处理器示例

---

<span id="aop"></span>
### 1.aop

#### 概述

允许您在路由交换时进行一些自定义之前或之后的处理。

#### 示例

##### Blueprint DSL 配置

```xml
<route>
    <from uri="jms:queue:inbox"/>
    <aop beforeUri="log:before" afterUri="log:after">
        <to uri="bean:order?method=validate"/>
        <to uri="bean:order?method=handle"/>
    </aop>
    <to uri="jms:queue:order"/>
</route>
```

##### 在运行时解析为消息流

```
inbox -> log:before -> bean.validate -> bean.handle -> log:after -> order
```

#### aop属性

- beforeUri
- afterUri
- afterFinallyUri

---

<span id="aggregate"></span>
### 2.aggregate

#### 概述

聚合器是一种特殊的过滤器，它接收一个消息流并识别出相互关联的消息。一旦接收到完整的消息集合，聚合器就从每个相关的消息中收集消息，并把它们组合为一个聚合消息发布到输出通道上，以便进一步处理。

设计一个聚合器时，需要指定以下属性：相关性，完成条件，聚合算法。

![](https://i.imgur.com/eOhrp4p.png)

#### 示例

##### 实现聚合策略

```java
import org.apache.camel.Exchange; 
import org.apache.camel.processor.aggregate.AggregationStrategy;
public class MyAggregationStrategy implements AggregationStrategy {
    @Override
    public Exchange aggregate(Exchange oldExchange, Exchange newExchange) {
           if (oldExchange == null) {
                   return newExchange;           
            }
           String oldBody = oldExchange.getIn().getBody(String.class);     
           String newBody = newExchange.getIn().getBody(String.class);                                  
           String body = oldBody + newBody;            
           oldExchange.getIn().setBody(body);
           return oldExchange;
    } 
} 
```

##### 测试数据

```java
    //第一个消息：
    exchange.getIn().setHeader("myId", 1);
    exchange.getIn().setBody("A");

    //第二个消息：
    exchange.getIn().setHeader("myId", 1);
    exchange.getIn().setBody("B");

    //第三个消息：
    exchange.getIn().setHeader("myId", 2);
    exchange.getIn().setBody("C");

    //第四个消息：
    exchange.getIn().setHeader("myId", 1);
    exchange.getIn().setBody("D");
```

##### Blueprint DSL 配置

```xml
<bean id="myAggregationStrategy" class="camelinaction.MyAggregationStrategy"/>
<camelContext xmlns="http://camel.apache.org/schema/spring">
    <route>
        <from uri="direct:start"/>
        <log message="Sending ${body} with key ${header.myId}"/>
        <aggregate strategyRef="myAggregationStrategy" completionSize="3">
             <correlationExpression>
                  <header>myId</header>
             </correlationExpression>
             <log message="Sending out ${body}"/>
             <to uri="mock:result"/>
        </aggregate>
    </route> 
</camelContext>
```

##### 示例分析

示例中有四条消息依次到达聚合器，下表为运行时聚合器处理细节。

Arrived|oldExchange|newExchange|Description
----|----|----|----
A|null|A|第一条消息到达聚合器
B|A|B|第二条消息到达聚合器
C|null|C|第三条消息到达聚合器
D|AB|D|第四条消息到达聚合器

从上表可以看出聚合器是按照聚合策略去实现功能的。可是这里第三次消息到达的时候为什么oldExchange为null?C消息为什么没有被聚合？

问题一解答：因为在DSL配置中使用correlationExpression属性,以header为聚合条件。所以在myId=2时又会重新聚合消息，直到满足条件时返回。
问题二解答：同上，以header为聚合条件。myId=1，myId=2分别在不同的聚合器中。

DSL配置中还使用了completionSize属性。在上面的示例中第四条消息到达时，聚合器完成3次聚合任务，满足completionSize="3"。这时会将ABD发送给端点mock。

#### 聚合器EIP提供的不同类型的完成条件

条件|描述
----|----
completionSize|完成一次聚合任务需要聚合消息的次数。
completionTimeout|聚合超时。
completionInterval|聚合周期。
completionPredicate|根据断言来满足聚合条件。
completionFromBatchConsumer|定义一个完成条件，只有当到达的交换来自一个[BatchConsumer](http://camel.apache.org/batch-consumer.html)时才适用。

---

<span id="bean"></span>
### 3.bean

#### 概述

用于引用注册表中的实例，并可以调用其方法来处理交换。

#### 示例

##### Blueprint DSL 配置

```xml
<!--无参构造-->
<bean id="myBeanId" class="com.shudi.databridge.processor.MyBeanId"/>
<!--有参构造-->
<bean id="myBeanId" class="com.shudi.databridge.processor.MyBeanId"/>
    <property name="id" value="1001" />
    <property name="name" value="hunter" />  
</bean>
<!--静态工厂构造-->
<bean id="myBeanId" class="com.shudi.databridge.processor.MyBeanId" factory-method="getMyBeanId"/>

<route>
   <from uri="file:data/inbound"/>
   <bean ref="myBeanId" method="processBody('name','age')"/>
   <to uri="file:data/outbound"/>
</route>
```

##### 示例分析

这里的处理器bean和bean组件有相同的作用，详细可以查看bean组件相关文档。
bean组件链接：[http://camel.apache.org/bean.html](http://camel.apache.org/bean.html)

---

<span id="doTry"></span>
### 4.5.6.doTry-doCatch-doFinally

#### 概述

doTry-doCatch-doFinally处理器用于在路由中的异常处理。

#### 示例

##### Blueprint DSL 配置

```xml
<route>
    <from uri="direct:start"/>
    <doTry>
        <process ref="processorFail"/>
        <to uri="mock:result"/>
        <doCatch>
            <exception>java.io.IOException</exception>
            <exception>java.lang.IllegalStateException</exception>
            <to uri="mock:catch"/>
        </doCatch>
        <doCatch>
            <exception>org.apache.camel.CamelExchangeException</exception>
            <to uri="mock:catchCamel"/>
        </doCatch>
        <doFinally>
            <to uri="mock:finally"/>
        </doFinally>
    </doTry>
</route>
```

#####  示例分析

Apache Camel 中的doTry-doCatch-doFinally和JAVA中异常处理作用相同。
使用方法简单明了，方便大家在路由中进行异常处理。

---

<span id="onException"></span>
### 7.onException

#### 概述

onException处理器是一种捕获异常的机制，一旦您定义了一个onException子句，它就会捕获在路由中任何位置发生的异常。

#### 示例

##### Blueprint DSL 配置

```xml
<onException>
    <exception>com.mycompany.ValidationException</exception>
    <to uri="activemq:validationFailed"/>
</onException>
<route>
     <from uri="seda:inputA"/>
     <to uri="validation:foo/bar.xsd"/>
     <to uri="activemq:someQueue"/>
</route>
<route>
     <from uri="seda:inputB"/>
     <to uri="rnc:mySchema.rnc"/>
     <to uri="activemq:anotherQueue"/>
</route>
```

##### 示例分析

在上面的Java DSL示例中，onException子句适用于在RouteBuilder类中定义的所有路由。如果一个ValidationException异常发生在处理任何路由(从(“seda:inputA”)或来自(“seda:inputB”))时，onException子句会捕捉异常，并将当前的交换重定向到validation失败的JMS队列(这是一个死信队列)。

---

<span id="choice"></span>
### 8.9.10.choice-when-otherwise

#### 概述

choice-when-otherwise处理器用于在路由中根据判断条件选择特定的子路由，如果when子句都不成立，则进入otherwise子句。

#### 示例

##### Blueprint DSL 配置

```xml
<route>
    <from uri="SourceURL"/>
    <choice>
      <when>
        <!-- First predicate -->
        <simple>${header.foo} == 'bar'</simple>
        <to uri="Target1"/>
      </when>
      <when>
        <!-- Second predicate -->
        <simple>${header.foo} == 'manchu'</simple>
        <to uri="Target2"/>
      </when>
      <otherwise>
        <to uri="Target3"/>
      </otherwise>
    </choice>
 </route>
```

##### 示例分析

示例中有2个when判断语句，当满足simple条件时，则选择各自的目标端点。如果都不满足，则选择otherwise子句中的端点。

---

<span id="convertBodyTo"></span>
### 11.convertBodyTo

#### 概述

convertBodyTo处理器用于将消息中的消息体转换为指定的类型。

#### 示例

##### Blueprint DSL 配置

```xml
<route>
    <from uri="SourceURL"/>
    <convertBodyTo type="String"/>
    <to uri="TargetURL"/>  
 </route>
```

##### 示例分析

convertBodyTo可以将body中的数据转换为规定的类型。关于转换类型，这里不做过多讲解。

---

<span id="delay"></span>
### 12.delay

#### 概述

delay处理器会延迟消息进入后续端点。

#### 示例

##### Blueprint DSL 配置

```xml
<route>
    <from uri="SourceURL"/>
    <delay>
        <constant>1000</constant>
    </delay>
    <to uri="TargetURL"/>  
 </route>
```

##### 示例分析

delay提供了消息交换延迟传播的功能。

#### delay模式支持以下选项

名称|默认值|描述
----|----|----
asyncDelayed|false|如果启用，则延迟消息将使用一个调度的线程池异步发生。
executorServiceRef|暂无|如果启用了异步延时，则需要使用自定义的线程池。
callerRunsWhenRejected|true|如果线程池拒绝执行任务，则该控制线程是否应该执行该任务。(如果启用了异步延迟，则使用该功能)

---

<span id="dynamicRouter"></span>
### 13.dynamicRouter

#### 概述

在下图中，除了使用输入输出通道，动态路由器使用一个附加的控制通道。系统启动时，每个潜在的接收者都利用该控制通道向动态路由器发送一条特殊的消息，宣布这个接收者的存在，并给出此接收者所处理消息的路由条件。动态路由器把每个参与者的偏好存储在一个规则集中。当有消息到达时，动态路由器会计算所有规则，并把消息路由给符合规则的接收者。它能实现高效的预测型路由，而在维护动态路由器时不必依赖于各个潜在的接收者。

![](https://i.imgur.com/7z9gLrh.png)

>注：您必须确保动态路由器(如bean)所使用的表达式，返回null来表示结束。否则，动态路由器将会在一个无限循环中继续。

#### 示例

##### DynamicRouterTest类
动态路由处理类

```java
public class DynamicRouterTest{

     public String slip(String body) {
	     if (body.equals("123")) {
	        return "class:com.shudi.databridge.test.DataSetProcessor";
	     } else if (body.equals("456")) {
	        return "class:com.shudi.databridge.test.BeanProcessor";
	     } 
	     //没有选项则返回null
	     return null;
	     }    
}
```

#### DataSetProcessor类

```java
public class DataSetProcessor implements Processor {

	@Override
	public void process(Exchange exchange) throws Exception {
		   exchange.getIn().setBody("动态路由一打印");
	}
}
//省略BeanProcessor类
```

##### Blueprint DSL 配置

```xml
<!--注册处理器-->
<bean id="mySlip" class="com.shudi.integration.test.DynamicRouterTest"/>
<camelContext xmlns="http://camel.apache.org/schema/blueprint">
    <route>
        <from uri="timer://kickoff?period=10s"/>
        <setBody>
              <constant>123</constant>
        </setBody>
        <!--动态路由器-->
        <dynamicRouter>
            <!-- 使用一个方法调用bean作为动态路由器 -->
            <method ref="mySlip" method="slip"/>
        </dynamicRouter>
        <to uri="log:hunter"/>
        <to uri="direct:foo"/>
    </route>
    <route>
        <from uri="direct:foo"/>
        <transform>
             <constant>Hello Apache Camel</constant>
        </transform>
        <to uri="log:hunter"/>
    </route>
</camelContext>
```

##### 示例运行

当Body为123时
![](https://i.imgur.com/I20unsd.png)

当Body为456时
![](https://i.imgur.com/r1APh0L.png)

##### 示例分析

上述示例中我们采用动态路由处理来实现了整个路由。当发送消息时，路由会进入dynamicRouter的bean中，通过计算来选择后续的路由端点。如果返回的端点为null，动态路由器将会结束。

#### dynamicroute DSL命令支持以下选项

名称|默认值|描述
----|----|----
uriDelimiter|,|如果表达式返回多个端点，使用的分隔符。
ignoreInvalidEndpoints|false|如果端点uri不能被解析，则应该忽略它。否则，驼峰会抛出一个异常，说明端点uri是无效的(默认抛出异常)。

---

<span id="enrich"></span>
### 14.enrich

#### 概述

当原始消息不满足下游端点的需求时，可以使用内容扩充器。内容扩充器将原始交换数据和资源数据聚合为一个消息，以满足消息的完整性。

![](https://i.imgur.com/mFD2K8N.png)

#### 示例

##### ExampleAggregationStrategy类
聚合策略类

```java
public class ExampleAggregationStrategy implements AggregationStrategy {
    public Exchange aggregate(Exchange original, Exchange resource) {
        //原始的交换数据
        Object originalBody = original.getIn().getBody();
        //引用资源数据
        Object resourceResponse = resource.getIn().getBody();
        //聚合数据
        Map< String, Object> map = new HashMap<>();
        map.put("originalBody", originalBody);
        map.put("resourceResponse", resourceResponse);
        Object mergeResult = (Object)map;
        //判断交换的模式
        if (original.getPattern().isOutCapable()) {
            original.getOut().setBody(mergeResult);
        } else {
            original.getIn().setBody(mergeResult);
        }
        return original;
     }
}
```

##### Blueprint DSL 配置
```xml
<bean id="aggregationStrategy" class="com.shudi.integration.test.ExampleAggregationStrategy"/>
<camelContext xmlns="http://camel.apache.org/schema/blueprint">
    <route>
        <from uri="direct:start"/>
        <enrich strategyRef="aggregationStrategy">
              <constant>direct:resource</constant>
        </enrich>
        <to uri="direct:result"/>
    </route>
    <route>
        <from uri="direct:resource"/>
        ...
    </route>
</camelContext>
```

##### 示例分析

内容扩充器模式将原始数据和资源数据进行重组，然后把重组数据返回到交换中进入后续路由端点处理。

#### enrich DSL命令支持以下选项

名称|默认值|描述
----|----|----
uri|暂无|外部servie的端点uri可以从这个uri中进行丰富。您必须使用uri或ref。
ref|暂无|指的是外部服务的端点。您必须使用uri或ref。
expression|暂无|camel 2.16：强制性的。 用于配置uri的表达式，例如Simple或Constant或其他动态语言，可以使用当前Exchange的值动态计算uri。
strategyRef|暂无|指定一个聚合策略，用于来自外部服务的应答合并到一个单独的传出消息中。默认情况下，驼峰会使用外部服务的应答作为输出消息。
strategyMethodName|暂无|camel 2.12：当使用POJO作为AggregationStrategy时，这个选项可以用来显式声明要使用的方法名称。
strategyMethodAllowNull|false|camel 2.12：如果这个选项是false的，那么如果没有数据要丰富，则不使用聚合方法。 如果此选项为true，则在使用POJO作为AggregationStrategy时，将使用空值作为oldExchange（当没有数据进行充实时）。
aggregateOnException|false|Camel 2.14：如果这个选项是false，那么如果在尝试检索数据以从资源中进行丰富时抛出异常，则不使用聚合方法。 如果将此选项设置为true，则允许最终用户控制在聚合方法中出现异常时应执行的操作。 例如要抑制异常或设置一个自定义消息体等。
shareUnitOfWork|false|原始交换器和资源交换器共享工作单元。但是，在默认情况下，聚合器不共享原始交换和资源交换之间的工作单元。同样，资源交换也有自己的工作单元。
cacheSize|暂无|Camel 2.16：允许为ProducerCache配置缓存大小，缓存生成器以便在内容扩充器中重用。 默认情况下会使用默认的缓存大小1000.将值设置为-1允许关闭缓存。
ignoreInvalidEndpoint|false|Camel 2.16：是否忽略无法解析的端点URI。 如果禁用，Camel将会抛出一个异常来识别无效的端点URI。

---

<span id="pollEnrich"></span>
### 15.pollEnrich

#### 概述

pollEnrich的工作方式和Enrich类似，但它使用轮询的消费者。

#### 示例

##### MergeCreditRequestAndRatings类
聚合策略

```java
public class MergeCreditRequestAndRatings implements AggregationStrategy{
   //此处省略聚合方法，聚合方法和enrich处理器相同
   ......
}
```

##### Blueprint DSL 配置

```xml
<bean id="merge" class="com.shudi.integration.test.MergeCreditRequestAndRatings"/>
<camelContext xmlns="http://camel.apache.org/schema/blueprint">
    <route>
        <from uri="direct:start"/>
        <pollEnrich uri="file:inbox?fileName=data.txt" timeout="5000" strategyRef="merge"/>
        <to uri="direct:result"/>
    </route>
</camelContext>
```

>注意：pollEnrich不访问当前Exchange中的任何数据，因此在轮询时，它不能使用您在Exchange上设置的任何现有Header。例如，你不能在文件中设置文件名Exchange.FILE_NAME并使用该文件。 为此，您必须在端点URI中设置文件名。

##### 示例分析

pollEnrich和Enrich的不同在于前者的资源数据来自于轮询消费者端点，而后者来自于生产者端点。

pollEnrich运行步骤：
轮询消费者端点--->返回交换中的资源数据--->调用聚合策略进行数据重组--->将重组后的数据返回到交换

#### pollEnrich DSL命令支持以下选项

名称|默认值|描述
----|----|----
uri|暂无|外部servie的端点uri可以从这个uri中进行丰富。您必须使用uri或ref。
ref|暂无|指的是外部服务的端点。您必须使用uri或ref。
expression|暂无|camel 2.16：强制性的。 用于配置uri的表达式，例如Simple或Constant或其他动态语言，可以使用当前Exchange的值动态计算uri。
strategyRef|暂无|指定一个聚合策略，用于来自外部服务的应答合并到一个单独的传出消息中。默认情况下，驼峰会使用外部服务的应答作为输出消息。
strategyMethodName|暂无|camel 2.12：当使用POJO作为AggregationStrategy时，这个选项可以用来显式声明要使用的方法名称。
strategyMethodAllowNull|false|camel 2.12：如果这个选项是false的，那么如果没有数据要丰富，则不使用聚合方法。 如果此选项为true，则在使用POJO作为AggregationStrategy时，将使用空值作为oldExchange（当没有数据进行充实时）。
timeout|-1|从外部服务轮询时，以毫秒为单位的超时。
aggregateOnException|false|Camel 2.14：如果这个选项是false，那么如果在尝试检索数据以从资源中进行丰富时抛出异常，则不使用聚合方法。 如果将此选项设置为true，则允许最终用户控制在聚合方法中出现异常时应执行的操作。 例如要抑制异常或设置一个自定义消息体等。
cacheSize|暂无|Camel 2.16：允许为ProducerCache配置缓存大小，缓存生成器以便在内容扩充器中重用。 默认情况下会使用默认的缓存大小1000.将值设置为-1允许关闭缓存。
ignoreInvalidEndpoint|false|Camel 2.16：是否忽略无法解析的端点URI。 如果禁用，Camel将会抛出一个异常来识别无效的端点URI。

---

<span id="filter"></span>
### 16.filter

#### 概述

消息过滤器是一种只有一个输出通道的消息路由器。如果输入消息的内容与消息过滤器指定的规则匹配，该消息将路由到输出通道。如果消息的内容与规则不匹配，这个消息则被丢弃。

![](https://i.imgur.com/frmoQWG.png)

#### 示例一
使用了断言来作为过滤条件

##### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint">
   <route>
       <from uri="SourceURL"/>
       <filter>
          <simple>header.foo = 'bar'</simple>
          <to uri="TargetURL"/>
       </filter>
   </route>
</camelContext>
```

#### 示例二
使用自定义断言过滤条件

##### MyPredicate
自定义断言

```java
public class MyPredicate implements Predicate {
    public boolean matches(Exchange exchange) {
        String body = exchange.getIn().getBody(String.class);
        if (body.contains("Camel")) {
            return true;
        } else if (body.startsWith("Secret")) {
            return true;
        }
        return false;
    } 
}
```
 
##### Blueprint DSL 配置

```xml
<bean id="myPredicate" class="camelinaction.MyPredicate"/>
<camelContext id="camel" xmlns="http://camel.apache.org/schema/spring">
    <route>
        <from uri="direct:quotes"/>
        <filter>
            <method ref="myPredicate"/>
            <to uri="direct:camelQuotes"/>
        <filter>
    </route> 
</camelContext>
```

#### 示例分析

示例中filter使用了断言来作为过滤条件。当header.foo = 'bar'时，才会进入目标端点。否则就会丢弃消息。

---

<span id="idempotentConsumer"></span>
### 17.idempotentConsumer

#### 概述

iidempotent的消费者模式用于过滤重复的消息。例如，考虑这样一个场景:消息系统与使用者端点之间的连接由于系统中的某些故障而突然丢失。如果消息传递系统处于传递消息的中心，则可能不清楚用户是否收到了最后一条消息。为了提高交付的可靠性，消息传递系统可能决定在重新建立连接时重新发送此类消息。不幸的是，这就意味着消费者可能会收到重复的消息，而且在某些情况下，复制消息的效果可能会带来不良后果(比如从你的账户中两次将一笔钱从你的账户中拿出来)。在此场景中，可以使用idempotentConsumer从消息流中清除不期望的副本。

#### 骆驼提供了以下功能强大的消费者实现

- MemoryIdempotentRepository
- File
- HazelcasIdempotentRepository
- JdbcMessageIdRepository
- JpaMessageIdRepository

#### idempotentConsumer处理器参数

messageIdExpression---返回当前消息的消息ID字符串的表达式。
messageIdRepository---对消息标识库的引用，它存储接收到的所有消息的标识。

#### 示例

##### Blueprint DSL 配置

```xml
<!--引用Camel-Core包中的消息ID贮藏库MemoryMessageIdRepository类-->
<bean id="MsgIDRepos" class="org.apache.camel.processor.idempotent.MemoryMessageIdRepository">
    <!-- 指定缓存大小 -->
    <constructor-arg type="int" value="200"/>
</bean>
<camelContext id="buildIdempotentConsumer" xmlns="http://camel.apache.org/schema/blueprint">
    <route>
        <from uri="seda:a"/>
        <idempotentConsumer messageIdRepositoryRef="MsgIDRepos">
            <!--判断重复条件-->
            <simple>header.TransactionID</simple>
            <to uri="seda:b"/>
        </idempotentConsumer>
    </route>
</camelContext>
```

##### 示例分析

示例中当消息进入过滤器idempotentConsumer中时，过滤器会拿到header.TransactionID去MemoryMessageIdRepository仓库中去匹配。
如果匹配到相同TransactionID，则会阻塞当前的消息进入seda:b端点。
如果没有匹配到相同的TransactionID，则消息会进入seda:b端点，并在MemoryMessageIdRepository中保存TransactionID。

---

<span id="inOnly-inOut"></span>
### 18.inOnly-inOut

#### 概述

inOnly为事件消息，它是单向消息传递模式。
inOut为请求响应消息模式。

#### 示例

##### inOnly Blueprint DSL 配置

```xml
<route>
    <from uri="mq:someQueue"/>
    <inOnly uri="mq:anotherQueue"/>
</route>
```

##### inOnly 示例分析

JMS消息经常就以事件消息来使用,因为JMS端点只需要得到请求数据即可。 
Event Message类型的消息，其ExchangePattern默认设置为InOnly。

##### inOut Blueprint DSL 配置

```xml
<route>
    <from uri="direct:start"/>
    <inOut uri="http://oldhost?order=123&detail=short"/>
</route>
```

##### inOut 示例分析

例如，HTTP。一个客户端请求一个需要返回的web页面，等待server回复。

---

<span id="intercept"></span>
### 19.intercept

#### 概述

拦截器,在路由交换路由时拦截每个处理步骤(这个拦截器在消息路由时被不断调用)。

#### 示例

##### Blueprint DSL 配置

```xml
<camelContext id="buildIdempotentConsumer" xmlns="http://camel.apache.org/schema/blueprint">

    <intercept>
        <to uri="log:hello"/>
    </intercept>

    <route>
        <from uri="jms:queue:order"/>
        <to uri="bean:validateOrder"/>
        <to uri="bean:handleOrder"/>
    </route>
</camelContext>
```

##### 示例分析

示例路由中，定义了intercept拦截器,拦截器中有端点log:hello。当运行路由时拦截器两次拦截交换，并将两次交换的内容依次发送给log:hello端点。
intercept拦截器中还可以使用when处理器表达式语言(<when><simple>${in.body} contains 'Hello'</simple></when>)，作为拦截条件。

intercept拦截器拦截流程图

![](https://i.imgur.com/3iZqpU2.png)

---

<span id="interceptFrom"></span>
### 20.interceptFrom

#### 概述

拦截器，拦截路由中传入的交换（这个拦截器只被调用一次）。

#### 示例

##### Blueprint DSL 配置

```xml
<camelContext id="buildIdempotentConsumer" xmlns="http://camel.apache.org/schema/blueprint">
    <interceptFrom>
        <to uri="mock:middle1"/>
    </interceptFrom>
    <route>
        <from uri="direct:start"/>
        <to uri="mock:end"/>
    </route>
</camelContext>
```

##### 示例分析

示例路由中运用了interceptFrom拦截器,所以拦截器会拦截传入的消息并发送至mock:middle1,然后继续运行路由至下一端点mock:end。

interceptFrom拦截器拦截流程图

![](https://i.imgur.com/wVJ0Z1j.png)

---

<span id="interceptSendToEndpoint"></span>
### 21.interceptSendToEndpoint

#### 概述

拦截器，当交换即将被发送到给定的端点时截获(这个拦截器只被调用一次)。

#### 示例

##### Blueprint DSL 配置

```xml
<camelContext id="buildIdempotentConsumer" xmlns="http://camel.apache.org/schema/blueprint">

    <interceptSendToEndpoint uri="mock:bar">
        <when><simple>${body} == 'Hello World'</simple></when>
        <to uri="mock:detour"/>
        <transform>
            <constant>Bye World</constant>
        </transform>
    </interceptSendToEndpoint>
    <route>
        <from uri="direct:second"/>
        <to uri="mock:bar"/>
        <to uri="mock:foo"/>
        <to uri="mock:result"/>
    </route>
</camelContext>
```

##### 示例分析

示例路由使用interceptSendToEndpoint拦截器。消息进入指定拦截端点时触发拦截器，拦截器通过when进行消息验证。验证成功后会发送至mock:detour端点,然后替换消息体的内容，并发送至mock:result端点。
interceptSendToEndpoint拦截器可以指定端点进行拦截，并只拦截一次。

interceptSendToEndpoint拦截器拦截流程图

![](https://i.imgur.com/5qMOvyz.png)

---

<span id="loadBalance"></span>
### 22.loadBalance

#### 概述

负载平衡器模式允许您将消息处理委托给多个端点之一，使用各种不同的负载平衡策略。

![](https://i.imgur.com/7OVugBr.png)

#### 示例

##### Blueprint DSL 配置

```xml
<route>
    <from uri="direct:start"/>
    <loadBalance>
        <roundRobin/>
        <to uri="seda:a"/>
        <to uri="seda:b"/>
    </loadBalance> 
</route>
<route>
    <from uri="seda:a"/>
    <log message="A received: ${body}"/>
    <to uri="mock:a"/> 
</route>
<route>
    <from uri="seda:b"/>
    <log message="B received: ${body}"/>
    <to uri="mock:b"/> 
</route>
```

##### 示例分析

在本例中，您使用SEDA组件来模拟远程服务。 在一个真实的情况下，远程服务可能是一个Web服务。
假设你开始发送消息到路由。 第一条消息将被发送到“seda：a”端点，而下一条将会转到“seda：b”。 第三条消息将重新开始并被发送到“seda：a”，loadBalance会如此循环的将消息发送到这两个端点上。

#### 负载平衡策略

Policy|描述
----|----
RoundRobin|交换是从循环方式中选择的。 这是一个众所周知的和经典的策略，均匀分布的负载。
Random|为每个交换选择一个随机端点。
Sticky|用一个表达式计算一个相关键来执行高级的负载平衡;更像是web上的jsessionid，或者JMS中的JMSXGroupID。
Topic|发送到所有目的地的主题（与JMS主题相似）。
Failover|如果发生故障，将在下一个端点上尝试交换。
WeightedRound-Robin|camel 2.5:加权负载平衡策略允许您指定每个服务器的处理负载加权。除了加权比之外，端点选择还可以根据加权的循环分布进一步细化。
WeightedRandom|Weighted负载平衡策略允许您指定每个服务器的处理负载分配比。除了加权比之外，端点选择还可以根据加权的随机分配进一步细化。
Custom|camel 2.8:从camel 2.8开始，使用自定义负载平衡器的首选方法是使用这个策略，而不是使用@deprecated ref属性。
CircuitBreaker|实现断路器模式。

---

<span id="log"></span>
### 23.log

#### 概述

log处理器用于路由中的日志处理。

#### 示例

##### Blueprint DSL 配置

```xml
<!--日志打印-->
<route id="foo">
    <from uri="direct:foo"/>
    <log message="Got ${body}"/>
    <to uri="mock:foo"/>
</route>
<!--抓取错误-->
<route id="foo">
    <from uri="direct:foo"/>
    <doTry>
        <to uri="mock:foo"/>
        <doCatch>
            <exception>java.lang.Exception</exception>
            <log message="${exception.message}" loggingLevel="ERROR"/>
            ...
        </doCatch>
    </doTry>
</route>
```

##### 示例分析

示例中，log处理器将消息中body输出在控制台上，让您测试时更直观的观察数据。

---

<span id="loop"></span>
### 24.loop

#### 概述

循环处理器允许对消息进行多次处理，可能以不同的方式对每个迭代进行处理。主要用于测试过程中。

#### Options

名称|默认值|描述
----|----|----
copy|false|camel 2.8:是否使用了复制模式。如果是false，那么每次迭代都将使用相同的交换。因此，前一次迭代的结果将在下一次迭代中可见的。相反，您可以启用copy模式，然后每次迭代都以新的输入交换副本重新启动。
doWhile|暂无|camel 2.17：启用while循环，直到断言的计算结果为false或null。

#### 示例

##### Blueprint DSL 配置

```xml
<route>
     <from uri="timer://foo?period=100s"/> 		
	 <setBody>
		  <constant>123</constant>
	 </setBody>
	 <!--循环处理-->		
	 <loop copy="true">
          <constant>3</constant>
          <transform>
              <!--向body中追加字符串B-->
              <simple>${body}B</simple>
          </transform>
          <to uri="log:LogTesting?level=WARN"/>
     </loop>
</route> 
```

##### 示例运行

当copy="true"时
![](https://i.imgur.com/l2m71mV.png)

当copy="false"时
![](https://i.imgur.com/xI5KB6F.png)

##### 示例分析

示例中循环3次将交换发送到mock端点。
当copy="true"时，循环发送数据的为交换的副本。copy为loop中的参数，默认为false。

---

<span id="marshal-unmarshal"></span>
### 25.marshal-unmarshal

#### 概述

marshal编组，使用指定的数据格式将消息体从高级格式转换为低级或二进制格式。unmarshal解组，使用指定的数据格式将消息体从低级或二进制格式转换为高级格式。

#### 示例

##### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint">
    <!--指定数据格式-->
    <dataFormats> 
        <json id="json" library="Jackson"/>   
    </dataFormats>
    <!--路由一：编组-->
    <route>
        <from uri="timer://kickoff?period=100s"/>
        <setBody>
		    <constant>{\"book\":[{\"name\":\"true\",\"author\":\"10\"}]}</constant>
	    </setBody>
        <marshal> 
            <serialization/>  
        </marshal>            
        <to uri="log:LogTesting?level=WARN"/>
    </route>
    <!--路由二：解组-->
    <route>
        <from uri="timer://kickoff?period=100s"/>
	    <setBody>
		    <constant>{\"book\":[{\"name\":\"true\",\"author\":\"10\"}]}</constant>
	    </setBody>
        <convertBodyTo type="java.io.InputStream"/>
        <unmarshal ref="json"/>                
        <to uri="log:LogTesting?level=WARN"/>
    </route> 
</camelContext>
```

##### 示例运行

路由一：编组本地运行
![](https://i.imgur.com/Ua2ib7d.png)

路由二：解组本地运行
![](https://i.imgur.com/dTnWT1s.png)

##### 示例分析

路由一，marshal处理器将String类型数据编组为byte[]类型。
路由二，unmarshal处理器将InputStream类型数据解组为Map类型(通过json数据格式转换的为Map类型)。

---

<span id="multicast"></span>
### 26.multicast

#### 概述

多点发送允许将相同的消息路由到多个端点，并以不同的方式处理它们。 Multicast和Splitter的主要区别在于Splitter将消息拆分成多个部分，但Multicast不会修改请求消息。

![](https://i.imgur.com/fTY0JQk.png)

#### 示例

##### HighestBidAggregationStrategy类
聚合策略(用于处理交换的聚合)

```java
import org.apache.camel.processor.aggregate.AggregationStrategy;
import org.apache.camel.Exchange;
public class HighestBidAggregationStrategy implements AggregationStrategy
{
    public Exchange aggregate(Exchange oldExchange, Exchange newExchange)
{
        float oldBid = oldExchange.getOut().getHeader("Bid",
Float.class);
        float newBid = newExchange.getOut().getHeader("Bid",
Float.class);
        return (newBid > oldBid) ? newExchange : oldExchange;
    }
}
```

##### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint">
     <route>
         <from uri="cxf:bean:offer"/>
         <multicast strategyRef="highestBidAggregationStrategy" parallelProcessing="true">
            <to uri="cxf:bean:Buyer1"/>
            <to uri="cxf:bean:Buyer2"/>
            <to uri="cxf:bean:Buyer3"/>
         </multicast>
    </route>
  </camelContext>
  <bean id="highestBidAggregationStrategy" class="com.acme.example.HighestBidAggregationStrategy"/>
```

##### 示例分析

示例中，使用multicast处理器可以将消息依次的发送到下游端点，并通过聚合策略来处理返回的消息。

#### multicast DSL 命令支持以下选项

名称|默认值|描述
----|----|----
strategyRef|暂无|指的是一个聚合策略，用于将多点发送的回复组合到一起，返回一个消息。默认情况下，骆驼会使用最后的应答作为传出消息。
strategyMethodName|暂无|当使用POJO作为AggregationStrategy时，他的选项可以用来显式指定要使用的方法名称。
strategyMethodAllowNull|false|可以使用这个选项，当使用POJO作为AggregationStrategy。 如果为false，则不使用聚合方法。 如果为true，则在没有要充实的数据时，oldExchange使用空值。
parallelProcessing|false|如果启用，发送消息到multicast。同时，请注意，调用者线程仍然会等待，直到所有的消息已经完全处理，然后才继续。它只发送和处理来自multicast的同时发生的应答。
parallelAggregate|false|如果启用，可以同时调用AggregationStrategy上的聚合方法。 请注意，这需要AggregationStrategy的实现是线程安全的。 默认情况下，这个选项是false，这意味着Camel会自动同步对聚合方法的调用。
executorServiceRef|暂无|指的是用于并行的自定义线程池处理。 注意，如果你设置了这个选项，那么并行处理是自动隐含的，你不必启用这个选项。
stopOnException|false|发生异常时是否立即停止继续处理。 默认false，Camel将把消息发送到所有多点传送，若某节点发生异常,multicast会继续处理。 你也可以在AggregationStrategy类中处理异常。
streaming|false|如果启用，那么Camel将按照它们返回的顺序乱序处理回复。 如果禁用，骆驼将按照与multicast相同的顺序处理回复。
timeout|暂无|设置以毫秒为单位指定的总超时时间。
onPrepareRef|暂无|指定制处理器来准备副本,每个multicast的交换将收到。
shareUnitOfWork|false|工作单元是否应该共享。 

---

<span id="onCompletion"></span>
### 27.onCompletion

#### 概述

OnCompletion处理器用于定义在工作单元完成时要执行的操作。工作单元是一个包含整个交换的骆驼概念。

#### 示例

##### Blueprint DSL 配置

```xml
<route>
    <from uri="direct:start"/>
    <onCompletion onFailureOnly="true">
        <to uri="log:sync"/>
        <to uri="mock:sync"/>
    </onCompletion>
    <process ref="myProcessor"/>
    <to uri="mock:result"/>
</route>
```

##### 示例分析

在上面的例子中，当交易失败时，触发onCompletion操作,进入log,mock端点。
onFailureOnly的默认值为false,当onFailureOnly="false"时，无论失败还是成功，都会触发onCompletion操作。
onCompletion处理器类似于Java异常处理中的finally。

---

<span id="pipeline"></span>
### 28.pipeline

#### 概述

pipeline处理器发送交换到一系列端点，其中一个端点的输出成为下一个端点的输入,将所有步骤通过管道连接。

#### 示例

##### Blueprint DSL 配置

```xml
<!--路由一-->
<route>
    <from uri="direct:start"/>
    <to uri="bean:checkLuggage"/>
    <to uri="mock:testCheckpoint"/>
    <to uri="bean:dataEnricher"/>
    <to uri="mock:result"/>
</route>
<!--路由二-->
<route>
    <from uri="direct:start"/>
    <pipeline>
        <to uri="bean:checkLuggage"/>
        <to uri="mock:testCheckpoint"/>
        <to uri="bean:dataEnricher"/>
        <to uri="mock:result"/>
    </pipeline>
</route>
```

##### 示例分析

上例中，路由一和路由二产生的结果相同。pipeline只是另一种语法。

---

<span id="to"></span>
### 29.to

#### 概述

to处理器将交换发送到一个或多个端点,该端点则为生产者端点。

#### 示例

##### Blueprint DSL 配置

```xml
<route>
    <from uri="SourceURL"/>
    <to uri="TargetURL"/>
</route>
```

##### 示例分析

to为路由中最常见的处理器，用来发送交换到下一个端点。

---

<span id="toD"></span>
### 30.toD

#### 概述

toD允许发送消息到动态计算的端点表达式上。

#### 示例

##### Blueprint DSL 配置

```xml
<!--路由一-->
<route>
  <from uri="direct:start"/>
  <!--从header中获取端点uri-->
  <toD uri="${header.foo}"/>
</route>
<!--路由二-->
<route>
  <from uri="direct:start"/>
  <!--从文件中获取端点uri-->
  <toD uri="language:xpath:/order/@uri/">
</route>
```

##### 示例分析

路由一中动态端点使用${}格式来获取header.foo的值,并填充端点表达式。
路由二中是通过language组件来获取参数。

#### toD DSL 有以下选项可供选择

名称|默认值|描述
----|----|----
uri|暂无|使用的uri。
pattern|暂无|设置发送到端点时要使用的特定Exchange模式。
cacheSize|暂无|配置ProducerCache的缓存大小，该缓存将缓存生产者以供重用。 默认缓存大小为1000，如果没有指定其他值，将使用该缓存大小。 将该值设置为-1会完全关闭缓存。
ignoreInvalidEndpoint|false|指定是否忽略无法解析的端点URI。 如果禁用，Camel将会抛出一个异常来识别无效的端点URI。

---

<span id="process"></span>
### 31.process

#### 概述

用于加工处理端点之间的交换。

#### 示例

##### BeanProcessor类

```java
package com.shudi.databridge.test;

import org.apache.camel.Exchange;
import org.apache.camel.Processor;

public class BeanProcessor implements Processor {
	@Override
	public void process(Exchange exchange) throws Exception {
		exchange.getIn().setBody("Apache Camel Quartz");
	}
}
```

##### Blueprint DSL 配置

```xml
<bean id="beanProcessor" class="com.shudi.databridge.test.BeanProcessor"/>
<route id="Timer组件测试">
        <from uri="timer://foo?time=2017-11-04 18:18:30&amp;period=5s"/>
        <process ref="beanProcessor"/>
        <log message="Got ${body}"/>
 </route>
```

##### 示例分析

上例向交换的body中添加了一条数据，可以作用于后续端点。
process也在路由中比较常见，用于处理交换中的消息。

---

<span id="recipientList"></span>
### 32.recipientList

#### 概述

将交换发送到在运行时计算的收件人列表。

![](https://i.imgur.com/Qt3W59f.png)

#### 示例

##### Blueprint DSL 配置

```xml
<route>
   <from uri="direct:a"/>
   <!--strategyRef引用的聚合策略，对多个端点的Out消息进行处理，最终只有一个消息交换传入direct:b端点-->
   <!--这里的聚合策略和上面相同-->
   <recipientList strategyRef="myStrategy">
      <header>myHeader</header>
   </recipientList>
   <to uri="direct:b"/>
</route>
```

##### 示例分析

在路由运行时recipientList处理器会到header.myHeader参数(参数类型应是集合，列表，数组)，用于计算出收件人列表。然后向列表中的接受者发送消息，接受者回复的消息通过聚合策略处理。最后将消息发送给direct:b端点。
recipientList和multicast区别在于前者是通过计算来确定接受者列表。

#### recipientList DSL 命令支持以下选项

名称|默认值|描述
----|----|----
delimiter|,|如果表达式返回多个端点，使用的分隔符。
strategyRef|暂无|引用一个AggregationStrategy来将来自收件人的回复组合成单个传出。 默认情况下，骆驼将使用最后一个答复作为传出的消息。
strategyMethodName|暂无|当使用POJO作为AggregationStrategy时，他的选项可以用来显式指定要使用的方法名称。
strategyMethodAllowNull|false|可以使用这个选项，当使用POJO作为AggregationStrategy。 如果为false，则不使用聚合方法。 如果为true，则在没有要充实的数据时，oldExchange使用空值。
parallelProcessing|false|如果启用，发送消息到multicast。同时，请注意，调用者线程仍然会等待，直到所有的消息已经完全处理，然后才继续。它只发送和处理来自multicast的同时发生的应答。
parallelAggregate|false|如果启用，可以同时调用AggregationStrategy上的聚合方法。 请注意，这需要AggregationStrategy的实现是线程安全的。 默认情况下，这个选项是false，这意味着Camel会自动同步对聚合方法的调用。
executorServiceRef|暂无|指的是用于并行的自定义线程池处理。 注意，如果你设置了这个选项，那么并行处理是自动隐含的，你不必启用这个选项。
stopOnException|false|发生异常时是否立即停止继续处理。 默认false，Camel将把消息发送到所有多点传送，若某节点发生异常,multicast会继续处理。 你也可以在AggregationStrategy类中处理异常。
ignoreInvalidEndpoints|false|如果端点uri无法解析，应该忽略。 否则，Camel将抛出一个异常，说明端点uri无效。
streaming|false|如果启用，那么Camel将按照它们返回的顺序乱序处理回复。 如果禁用，骆驼将按照与multicast相同的顺序处理回复。
timeout|暂无|设置以毫秒为单位指定的总超时时间。
onPrepareRef|暂无|指定制处理器来准备副本,每个multicast的交换将收到。
shareUnitOfWork|false|工作单元是否应该共享。 

---

<span id="removeHeader"></span>
### 33.removeHeader

#### 概述

从交换消息中删除指定的header。

#### 示例

##### Blueprint DSL 配置

```xml
camelContext xmlns="http://camel.apache.org/schema/blueprint">
	  <route>
           <from uri="timer://foo?period=10s"/>
           <!--添加头部数据-->
           <process ref="beanProcessor"/>
		   <removeHeader headerName="test"/>
           <!--打印头部数据-->
	       <process ref="dataSetProcessor"/>
      </route>    
</camelContext>
```

##### 示例分析

示例中，执行了removeHeader后，在下面的处理器中头部数据为null。

---

<span id="removeHeaders"></span>
### 34.removeHeaders

#### 概述

从交换的输入消息中删除与指定模式匹配的头部。 该模式可以有形式，前缀* - 在这种情况下，它匹配每个名称以前缀开头 - 否则，它被解释为一个规则表达。

#### 示例

##### Blueprint DSL 配置

```xml
camelContext xmlns="http://camel.apache.org/schema/blueprint">
	  <route>
           <from uri="timer://foo?period=10s"/>
           <!--添加头部数据-->
           <process ref="beanProcessor"/>
		   <removeHeaders pattern="CamelHttp*"/>
           <!--打印头部数据-->
	       <process ref="dataSetProcessor"/>
      </route>    
</camelContext>
```

##### 示例分析

示例中removeHeaders处理器会将CamelHttp为前缀的头部数据全部删除。

--- 

<span id="removeProperties"></span>
### 35.removeProperties

#### 概述

从交换中删除与指定模式匹配的属性。

#### 示例

##### Blueprint DSL 配置

```xml
camelContext xmlns="http://camel.apache.org/schema/blueprint">
	  <route>
           <from uri="timer://foo?period=10s"/>
		   <removeProperties pattern="Camel*" excludePattern="CamelHttp,CamelTcp"/>
           <!--打印属性-->
	       <process ref="dataSetProcessor"/>
      </route>    
</camelContext>
```

##### 示例分析

上例中，使用removeProperties处理器后，会删除除CamelHttp,CamelTcp以外Camel开头的所有属性值。

---

<span id="removeProperty"></span>
### 36.removeProperty

#### 概述

从交换中删除指定的属性。

#### 示例

##### Blueprint DSL 配置

```xml
camelContext xmlns="http://camel.apache.org/schema/blueprint">
	  <route>
           <from uri="timer://foo?period=10s"/>
		   <removeProperty propertyName="CamelTimerName"/>
           <!--打印属性-->
	       <process ref="dataSetProcessor"/>
      </route>    
</camelContext>
```

##### 示例分析

上例中，removeProperty处理器会删除名称为CamelTimerName的属性值。

---

<span id="resequence"></span>
### 37.resequence

#### 概述

重排序模式根据规则对输入的交换进行重新排序，并依次输出到下游端点。

#### 排序器

![](https://i.imgur.com/LxYYARr.png)

#### 批处理模式

将消息收集到一个批处理中，对消息进行分类并将其发送到输出。

##### 示例

###### BeanProcessor类

```java
public class BeanProcessor implements Processor {
	@Override
	public void process(Exchange exchange) throws Exception {
		exchange.getIn().setBody("路由一交换数据");
		exchange.getIn().setHeader("id",2);
		System.out.println("BeanProcessor处理器运行--->");
	}	
}
```

###### DataSetProcessor类

```java
public class DataSetProcessor implements Processor {
	@Override
	public void process(Exchange exchange) throws Exception {
		exchange.getIn().setBody("路由二交换数据");
		exchange.getIn().setHeader("id",5);
		System.out.println("DataSetProcessor处理器运行--->");
	}
}
```

###### Blueprint DSL 配置

```xml
<!--路由一-->
<route>
    <from uri="timer://foo?delay=3s&amp;period=50s" />
    <to uri="class:com.shudi.databridge.test.BeanProcessor"/>
    <to uri="direct:a"/>
</route>

<!--路由二-->
<route>
    <from uri="timer://foo?period=50s" />
    <to uri="class:com.shudi.databridge.test.DataSetProcessor"/>
    <to uri="direct:a"/>
</route>

<!--路由三-->
<route>
    <from uri="direct:a" />
    <resequence>
      <!--批处理配置-->
      <!--batchSize表示批大小-->
      <!--batchTimeout表示每个交换进入resequence之后的超时值-->
      <batch-config batchSize="2" batchTimeout="10000" />
      <!--重排序条件-->
      <simple>header.id</simple>
      <to uri="log:hunter" />
    </resequence>
</route>
```
###### 示例本地运行

![](https://i.imgur.com/XJSKRWa.png)

###### 示例分析

当消息进入resequence处理器后，消息将会重新排序，再发送至下游端点mock:result。上例是按照消息头部中的id进行排序的，batchTimeout为重排序的超时值（毫秒为单位），batchSize为重排序处理器接收消息的批大小（这里最多可以达到2条）。
也就是说当消息重排序满足2条消息或超时10000毫秒任意一个条件就会完成重排序,并按照顺序发送交换至log:hunter端点。

###### 补充

默认的批处理超时为1000毫秒,默认的批处理大小为100条消息。

##### 批处理Options
只在批处理模式下可用的选项

名称|默认值|描述
----|----|----
allowDuplicates|false|如果为true，则不要丢弃批处理中的重复消息（其中重复的意思是消息表达式评估为相同的值）。
reverse|false|如果为true，则以相反的顺序放置消息（应用于消息表达式的默认顺序是基于Java的字符串词法排序，如定义String.compareTo（））。

#### 流处理模式

流处理重排序器算法基于检测消息流中的间隙，而不是以固定的批量大小。 间隙检测，结合超时，预先消除了需要知道序列消息数（即批量大小）的限制。 消息必须包含一个唯一的序列号。无论输入多少个交换进入重排器，它输出的流量都固定的，超出流量值的将会被丢弃。
例如，序列号为3的消息具有序列号为2的前驱消息和序列号为4的后继消息。消息序列2,3,5有一个缺口，因为3的继任者失踪。 因此重排序器必须保留消息5，直到消息4到达（或发生超时）。

##### 示例

###### TestProcessor类

```java
public class TestProcessor implements Processor {
	@Override
	public void process(Exchange exchange) throws Exception {
		exchange.getIn().setBody("路由四交换数据");
		exchange.getIn().setHeader("id",7);
		System.out.println("TestProcessor处理器运行--->");
	}
}
```

###### Blueprint DSL 配置
结合批处理模式中的示例

```xml
<!--路由一-->
<route>
    <from uri="timer://foo?delay=3s&amp;period=50s"/>
    <to uri="class:com.shudi.databridge.test.BeanProcessor"/>
    <to uri="direct:a"/>
</route>

<!--路由二-->
<route>
    <from uri="timer://foo?period=50s"/>
    <to uri="class:com.shudi.databridge.test.DataSetProcessor"/>
    <to uri="direct:a"/>
</route>

<!--路由三-->
<route>
    <from uri="direct:a"/>
    <resequence>
      <!--批处理配置-->
      <!--capacity表示输出交换的流量大小-->
      <!--timeout表示每个交换进入resequence之后的超时值-->
      <stream-config capacity="2" timeout="10000"/>
      <!--重排序条件-->
      <simple>header.id</simple>
      <to uri="log:hunter"/>
    </resequence>
</route>

<!--路由四-->
<route>
    <from uri="timer://foo?delay=1s&amp;period=50s"/>
    <to uri="class:com.shudi.databridge.test.TestProcessor"/>
    <to uri="direct:a"/>
</route>
```

###### 示例本地运行

![](https://i.imgur.com/Jwttq4A.png)

当属性capacity="3"时，运行结果

![](https://i.imgur.com/8CfBXGT.png)

###### 示例分析

流处理模式示例中配置了消息容量为2，超时时间为10000毫秒的流重排器(默认值均与批次重排器相同)。而这里使用capacity容量参数来防止重排器耗尽内存。所以流重排器是无法确定消息数，只能依靠超时和最大内存来完成重排序。

##### 批处理和流处理的区别

相同点：采用规则判断排序；使用超时作为一次完整排序的结束。
不同点：批处理使用批大小和超时来控制一次完整的排序，后续的交换则进入下一次重排序；流处理只使用超时控制一次完整的排序，当容量溢出时，流处理会丢弃超出capacity容量值之后进入的交换。

---

<span id="rollback"></span>
### 38.rollback

#### 概述

事务回滚，只标记回滚的当前事务(默认情况下也会抛出异常)。

#### 事务回滚机制

![](https://i.imgur.com/5rVfz0z.png)

#### 示例

##### Blueprint DSL 配置

```xml
<onException>
    <exception>com.mycompany.ValidationException</exception>
    <to uri="activemq:validationFailed"/>
    <rollback markRollbackOnly="true"/>
</onException>
<route>
    <from uri="direct:start"/>
    <enrich strategyRef="aggregationStrategy">
          <constant>direct:resource</constant>
    <to uri="direct:result"/>
</route>
<route>
     <from uri="direct:resource"/>
     ...
</route>
```

##### 示例分析

示例中，当聚合器将消息聚合到一起后会提交到可回收的聚合仓库，然后进行下一步端点处理消息。此时若发生异常，聚合消息则会先发送到activemq端点，然后触发事务回滚机制。所以最初的聚合消息不会在聚合仓库保存下来。

---

<span id="route"></span>
### 39.route

#### 概述

标记一段路由。

#### 示例

##### Blueprint DSL 配置

```xml
<route>
     <from uri="seda:inputA"/>
     <to uri="validation:foo/bar.xsd"/>
     <to uri="activemq:someQueue"/>
</route>
```

##### 示例分析

route处理器为骆驼中常用的处理器之一，用于标记路由，为路由运行的最小单位。(route中必须包含一个消费者端点from)

---

<span id="routingSlip"></span>
### 40.routingSlip

#### 概述

路由滑动模式图中显示的路由滑动模式允许您通过一系列处理步骤连续路由消息，其中步骤的顺序在设计时不知道，并且可能因每条消息而异。 消息应该通过的端点列表存储在一个标题字段（slip）中，Apache Camel在运行时读取这个头部字段以便动态地构建管道。

#### 路由滑动模式

![](https://i.imgur.com/L66no8K.png)

#### 示例

##### Blueprint DSL 配置

```xml
<route>
    <from uri="direct:c"/>
    <routingSlip uriDelimiter="#">
      <headerName>aRoutingSlipHeader</headerName>
    </routingSlip>
</route>
```

##### 示例分析

routingSlip处理器实现了路由动态模式。上例中当消息发送到routingSlip处理器时，处理器会读取交换头部的端点列表来构建下一个端点（uriDelimiter属性表示返回多个端点的分隔符）。
可以看出这样的路由通道完全是动态构建的，提高了开发的灵活度。

#### routingSlip DSL 命令支持以下选项

名称|默认值|描述
----|----|----
uriDelimiter|,|返回多个端点时的分隔符。
ignoreInvalidEndpoints|false|如果端点uri无法解析，应该忽略。 否则，Camel将抛出一个异常，说明端点uri无效。
cacheSize|0|配置ProducerCache的缓存大小，该缓存将缓存生产者以供重用。 默认缓存大小为1000，如果没有指定其他值，将使用该缓存大小。 将该值设置为-1会完全关闭缓存。

---

<span id="sample"></span>
### 41.sample

#### 概述

采样器允许您从一个路由中抽取一个交换的样本。它是配置了一个采样周期，在此期间只能允许一个单交换通过。所有其他交换所都将停止。

#### 示例

##### Blueprint DSL 配置

```xml
<!--路由一-->
<route>
    <from uri="timer://foo?period=50s"/>
    <setBody>
          <constant>路由一运行</constant>
    </setBody>
    <to uri="direct:a"/>
</route>
<!--路由二-->
<route>
    <from uri="timer://foo?delay=10s&amp;period=50s"/>
    <setBody>
          <constant>路由二运行</constant>
    </setBody>
    <to uri="direct:a"/>
</route>
<!--路由三-->
<route>
    <from uri="timer://foo?delay=20s&amp;period=50s"/>
    <setBody>
          <constant>路由三运行</constant>
    </setBody>
    <to uri="direct:a"/>
</route>

<!--路由四-->
<route>
    <from uri="direct:a"/>
    <sample samplePeriod="5" units="seconds">
        <to uri="log:hunter"/>
    </sample>
</route>
```

##### 示例运行

![](https://i.imgur.com/urykG3x.png)

##### 示例分析

上述示例中3条消息依次传入direct:a端点，sample处理器会每隔5秒采集一次交换信息，并发送至log:hunter端点(如果有重复消息，sample处理器将不会采集发送至log:hunter端点)。
sample处理器常用于对路由消息的测试。

#### sample DSL 命令支持以下选项

名称|默认值|描述
----|----|----
messageFrequency|暂无|每个样本消息信息。 您限定使用的频率或周期。
samplePeriod|1|每隔一段时间对信息进行采样。您限定使用的频率或周期。
units|SECOND|时间单位作为java.util.concurrent的枚举。来自`JDK TimeUnit`。

---

<span id="script"></span>
### 42.script

#### 概述

用于执行不会更改消息的脚本（默认情况下）。 当您需要调用某些不在Java代码中的逻辑（如JavaScript，Groovy或任何其他语言）时，这非常有用。 消息正文没有被改变（默认），但是脚本上下文可以访问当前的Exchange，并且可以直接改变消息或者消息头。 但是脚本的返回值被丢弃并且不被使用。

#### 示例

##### Blueprint DSL 配置

```xml
<route>
   <from uri="file://inbox"/>
   <script>
     <groovy>// some groovy code goes here</groovy>
   </script>
   <beanRef ref="myServiceBean" method="processLine"/>
</route>
```

##### 示例分析

script处理器可以使用脚本语言来执行其他逻辑，而不改变消息体。

---

<span id="setBody"></span>
### 43.setBody

#### 概述

设置交换消息的消息体。

#### 示例

##### Blueprint DSL 配置

```xml
<route>
   <from uri="direct:start"/>
       <setBody>
          <xpath resultType="org.w3c.dom.Document">//foo:bar</xpath>
       </setBody>
   <to uri="activemq:Output"/>
</route>
```

##### 示例分析

setBody处理器会读取//foo:bar文件下的数据，写入到消息的body中，然后发送到activemq端点。
setBody处理器实现了在路由中设置消息的body。

---

<span id="setExchangePattern"></span>
### 44.setExchangePattern

#### 概述

将当前交换模式设置为指定的值。

#### 示例

##### Blueprint DSL 配置

```xml
<route>
    <from uri="mq:someQueue"/>
    <setExchangePattern pattern="inOnly"/>
    <inOnly uri="mq:anotherQueue"/>
</route>
```

##### 示例分析

setExchangePattern处理器和inOnly/inOut作用相同，详情请了解inOnly/inOut处理器。

---

<span id="setFaultBody"></span>
### 45.setFaultBody

#### 概述

添加一个处理器，在错误消息上设置正文。

#### 示例

##### Blueprint DSL 配置

```xml
<route>
   <from uri="direct:start"/>
       <setFaultBody>
           <constant>123</constant>
       </setFaultBody>
   <to uri="activemq:Output"/>
</route>
```

##### 示例分析

setFaultBody处理器是当消息主体出现错误时，设置为给定的表达式的值，并在该消息上设置故障标志fault=true;

---

<span id="setHeader"></span>
### 46.setHeader

#### 概述

设置交换消息的消息头。

#### 示例

##### Blueprint DSL 配置

```xml
<route>
   <from uri="direct:start"/>
       <setHeader headerName="id">
           <constant>123</constant>
       </setHeader>
   <to uri="activemq:Output"/>
</route>
```

##### 示例分析

setHeader处理器设置了消息头部数据id=123。Header的类型为Map。

---

<span id="setOutHeader"></span>
### 47.setOutHeader

#### 概述

在交换消息中设置输出的消息头。

#### 示例

##### Blueprint DSL 配置

```xml
<route>
   <from uri="direct:start"/>
       <setOutHeader headerName="id">
           <constant>123</constant>
       </setOutHeader>
   <to uri="activemq:Output"/>
</route>
```

##### 示例分析

setOutHeader处理器设置了输出消息的头部数据。

---

<span id="setProperty"></span>
### 48.setProperty

#### 概述

设置指定的交换属性。

#### 示例

##### Blueprint DSL 配置

```xml
<route>
   <from uri="direct:start"/>
       <setProperty propertyName="Cameltest">
           <constant>123456</constant>
       </setProperty>
   <to uri="activemq:Output"/>
</route>
```

##### 示例分析

setProperty处理器会将常量值添加到属性里。

---

<span id="sort"></span>
### 49.sort

#### 概述

对In消息体的内容进行排序（可以选择指定自定义比较器的位置）。

#### 示例

##### Blueprint DSL 配置

```xml
<!--自定义比较器(需要实现Comparator<T>接口)
<bean id="myReverseComparator" class="com.mycompany.MyReverseComparator"/>
-->
<route>
   <from uri="timer://foo?period=50s"/>
   <setBody>
        <constant>camel,java,ajax</constant>
   </setBody>
   <!--这里可以在sort节点中添加自定义比较器:comparatorRef="myReverseComparator"-->
   <sort>
        <tokenize token=","/>
   </sort>
   <to uri="log:hunter"/>
</route>
```

##### 示例运行

![](https://i.imgur.com/Dc0tAdh.png)

##### 示例分析

示例中，sort处理器引用自定义比较器来对消息内容进行排序，从而达到我们预想的结果。
如果没有自定义比较器，骆驼会默认使用一个A-Z的比较器。

---

<span id="split"></span>
### 50.split

![](https://i.imgur.com/jxMBQZe.png)

#### Apache骆驼分解器实际上支持两种模式

##### 简单的分解器

###### Blueprint DSL 配置

```xml
<!--字符串拆分-->
<route>
    <from uri="timer://foo?period=5s"/>
    <setBody>
       <constant>camel,java,ajax</constant>
    </setBody>
    <split streaming="true">
       <tokenize token="," regex ="true" group="1"/>
       <to uri="log:hunter"/>
    </split>
</route>
<!--集合拆分-->
<route>
    <from uri="timer://foo?period=5s"/>
    <split>
       <simple>${body}</simple>
       <to uri="log:hunter"/>
    </split>
</route>
```

###### 示例本地运行

![](https://i.imgur.com/diqgCp3.png)

###### 示例分析

路由中split处理器使用标记化表达式(,)来拆分主体或标题。streaming="true"是表示不会立即读取整个文件（如果文件很大，就会得到更好的性能）。group="1"表示每1行分解为一个子交换。(tokenize标签中还有更多参数可用，您可到源码上进行了解)

##### 分解器/聚合器模式

###### Blueprint DSL 配置

```xml
<route>
    <from uri="file:inbox"/>
     <split streaming="true" strategyRef="myAggregationStrategy">
       <tokenize token="," regex ="true"/>
       <to uri="http://oldhost/order"/>
     </split>
     <to uri="activemq:queue:order"/>
</route>
```

###### 示例分析

上例split处理器中的消息模式为inOut请求/响应模式。当分解出子交换后发送给http端点，然后采用聚合策略将所有的响应消息进行处理，最后将消息发送给activemq端点。
分解器/聚合器模式在实现消息处理时会表现的更为全面。

##### split DSL命令支持以下选项

名称|默认值|描述
----|----|----
strategyRef|暂无|引用一个AggregationStrategy来将来自收件人的回复组合成单个传出。 默认情况下，骆驼将使用最后一个答复作为传出的消息。
strategyMethodName|暂无|当使用POJO作为AggregationStrategy时，他的选项可以用来显式指定要使用的方法名称。
strategyMethodAllowNull|false|可以使用这个选项，当使用POJO作为AggregationStrategy。 如果为false，则不使用聚合方法。 如果为true，则在没有要充实的数据时，oldExchange使用空值。
parallelProcessing|false|如果启用，发送消息到multicast。同时，请注意，调用者线程仍然会等待，直到所有的消息已经完全处理，然后才继续。它只发送和处理来自multicast的同时发生的应答。
parallelAggregate|false|如果启用，可以同时调用AggregationStrategy上的聚合方法。 请注意，这需要AggregationStrategy的实现是线程安全的。 默认情况下，这个选项是false，这意味着Camel会自动同步对聚合方法的调用。
executorServiceRef|暂无|指的是用于并行的自定义线程池处理。 注意，如果你设置了这个选项，那么并行处理是自动隐含的，你不必启用这个选项。
stopOnException|false|发生异常时是否立即停止继续处理。 默认false，Camel将把消息发送到所有多点传送，若某节点发生异常,multicast会继续处理。 你也可以在AggregationStrategy类中处理异常。
streaming|false|如果启用，那么Camel将按照它们返回的顺序乱序处理回复。 如果禁用，骆驼将按照与multicast相同的顺序处理回复。
timeout|暂无|设置以毫秒为单位指定的总超时时间。
onPrepareRef|暂无|指定制处理器来准备副本,每个multicast的交换将收到。
shareUnitOfWork|false|工作单元是否应该共享。 

##### 获取计数信息 

通过Exchange properties获取计数信息

property|类型|描述
----|----|----
CamelSplitIndex|int|一个分割计数器，每一个交换被分割。计数器从0开始。
CamelSplitSize|int|已拆分的交换总数。 此标头不适用于基于流的分割。 从camel 2.9开始，这个标头也被设置为基于流的拆分，但只在完成的Exchange上。
CamelSplitComplete|boolean|camel 2.4：这个交易是否是最后一个。

###### 示例

```xml
<log message="航信发票开具开始：${exchange.getProperty(CamelSplitSize)} / ${exchange.getProperty(CamelSplitIndex)}++ :订单号${body.get(trade_no)},订单信息：${body}"/>
```

---

<span id="stop"></span>
### 51.stop

#### 概述

停止路由当前交换并将其标记为已完成。

#### 示例

##### Blueprint DSL 配置

```xml
<route>
  <from uri="jms:incomingOrders"/>
  <choice>
    <when>
      <simple>${header.CamelFileName} regex '^.*xml$'</simple>
      <to uri="jms:xmlOrders"/>
    </when>
    <when>
      <simple>${header.CamelFileName} regex '^.*(csv|csl)$'</simple>
      <to uri="jms:csvOrders"/>
    </when>
    <otherwise>
      <to uri="jms:badOrders"/>
      <stop/>
    </otherwise>
  </choice>
  <to uri="jms:continuedProcessing"/> 
</route>
```

##### 示例分析

上例中，当消息进入otherwise处理器中的端点jms:badOrders后，使用了stop处理器停止了路由。所以消息不会发送给jms:continuedProcessing端点。

---

<span id="threads"></span>
### 52.threads

#### 概述

创建一个线程池，用于并发处理路由的后一部分。

#### 示例

##### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint">
    <threadPool id="myPool" threadName="Cool" poolSize="5" maxPoolSize="15" maxQueueSize="250"/>
    <route>
        <from uri="direct:start"/>
        <to uri="log:start"/>
        <threads executorServiceRef="myPool">        
            <to uri="log:hello"/>
        </threads>
    </route> 
</camelContext>
```

##### 示例分析

上例中，当消息到达threads处理器时，executorServiceRef会根据threadPool的配置创建一个线程池，作用于端点log:hello。
所以说log:start和log:hello不在同一线程中，threads处理器在路由高并发情况下有很好的处理效果。

---

<span id="throttle"></span>
### 53.throttle

#### 概述

throttle处理器确保生产者端点不会过载。 调节器通过限制每秒可以通过的消息的数量来工作。 如果传入的消息超过了指定的速率，throttler会将多余的消息累积到一个缓冲区中将它们更慢地传送给生产者端点。

#### 示例

##### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint">
  <route>
     <from uri="SourceURL"/>
     <throttle maximumRequestsPerPeriod="100" timePeriodMillis="1000">
       <to uri="TargetURL"/>
     </throttle>
  </route>
</camelContext>
```

##### 示例分析

上例中，调节器将发送的速率设置为每秒100条消息。当目标端点处理消息的效率无法保证的时候，就可以使用调节器来控制流量，减小服务器的压力。

---

<span id="throwException"></span>
### 54.throwException

#### 概述

抛出指定的Java异常。

#### 示例

##### Blueprint DSL 配置

```xml
<bean id="damn" class="java.lang.IllegalArgumentException">
     <constructor-arg index="0" value="Damn"/>
</bean>
<camelContext xmlns="http://camel.apache.org/schema/blueprint">
 <route>
    <from uri="SourceURL"/>
    <choice>
      <when>
        <!-- First predicate -->
        <simple>header.foo = 'bar'</simple>
        <to uri="Target1"/>
      </when>
      <when>
        <!-- Second predicate -->
        <simple>header.foo = 'manchu'</simple>
        <to uri="Target2"/>
      </when>
      <otherwise>
        <throwException ref="damn"/>
      </otherwise>
    </choice>
 </route>
</camelContext>
```

##### 示例分析

示例中当两种条件都不满足时，消息会进入otherwise处理器，抛出异常java.lang.IllegalArgumentException。
这里也可以自定义的异常来作为throwException处理器的引用。

---

<span id="transacted"></span>
### 55.transacted

#### 概述

创建一个包含路由后一部分的Spring事务范围。

#### 示例

##### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint">
    <route id="partnerToDB">
        <from uri="activemq:queue:partners"/>
        <transacted ref="pandianTxPolicy"/>
        <bean ref="partner" method="toSql"/>
        <to uri="jdbc:pandiandatasource"/>
    </route> 
</camelContext>

<!--数据库库事务配置-->
<bean id="pandianTxPolicy" class="org.apache.camel.spring.spi.SpringTransactionPolicy">
	<property name ="transactionManager" ref ="pandianTxManager"/> 
	<property name ="propagationBehaviorName" value="PROPAGATION_REQUIRED"/> 
</bean>	
<bean id="pandianTxManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager"> 
    <property name="dataSource" ref="pandiandatasource"/>	
</bean>

<!--数据源配置-->
<bean id="pandiandatasource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
	<property name="maxActive" value="50" />
	<property name="minIdle" value="10" />
	<property name="initialSize" value="10" />
	<property name="driverClassName" value="${pandian.driverClassName}" />
	<property name="url" value="${pandian.url}" />
	<property name="username" value="${pandian.username}" />
	<property name="password" value="${pandian.password}" />
</bean>
```

##### 示例分析

示例中，从mq中拿到消息后，使用transacted处理器开启了事务。然后经过bean处理消息并发送给jdbc端点保存数据。
Apache Camel是通过和Spring的框架集成进行事务控制的.

---

<span id="transform"></span>
### 56.transform

#### 概述

转换消息，将In消息头信息复制到Out消息头，并将Out消息体设置为指定的值。

#### 示例

##### 示例一 Blueprint DSL 配置

```xml
<route>
     <from uri="activemq:queue:orderQueue"/>
     <transform>
        <constant>DummyBody</constant>
     </transform>
     <to uri="activemq:queue:billingQueue"/>
</route> 
```

##### 示例分析

示例一中路由显示了一个transform命令，该命令使用一条消息创建一个Out消息包含了字符串的身体。
常量(“DummyBody”)表示一个常量表达式。您不能直接传递字符串"DummyBody"，因为transform处理器的参数必须是一个表达式类型

##### 示例二 Blueprint DSL 配置

```xml
<bean id="htmlBean" class="camelinaction.HtmlBean"/>
<camelContext xmlns="http://camel.apache.org/schema/blueprint">
  <route>
     <from uri="direct:start"/>
     <transform>
       <method bean="htmlBean" method="toHtml"/>
     </transform>
     <to uri="mock:result"/>
  </route>
</camelContext>
```

##### HtmlBean
转换处理类

```java
public class HtmlBean {
     public static String toHtml(String body) {
          //全部转换
          body = body.replaceAll("\n", "<br/>");
          body = "<body>" + body + "</body>";
          return body;
     }
}
```

##### 示例分析

示例二我们将HTML文本中的<br/>标签全部替换成</n>。在transform处理器中,引用了bean中方法来处理转换逻辑。

---

<span id="validate"></span>
### 57.validate

#### 概述

使用断言表达式来测试当前消息是否有效。 如果断言返回false，则抛出PredicateValidationException异常。

#### 示例

##### Blueprint DSL 配置

```xml
<!--路由一-->
<route>
  <from uri="jms:queue:incoming"/>
  <validate>
    <simple>${body} regex ^\\w{10}\\,\\d{2}\\,\\w{24}$</simple>
  </validate>
  <beanRef ref="myServiceBean" method="processLine"/>
</route>
<!--路由二-->
<route>
  <from uri="jms:queue:incoming"/>
  <validate>
    <simple>${in.header.bar} == 100</simple>
  </validate>
  <beanRef ref="myServiceBean" method="processLine"/>
</route>
```

##### 示例分析

上例中，路由一采用正则表达式验证了消息体；路由二验证In消息头bar是否等于100；
若表达式返回为false，则抛出PredicateValidationException异常，停止路由。
想要熟练的运用validate处理器，必须多了解表达式语言。

---

<span id="wireTap"></span>
### 59.wireTap

#### 概述

窃听节点,将当前交换的副本发送到指定的线路URI。

![](https://i.imgur.com/rV4O0Yv.png)

#### 示例

##### Blueprint DSL 配置
```xml
<route>
    <from uri="direct:start"/>
    <wireTap uri="direct:foo">
        <body><constant>Bye World</constant></body>
    </wireTap>
    <to uri="mock:result"/>
</route>
```

##### 示例分析

路由中，当消息进入wireTap处理器时，wireTap会创建另一个线程，将改变后消息副本发送给direct:foo端点。
wireTap窃听器和主线路不在同一线程，所以不会影响路由整体的运行。
wireTap窃听器主要用于测试，日志记录等功能。

---

<span id="xpath"></span>
### 62.xpath

#### 概述

Camel支持XPath，允许在DSL或Xml配置中使用表达式或谓语。XPath是一门在XML文档中查找信息的语言。XPath可用来在XML文档中对元素和属性进行遍历。

>w3school xpath相关资料链接：[http://www.w3school.com.cn/xpath/index.asp](http://www.w3school.com.cn/xpath/index.asp)

#### 示例

##### test.xml

```xml
<?xml version="1.0" encoding="utf-8" ?>
<ric>
  <catalog>
    <book price="75">
      <author>Kalen Delaney</author>
      <name>Inside SQL Server 2000</name>
    </book>
  </catalog>
</ric>
```

##### Blueprint DSL 配置

```xml
<route>
   <from uri="file:H:/Data?fileName=test.xml&amp;noop=true"/>
   <filter>
      <xpath>/ric/catalog/book/author='Kalen Delaney'</xpath>
      <to uri="class:com.shudi.databridge.test.DataSetProcessor"/>
   </filter>
   <to uri="log:hunter"/>
</route>
```

##### 示例运行

当满足author=Kalen Delaney时
![](https://i.imgur.com/tWFRayE.png)

当不满足author=Kalen Delaney时
![](https://i.imgur.com/h6cGfdr.png)

##### 示例分析

XPath是一门在XML文档中查找信息的语言，使用表达式或谓语进行判断。多用于过滤器处理器子节点。
更多的xpath语法请查看w3school xpath相关资料链接。

---

<span id="constant"></span>
### 63.constant

#### 概述

常量语言是一种简单的内置语言，用于指定纯文本字符串。 这个可以在任何需要表达式类型的上下文中提供纯文本字符串。

#### 示例

##### Blueprint DSL 配置

```xml
<route>
   <from uri="SourceURL"/>
   <setHeader headerName="username">
      <constant>Jane Doe</constant>
   </setHeader>
   <to uri="TargetURL"/>
</route>
```

##### 示例分析

示例中，使用constant节点添加了username的value值。constant节点常用于处理器中的参数设置。

---

<span id="simple"></span>
### 64.simple

#### 概述

Simple语言是Apache Camel专门为访问和操作交换对象的各个部分而开发的语言。 这种语言并不像最初创建时那么简单，它现在具有一套全面的逻辑运算符和连词。

Simple语言资料链接：[http://camel.apache.org/simple.html](http://camel.apache.org/simple.html)

#### 变量

变量|类型|描述
----|----|----
camelId|String|camel 2.10:CamelContext名称。
camelContext.OGNL|Object|camel 2.11:使用骆驼 OGNL 表达式调用的 CamelContext 
collate(group)|List|camel 2.17：collate函数迭代消息正文并将数据分组到指定大小的子列表中。 这可以与Splitter EIP一起使用来拆分消息主体，并将拆分的子消息分组/分批成一组N个子列表。 此方法与Groovy中的整理方法类似。
exchange|Exchange|获取交换。
exchange.OGNL|Object|camel 2.16:"Exchange使用骆驼OGNL表达式调用。
exchangeId|String|获取交换Id。
id|String|输入消息Id。
body|Object|正文。
in.body|Object|输入正文。
body.OGNL|Object|camel 2.3:使用Camel OGNL表达式调用的输入正文。
in.body.OGNL|Object|camel 2.3:使用Camel OGNL表达式调用的输入正文。
bodyAs(type)|Type|camel 2.3:将正文转换为由其类确定的给定类型。已转换的正文可以为null。
bodyAs(type).OGNL|Object|camel 2.18:将正文转换为由其类确定的给定类型, 然后使用Camel OGNL表达式调用方法。已转换的正文可以为 null。
mandatoryBodyAs(type)|Type|camel 2.5:将正文转换为由其类确定的给定类型, 并期望正文不为null。
mandatoryBodyAs(type).OGNL|Object|camel 2.18:将正文转换为由其类确定的给定类型, 然后使用Camel OGNL表达式调用方法。
out.body|Object|输出正文。
header.foo|Object|获取header中foo属性。
headers[foo]|Object|camel 2.9.2:引用输入foo header。
in.header.foo|Object|获取输入foo header。
in.header[foo]|Object|camel 2.9.2:引用输入foo header。
in.headers.foo|Object|获取输入foo header。
in.headers[foo]|Object|camel 2.9.2:引用输入foo header。
header.foo[bar]|Object|camel 2.3:将输入foo标头作为映射, 并在映射中以bar作为键执行查找。
in.header.foo[bar]|Object|camel 2.3:将输入foo标头作为映射, 并在映射中以bar作为键执行查找。
in.headers.foo[bar]|Object|camel 2.3:将输入foo标头作为映射, 并在映射中以bar作为键执行查找。
header.foo.OGNL|Object|camel 2.3:引用输入foo标头, 并使用camel OGNL表达式调用其值。
in.header.foo.OGNL|Object|camel 2.3:引用输入foo标头, 并使用camel OGNL表达式调用其值。
in.headers.foo.OGNL|Object|camel 2.3:引用输入foo标头, 并使用camel OGNL表达式调用其值。
out.header.foo|Object|获取输出foo header。
out.header[foo]|Object|camel 2.9.2:引用输出foo header。
out.headers.foo|Object|获取输出foo header。
out.headers[foo]|Object|camel 2.9.2:引用输出foo header。
headerAs(key,type)|Type|camel 2.5:将标头转换为由其类确定的给定类型。
headers|Map|camel 2.9:引用输入标头(headers)。
in.headers|Map|camel 2.9:引用输入标头(headers)。
property.foo|Object|已弃用:引用exchange上的foo属性。
exchangeProperty.foo|Object|camel 2.15:引用exchange上的foo属性。
property[foo]|Object|已弃用:引用exchange上的foo属性。
exchangeProperty[foo]|Object|camel 2.15:引用exchange上的foo属性。
property.foo.OGNL|Object|已弃用:引用exchange上的foo属性, 并使用骆驼OGNL表达式调用其值。
exchangeProperty.foo.OGNL|Object|camel 2.15:引用exchange上的foo属性,并使用骆驼OGNL表达式调用它的值。
sys.foo|String|获取系统属性foo。
sysenv.foo|String|camel 2.3:引用系统环境属性foo。
exception|Object|camel 2.4:引用exchange上的异常对象, 如果exchange上没有设置异常, 则为null。如果exchange中有任何异常 (Exchange.EXCEPTION_CAUGHT),则将回退和捕捉异常情况。
exception.OGNL|Object|camel 2.4:引用使用骆驼OGNL表达式对象调用的交换异常。
exception.message|String|指的是异常信息，如果没有异常，则为null。如果交换器有任何问题，将会返回并捕获异常信息。
exception.stacktrace|String|异常跟踪，camel 2.6,如果在交换机上没有=异常，则结果为空。 如果Exchange有任何异常，将回退并抓取异常（Exchange.EXCEPTION_CAUGHT）。
date:command:pattern|String|日期格式使用java.text.SimpleDateFormat模式。 支持的命令是：对于当前的时间戳。in.header.xxx 或 Date header.xxx在IN对象中与关键字xxx。 out.header.xxx在OUT标头中使用Date对象, 其中有key xxx。
bean:bean expression|Object|使用bean语言调用bean表达式。指定方法名称必须使用点作为分隔符。我们还支持Bean组件使用的?method=methodname语法。
properties : locations : key|String|已弃用: (使用properties-location代替)camel 2.3:使用给定的键查找属性。locations选项是可选的。在使用 PropertyPlaceholder中查看更多。
properties-location:locations:key|String|camel 2.14.1:使用给定的键查找属性。locations选项是可选的。在使用 PropertyPlaceholder中查看更多。
properties : key : default|String|camel 2.14.1: 使用给定的键查找属性。如果该键不存在或没有值, 则可以指定可选的默认值。
routeId|String|camel 2.11:返回路由Exchange的当前路由的Id。
threadName|String|camel 2.3:返回当前线程的名称。可用于记录目的。
ref:xxx|Object|camel 2.6:从注册表中查找具有给定Id的bean。
type:name.field|Object|camel 2.11:通过其 FQN 名称引用类型或字段。若要引用字段,可以追加 .FIELD_NAME。例如, 您可以从 Exchange中引用常量字段, 如下所示:org.apache.camel.Exchange.FILE_NAME 
null|null|camel 2.12.3:表示为null。
random(value)|Integer|camel 2.16.0:在0(包括)和value(排除) 之间返回一个随机整数。
random(min,max)|Integer|骆驼 2.16.0:在min(包括)和max(排除)之间返回一个随机整数。
skip(number)|Iterator|camel 2.19：跳过函数迭代消息正文，并跳过第一项。 这可以与Splitter EIP一起使用来拆分消息主体并跳过前N项。
messageHistory|String|camel 2.17:当前交换的消息历史，它是如何被路由的。 这与错误处理程序在未处理的异常情况下记录的路由堆栈跟踪消息历史记录类似。
messageHistory(false)|String|camel 2.17：作为messageHistory，但没有交换细节（只包括路线追踪）。如果您不想从邮件本身记录敏感数据，则可以使用此功能。

#### 运算符

运算符|描述
----|----
==|等于。
=~|camel 2.16:等于忽略大小写 (在比较String值时将忽略大小写)。
>|大于。
>=|小于。
<=|小于或等于。
!=|不等于。
contains|用于测试是否包含在基于字符串的值中。
not contains|用于测试如果不包含在基于字符串的值中。
regex|用于匹配定义为String值的给定正则表达式模式。
not regex|用于不匹配定义为String值的给定正则表达式模式。
in|如果在一组值中匹配，则每个元素必须用逗号分隔。如果你想包含一个空值, 那么它必须用双逗号来定义，如",,one,tow"
not in|如果不是在一组值中进行匹配，则每个元素必须用逗号分隔。如果要包含空值, 则必须使用双逗号来定义它。
is|如果左侧类型为instanceof值, 则为匹配。
not is|用于匹配如果左侧类型不是instanceof的值。
range|如果左边在数值范围内，则匹配：from..to。从camel 2.9: 范围值必须用单引号括起来。
not range|用于匹配如果左侧不在定义为数字的值范围内: from..to。从camel 2.9: 范围值必须用单引号括起来。
starts with|camel 2.17.1,2.18: 用于测试左侧字符串是否以右侧字符串开头。
ends with|camel 2.17.1,2.18: 用于测试左侧字符串是否以右侧字符串结束。
++|camel 2.9：数字递加。 左侧必须是一个函数，否则解析为文字。
--|camel 2.9：数字递减。 左侧必须是一个函数，否则解析为文字。
\\|camel 2.9.3 到 2.10.x若要转义值(例如 \$) 以指示$符号。特殊:使用\n为新行、\t teb,以及\r以进行回车。注意：使用文件语言不支持转义。注意:从camel 2.11, 不再支持转义符。已将其替换为以下三转义序列。
\n|Camel 2.11: 使用换行符。
\t|Camel 2.11:使用 tab 字符。
\r|Camel 2.11:使用回车符。
\}|camel 2.18:将 } 字符用作文本。
and|已弃用: 改用 && 。逻辑and运算符用于分组两个表达式。
or|已弃用: 改用 &brvbar;&brvbar; 。逻辑or运算符用于分组两个表达式。
&&|camel 2.9:逻辑与运算符用于分组两个表达式。
&brvbar;&brvbar;|camel 2.9:逻辑或运算符用于分组两个表达式。

#### 示例

##### 示例一：获取headers,获取当前时间

###### Blueprint DSL 配置

```xml
<route>
   <from uri="timer://foo?period=10s"/>
   <transform>
        <simple>Headers: ${headers} \n 当前时间：${date:now:yyyy-MM-dd HH:mm:ss}</simple> 
   </transform>
   <!--打印正文和标头数据-->
   <to uri="class:com.shudi.databridge.test.DataSetProcessor"/>
</route>
```

###### 示例运行

![](https://i.imgur.com/pmjkkOe.png)

##### 示例二：转换正文类型

###### Blueprint DSL 配置

```xml
<route>
   <from uri="timer://foo?period=10s"/>
   <setBody>
        <constant>123</constant>
   </setBody>
   <to uri="log:kaka"/>
   <transform>
        <simple>${bodyAs(Integer)}</simple> 
   </transform>
   <to uri="log:kaka"/>
</route>
```

###### 示例运行

![](https://i.imgur.com/cRrdDov.png)

##### 示例三:引用bean,判断正文取值范围

###### Blueprint DSL 配置

```xml
<bean id="integer" class="java.lang.Integer">
   <property name="value" value="456"/> 
</bean>
<camelContext xmlns="http://camel.apache.org/schema/blueprint">
   <route>
      <from uri="timer://foo?period=10s"/>
      <setBody>
         <constant>123</constant>
      </setBody>
      <to uri="log:kaka"/>
      <transform>
         <simple>${ref:integer}</simple> 
      </transform>
      <to uri="log:kaka"/>
      <filter>
          <simple>${body} < 457 && ${body} > 0</simple>
          <to uri="log:kaka"/>
      </filter>  
    </route>
</camelContext>
```

###### 示例运行

![](https://i.imgur.com/TmfYFLo.png)

#### 示例分析

simple语言用于对交换数据，bean的引用。也可以作为表达式或者谓语，对条件进行判断。simple节点多余filter，transform处理器中。

---

<span id="cm:property-placeholder"></span>
### 65.cm:property-placeholder

#### 概述 

用于在OSGi blueprint文件的上下文中用于访问在OSGi配置管理服务中定义的属性。

#### 示例

##### 配置文件
将配置文件放入databridge\deploy\etc文件夹下

![](https://i.imgur.com/kt69JiD.png)

![](https://i.imgur.com/Op1E7S7.png)

##### Blueprint DSL 配置

```xml
<blueprint xmlns="http://www.osgi.org/xmlns/blueprint/v1.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:cm="http://aries.apache.org/blueprint/xmlns/blueprint-cm/v1.0.0"
           xsi:schemaLocation="
           http://www.osgi.org/xmlns/blueprint/v1.0.0 http://www.osgi.org/xmlns/blueprint/v1.0.0/blueprint.xsd">
	<!--在OSGI环境中自定义属性-->	   
	<cm:property-placeholder id="mytest" persistent-id="mytest">
        <cm:default-properties>
			<cm:property name="test" value="测试cm:property-placeholder自定义属性"/>
        </cm:default-properties>
    </cm:property-placeholder>

	<!--从F:\WorkSoftware\databridge\databridge\deploy\etc文件夹下读取fxk.cfg文件-->
    <!--persistent-id必须和配置文件名相同-->
    <!--placeholder-prefix,placeholder-suffix可以指定任意字符，且不能重复（注意：在路由中获取默认为{{key}};在路由以外，如果指定placeholder-prefix,placeholder-suffix，则使用指定的前缀后缀。如果不指定，则默认的为${key}）-->
	<cm:property-placeholder id="fxk" persistent-id="fxk" placeholder-prefix="${{" placeholder-suffix="}}">
	</cm:property-placeholder>

    <!--从F:\WorkSoftware\databridge\databridge\deploy\etc文件夹下读取k3.cfg文件-->
	<cm:property-placeholder id="k3" persistent-id="k3" placeholder-prefix="${{{" placeholder-suffix="}}}"/>
	
    <!--将cm:property-placeholder加载到Properties组件中，必要的-->
	<bean id="properties" class="org.apache.camel.component.properties.PropertiesComponent">
		<property name="location" value="blueprint:k3,blueprint:fxk,blueprint:mytest"/>
	</bean>
	
	<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	  <route>
		 <from uri="timer://foo?period=20s"/> 
		 <setBody>
		     <constant>{{test}}</constant>
		 </setBody>
		 <to uri="class:com.shudi.databridge.test.DataSetProcessor"/>	  
         <setBody>
		     <constant>{{fxk.sql}}</constant>
		 </setBody>
         <to uri="jdbc:dataSource"/>	 
		 <to uri="class:com.shudi.databridge.test.DataSetProcessor"/>	  
      </route>	
		
	</camelContext>	
	 
	<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
		<property name="driverClassName" value="${{{driverClassName}}}"/>
		<property name="url" value="${{{url}}}"/>
		<property name="username" value="${{{username}}}"/>
		<property name="password" value="${{{password}}}"/>
	</bean>
</blueprint> 
```

##### 示例运行

![](https://i.imgur.com/pJSV2rC.png)

##### 示例分析

cm:property-placeholder提供了自定义配置属性和读取外部配置文件的功能。

---

<span id="endpoint"></span>
### 66.endpoint

#### 概述 

在上下文环境中配置端点URI,以便对端点进行管理。

#### 示例

##### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	<endpoint id="foo" uri="timer://foo?period=20s"/>
	<route>
	   <from uri="ref:foo"/> 
	   <setBody>
		    <constant>测试endpoint标签......</constant>
	   </setBody>
	   <to uri="class://com.shudi.databridge.test.DataSetProcessor"/>	  
    </route>		
</camelContext>
```

##### 示例分析

当路由端点过多而且冗长时，可以使用endpoint处理器进行管理。实现端点的重用，让路由线路更加简洁。

---

<span id="toF"></span>
### 67.toF

#### 概述

使用字符串格式将交换发送到一个端点,也就是说，端点URI字符串可以在C printf()函数的样式中嵌入替换。(注意：toF处理器在XML DSL中是不适用的,只能在Java DSL中使用)

#### 示例

##### Java DSL 配置

```java
@Test
public void Test1() throws Exception {
	CamelContext context = new DefaultCamelContext();
	context.start();
    //level为日志级别，maxChars为每行记录的字符数。
	context.addRoutes(new RouteBuilder() { 
		@Override
		public void configure() throws Exception {
			from("timer://foo?period=20s")
			.setBody(constant("测试处理器toF"))
			.toF("log:tofTest?level=WA%s&maxChars=%d", "RN",20)
			.to("log:tofTest");
		}
	  });
		Thread.sleep(8000000);
}
```

##### 示例运行

![](https://i.imgur.com/L4wPb9Y.png)

##### 示例分析

toF处理器运用了java.util.Formatter格式化输入，实现了对生产者端点参数的验证，转换，拼接，替换。
java.util.Formatter文档链接：[https://docs.oracle.com/javase/1.5.0/docs/api/java/util/Formatter.html](https://docs.oracle.com/javase/1.5.0/docs/api/java/util/Formatter.html)
java.util.Formatter使用示例链接：[http://blog.51cto.com/lavasoft/179081](http://blog.51cto.com/lavasoft/179081)

---

<span id="errorHandler"></span>
### 68.errorHandler

#### 概述 

errorHandler处理器提供了与onException处理器类似的功能，只是此机制不能区分不同的异常类型。errorHandler处理器是Apache Camel提供的原始异常处理机制，在执行onException子句之前可用。

#### 示例

##### Blueprint DSL 配置

```xml
<blueprint xmlns="http://www.osgi.org/xmlns/blueprint/v1.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:cm="http://aries.apache.org/blueprint/xmlns/blueprint-cm/v1.0.0"
           xsi:schemaLocation="
           http://www.osgi.org/xmlns/blueprint/v1.0.0 http://www.osgi.org/xmlns/blueprint/v1.0.0/blueprint.xsd">
	<!--调用异常IllegalArgumentException-->   
    <bean id="damn" class="java.lang.IllegalArgumentException"/>
	<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	   <!--id为名称，必要的。type为错误处理类型，可选择值有：死信通道-DeadLetterChannel。deadLetterUri为交换传入端点-->
	   <errorHandler id="dlc" type="DeadLetterChannel" deadLetterUri="class://com.shudi.databridge.test.DataSetProcessor"/>
       <!--errorHandlerRef为错误处理器的引用-->
	   <route errorHandlerRef="dlc">
		 <from uri="timer://foo?period=20s"/> 
		 <setBody>
		     <constant>今天</constant>
		 </setBody>
         <!--使用throwException处理器抛出异常-->
		 <throwException ref="damn"/>
		 <setBody>
		     <constant>明天</constant>
		 </setBody>
		 <to uri="class://com.shudi.databridge.test.DataSetProcessor"/>	  
       </route>	
	</camelContext>	
</blueprint> 
```

##### 示例运行

![](https://i.imgur.com/opABfjv.png)

##### 示例分析

errorHandler处理器也提供了捕获异常的机制，与onException不用的是errorHandler处理器将捕获所有的异常，并传入端点进行处理。
注意：errorHandler和onException不能同时使用。

---

<span id="tokenize"></span>
### 69.tokenize

#### 概述 

标记器语言是camel内核中的一种内置语言，最常用于Splitter EIP使用基于标记的策略来分割消息。标记器语言旨在使用指定的分隔符模式标记文本文档。 它也可以用来标记XML文档。 对于真正意义上的XML标记，推荐使用XMLTokenizer语言，因为它提供了一种专门用于XML文档的更快，更高效的标记。

#### 示例

##### Blueprint DSL 配置

```xml
<!--路由一-->
<route>  
    <from uri="timer://aaa?period=60000" />  
    <setHeader headerName="ids">
           <constant>java@php@c++</constant>
    </setHeader>   
    <split>  
        <!-- 把${in.header.ids} 以@符号分隔 -->  
        <tokenize token="@" headerName="ids" />  
        <to uri="class://com.shudi.databridge.test.DataSetProcessor" />  
    </split>  
</route>
<!--路由二-->  
<route>  
    <from uri="timer://aaa?period=60000" />  
    <setBody>
           <constant>string,int,boolean</constant>
    </setBody> 
    <split>  
        <!-- 分隔body，当body是字符串的时候 -->  
        <tokenize token="," />  
        <to uri="class://com.shudi.databridge.test.DataSetProcessor" />  
    </split>  
</route> 
```

##### 示例运行

路由一
![](https://i.imgur.com/gAzAXNL.png)

路由二
![](https://i.imgur.com/6OWFm9x.png)

##### 示例分析

tokenize常用于分解器中的分解策略。tokenize还有更多用法请参考源码ExpressionClause.class。

---

<span id="jxpath"></span>
### 70.jxpath

#### 概述

JXPath语言使您能够使用Apache Commons JXPath语言调用Java bean。 JXPath语言与XPath具有相似的语法，但不是从XML文档中选择元素或属性节点，而是在Java bean的对象图上调用方法。如果其中一个bean属性返回XML文档(dom/jdom实例)，则该路径的其余部分将被解释为XPath表达式，并用于从文档中提取XML节点。换句话说，JXPath语言提供了对象图导航和XML节点选择的混合。

#### 示例

##### Blueprint DSL 配置

```xml
<blueprint xmlns="http://www.osgi.org/xmlns/blueprint/v1.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:cm="http://aries.apache.org/blueprint/xmlns/blueprint-cm/v1.0.0"
           xsi:schemaLocation="
           http://www.osgi.org/xmlns/blueprint/v1.0.0 http://www.osgi.org/xmlns/blueprint/v1.0.0/blueprint.xsd">
		   
	<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 	
       <route>  
          <from uri="timer://aaa?period=60000" /> 
          <setHeader headerName="id">
	         <constant>123456</constant>
          </setHeader>	
          <setBody>
             <simple>${ref:obj}</simple> 
          </setBody> 
          <filter>
             <!--作为过滤条件,操作的为Exchange实例-->
             <jxpath>in/headers/id = '123456' and in/body/name = 'Tom'</jxpath>
             <to uri="class://com.shudi.databridge.test.DataSetProcessor"/>
          </filter>
        </route> 	
	</camelContext>	
    <!--实例bean-->
    <bean id="obj" class="com.shudi.databridge.test.BeanObj">
	      <property name="name" value="Tom"/>
		  <property name="password" value="999999"/>
	</bean>
</blueprint> 
```

##### 示例运行

![](https://i.imgur.com/FrWlQZ8.png) 

##### 示例分析

当jxpath表达式条件满足时，交换才能进入下一端点。jxpath操作的为Java bean,调用get方法获得值。当返回XML文档(dom/jdom实例)时，则该路径的其余部分将被解释为XPath表达式。