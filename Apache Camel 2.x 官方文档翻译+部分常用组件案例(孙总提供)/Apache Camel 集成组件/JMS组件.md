# JMS组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-27*
*相关连接:[http://camel.apache.org/jms.html](http://camel.apache.org/jms.html)*

## 概述

JMS即Java消息服务（Java Message Service）应用程序接口，是一个Java平台中关于面向消息中间件（MOM）的API，用于在两个应用程序之间，或分布式系统中发送消息，进行异步通信。
JMS组件允许将消息发送到(或使用)JMS队列或主题。它使用Spring的JMS支持来进行声明性事务，包括用于发送的Spring的JmsTemplate和用于消费的MessageListenerContainer。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-jms</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
jms:[queue:|topic:]destinationName[?options]
```
queue：队列模式（消息只能供一个消费者消费）
topic：主题模式(消息可以提供给多个消费者共同消费)
其中 destinationName 是 jms 队列或主题名称

## JMS消息传递模式

![](https://i.imgur.com/NgBVPbM.png)

## URI参数

### Common Options

名称|默认值|描述
----|----|----
clientId|null|设置要使用的JMS客户端ID。请注意，如果指定了这个值，那么它必须是惟一的，并且只能由一个JMS连接实例使用。
concurrentConsumers|1|指定并发消费者的默认数目。
disableReplyTo|false|当为true时,生产者的行为将与InOnly交换一样,而JMSReplyTo标头将被发送出去, 并且不会像InOnly那样被禁止。与 InOnly一样, 生产者不会等待答复。具有此标志的使用者的行为将与InOnly类似。此功能可用于将InOut请求桥接到另一个队列, 以便其他队列上的路由将直接将响应发送回原始JMSReplyTo .
durableSubscriptionName|null|持久订户名称，用于指定持久的主题订阅。使用此选项clientId选项也必须配置。
maxConcurrentConsumers|1|指定并发消费者的最大数量。
maxMessagesPerTask|-1|任务在终止后可以接收到的消息的数量。默认值 -1是无限制的。
preserveMessageQos|false|如果要使用在消息中指定的 qos 设置来发送消息, 而不是 JMS 端点上的 qos 设置, 则设置为 true。将考虑以下标头:JMSPriority;JMSDeliveryMode;JMSExpiration。如果未提供, 则骆驼将退回以使用端点中的值。因此, 当使用此选项时, 标题将覆盖端点中的值。相比之下, explicitQosEnabled 选项将只使用在端点上设置的选项, 而不是邮件头中的值。
replyTo|null|提供显式的ReplyTo目标, 它重写消息的任何传入值 Message.getJMSReplyTo()。如果您在 jms 上执行请求回复, 则确保阅读下面的请求-答复jms进一步了解更多详细信息,以及replyToType选项。
replyToConcurrentConsumers|1|camel 2.16: 指定在对 JMS 执行请求/答复时并发消费者的默认数量。
replyToMaxConcurrentConsumers|1|camel 2.16: 指定在对 JMS 执行请求/应答时并发消费者的最大数量。
replyToOnTimeoutMaxConcurrentConsumers|1|Camel 2.17.2：指定在使用通过JMS请求/回复时发生超时时继续路由的最大并发使用者数。
replyToOverride|null|Camel 2.15:在JMS消息中提供一个明确的ReplyTo目标, 它覆盖ReplyTo的设置。如果要将消息转发到远程队列并从ReplyTo目标接收应答消息, 则此项非常有用。
replyToType|null|驼峰2.9：允许明确指定在通过JMS请求/回复时用于replyTo队列的策略类型。 可能的值是：Temporary;Shared;Exclusive。默认情况下, 骆驼将使用Temporary队列。但是, 如果已配置replyTo , 则默认情况下将使用Shared 。此选项允许您使用独占队列而不是共享队列。
requestTimeout|2000|仅生产者:在使用 InOut Exchange 模式(以毫秒为单位) 时等待答复的超时。从camel 2.13/2.12.3: 您可以包括标头 CamelJmsRequestTimeout 以覆盖此端点配置的超时值, 因此每个消息都有单个超时值。
selector|null|设置JMS选择器，该选择器是用于在代理中过滤消息的SQL 92谓词。 您可能必须将特殊字符（如“=”）编码为％3D。camel 2.3.0:在CamelConsumerTemplate中不支持此选项.
timeToLive|null|在发送消息时，指定消息的生存时间(以毫秒为单位)。
transacted|false|指定是否使用 InOnly Exchange 模式来发送/接收消息的事务处理模式。
testConnectionOnStartup|false|camel 2.1:指定是否在启动时测试连接。这可确保当骆驼启动时, 所有jms消费者都具有与jms代理的有效连接。如果无法授予连接, 则骆驼在启动时抛出异常。这可确保不以失败的连接启动骆驼。从camel 2.8: 也测试了JMS生产者。

### Advanced Options

名称|默认值|描述
----|----|----
acceptMessagesWhileStopping|false|指定消费者在停止时是否接受消息。如果您在运行时启动和停止JMS路由，则仍然可以考虑启用此选项，而队列中仍有消息排队。 如果此选项为false，并且停止JMS路由，则可能会拒绝消息，并且JMS代理将不得不尝试重新交付，而这又可能会被拒绝，并且最终可能会将消息移动到死信队列。 为了避免这种情况，建议将此选项设置为true。
acknowledgementModeName|AUTO_ACKNOWLEDGE|JMS确认名称，它是下列之一：SESSION_TRANSACTED;CLIENT_ACKNOWLEDGE;AUTO_ACKNOWLEDGE;DUPS_OK_ACKNOWLEDGE。
acknowledgementMode|-1|定义为整数的 JMS 确认模式。允许您将特定于供应商的扩展设置为确认模式。对于常规模式, 最好改用 acknowledgementModeName 。
allowNullBody|true|camel 2.9.3/2.10.1:是否允许发送没有正文的消息。如果此选项为false ,并且消息正文为null, 则会引发 JMSException 。
allowReplyManagerQuickStop|false|响应管理器中用于请求-回复消息传递的DefaultMessageListenerContainer是否允许DefaultMessageListenerContainer.runningAllowed标志快速停止，以防链接JmsConfigurationisAcceptMessagesWhileStopping（）被启用，并且当前正在停止CamelContext。 在常规JMS使用者中默认启用此快速停止功能，但要启用回复管理器，您必须启用此标志。
alwaysCopyMessage|false|如果为true，那么Camel将始终在将消息传递给生产者进行发送时生成消息的JMS消息副本。 在某些情况下需要复制消息，例如设置了replyToDestinationSelectorName。如果设置了replyToDestinationSelectorName,则camel将设置 alwaysCopyMessage=true。
asyncConsumer|false|camel 2.9：JmsConsumer是否异步处理Exchange。 如果启用，则JmsConsumer可以从JMS队列中拾取下一条消息，而之前的消息正在异步处理（由异步路由引擎）。 这意味着邮件可能不是100％严格按照处理。 如果禁用（默认），则在JmsConsumer从JMS队列中拾取下一条消息之前，将完全处理Exchange。注意: 如果已启用事务处理, 则 transacted asyncConsumer=true 不会异步运行, 因为必须同步执行交易记录 (camel 3.0可能支持异步事务)。
asyncStartListener|false|camel 2.10:启动路由时是否异步启动JmsConsumer消息监听器。例如, 如果JmsConsumer无法获得到远程JMS代理的连接, 则在重试和/或故障转移时可能会阻塞。这将导致camel在启动路径时阻塞。通过将此选项设置为true, 您将允许路由启动, 而 JmsConsumer使用异步模式下的专用线程连接到JMS代理。如果使用此选项, 则请注意, 如果无法建立连接, 则会在WARN级别记录异常, 并且使用者将无法接收消息。然后可以重新启动该路由以重试。
asyncStopListener|false|camel 2.10:停止路由时是否异步停止JmsConsumer消息监听器。
autoStartup|true|指定消费者容器是否应该自动启动。
cacheLevel|暂无|根据基础JMS资源的ID设置缓存级别。有关更多详细信息, 请参见cacheLevelName选项。
cacheLevelName|CACHE_AUTO|根据基础JMS资源的ID设置缓存级别。有效值为:CACHE_NONE;CACHE_CONNECTION;CACHE_SESSION;CACHE_CONSUMER;CACHE_AUTO。从camel 2.8之后: 默认值为 CACHE_AUTO。对于camel 2.7.1和更旧版本默认值为CACHE_CONSUMER。
connectionFactory|null|用于listenerConnectionFactory和templateConnectionFactory的默认JMS连接工厂（如果没有指定）。
consumerType|Default|要使用的 Spring JMS 监听器类型。有效值为: Simple,Default或Custom。此选项在camel 2.7 和2.8暂时删除, 但在camel 2.9中恢复。
defaultTaskExecutorType|SimpleAsync|Camel 2.10.4：指定在DefaultMessageListenerContainer中为消费者端点和生产者端点的ReplyTo使用者指定默认的TaskExecutor类型。 可能的值：SimpleAsync（使用Spring的SimpleAsyncTaskExecutor）或ThreadPool（使用具有最佳值的Spring的ThreadPoolTaskExecutor - 类似缓存的线程池）。如果未设置, 则默认为以前的行为, 即使用缓存的线程池来为使用者终结点和 SimpleAsync 提供答复消费者。建议使用ThreadPool来减少弹性配置中的 "线程垃圾", 同时动态增加和减少并行用户。
deliveryMode|null|camel 2.12.2/2.13:指定要使用的传递模式。可能值是由 javax.jms.DeliveryMode 定义的.
deliveryPersistent|true|指定默认情况下是否使用持久传递。
destination|null|指定要在此端点上使用的JMS目标对象。
destinationName|null|指定要在此端点上使用的JMS目标名称。
destinationResolver|null|可插入的 org.springframework.jms.support.destination.DestinationResolver , 允许您使用自己的冲突解决程序 (例如, 在JNDI注册表中查找真正的目标)。
disableTimeToLive|false|骆驼 2.8:使用此选项可强制禁用生存时间。例如, 当您对 JMS 执行请求/应答时, camel默认情况下将使用 requestTimeout值作为要在发送的消息上生存的时间。问题是发件人和接收器系统必须同步时钟, 所以它们是同步的。这并不总是那么容易存档。因此, 您可以使用disableTimeToLive=true来不在已发送邮件上设置实时值的时间。然后消息将不会在接收系统上过期。
eagerLoadingOfProperties|false|允许在收到消息后立即加载jms属性, 这通常是低效的, 因为可能不需要jms属性。但是, 此功能有时会及早捕获底层jms提供程序的任何问题以及jms属性的使用。此功能还可用于测试目的, 以确保能够正确理解和处理JMS属性。
errorHandler|null|camel 2.8.2, 2.9:指定在处理Message时引发的任何捕获异常的情况下调用org.springframework.util.ErrorHandler。默认情况下, 如果未配置 errorHandler , 则这些异常将记录在WARN级别。从camel 2.9.1: 您可以配置日志记录级别, 以及是否应使用以下两个选项记录堆栈跟踪。这使得配置起来比必须编写自定义errorHandler更容易。
errorHandlerLoggingLevel|WARN|camel 2.9.1:配置errorHandler将记录捕获异常的日志级别。
errorHandlerLogStackTrace|true|camel 2.9.1:控制是否应由默认的errorHandler记录堆栈。
exceptionListener|null|指定JMS异常侦听器，该侦听器将被通知任何潜在的JMS异常。
explicitQosEnabled|false|设置是否在发送消息时使用deliveryMode，priority或timeToLive服务质量。 这个选项基于Spring的JmsTemplate。 deliveryMode，priority和timeToLive选项应用于当前端点。 这与preserveMessageQos选项相反，该选项以消息粒度进行操作，专门从Camel In消息头中读取QoS属性。
exposeListenerSession|true|指定在使用消息时是否应公开监听器会话。
forceSendOriginalMessage|false|camel 2.7：当使用mapJmsMessage = false时，如果您在路由期间触摸标题（获取或设置），骆驼将创建一个新的JMS消息发送到新的JMS目的地。 将此选项设置为true可强制Camel发送接收到的原始JMS消息。
idleConsumerLimit|1|camel 2.8.2, 2.9: 指定允许在任何给定时间空闲的消费者数量的限制。
idleTaskExecutionLimit|1|指定接收任务的空闲执行限制，在其执行中未收到任何消息。 如果达到此限制，任务将关闭并保留接收到其他正在执行的任务（在动态调度的情况下，请参阅maxConcurrentConsumers设置）。
includeSentJMSMessageID|false|camel 2.10.3:只有在使用 InOnly发送到 JMS 目标时才适用,启用此选项将使camel Exchange与jms 客户端在将消息发送到jms目标时使用的实际JMSMessageID丰富起来。
includeAllJMSXProperties|false|camel 2.11.2/2.12:在从JMS映射到骆驼消息时是否包括所有JMSXxxx 属性。如果设置为true属性 (如 JMSXAppID和JMSXUserID 等), 将包括在内。注意:如果您使用的是自定义headerFilterStrategy, 则此选项不适用。
jmsKeyFormatStrategy|default|可插入的策略, 用于对 jms 密钥进行编码和解码, 以便它们能够符合 jms 规范。default:安全地排列点和连字符,'.'和'-'。passthrough:适用于任何允许包含非法字符的JMS标头键的JMS代理。注意: 可选的, 自定义实现可以提供 org.apache.camel.component.jms.JmsKeyFormatStrategy , 并使用#符号来引用。
jmsMessageType|null|允许您强制使用特定的 javax.jms.Message实现来发送jms消息。可能的值为:Bytes;Map;Object;Stream;Text。默认情况下, camel确定在正文类型In用于的JMS消息类型。此选项将覆盖默认行为。
jmsOperations|null|允许您使用您自己的org.springframework.jms.core.JmsOperations接口的实现。默认情况下, camel使用 JmsTemplate。可用于测试目的, 但不像 spring API 文档中所述的那样使用。
lazyCreateTransactionManager|true|如果true，骆驼将创建 JmsTransactionManager，如果在选项transacted=true时没有执行transactionManager。
listenerConnectionFactory|null|用于消费消息的JMS连接工厂。
mapJmsMessage|true|指定骆驼是否应自动将接收到的 JMS 消息映射到适当的有效负载类型, 如 javax.jms.TextMessage 到 java.lang.String 等有关消息类型映射工作方式的详细信息, 请参见下面的内容。
maximumBrowseSize|-1|在使用浏览或 JMX API 浏览端点时, 限制最多获取的消息数。
messageConverter|null|若要使用自定义Spring org.springframework.jms.support.converter.MessageConverter,因此，您可以控制如何映射to/from到javax.jms.Message。
messageIdEnabled|true|在发送时，指定是否应该添加消息id。
messageListenerContainerFactoryRef|null|camel 2.10.2:用于确定用于使用消息的 org.springframework.jms.listener.AbstractMessageListenerContainer的MessageListenerContainerFactory的注册表ID。设置此项将自动将 consumerType 设置为Custom。
messageTimestampEnabled|true|指定是否应在发送消息时默认启用时间。
password|null|连接器工厂的密码。
priority|4|大于1的值在发送时指定消息优先级(0是最低的优先级，9是最高的)。为了使该选项具有任何效果, 还必须启用 explicitQosEnabled选项。
pubSubNoLocal|false|指定是否禁止由其自身连接发布的消息的传递。
receiveTimeout|1000|接收消息的超时(以毫秒为单位)。
recoveryInterval|5000|指定恢复尝试之间的间隔, 例如, 在刷新连接时, 以毫秒为单位。默认值为5000ms。
replyToSameDestinationAllowed|false|camel 2.16:仅消费者:是否允许JMS使用者将回复消息发送到使用者从中消耗的同一目的地。这样可以防止无休止的循环, 将相同的消息发送回自身。
replyToCacheLevelName|CACHE_CONSUMER|camel 2.9.1:在对 JMS 执行请求/应答时, 按名称为答复使用者设置缓存级别。此选项仅在使用固定答复队列 (非临时) 时适用。默认情况下, "camel" 将使用: CACHE_CONSUMER 用于独占或共享 w/replyToSelectorName 和 CACHE_SESSION 共享, 没有 replyToSelectorName。某些 JMS 代理 (如 IBM WebSphere) 可能要求将此参数设置为 CACHE_NONE 才能正常工作。注意:值 CACHE_NONE 不能与临时队列一起使用。必须使用更高的值, 如 CACHE_CONSUMER 或 CACHE_SESSION。
replyToDestinationSelectorName|null|使用固定名称设置JMS选择器, 以便您可以在使用共享队列时从其他人那里筛选出自己的答复 (即, 如果您没有使用临时答复队列)。
replyToDeliveryPersistent|true|指定默认情况下是否对答复使用永久性传递。
requestTimeoutCheckerInterval|1000|camel 2.9.2:配置在对 JMS 执行请求/应答时, camel应检查超时Exchange的频率。默认情况下, 每秒进行一次camel检查。但是, 如果在超时时必须做出更快的反应, 则可以降低此间隔, 以便更频繁地检查。超时由选项 requestTimeout 确定。
subscriptionDurable|false|已弃用:默认情况下启用, 如果指定 durableSubscriptionName 和 clientId 。
taskExecutor|null|允许您为使用消息指定自定义任务执行器。
taskExecutorSpring2|null|camel 2.6:用camel使用spring 2.x。允许您指定用于消费消息的自定义任务执行器。
templateConnectionFactory|null|用于发送消息的JMS连接工厂。
transactedInOut|false|已弃用：指定是否使用事务处理模式来使用 InOut Exchange 模式来发送邮件。仅适用于生产者端点。
transactionManager|null|要使用的 Spring 事务管理器。
transactionName|"JmsConsumer[destinationName]"|要使用的事务的名称。
transactionTimeout|null|如果使用事务模式, 则事务的超时值 (以秒为单位)。
transferException|false|如果启用并且您使用的是请求答复消息 (InOut), 而用户端出现Exchange失败, 则导致的Exception将作为发送回响应javax.jms.ObjectMessage。如果客户端是camel, 则返回的Exception异常。
transferExchange|false|您可以通过传递而不是仅仅身体和头部来传输交换。
username|null|连接器工厂的用户名。
useMessageIDAsCorrelationID|false|指定JMSMessageID是否应始终作为JMSCorrelationID用于InOut消息。
useVersion102|false|已弃用：指定是否应使用低版本的JMS API。

## JMS 和骆驼之间的消息映射

**骆驼自动在 javax.jms.Message 和 org.apache.camel.Message之间映射邮件。发送 jms 消息时, 骆驼将消息正文转换为以下 jms 消息类型:**

Body类型|JMS消息|注释
----|----|----
byte[]|javax.jms.BytesMessage|暂无
java.io.File|javax.jms.BytesMessage|暂无
java.io.InputStream|javax.jms.BytesMessage|暂无
java.io.Reader|javax.jms.BytesMessage|暂无
java.io.Serializable|javax.jms.ObjectMessage|暂无
java.nio.ByteBuffer|javax.jms.BytesMessage|暂无
Map|javax.jms.MapMessage|暂无
org.w3c.dom.Node|javax.jms.TextMessage|DOM 将转换为String
String|javax.jms.TextMessage|暂无

**当接收到 jms 消息时, 骆驼将 jms 消息转换为以下正文类型:**

JMS消息|Body类型
----|----
javax.jms.BytesMessage|byte[]
javax.jms.MapMessage|Map<String, Object>
javax.jms.ObjectMessage|Object
javax.jms.TextMessage|String

## 示例

在示例中使用JMS组件，必须要引用jms的实现(因为JMS只是一个标准的API，制定了数据结构和交互流程)。在这里我们使用activemq消息队列服务，是面向消息中间件（MOM）的最终实现，是真正的服务提供者。

使用activemq需添加依赖：
```xml
<dependency>
	<groupId>org.apache.activemq</groupId>
	<artifactId>activemq-all</artifactId>
	<version>5.13.4</version>
</dependency>
<dependency>
	<groupId>org.apache.activemq</groupId>
	<artifactId>activemq-camel</artifactId>
	<version>5.14.5</version>
</dependency>
```

### 安装ActiveMQ

要使用ActiveMQ中间件，首先要将ActiveMQ部署到本地。
下面是安装教程链接：
[http://blog.csdn.net/jiuqiyuliang/article/details/47160259](http://blog.csdn.net/jiuqiyuliang/article/details/47160259)

### Blueprint DSL 配置

```xml
<!--jms组件配置-->
<bean id="jms" class="org.apache.camel.component.jms.JmsComponent">
      <!--连接工厂：使用ActiveMQ代理-->
      <property name="connectionFactory">
           <bean class="org.apache.activemq.ActiveMQConnectionFactory">
                <!--brokerURL为传输模式，ActiveMQ具有各种传输模式，详情链接：http://activemq.apache.org/configuring-transports.html-->
	            <property name="brokerURL" value="tcp://localhost:61616"/>
           </bean>
      </property> 
</bean>
	
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	
	  <route>
           <!--定时器触发流程-->
           <from uri="timer://foo?delay=20s&amp;period=10s"/>
           <!--添加正文数据-->  	
		   <to uri="class:com.shudi.databridge.test.BeanProcessor"/>
           <!--连接jms生产者端点-->
           <to uri="jms:queue:Hunter.Test"/>
		   <log message="fang.${body}"/>
	  </route> 
		
	  <route>
           <from uri="jms:queue:Hunter.Test"/> 
           <!--读取打印队列数据--> 	
		   <to uri="class:com.shudi.databridge.test.DataSetProcessor"/>
		   <log message="fang.${body}"/>
	  </route> 
		
	</camelContext>	
```

### 示例运行

![](https://i.imgur.com/jgepbfF.png)

### 示例分析
JMS组件实现了异步消息传递，并且可以熟练的运用到集成中。从上例可以看出JMS组件和ActiveMQ组件做着相同的工作。
JMS是消息服务的标准API，而ActiveMQ是基于jms的最终实现，camel对两者都有对应的组件实现。





