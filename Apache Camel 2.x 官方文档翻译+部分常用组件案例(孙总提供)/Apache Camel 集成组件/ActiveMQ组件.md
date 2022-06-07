# ActiveMQ组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-10*
*相关连接:[http://camel.apache.org/activemq.html](http://camel.apache.org/activemq.html)*

## 概述
ActiveMQ 组件允许将消息发送到jms队列, 或使用Apache ActiveMQ从 jms 队列或主题中消耗的主题或消息。此组件基于jms 组件, 并使用 spring 的 JmsTemplate 发送和 MessageListenerContainer 以进行声明性事务, 并使用它的 jms 支持。JMS组件中的所有选项也适用于此组件。

若要使用此组件，需添加依赖activemq-camel。

## URI

```
activemq:[queue:|topic:]destinationName 
```

queue：队列模式（消息只能供一个消费者消费）
topic：主题模式(消息可以提供给多个消费者共同消费)
其中 destinationName 是 ActiveMQ 队列或主题名称

## URI参数

### Common Options

选项|默认值|描述
----|----|----
clientId|null|设置要使用的 JMS 客户端 ID。请注意, 此值 (如果指定) 必须是唯一的, 并且只能由单个 JMS 连接实例使用。
concurrentConsumers|1|指定并发使用者的默认数目。
disableReplyTo|false|如果为true，生产者的行为将与 InOnly 交换一样,除了jmsreplyto header被发送出去而不是被禁止的情况下以外。
durableSubscriptionName|null|用于指定持久性主题订阅的持久订户名称。也必须对 clientId 选项进行配置。
maxConcurrentConsumers|1|指定并发使用者的最大数量。
maxMessagesPerTask|-1|任务在终止后可以接收的消息数。默认值 -1是无限制的。
preserveMessageQos|false|如果要使用在消息中指定的 qos 设置来发送消息, 而不是 JMS 端点上的 qos 设置, 则设置为 true。将考虑以下标头：JMSPriority，JMSDeliveryMode，JMSExpiration。
replyTo|null|提供显式的ReplyTo目标, 它覆盖消息的任何传入值 Message.getJMSReplyTo()。
replyToConcurrentConsumers|1|指定在对 JMS 执行请求/应答时并发使用者的默认数量。
replyToMaxConcurrentConsumers|1|指定在对 JMS 执行请求/答复时并发使用者的最大数量。
replyToOnTimeoutMaxConcurrentConsumers|1|指定在使用 JMS 的请求/答复时发生超时时继续路由的最大并发用户数。
replyToOverride|null|在 JMS 消息中提供一个显式的ReplyTo目标, 它将重写ReplyTo的设置。如果要将邮件转发到远程队列, 并从ReplyTo目标接收应答消息, 则该消息非常有用。
replyToType|null|允许在对 JMS 执行请求/答复时, 显式指定用于replyTo队列的策略类型。可能的值为:Temporary，Shared，Exclusive。默认情况下, 骆驼将使用Temporary队列。
requestTimeout|20000|仅生产者:在使用 InOut Exchange 模式(以毫秒为单位) 时等待答复的超时。
selector|null|设置JMS选择器，它是用于在代理中过滤消息的SQL。
timeToLive|null|发送消息时, 指定消息的生存时间 (以毫秒为单位)。
transacted|false|指定是否使用 InOnly Exchange 模式来发送/接收消息的事务处理模式。
testConnectionOnStartup|false|指定是否在启动时测试连接。这可确保当骆驼启动时, 所有 jms 使用者都具有与 jms 代理的有效连接。如果无法授予连接, 则骆驼在启动时抛出异常。这可确保不以失败的连接启动骆驼。

### Advanced Options

选项|默认值|描述
----|----|----
acceptMessagesWhileStopping|false|指定使用者在停止时是否接受消息。
acknowledgementModeName|AUTO_ACKNOWLEDGE|JMS 确认名称, 它是以下内容之一:SESSION_TRANSACTED，CLIENT_ACKNOWLEDGE，AUTO_ACKNOWLEDGE，DUPS_OK_ACKNOWLEDGE。
acknowledgementMode|-1|定义为整数的 JMS 确认模式。允许您将特定于供应商的扩展设置为确认模式。对于常规模式, 最好改用 acknowledgementModeName 。
allowNullBody|true|是否允许发送没有正文的消息。如果此选项为false , 并且消息正文为 null, 则会引发 JMSException 。
allowReplyManagerQuickStop|false|DefaultMessageListenerContainer是否用于请求-应答消息的回复管理允许DefaultMessageListenerContainer.runningAllowed标记快速停止,以防链接JmsConfigurationisAcceptMessagesWhileStopping()启用和CamelContext被停止。在常规JMS使用者中默认启用这种快速停止功能，但是为了启用应答管理器，您必须启用此标志。
alwaysCopyMessage|false|如果为 true, 则在将消息传递给生产者进行发送时, 骆驼总是会将其作为 JMS 消息的副本。
asyncConsumer|false|JmsConsumer 是否处理Exchange 异步。如果启用, 则 JmsConsumer 可能会从 JMS 队列中拾取下一条消息, 而前一条消息将异步处理 (通过异步路由引擎)。
asyncStartListener|false|启动路由时是否异步启动 JmsConsumer 消息监听器。
asyncStopListener|false|停止路由时是否异步停止 JmsConsumer 消息监听器。
autoStartup|true|指定使用者容器是否应启动。
cacheLevel|暂无|根据基础 JMS 资源的 ID 设置缓存级别。有关更多详细信息, 请参见 cacheLevelName 选项。
cacheLevelName|CACHE_AUTO|根据基础 JMS 资源的名称设置缓存级别。有效值为:CACHE_NONE，CACHE_CONNECTION，CACHE_SESSION，CACHE_CONSUMER，CACHE_AUTO。
connectionFactory|null|用于 listenerConnectionFactory 和 templateConnectionFactory的默认 JMS 连接工厂 (如果两者都未指定)。
consumerType|Default|要使用的 Spring JMS 侦听器类型。有效值为: Simple,Default或Custom。
defaultTaskExecutorType|SimpleAsync|指定要在 DefaultMessageListenerContainer中使用的默认 TaskExecutor 类型, 以用于用户终结点和制造商终结点的ReplyTo用户。可能的值: SimpleAsync (使用 spring 的SimpleAsyncTaskExecutor) 或ThreadPool (使用具有最佳值的 spring ThreadPoolTaskExecutor -缓存的threadpool线程)。
deliveryMode|null|指定要使用的传递模式。可能值是由 javax.jms.DeliveryMode 定义的。
deliveryPersistent|true|指定默认情况下是否使用持久传递。
destination|null|指定要在此端点上使用的 JMS 目标对象。
destinationName|null|指定要在此端点上使用的 JMS 目标名称。
destinationResolver|null|可插入的 org.springframework.jms.support.destination.DestinationResolver , 允许您使用自己的冲突解决程序 (例如, 在 JNDI 注册表中查找真正的目标)。
disableTimeToLive|false|使用此选项可强制禁用生存时间。
eagerLoadingOfProperties|false|在收到消息后立即启用对 jms 属性的急切加载, 这通常是低效的, 因为可能不需要 jms 属性。但是, 此功能有时可以及早捕获底层 jms 提供程序的任何问题以及 jms 属性的使用。此功能还可用于测试目的, 以确保能够正确理解和处理 JMS 属性。
errorHandler|null|指定在处理Message时引发的任何捕获异常的情况下, 调用 org.springframework.util.ErrorHandler。默认情况下, 如果未配置 errorHandler , 则这些异常将记录在WARN级别。
errorHandlerLoggingLevel|WARN|配置 errorHandler 将记录捕获异常的日志级别。
errorHandlerLogStackTrace|true|控制是否应由默认的 errorHandler 记录堆栈。
exceptionListener|null|指定JMS异常侦听器，该侦听器将被通知任何潜在的JMS异常。
explicitQosEnabled|false|如果发送消息时应该使用服务的传递模式、优先级或时间,则设置该属性。此选项基于 Spring 的 JmsTemplate。 deliveryMode、priority和 timeToLive 选项应用于当前端点。
exposeListenerSession|true|指定在使用消息时是否应公开监听器会话。
forceSendOriginalMessage|false|当使用 mapJmsMessage=false 时,如果您在途中碰到headers(get或set), 则将创建一个新的 jms 消息以发送到新的 jms 目标。将此选项设置为 true 将强制骆驼发送收到的原始 JMS 消息。
idleConsumerLimit|1|指定允许在任何给定时间空闲的使用者数量的限制。
idleTaskExecutionLimit|1|指定一个接收任务的空闲执行的限制，而不是在执行过程中接收到任何消息。
includeSentJMSMessageID|false|只有在使用 InOnly发送到 JMS 目标时才适用,启用此选项将使骆驼Exchange与 jms 客户端在将消息发送到 jms 目标时使用的实际 JMSMessageID 丰富起来。
includeAllJMSXProperties|false|在从 JMS 映射到骆驼消息时是否包括所有 JMSXxxx 属性。如果设置为true属性 (如 JMSXAppID和 JMSXUserID 等), 将包括在内。注意:如果您使用的是自定义 headerFilterStrategy , 则此选项不适用。
jmsKeyFormatStrategy|default|用于对 jms 密钥进行编码和解码的可插拔策略, 以便它们可以与 jms 规范兼容。default--安全地封送点和连字符, '.'和'-';passthrough--适合用于任何JMS代理，它可以容忍包含非法字符的JMS header keys。
jmsMessageType|null|允许您强制使用特定的 javax.jms.Message 实现来发送 jms 消息。可能的值为:Bytes，Map，Object，Stream，Text。默认情况下, 骆驼确定在正文类型In用于的 JMS 消息类型。此选项将覆盖默认行为。
jmsOperations|null|允许您使用您自己的 org.springframework.jms.core.JmsOperations 接口的实现。默认情况下, 骆驼使用 JmsTemplate 。
lazyCreateTransactionManager|true|如果true，骆驼将创建JmsTransactionManager，在选项事务 transacted=true 时没有 transactionManager 时注入。
listenerConnectionFactory|null|用于使用消息的 JMS 连接工厂。
mapJmsMessage|true|指定骆驼是否应自动将接收到的 JMS 消息映射到一个适当的有效负载类型。
maximumBrowseSize|-1|当使用浏览或 JMX API 浏览端点时, 最多限制获取的消息数。
messageConverter|null|使用自定义 Spring org.springframework.jms.support.converter.MessageConverter。
messageIdEnabled|true|发送时, 指定是否应添加消息 id。
messageListenerContainerFactoryRef|null|用于确定用于使用消息的 org.springframework.jms.listener.AbstractMessageListenerContainer 的 MessageListenerContainerFactory 的注册表 ID。设置此项将自动将 consumerType 设置为Custom。
messageTimestampEnabled|true|指定是否在发送消息时默认启用时间。
password|null|连接器工厂的密码。
priority|4|大于 1 的值在发送时指定消息优先级 (其中 0 是最低优先级, 9 是最高的)。为了使此选项具有任何效果, 还必须启用 explicitQosEnabled 选项。
pubSubNoLocal|false|指定是否禁止传递由其自己的连接发布的消息。
receiveTimeout|1000|接收消息的超时 (以毫秒为单位)。
recoveryInterval|5000|指定恢复尝试之间的间隔, 例如, 在刷新连接时, 以毫秒为单位。默认值为5000 ms。
replyToSameDestinationAllowed|false|是否允许JMS使用者将应答消息发送到消费者正在使用的同一目的地。这样可以防止无休止的循环, 将相同的消息发送回自身。
replyToCacheLevelName|CACHE_CONSUMER|在通过JMS进行请求/应答时，为应答使用者设置缓存级别。此选项仅在使用固定答复队列 (非临时) 时适用。默认情况下, "骆驼" 将使用:在默认情况下，骆驼将会使用:cacheconsumer用于独家或共享的w/replyToSelectorName和cachesession，而不使用replyToSelectorName。
replyToDestinationSelectorName|null|使用固定名称设置 JMS 选择器, 以便您可以在使用共享队列时从其他人那里筛选出自己的答复。
replyToDeliveryPersistent|true|指定默认情况下是否对答复使用永久性传递。
requestTimeoutCheckerInterval|1000|配置在通过JMS进行请求/应答时，骆驼应该检查超时的频率。默认情况下, 每秒进行一次骆驼检查。但是, 如果在超时时必须做出更快的反应, 则可以降低此间隔, 以便更频繁地检查。超时由选项 requestTimeout 确定.
subscriptionDurable|false|已废弃：默认情况下启用, 如果指定 durableSubscriptionName 和 clientId 。
taskExecutor|null|允许您指定用于消费消息的自定义任务执行器。
taskExecutorSpring2|null|骆驼在使用Spring 2.x时，许您指定用于消费消息的自定义任务执行器。
templateConnectionFactory|null|用于发送消息的 JMS 连接工厂。
transactedInOut|false|已废弃：指定是否使用事务处理模式来使用 InOut Exchange 模式来发送邮件。
transactionManager|null|要使用的 Spring 事务管理器。
transactionName|"JmsConsumer[destinationName]"|要使用的事务的名称。
transactionTimeout|null|如果使用事务模式，事务的超时值(以秒为单位)。
transferException|false|如果启用，并且您使用的是请求答复消息 (InOut), 而用户端出现Exchange失败, 则导致的Exception将作为发送回响应 javax.jms.ObjectMessage，如果客户端是驼峰，则返回的异常会被重新抛出。
transferExchange|false|您可以通过导线转移交换, 而不只是正文和标题。
transferFault|false|如果启用, 并且您使用的是请求应答消息 (InOut), 并且用户端的 SOAP 错误 (不是异常) 失败, 则 org.apache.camel.Message.isFault() 上的错误标志将被发送在响应中作为 JMS 标头返回, 并使用键 JmsConstants.JMS_TRANSFER_FAULT。如果客户端是骆驼, 则返回的错误标志将在组织 org.apache.camel.Message.setFault(boolean) 中设置。
username|null|连接器工厂的用户名。
useMessageIDAsCorrelationID|false|指定 JMSMessageID 是否应始终作为 JMSCorrelationID 用于 InOut 消息。
useVersion102|false|已废弃：指定是否应使用旧的 JMS API。

## JMS 和骆驼之间的消息映射

**骆驼自动在javax.jms.Message 和 org.apache.camel.Message之间映射邮件。发送jms消息时,骆驼将消息正文转换为以下jms消息类型**

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

**当接收到 jms消息时,骆驼将jms消息转换为以下正文类型**

JMS消息|Body类型
----|----
javax.jms.BytesMessage|byte[]
javax.jms.MapMessage|Map<String,Object>
javax.jms.ObjectMessage|Object
javax.jms.TextMessage|String


## 示例

### 安装ActiveMQ

要使用ActiveMQ中间件，首先要将ActiveMQ部署到本地。
下面是安装教程链接：
[http://blog.csdn.net/jiuqiyuliang/article/details/47160259](http://blog.csdn.net/jiuqiyuliang/article/details/47160259)

### Blueprint DSL 配置

```xml
<blueprint xmlns="http://www.osgi.org/xmlns/blueprint/v1.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:cm="http://aries.apache.org/blueprint/xmlns/blueprint-cm/v1.0.0"
           xsi:schemaLocation="
           http://www.osgi.org/xmlns/blueprint/v1.0.0 http://www.osgi.org/xmlns/blueprint/v1.0.0/blueprint.xsd">
    <!--配置ActiveMQ参数-->
	<bean id="activemq" class="org.apache.activemq.camel.component.ActiveMQComponent"> 
          <!--brokerURL为传输模式，ActiveMQ具有各种传输模式，详情链接：http://activemq.apache.org/configuring-transports.html-->
	      <property name="brokerURL" value="tcp://192.168.1.190:61616"/> 
    </bean>

	<!--添加交换数据，提供给ActiveMQ生产者-->
	<bean id="beanProcessor" class="com.shudi.databridge.test.BeanProcessor" />

    <!--读取ActiveMQ消费者数据-->
	<bean id="dataSetProcessor" class="com.shudi.databridge.test.DataSetProcessor" />
	
	<camelContext xmlns="http://camel.apache.org/schema/blueprint">
	    
		<!--ActiveMQ组件测试路由一:生产者-->
		<route>
		     <from uri="timer://foo?period=10s"/>
			 <process ref="beanProcessor"/>
		     <to uri="activemq:topic:hunter.Test"/>
		</route>
		<!--ActiveMQ组件测试路由二:消费者-->
		<route>
			<from uri="activemq:topic:hunter.Test" />
			<process ref="dataSetProcessor" />
		</route>
	</camelContext>

</blueprint>
```
### 示例运行

![](https://i.imgur.com/USku91d.png)

### 查看ActiveMQ管理平台

![](https://i.imgur.com/gkea9yJ.png)

### 示例分析

- ActiveMQ组件集成Apache Camel和ActiveMQ，并可以很好的契合到集成环境中。
- ActiveMQ的消息异步接受，减少软件多系统集成的耦合度。消息可靠接收，确保消息在中间件可靠保存，多个消息也可以组成原子事务。
- ActiveMQ是一个开源消息组件(框架)，支持集群，同等网络，自动检测，TCP，SSL，广播，持久化，XA，和J2EE1.4容器无缝结合，并且支持轻量级容器和大多数跨语言客户端上的Java虚拟机。
- 当然，这里只是简单的教大家如何使用ActiveMQ组件和骆驼集成。后续将带给大家更详细的开发文档。