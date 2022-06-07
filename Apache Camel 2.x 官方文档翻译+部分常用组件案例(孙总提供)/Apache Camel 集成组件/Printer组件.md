# Printer组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-12-26*
*相关连接:[http://camel.apache.org/printer.html](http://camel.apache.org/printer.html)*

## 概述

Printer组件提供了一种方法，可以将有效负载引导到一台打印机上。显然，有效载荷必须是一种格式化的有效载荷，以便组件能够适当地打印它。目标是能够将特定的有效载荷作为作业指向Camel流中的行式打印机。
该组件仅支持camel生产者端点。
该功能允许在默认打印机(名为本地、远程或无线链接的打印机)上使用 javax printing API 在封面下打印有效负载。

>注：该组件版本需在2.1以上

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-printer</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
lpr://localhost/default[?options]
lpr://remotehost:port/path/to/printer[?options]
```

## URI参数

名称|默认值|描述
----|----|----
mediaSize|NA_LETTER|根据javax.print.attribute.standard.MediaSizeName API中的枚举名称设置固定。默认设置是使用北美字母大小的固定,该值被忽略，例如可以使用iso_a4和ISO_A4的值(纸张尺寸)。
copies|1|根据javax.print.attribute.standard.Copies API设置打印份数。
sides|Sides.ONE_SIDED|根据javax.print.attribute.standard.Sides API设置单面或双面打印。
flavor|DocFlavor.BYTE_ARRAY|基于javax.print.DocFlavor API设置DocFlavor(服务格式化打印数据)。
mimeType|AUTOSENSE|设置javax.print.DocFlavor API支持的mimeTypes(MIME媒体类型)。
mediaTray|AUTOSENSE|Camel 2.11.x,设置javax.print.DocFlavor API支持的MediaTray。
printerPrefix|null|Camel 2.11.x,设置打印机的前缀名称，当打印机名称不以//hostname/printer开始时，这很有用。
sendToPrinter|true|将此选项设置为false可防止将打印数据发送到打印机。
orientation|portrait|Camel 2.13.x,设置页面方向。可能的值：portrait,landscape,reverse-portrait or reverse-landscape，基于javax.print.attribute.standard.OrientationRequested。

>javax.print API帮助文档链接：[http://www.oschina.net/uploads/doc/javase-6-doc-api-zh_CN/javax/print/package-summary.html](http://www.oschina.net/uploads/doc/javase-6-doc-api-zh_CN/javax/print/package-summary.html)

## 示例

### 本地配置打印机
Windows10系统

![](https://i.imgur.com/j1YIBys.png)

### 示例一：打印文本

#### Blueprint DSL 配置

```xml
<route>
    <!--读取Data文件夹下的文件-->
	<from uri="file:H:/Data?delay=60s&delete=true"/>
    <!--localhost/default为本地默认打印机，copies=1指打印一份，flavor=DocFlavor.INPUT_STREAM指服务格式为输入流，mimeType=AUTOSENSE表示自动获取MIME媒体类型,mediaSize=ISO_A4指纸张大小为A4--> 
	<to uri="lpr://localhost/default?copies=1&flavor=DocFlavor.INPUT_STREAM&mimeType=AUTOSENSE&mediaSize=NA_LETTER&mediaSize=ISO_A4"/>  
    <to uri="log:Test"/>	  
</route>
```

### 示例二：打印GIF格式图片

#### Blueprint DSL 配置

```xml
<route>
    <!--读取Data文件夹下的文件-->
	<from uri="file:H:/Data?delay=60s&delete=true"/>
    <!--localhost/default为本地默认打印机，copies=1指打印一份，mediaSize=ISO_A4指纸张大小为A4,flavor=DocFlavor.INPUT_STREAM指服务格式为输入流，mimeType=GIF表示MIME媒体类型为GIF--> 
	<to uri="lpr://localhost/default?copies=1&mediaSize=ISO_A4&flavor=DocFlavor.INPUT_STREAM&mimeType=GIF"/>  
    <to uri="log:Test"/>	  
</route>
```

### 示例分析

Printer组件提供在路由中操作打印机的功能，可作用于日志打印、数据打印、图片打印等。





