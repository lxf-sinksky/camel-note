# PDF组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-12-14*
*相关连接:[http://camel.apache.org/pdf.html](http://camel.apache.org/pdf.html)*

## 概述

PDF组件提供了创建、修改或提取PDF文档中内容的功能。此组件使用Apache PDFBox作为基础库来处理PDF文档。

注：PDF组件只用于生产者。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-amqp</artifactId>
    <version>camel-pdf</version>
</dependency>
```

## URI

```
pdf:operation[?options]
```

其中，operation为操作名。
- create:用给定的文本创建新的PDF文档。消息体的类型应该是String。 此操作的输出类型是OutputStream。
- append:附加文本到PDF文件。 文本将在新页面上开始。消息体的类型应该是String。 名称为“pdf-document”和PDFBox文档的强制性标题为预期值(原PDF文件)。 此操作的输出类型是OutputStream。
- extractText:从PDF文档中提取文本。此操作的输出类型为String。

## URI参数

名称|默认值|描述
----|----|----
marginTop|20|设置文本和页面顶部边缘之间的边距大小（以像素为单位）。
marginBottom|20|设置页面的文本和底部边缘之间的边距大小（以像素为单位）。
marginLeft|20|设置文本和页面左边缘之间的边距大小（以像素为单位）。
marginRight|40|设置页面的文本和右边缘之间的边距大小（以像素为单位）。 当textProcessingFactory选项为lineTermination时，此选项将被忽略。
fontSize|14|以像素为单位设置字体大小。
pageSize|PAGE_SIZE_A4|设置页面的大小。可能的值:PAGE_SIZE_A0,PAGE_SIZE_A1,PAGE_SIZE_A2,PAGE_SIZE_A3,PAGE_SIZE_A4,PAGE_SIZE_A5,PAGE_SIZE_A6,PAGE_SIZE_LETTER
font|Helvetica|PDFBox中的基本字体之一。
textProcessingFactory|lineTermination|设置文本处理工厂。 lineTermination - 为行终止写入策略。 文本被行终止符号分割，然后将被写入。 autoFormatting - 文本被逐词分割，然后最大量的文字将被写入PDF文档。 通过这个策略，所有不符合该行的词将被移到新行。

## Headers

Header|描述
----|----
pdf-document|附加操作的强制标题，在所有其他操作中被忽略。 预期的类型是PDDocument。存储将用于追加操作的PDF文件。
protection-policy|加密，预期类型是ProtectionPolicy。 如果指定，那么PDF文件将被加密。
decryption-material|解密，预期的类型是DecryptionMaterial。 如果PDF文档被加密，则为必填标题。

## 示例

### 示例一：读取PDF文档

#### BeanProcessor类
向正文添加PDDocument实例

```java
package com.shudi.databridge.test;

import java.io.IOException;
import org.apache.camel.Exchange;
import org.apache.camel.Processor;
import org.apache.pdfbox.pdmodel.PDDocument;

public class BeanProcessor implements Processor {
	
	@Override
	public void process(Exchange exchange) {
		try {
            //读取PDF文档需在PDF端点之前的消息正文中添加PDDocument实例,可参考下面的源码解析
			PDDocument document = PDDocument.load("H:\\Data\\formwork.pdf");
			exchange.getIn().setBody(document);
		} catch (IOException e) {
			e.printStackTrace();
		}
	}	
}
```

#### 补充：源码解析

![](https://i.imgur.com/7g39Qmj.png)

#### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	  <route>
		   <from uri="timer://order?period=20s"/>
           <!--添加pdf文档数据(PDDocument)-->
		   <to uri="class://com.shudi.databridge.test.BeanProcessor"/>
           <!--pdf生产者端点，extractText为读取操作-->
		   <to uri="pdf://extractText"/>
           <!--输出打印正文消息-->
		   <to uri="class://com.shudi.databridge.test.DataSetProcessor"/>
	  </route>
</camelContext>	
```

#### 示例运行

![](https://i.imgur.com/bmRO5L2.png)

### 示例二：附加文本到PDF文档

#### BeanProcessor类

```java
package com.shudi.databridge.test;

import java.io.IOException;
import org.apache.camel.Exchange;
import org.apache.camel.Processor;
import org.apache.pdfbox.pdmodel.PDDocument;

public class BeanProcessor implements Processor {
	
	@Override
	public void process(Exchange exchange) {
		try {
			PDDocument document = PDDocument.load("H:\\Data\\formwork.pdf");
            //附加操作需将原PDF文档实例PDDocument添加到Header中(注意：key必须是pdf-document)
			exchange.getIn().setHeader("pdf-document", document);
            //添加附加文本到消息正文
            exchange.getIn().setBody("Hello Apache Camel PDF!!!");
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```

#### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	  <route>
		   <from uri="timer://order?period=20s"/>
           <!--添加原pdf文档数据(PDDocument)到header,附加文本到正文-->
		   <to uri="class://com.shudi.databridge.test.BeanProcessor"/>
           <!--pdf生产者端点，append为追加操作-->
		   <to uri="pdf://append"/>
           <!--追加操作的输出类型为OutputStream,需要创建新的PDF文档（原PDF文档内容不会修改）-->
           <!--运用File组件创建新文档，并指定文件名和PDF格式-->
		   <to uri="file:H:/k3?fileName=formwork-1.pdf"/>
	  </route>
</camelContext>	
```

#### 示例运行

![](https://i.imgur.com/xemuHSB.png)

![](https://i.imgur.com/DMnIEo9.png)

>补充：追加中文内容会有乱码问题，需将新PDF文档进行转码。

### 示例三：创建PDF文档

#### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	  <route>
		   <from uri="timer://order?period=20s"/>
           <!--添加pdf文档数据-->
		   <setBody>
                 <constant>JMX is enabled \n Loaded 196 type converters</constant>
           </setBody>
           <!--pdf生产者端点，create为创建操作-->
		   <to uri="pdf://create"/>
           <!--运用File组件创建新文档，并指定文件名和PDF格式-->
		   <to uri="file:H:/k3?fileName=formwork-create.pdf"/>
	  </route>
</camelContext>	
```

#### 示例运行

![](https://i.imgur.com/1eyrcvl.png)

![](https://i.imgur.com/e52luwK.png)

### 示例分析

PDF组件提供了在路由中读取，附加，创建PDF文档的功能，使pdf格式的文件能够熟练的运用到集成中。




