# Dozer组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-30*
*相关连接:[http://camel.apache.org/dozer.html](http://camel.apache.org/dozer.html)*

## 概述

Dozer组件提供了在Java bean之间进行映射的框架。camel也支持触发Dozer映射的能力作为一种类型转换器。

使用Dozer端点和Dozer转换器之间的主要区别是: 
- 通过转换器注册表管理基于每个端点的Dozer映射配置与全局配置的能力。
- Dozer端点可以配置为使用Camel数据格式对输入和输出数据进行编组/解组，以支持任意转换端点。
- Dozer组件允许fine-grained集成和Dozer的扩展, 以支持附加功能 (例如, 映射字面值, 使用表达式进行映射等)。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-dozer</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
dozer:endpointId[?options]
```

其中,endpointId是用于唯一标识dozer端点配置的名称。

## URI参数

名称|默认值|描述
----|----|----
mappingFile|dozerBeanMapping.xml|dozer配置文件的位置。默认情况下, 该文件从类路径中加载, 但您可以使用file:、classpath:, or http: 从特定位置加载配置。
unmarshalId|none|在Camel上下文中定义的一个dataFormat的id，用于解组来自非Java类型的映射输入。
marshalId|none|在骆驼上下文中定义dataFormat的id，用于将映射输出编组为非java类型。
sourceModel|none|映射中使用的源类型的完全限定类名。如果指定了，在映射到Dozer之前，映射的输入被转换为指定的类型。
targetModel|none|映射中使用的目标类型的完全限定类名。这个选项是必需的。
mappingConfiguration|none|DozerBeanMapperConfiguration bean的名称在骆驼注册应该用于配置dozer映射。这是mappingFile选项的另一种选择，可以用于对Dozer的配置进行细粒度的控制。记住在值中使用一个“前缀”来表示bean位于骆驼注册表中(例如:“# myDozerConfig”)。

## 使用Dozer的数据格式

Dozer不支持非Java源和映射的目标，因此，它不能把一个XML文档映射到一个Java对象上。而camel有广泛的支持，可以使用数据格式在Java和各种格式之间进行编组。Dozer组件利用了这一支持，允许您指定输入和输出数据应通过数据格式传递, 然后再通过Dozer进行处理。

例如, 假设您希望使用Dozer组件在xml数据结构和JSON数据结构之间进行映射。您需在骆驼上下文中定义了以下数据格式:

```xml
<dataFormats>
    <json library="Jackson" id="myjson"/>
    <jaxb contextPath="org.example" id="myjaxb"/>
</dataFormats>
```

然后, 您可以配置一个Dozer端点, 使用JAXB数据格式编组输入XML, 并使用Jackson来映射输出。

```xml
<to uri="dozer:xml2json?marshalId=myjson&amp;unmarshalId=myjaxb&amp;targetModel=org.example.Order"/>
```

## 示例
使用Dozer组件映射扩展

### Book类

```java
package dozer;

public class Book {
	  private boolean name;
	  private int author;
      //省略构造方法，get,set方法......
}
```

### BookVo类

```java
package dozer;

public class BookVo {
	  private String namevo;
	  private String authorvo;
      //省略构造方法，get,set方法......
}
```

### BeanProcessor类
添加正文数据(Book实例)

```java
package com.shudi.databridge.test;

import org.apache.camel.Exchange;
import org.apache.camel.Processor;
import dozer.Book;

public class BeanProcessor implements Processor {

	@Override
	public void process(Exchange exchange) throws Exception {
		exchange.getIn().setBody(new Book(true, 888888));
	}
}
```

### DataSetProcessor类
打印正文BookVo实例

```java
package com.shudi.databridge.test;

import org.apache.camel.Exchange;
import org.apache.camel.Processor;
import dozer.BookVo;

public class DataSetProcessor implements Processor {

	@Override
	public void process(Exchange exchange) throws Exception {
		BookVo bookVo = exchange.getIn().getBody(BookVo.class);
		System.out.println("Dozer组件测试:bookVo实体类打印-->" + bookVo.getNamevo() + "--" + bookVo.getAuthorvo());
	}
}
```

### bookMapper.xml
自定义变量映射

```xml
<?xml version="1.0" encoding="UTF-8"?>
<mappings xmlns="http://dozer.sourceforge.net" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://dozer.sourceforge.net  
       http://dozer.sourceforge.net/schema/beanmapping.xsd">

	<mapping>
		<class-a>dozer.Book</class-a>
		<class-b>dozer.BookVo</class-b>
		<field>
			<a>name</a>
			<b>namevo</b>
		</field>
		<field>
			<a>author</a>
			<b>authorvo</b>
		</field>
	</mapping>
</mappings> 
```

### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint">		
	   <route>
            <from uri="timer://foo?period=10s"/> 
            <!--添加正文数据，Book实例--> 
			<to uri="class://com.shudi.databridge.test.BeanProcessor"/>
            <!--dozer组件端点，mappingFile为xml配置文件路径,targetModel为输出类型-->
			<to uri="dozer://transformBook?mappingFile=classpath:dozer/bookMapper.xml&amp;targetModel=dozer.BookVo"/>
            <!--获取映射后的BookVo实例,并打印-->
			<to uri="class://com.shudi.databridge.test.DataSetProcessor"/>
	    </route> 		
</camelContext>	
```

### 示例运行

![](https://i.imgur.com/1uLVtMc.png)

### 示例分析

示例中，构建了Book和BookVo实体类，属性的类型均不相同。然后我们自定义实体类的变量映射关系，并加载到路由中的dozer端点。
路由中输入的为Book实例,而在经过dozer端点后，可以在消息正文中拿到映射后的BookVo实例。

Dozer组件可以在集成路由中实现数据映射和数据转换，该组件集成dozer的所有功能，它可以灵活的处理复杂类型之间的映射。不但可以进行简单的属性映射、复杂的类型映射、双向映射、递归映射等，并且可以通过XML配置文件进行灵活的配置。 


