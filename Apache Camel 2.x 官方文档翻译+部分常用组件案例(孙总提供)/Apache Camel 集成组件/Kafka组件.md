# Kafka组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-12-11*
*相关连接:[http://camel.apache.org/kafka.html](http://camel.apache.org/kafka.html)*

## 概述

Kafka组件用于与Apache Kafka的消息代理进行通信。


使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-kafka</artifactId>
    <version>x.x.x</version>
</dependency>
```

camel-kafka版本2.16及以下需添加Scala库依赖：
```xml
<dependency>
    <groupId>org.scala-lang</groupId>
    <artifactId>scala-library</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
kafka:server:port[?options]
```

## URI参数(2.16及以下)

名称|默认值|描述
----|----|----
zookeeperHost|暂无|zookeeper主机名。
zookeeperPort|2181|zookeeper端口号。
zookeeperConnect|暂无|zookeeper连接。使用该选项，则不使用zookeeperHost/zookeeperPort。
topic|暂无|使用的主题名。
groupid|暂无|分组ID。
partitioner|暂无|分区。
consumerStreams|10|暂无
clientid|暂无|客户端ID。
zookeeperSessionTimeoutMs|暂无|以毫秒为单位的zookeeper会话超时。
zookeeperConnectionTimeoutMs|暂无|以毫秒为单位的zookeeper连接超时。
zookeeperSyncTimeMs|暂无|以毫秒为单位的zookeeper同步超时。
consumersCount|1|camel 2.15.0:连接到kafka服务器的消费者数量。
batchSize|100|camel 2.15.0:BatchingConsumerTask处理一次的batchSize, 自2.17.1后弃用, 2.18.0已删除。
barrierAwaitTimeoutMs|10000|camel 2.15.0:如果BatchingConsumerTask进程的交换超过了batchSize,它将等待barrierAwaitTimeoutMs。自2.17.1后已弃用, 删除自2.18.0.
bridgeEndpoint|false|camel 2.16.0: 如果bridgeEndpoint为true, 生产者将忽略邮件的主题标题设置。

## 生产者参数(2.16及以下)

名称|默认值|描述
----|----|----
producerType|sync|同步-立即发送消息/批处理，并等待接收到响应。异步——队列消息/批处理发送。每个代理(kafka节点)都有一个线程，从queueBufferingMaxMs(缓冲队列)或batchNumMessages(批处理)中发送消息。
compressionCodec|none|生产者生成的所有数据的压缩类型。 默认值是none（即不压缩）。 有效的值是none，gzip，snappy或者lz4。
compressedTopics|暂无|暂无
messageSendMaxRetries|暂无|暂无
retryBackoffMs|100|尝试重试对给定主题分区的失败请求之前等待的时间量。 这样可以避免在某些故障情况下重复发送请求。
topicMetadataRefreshIntervalMs|暂无|暂无
sendBufferBytes|131072|发送数据时要使用的TCP发送缓冲区（SO_SNDBUF）的大小。 如果值为-1，则将使用操作系统默认值。
requestRequiredAcks|暂无|暂无
requestTimeoutMs|30000|以毫秒为单位的请求超时。
queueBufferingMaxMs|暂无|缓冲队列最大等待时间。
queueBufferingMaxMessages|暂无|暂无
queueEnqueueTimeoutMs|暂无|暂无
batchNumMessages|暂无|暂无
serializerClass|暂无|用于实现org.apache.kafka.common.serialization.Serializer接口的值的序列化程序类。
keySerializerClass|暂无|用于实现org.apache.kafka.common.serialization.Serializer接口的键的序列化程序类。

## 消费者参数(2.16及以下)

名称|默认值|描述
----|----|----
consumerId|null|消费者ID,如果没有设置，自动生成。
socketTimeoutMs|30*1000|网络请求的socket超时。
socketReceiveBufferBytes|64*1024|socket接收网络请求的缓冲区。
fetchMessageMaxBytes|1024*1024|在每个提取请求中尝试为每个主题分区提取的消息的字节数。 这些字节将被读入每个分区的内存，所以这有助于控制消费者使用的内存。 获取请求大小必须至少与服务器允许的最大消息大小一样大，否则生产者可能发送大于消费者可以获取的消息。
autoCommitEnable|true|如果为true，消费者的偏移将在后台定期提交。
autoCommitIntervalMs|5000|如果enable.auto.commit设置为true，则消费者偏移的频率以毫秒为单位自动提交给Kafka。
queuedMaxMessages|暂无|暂无
rebalanceMaxRetries|4|当一个新的消费者加入一个消费者群体时，一组消费者试图“重新平衡”负载以将分区分配给每个消费者。 如果在分配发生时该组消费者发生更改，则重新平衡将失败并重试。 此设置控制放弃之前的最大尝试次数。
fetchMinBytes|1|服务器为获取请求返回的最小数据量。 如果没有足够的数据可用，请求将等待数据积累，然后再回答请求。 1字节的默认设置意味着只要有一个字节的数据可用，或者提取请求超时等待数据到达，就会立即应答提取请求。 将其设置为大于1的值将导致服务器等待大量的数据累积，这可以稍稍提高服务器吞吐量，但需要花费一定的额外延迟时间。
fetchWaitMaxMs|500|如果没有足够的数据立即满足fetch.min.bytes给定的要求，服务器在应答提取请求之前将阻塞的最大时间量。
rebalanceBackoffMs|2000|在重新平衡期间的重新尝试之间的间隔时间。如果未明确设置，则使用zookeeper.sync.time.ms中的值。
refreshLeaderBackoffMs|200|暂无
autoOffsetReset|latest|重置偏移量。
consumerTimeoutMs|-1|如果在指定的时间间隔内没有可供消费的消息，则向使用者抛出超时异常。

## URI参数(2.17及以上)

名称|默认值|描述
----|----|----
topic|暂无|使用的主题名。
groupid|暂无|分组ID。
consumerStreams|10|暂无
clientid|暂无|客户端ID。
consumersCount|1|连接到kafka服务器的消费者数量。
batchSize|100|如果自动提交为false,则每批提交的大小。
bridgeEndpoint|false|camel 2.16.0: 如果bridgeEndpoint为true, 生产者将忽略邮件的主题标题设置。

## 生产者参数(2.17及以上)

名称|类型|默认值|描述
----|----|----|----
serializerClass|class|暂无|用于实现org.apache.kafka.common.serialization.Serializer接口的值的序列化程序类。
keySerializerClass|class|暂无|用于实现org.apache.kafka.common.serialization.Serializer接口的键的序列化程序类。
requestRequiredAcks|暂无|暂无|暂无
bufferMemorySize|long|33554432|缓冲区内存大小。
compressionCodec|string|none|生产者生成的所有数据的压缩类型。 默认值是none（即不压缩）。 有效的值是none，gzip，snappy或者lz4。
retries|int|0|设置一个大于零的值将导致客户端重新发送任何发送失败的记录，并有可能出现瞬时错误。 请注意，此重试与客户端收到错误时重新发送记录没有区别。 在不将max.in.flight.requests.per.connection设置为1的情况下允许重试可能会更改记录的排序顺序，因为如果将两个批处理发送到单个分区，并且第一个失败并重试但第二个成功，则记录在第二批可能会先出现。
sslKeyPassword|password|null|密钥存储文件中的私钥密码。 这对于客户端是可选的。
sslKeystoreLocation|string|null|密钥存储文件的位置。 这对客户端是可选的，可以用于客户端的双向认证。
sslKeystorePassword|password|null|密钥存储文件的存储密码。 这对客户端是可选的，只有在配置了ssl.keystore.location时才需要。
sslTruststoreLocation|string|null|凭证存储文件的位置。
sslTruststorePassword|password|null|凭证存储文件的密码。 如果密码未设置，凭证库的访问仍然可用，但完整性检查被禁用。
producerBatchSize|int|16384|生产者批处理大小。
clientId|String|""|发出请求时传递给服务器的id字符串。 这样做的目的是通过允许在服务器端请求日志中包含一个逻辑应用程序名称来跟踪请求的来源，而不仅仅是ip/port。
connectionMaxIdleMs|long|600000|空闲连接超时:服务器套接字处理器线程关闭了空闲的连接。
lingerMs|long|0|延迟发送的时间，时间单位为毫秒。
maxBlockMs|long|60000|该配置控制KafkaProducer.send（）和KafkaProducer.partitionsFor（）将被阻塞多长时间。这些方法可能因为缓冲区已满或元数据不可用而被阻止。用户提供的序列化程序或分区程序中的阻塞将不计入此超时。
maxRequestSize|int|1048576|请求的最大值（以字节为单位）。 此设置将限制生产者在单个请求中发送的记录批次的数量，以避免发送大量请求。 这也是最大记录批量大小的有效上限。 请注意，服务器在记录批量大小上有自己的上限，这可能与此不同。
partitioner|class|org.apache.kafka.clients.producer.internals.DefaultPartitioner|实现org.apache.kafka.clients.producer.Partitioner接口的分区器类。
receiveBufferBytes|int|32768|读取数据时使用的TCP接收缓冲区（SO_RCVBUF）的大小。 如果值为-1，则将使用操作系统默认值。
requestTimeoutMs|int|30000|以毫秒为单位的请求超时。
saslKerberosServiceName|string|null|Kafka运行的Kerberos主体名称。 这可以在Kafka的JAAS配置或Kafka的配置中定义。
saslMechanism(camel 2.18)|string|GSSAPI|用于客户端连接的SASL机制。 这可能是安全提供者可用的任何机制。 GSSAPI是默认的机制。
securityProtocol|string|PLAINTEXT|用于与代理通讯的协议。 有效值为：PLAINTEXT，SSL，SASL_PLAINTEXT，SASL_SSL
sendBufferBytes|int|131072|发送数据时要使用的TCP发送缓冲区（SO_SNDBUF）的大小。 如果值为-1，则将使用操作系统默认值。
sslEnabledProtocols|list|TLSv1.2,TLSv1.1,TLSv1|启用了SSL连接的协议列表。
sslKeystoreType|string|JKS|密钥存储文件的文件格式。 这对于客户端是可选的。
sslProtocol|string|TLS|用于生成SSLContext的SSL协议。 默认设置是TLS，在大多数情况下这是可用的。 最近的JVM中允许的值是TLS，TLSv1.1和TLSv1.2。 较旧的JVM中可能支持SSL，SSLv2和SSLv3，但由于已知的安全漏洞，它们的使用不受欢迎。
sslProvider|string|null|用于SSL连接的安全提供程序的名称。 默认值是JVM的默认安全提供程序。
sslTruststoreType|string|JKS|凭证存储文件的文件格式。
maxInFlightRequest|int|5|在阻止之前，客户端将在单个连接上发送的未确认请求的最大数量。 请注意，如果此设置设置为大于1并且发送失败，则由于重试（即，如果重试被启用），存在重新排序消息的风险。
metadataMaxAgeMs|long|300000|在毫秒之后的一段时间内，即使我们没有看到任何分区的更改，我们强制刷新元数据，以主动发现任何新的代理或分区。
metricReporters|list|""|用作度量记录的类的列表。 实现org.apache.kafka.common.metrics.MetricsReporter接口允许插入将被通知新的度量标准创建的类。 JmxReporter始终包含在注册JMX统计信息中。
noOfMetricsSample|暂无|暂无|暂无
metricsSampleWindowMs|long|30000|计算指标样本的窗口时间。
reconnectBackoffMs|long|50|尝试重新连接到给定主机之前等待的基本时间。 这避免了在一个紧密的循环中重复连接到主机。 该退避适用于客户端向代理的所有连接尝试。
retryBackoffMs|long|100|尝试重试对给定主题分区的失败请求之前等待的时间量。 这样可以避免在某些故障情况下重复发送请求。
kerberosInitCmd|string|/usr/bin/kinit|Kerberos kinit命令路径。
kerberosBeforeReloginMinTime|long|60000|登录线程在刷新尝试之间的休眠时间。
kerberosRenewJitter|double|0.05|随机设定的百分比增加到更新时间。
kerberosRenewWindowFactor|double|0.8|登录线程将休眠，直到从上次刷新到票据过期的指定窗口时间已经到达，在此期间它将尝试更新票据。
sslCipherSuites|list|null|密码套件列表。 这是用于使用TLS或SSL网络协议来协商网络连接的安全设置的认证，加密，MAC和密钥交换算法的命名组合。 默认情况下，所有可用的密码套件都受支持。
sslEndpointAlgorithm|string|null|使用服务器证书验证服务器主机名的端点识别算法。
sslKeymanagerAlgorithm|string|SunX509|密钥管理器工厂用于SSL连接的算法。默认值是为Java虚拟机配置的密钥管理器工厂算法。
sslTrustmanagerAlgorithm|string|PKIX|凭证管理器工厂用于SSL连接的算法。 默认值是为Java虚拟机配置的凭证管理器工厂算法。

## 消费者参数(2.17及以上)

名称|类型|默认值|描述
----|----|----|----
keyDeserializer|class|暂无|用于实现org.apache.kafka.common.serialization.Deserializer接口的键的反序列化程序类。
valueDeserializer|class|暂无|用于实现org.apache.kafka.common.serialization.Deserializer接口的值的反序列化器类。
fetchMinBytes|int|1|服务器为获取请求返回的最小数据量。 如果没有足够的数据可用，请求将等待数据积累，然后再回答请求。 1字节的默认设置意味着只要有一个字节的数据可用，或者提取请求超时等待数据到达，就会立即应答提取请求。 将其设置为大于1的值将导致服务器等待大量的数据累积，这可以稍稍提高服务器吞吐量，但需要花费一定的额外延迟时间。
groupid|string|""|标识此消费者所属的消费者群组的唯一字符串。 如果消费者通过使用订阅（主题）或基于Kafka的偏移量管理策略来使用组管理功能，则此属性是必需的。
heartbeatIntervalMs|int|3000|在使用kafka的组管理设施时，心跳与消费者协调器之间的预期时间。
maxPartitionFetchBytes|int|1048576|服务器将返回每个分区的最大数据量。 记录由用户批量提取。 如果提取的第一个非空分区中的第一个记录批次大于此限制，则该批次仍将被返回，以确保用户可以取得进展。 代理接受的最大记录批量大小通过message.max.bytes（broker config）或max.message.bytes（topic config）进行定义。 请参阅fetch.max.bytes来限制消费者请求大小。
sessionTimeoutMs|int|10000|以毫秒为单位的会话超时。
sslKeyPassword|password|null|密钥存储文件中的私钥密码。 这对于客户端是可选的。
sslKeystoreLocation|string|null|密钥存储文件的位置。 这对客户端是可选的，可以用于客户端的双向认证。
sslKeystorePassword|password|null|密钥存储文件的存储密码。 这对客户端是可选的，只有在配置了ssl.keystore.location时才需要。
sslTruststoreLocation|string|null|凭证存储文件的位置。
sslTruststorePassword|password|null|凭证存储文件的密码。 如果密码未设置，凭证库的访问仍然可用，但完整性检查被禁用。
autoOffsetReset|string|latest|重置偏移量。
connectionMaxIdleMs|long|600000|空闲连接超时:服务器套接字处理器线程关闭了空闲的连接。
autoCommitEnable|boolean|true|如果为true，消费者的偏移将在后台定期提交。
partitionAssignor|暂无|暂无|暂无
receiveBufferBytes|int|32768|读取数据时使用的TCP接收缓冲区（SO_RCVBUF）的大小。 如果值为-1，则将使用操作系统默认值。
consumerRequestTimeoutMs|long|暂无|消费者请求超时。
saslKerberosServiceName|string|null|Kafka运行的Kerberos主体名称。 这可以在Kafka的JAAS配置或Kafka的配置中定义。
saslMechanism|string|GSSAPI|用于客户端连接的SASL机制。 这可能是安全提供者可用的任何机制。 GSSAPI是默认的机制。
securityProtocol|string|PLAINTEXT|用于与代理通讯的协议。 有效值为：PLAINTEXT，SSL，SASL_PLAINTEXT，SASL_SSL
sendBufferBytes|int|131072|发送数据时要使用的TCP发送缓冲区（SO_SNDBUF）的大小。 如果值为-1，则将使用操作系统默认值。
sslEnabledProtocols|list|TLSv1.2,TLSv1.1,TLSv1|启用了SSL连接的协议列表。
sslKeystoreType|string|JKS|密钥存储文件的文件格式。 这对于客户端是可选的。
sslProtocol|string|TLS|用于生成SSLContext的SSL协议。 默认设置是TLS，在大多数情况下这是可用的。 最近的JVM中允许的值是TLS，TLSv1.1和TLSv1.2。 较旧的JVM中可能支持SSL，SSLv2和SSLv3，但由于已知的安全漏洞，它们的使用不受欢迎。
sslProvider|string|null|用于SSL连接的安全提供程序的名称。 默认值是JVM的默认安全提供程序。
sslTruststoreType|string|JKS|凭证存储文件的文件格式。
autoCommitIntervalMs|int|5000|如果enable.auto.commit设置为true，则消费者偏移的频率以毫秒为单位自动提交给Kafka。
checkCrcs|boolean|true|自动检查消耗的记录的CRC32。 这可以确保没有在线或磁盘损坏的消息发生。 这个检查会增加一些开销，所以在寻求极高性能的情况下可能会被禁用。
clientid|string|""|发出请求时传递给服务器的id字符串。 这样做的目的是通过允许在服务器端请求日志中包含一个逻辑应用程序名称来跟踪请求的来源，而不仅仅是ip/port。
fetchWaitMaxMs|int|500|如果没有足够的数据立即满足fetch.min.bytes给定的要求，服务器在应答提取请求之前将阻塞的最大时间量。
metadataMaxAgeMs|long|300000|在毫秒之后的一段时间内，即使我们没有看到任何分区的更改，我们强制刷新元数据，以主动发现任何新的代理或分区。
metricReporters|list|""|用作度量记录的类的列表。 实现org.apache.kafka.common.metrics.MetricsReporter接口允许插入将被通知新的度量标准创建的类。 JmxReporter始终包含在注册JMX统计信息中。
noOfMetricsSample|暂无|暂无|暂无
metricsSampleWindowMs|long|30000|计算指标样本的窗口时间。
reconnectBackoffMs|long|50|尝试重新连接到给定主机之前等待的基本时间。 这避免了在一个紧密的循环中重复连接到主机。 该退避适用于客户端向代理的所有连接尝试。
retryBackoffMs|long|100|尝试重试对给定主题分区的失败请求之前等待的时间量。 这样可以避免在某些故障情况下重复发送请求。
kerberosInitCmd|string|/usr/bin/kinit|Kerberos kinit命令路径。
kerberosBeforeReloginMinTime|long|60000|登录线程在刷新尝试之间的休眠时间
kerberosRenewJitter|double|0.05|随机设定的百分比增加到更新时间。
kerberosRenewWindowFactor|double|0.8|登录线程将休眠，直到从上次刷新到票据过期的指定窗口时间已经到达，在此期间它将尝试更新票据。
sslCipherSuites|list|null|密码套件列表。 这是用于使用TLS或SSL网络协议来协商网络连接的安全设置的认证，加密，MAC和密钥交换算法的命名组合。 默认情况下，所有可用的密码套件都受支持。
sslEndpointAlgorithm|string|null|使用服务器证书验证服务器主机名的端点识别算法。
sslKeymanagerAlgorithm|string|SunX509|密钥管理器工厂用于SSL连接的算法。默认值是为Java虚拟机配置的密钥管理器工厂算法。
sslTrustmanagerAlgorithm|string|PKIX|凭证管理器工厂用于SSL连接的算法。 默认值是为Java虚拟机配置的凭证管理器工厂算法。

>补充：Kafka官方文档链接：[http://kafka.apache.org/documentation/](http://kafka.apache.org/documentation/)

## 示例

### 搭建Zookeeper和Kafka服务端

Zookeeper服务端配置在Zookeeper组件中已做讲解，详情请查看Zookeeper组件文档。

Kafka服务端配置链接：[http://blog.csdn.net/evankaka/article/details/52421314](http://blog.csdn.net/evankaka/article/details/52421314)

开启Zookeeper服务zkServer.cmd

![](https://i.imgur.com/8b68hSg.png)

开启Kafka服务

![](https://i.imgur.com/9CvMtG7.png)
![](https://i.imgur.com/c3LbPxi.png)

### DataSetProcessor类
处理器

```java
public class DataSetProcessor implements Processor {

	@Override
	public void process(Exchange exchange) throws Exception {
		System.out.println("Kafka组件测试--->"+"\n"+"Body:"+exchange.getIn().getBody(String.class));
		for (Entry<String,Object> map : exchange.getIn().getHeaders().entrySet()) {
			System.out.println("Header:"+"key--"+map.getKey()+",value--"+map.getValue());
		}
	}
}
```

### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
        <!--路由一：kafka生产者-->
	    <route>
		     <from uri="timer://kafkaoff?delay=5s&amp;period=20s"/>
			 <setBody>
			     <constant>今天是2017-12-12日，天气晴</constant>
			 </setBody>
             <!--生产者端点，topic为主题名，groupId为分组ID-->
			 <to uri="kafka://192.168.1.39:9092?topic=test&amp;groupId=testing"/>
		</route>
	    <!--路由二：kafka消费者-->
		<route>
             <!--消费者端点，consumersCount表示连接到kafka服务器的消费者数量。-->
		     <from uri="kafka://192.168.1.39:9092?topic=test&amp;consumersCount=1&amp;groupId=testing"/>
             <!--打印body,header-->
			 <to uri="class://com.shudi.databridge.test.DataSetProcessor"/>
		</route>
</camelContext>	
```

### 示例本地运行

![](https://i.imgur.com/U1RGUh9.png)

### 示例分析

从上述的例子可以看出Kafka组件发布和订阅消息流,它类似于一个消息队列或企业消息系统。

Kafka将消息流按Topic组织，保存消息的服务器称为Broker，消费者可以订阅一个或者多个Topic。为了均衡负载，一个Topic的消息又可以划分到多个分区(Partition)，分区越多，Kafka并行能力和吞吐量越高。Kafka的性能大大超过传统的ActiveMQ、RabbitMQ等MQ实现，尤其是Kafka还支持batch操作。

Kafka的缺点:重复消息,Kafka只保证每个消息至少会送达一次，虽然几率很小，但一条消息有可能会被送达多次。消息乱序,虽然一个Partition 内部的消息是保证有序的，但是如果一个Topic有多个Partition，Partition之间的消息送达不保证有序。复杂性,Kafka需要zookeeper集群的支持,Topic通常需要人工来创建，部署和维护较一般消息队列成本更高。

Kafka扩展知识链接：[http://orchome.com/5](http://orchome.com/5)