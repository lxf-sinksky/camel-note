# Cache组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-13*
*相关连接:[http://camel.apache.org/cache.html](http://camel.apache.org/cache.html);[http://camel.apache.org/ehcache.html](http://camel.apache.org/ehcache.html)*

## 概述

cache组件使您能够使用 [EHCache](<!-- https://mvnrepository.com/artifact/org.apache.camel/camel-ehcache -->)作为缓存实现来执行缓存操作。缓存本身是根据需要创建的，或者如果已经存在这个名称的缓存，那么它就可以使用它的原始设置。
该组件支持基于生产者和事件的消费者端点。
缓存使用者是基于事件的使用者，可用于侦听和响应特定的缓存活动。如果您需要从预先存在的缓存中执行选择，那么可以使用为缓存组件定义的处理器。

### 补充：使用Cache组件需添加camel-cache依赖

```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-cache</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
cache://cacheName[?options]
```

## URI参数

名称|默认值|描述
----|----|----
maxElementsInMemory|1000|可存储在定义的缓存中的元素数。
memoryStoreEvictionPolicy|MemoryStoreEvictionPolicy.LFU|可以存储在定义的缓存中的元素数。选项包括MemoryStoreEvictionPolicy. LFU-最少常用;MemoryStoreEvictionPolicy. LRU-最近很少使用;MemoryStoreEvictionPolicy 先进先出-首先在第一出, 最旧的元素按创建时间。
overflowToDisk|true|指定缓存是否可能溢出到磁盘。
eternal|false|设置元素是否是永恒的。如果是永恒的, 则忽略超时,元素永不过期。
timeToLiveSeconds|300|创建时间和元素过期之间的最长时间。仅当元素不是永恒时才使用。
timeToIdleSeconds|300|在元素过期之前访问之间的最大时间量。
diskPersistent|false|在虚拟机重新启动之间是否存在磁盘存储。
diskExpiryThreadIntervalSeconds|120|磁盘到期线程运行之间的秒数。
cacheManagerFactory|null|你可以使用一个自定义的工厂实例化和创建EHCache net.sf.ehcache.CacheManager。
eventListenerRegistry|null|设置一个EHCache.sf.ehcache.event的列表。用于所有新缓存的CacheEventListener——无需在EHCache xml配置中定义每个缓存。
cacheLoaderRegistry|null|为所有新缓存设置扩展 EHCache net.sf.ehcache.loader.CacheLoader的org.apache.camel.component.cache.CacheLoaderWrapper列表-无需再在 EHCache xml 配置中定义每个缓存。
key|null|默认情况下使用缓存键进行配置。
operation|null|默认情况下使用缓存操作进行配置。
objectCache|false|是否允许在缓存中存储非可串行化的对象。如果启用此选项, 则无法同时启用到磁盘的溢出。

### Cache Component options

名称|默认值|描述
----|----|----
configuration|暂无|使用自定义org.apache.camel.component.cache.CacheConfiguration配置。
cacheManagerFactory|暂无|使用自定义org.apache.camel.component.cache.CacheManagerFactory。
configurationFile|暂无|配置要使用的ehcache.xml文件的位置, 如类classpath:com/foo/mycache.xml来从类路径加载。如果未给出配置, 则使用 EHCache 的默认设置。

## 从缓存中发送/接收消息

Header|描述
----|----
CACHE_OPERATION|要在缓存上执行的操作。有效的选项是GET,CHECK,ADD,UPDATE,DELETE,DELETEALL。
CACHE_KEY|用于将消息存储在缓存中的缓存键。如果 CACHE_OPERATION 是 DELETEALL 的, 则缓存密钥是可选的。

## 缓存生产者

将数据发送到缓存中，包括在交换中直接将有效负载存储到预先存在的或按需定制的缓存中。
- 设置上面显示的消息交换头。
- 确保消息交换正文包含指向缓存的消息。

## 缓存使用者

从高速缓存中接收数据，可以使用CacheConsumer在使用事件侦听器时侦听预先存在的或按需定制的缓存，并在任何缓存活动发生时接收自动通知。 
- 包含消息交换头和消息交换体的交换器将被放置和发送。
- 在 CamelCacheDeleteAll 操作的情况下, 不填充消息交换头 CamelCacheKey 和消息交换正文。


## 示例

### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint">
     <!--路由一：向cache缓存端点中添加数据--> 
	 <route>
		  <!--定时器触发，repeatCount=1表示定时器调用一次-->
          <from uri="timer://foo?repeatCount=1"/>
          <!--想交换中的消息正文添加数据-->
		  <to uri="class://com.shudi.integration.test.HeaderProcessor"/>
          <!--连接到缓存端点。注意：key，operation是必须的。timeToLiveSeconds=10表示缓存数据的有效期为10S-->
          <to uri="cache://cacheData?key=helloword&amp;operation=add&amp;timeToLiveSeconds=10"/>		   
     </route> 
	 <!--路由二：向cache缓存端点中获取数据-->		
	 <route> 
          <!--定时器，delay=5s表示第一次调用的延时。period=5s表示轮询调度周期时长-->
          <from uri="timer://too?delay=5s&amp;period=5s"/>
          <to uri="cache://cacheData?key=helloword&amp;operation=get"/>
          <!--打印数据-->
		  <to uri="class://com.shudi.databridge.test.DataSetProcessor"/>
     </route> 
</camelContext>
```

### 示例运行

![](https://i.imgur.com/KdK7dbR.png)

### 示例分析

上例中，路由一执行一次将交换中的数据缓存到cache端点，并设置数据的有效期。路由二利用定时器轮询获取缓存中的数据。
从示例运行的结果显示可以知道cache组件实现了数据缓存的功能，也对部分参数进行了验证。在Apache Camel中cache组件也有扩展组件[Ehcache](http://camel.apache.org/ehcache.html)。




