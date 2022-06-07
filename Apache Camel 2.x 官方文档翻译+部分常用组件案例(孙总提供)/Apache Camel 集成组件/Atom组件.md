# Atom组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-12-27*
*相关连接:[http://camel.apache.org/atom.html](http://camel.apache.org/atom.html)*

## 概述

Atom组件用于轮询Atom提要。camel将默认每60秒轮询一次提要。
注意：该组件当前仅支持轮询（消费）提要。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-atom</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
atom://atomUri[?options] 
```

## URI参数

名称|默认值|描述
----|----|----
splitEntries|true|如果为true,camel将轮询提要,随后的轮询将通过轮询返回每个条目轮询。如果该提要包含7个条目，那么骆驼将返回第一个轮询的第一个条目，下一个轮询的第2个条目，直到不再有更多条目，驼峰将在提要上进行新的更新。如果是false，camel将在每次调用时轮询一个新的提要。
filter|true|仅由拆分项用于过滤要返回的项。camel将默认使用只从提要中返回新条目的UpdateDateFilter 。因此, 从feed中消耗的客户端不会多次收到相同的条目。过滤器将返回最新的最后一个命令的条目。
lastUpdate|null|仅被过滤器使用，作为选择的开始时间戳永不输入（使用entry.updated时间戳）。 语法格式是：yyyy-MM-ddTHH：MM：ss。 例如：2007-12-24T17：45：59。
throttleEntries|true|camel 2.5:设置是否应立即传递单个提要轮询中标识的所有条目。如果为true, 则每个consumer.delay只处理一个条目。仅当splitEntries设置为true时才适用。
feedHeader|true|设置是否将Abdera提要对象添加为标头。
sortEntries|false|如果splitEntries为true，则设置按更新的日期对这些条目进行排序。
consumer.delay|500|每轮轮询之间的时间延迟。
consumer.initialDelay|1000|在第一次轮询前的延迟。
consumer.userFixedDelay|false|如果为true，则在池之间使用固定的延迟，否则使用固定的速率。 有关详细信息，请参阅JDK中的ScheduledExecutorService。
username|暂无|camel 2.16：用于从HTTP提要进行轮询时的基本身份验证。
password|暂无|camel 2.16：用于从HTTP提要进行轮询时的基本身份验证。

## 交换数据格式
Camel将在返回的Exchange中对条目设置正文。根据splitEntries标记, 骆驼将返回一个Entry或List<Entry>。

名称|默认值|描述
----|----|----
splitEntries|true|只设置当前正在处理的提要中的单个条目: exchange.in.body(Entry)。
splitEntries|false|从提要中设置条目的整个列表: exchange.in.body(List<Entry>)。

Camel可以在标题中设置Feed对象 (请参阅feedHeader选项以禁用此功能)。

## 示例

### Blueprint DSL 配置

```xml
<route>
	<from uri="atom://http://hunterfang.blogspot.com/feeds/posts/default"/> 
    <!--打印交换中的body,header-->
	<to uri="class://com.shudi.databridge.test.DataSetProcessor"/>  	  
</route>	
```

### 示例分析

示例中，运用Atom组件在路由中读取Google Blogger博客。





