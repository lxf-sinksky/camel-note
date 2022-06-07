# JDBC组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-24*
*相关连接:[http://camel.apache.org/jdbc.html](http://camel.apache.org/jdbc.html)*

## 概述

jdbc组件使您能够通过jdbc访问数据库, 其中SQL查询 (SELECT)和操作(插入、更新等)在消息正文中发送。此组件使用标准的jdbc API, 与使用 spring jdbc的SQL组件组件不同。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-jdbc</artifactId>
    <version>x.x.x</version>
</dependency>
```

注：此组件只能用于定义生产者端点, 这意味着不能在from()语句中使用JDBC组件。

## URI

```
jdbc:dataSourceName[?options] 
```

其中,dataSourceName为注册表中数据源ID。

## URI参数

名称|默认值|描述
----|----|----
readSize|0|轮询查询可以读取的默认最大行数。 默认值是0。
statement.<xxx>|null|Camel 2.1：在后台用来执行查询的java.sql.Statement上设置附加选项。 例如，statement.maxRows = 10。 有关详细的文档，请参阅java.sql.Statement javadoc文档。
useJDBC4ColumnNameAndLabelSemantics|true|Camel 2.2：设置是否使用JDBC 4/3列标签/名称语义。 如果您在使用JDBC驱动程序选择数据时遇到问题，可以使用此选项将其设置为false。 这仅适用于使用SQL SELECT使用别名（例如，SQL SELECT id作为标识符，name作为given from person）的情况。
resetAutoCommit|true|Camel 2.9：如果为true，则Camel会将JDBC连接上的autoCommit设置为false，执行语句后提交更改，并在最后重置连接的autoCommit标志。 如果JDBC连接不支持重置autoCommit标志，请将其设置为false。当与XA事务一起使用时，您很可能需要将其设置为false，以便事务管理器负责提交此tx。
allowNamedParameters|true|Camel 2.12:是否允许在查询中使用命名参数。
prepareStatementStrategy|暂无|Camel 2.12:允许插件使用自定义org.apache.camel.component.jdbc.JdbcPrepareStatementStrategy来控制查询和准备语句的准备工作。
useHeadersAsParameters|false|Camel 2.12：将此选项设置为true以使用具有命名参数的prepareStatementStrategy。 这允许使用命名占位符来定义查询，并使用包含查询占位符的动态值的标题。
outputType|SelectList|Camel 2.12.1：输出类型='SelectList'，为消费者或生产者，将输出一个映射列表。 SelectOne将以下列方式输出单个Java对象：a）如果查询只有单列，则返回JDBC Column对象。 （如SELECT COUNT（*）FROM PROJECT将返回一个Long对象。b）如果查询有多个列，那么它将返回该结果的映射。c）如果outputClass被设置，那么它将通过调用与列名匹配的所有设置器将查询结果转换成Java bean对象。 它会假设你的类有一个默认的构造函数来创建实例。 从驼峰2.14开始，SelectList也被支持。d）如果查询结果不止一行，则会抛出一个非唯一的结果异常。Camel 2.14.0：使用Iterator <Map <String，Object>流式查询结果的新StreamList输出类型值，它可以与Splitter EIP一起使用。
outputClass|null|Camel 2.12.1：当outputType = SelectOne时，指定完整的包和类名作为转换。 从Camel 2.14开始，SelectList也被支持。
beanRowMapper|暂无|Camel 2.12.1：在使用outputClass时使用自定义的org.apache.camel.component.jdbc.BeanRowMapper。 默认实现将小写行名称并跳过下划线和破折号。 例如“CUST_ID”被映射为“custId”。
useGetBytesForBlob|false|Camel 2.16：读取BLOB列作为字节，而不是字符串数据。 对于某些数据库（如Oracle），您可能需要这样做，您必须将BLOB列作为字节读取。

## Result

默认情况下，结果作为ArrayList < HashMap < String, Object > >在OUT主体中返回。 List对象包含行列表，Map对象包含每个以String键作为列名的行。 您可以使用选项outputType来控制结果。

注意:此组件获取ResultSetMetaData , 以便能够将列名作为Map中的键返回.

## Message Headers

Header|描述
----|----
CamelJdbcRowCount|如果查询是SELECT，则查询在此OUT Header中返回的行数。
CamelJdbcUpdateCount|果查询是UPDATE, 则查询此OUT Header中返回的更新计数。
CamelGeneratedKeysRows|camel 2.10:包含生成的键的行。
CamelGeneratedKeysRowCount|camel 2.10:包含生成的键的Header中的行数。
CamelJdbcColumnNames|camel 2.11.1:将结果集中的列名作为java.util.Set类型。
CamelJdbcParameters|Camel 2.12:一个java.util.Map，如果useHeadersAsParameters已经被启用的话，这个java.util.Map会被使用。

## 示例

### 添加依赖
```xml
<!--dbcp连接池-->
<dependency>
	<groupId>commons-dbcp</groupId>
	<artifactId>commons-dbcp</artifactId>
	<version>1.4</version>
</dependency>
<!--MySql驱动包-->
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<version>5.1.38</version>
</dependency>
```

### 示例一：查询数据

#### DataSetProcessor类
打印查询结果集
```java
package com.shudi.databridge.test;
import org.apache.camel.Exchange;
import org.apache.camel.Processor;
public class DataSetProcessor implements Processor{

	@Override
	public void process(Exchange exchange) throws Exception {
 		   System.out.println("JDBC组件测试打印---->："+exchange.getIn().getBody(String.class));
	}
}
```

#### Blueprint DSL 配置
```xml
<!--数据源配置-->
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
	 <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
	 <property name="url" value="jdbc:mysql://localhost:3306/changer?characterEncoding=UTF-8"/>
	 <property name="username" value="root"/>
	 <property name="password" value="123456"/>
</bean>
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 	
	 <route>
          <from uri="timer://foo?period=30s"/>
          <!--向正文中添加查询语句-->  
		  <setBody>
			   <constant>select * from `user`</constant>
		  </setBody>
          <!--连接jdbc端点-->
		  <to uri="jdbc:dataSource"/>
          <!--打印查询结果-->
          <to uri="class:com.shudi.databridge.test.DataSetProcessor"/>
		  <log message="fang.${body}"/>
	 </route> 	
</camelContext>	
```

#### 示例运行
![](https://i.imgur.com/TAPAyRJ.png)

### 示例二：使用命名参数查询

#### Blueprint DSL 配置
```xml
<!--注：此处省略数据源配置......-->
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	
	    <route>
            <from uri="timer://foo?period=30s"/>  
            <!--向Header中添加参数-->
			<setHeader headerName="id">
			    <constant>2</constant>
			</setHeader>
            <!--:?id格式读取Header的参数-->
			<setBody>
			     <constant>select * from `user` where id = :?id</constant>
			</setBody>
            <!--注意：useHeadersAsParameters=true一定要添加，否则无法加载命名参数。-->
			<to uri="jdbc:dataSource?useHeadersAsParameters=true"/>
            <!--打印结果集-->
            <to uri="class:com.shudi.databridge.test.DataSetProcessor"/>
			<log message="fang.${body}"/>
	    </route> 
	
</camelContext>
```

#### 示例运行
结果显示打印第二条数据，此处省略截图。

### 示例三:使用命名参数新增

```xml
<!--注：此处省略数据源配置......-->
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	
	    <route>
            <from uri="timer://foo?period=30s"/>
            <!--set查询sql-->
			<setBody>
			    <simple>select * from t_man_switch</simple>
			</setBody>
            <!--连接数据库1-->
            <to uri="jdbc:dataSource1"/>
            <!--拆分body，body类型为ArrayList<Map<Object,Object>>-->
            <split>
                <!--拆分条件-->
                <simple>${body}</simple>
                <!--CamelJdbcParameters为jdbc组件内置属性,需将参数集set到该属性中，供拼接sql语句使用-->
                <!--补充：jdbc组件不支持批量操作，所以这里拆分数据来处理-->
                <setHeader headerName="CamelJdbcParameters">
			         <simple>${body}</simple>
			    </setHeader>
                <setBody>
                     <simple>insert into t_man_code (type,code,value_display,flag_display) values (:?type,:?code,:?value_display,:?flag_display)</simple>
                </setBody>
                <!--连接数据库2-->
                <!--注意：useHeadersAsParameters=true一定要添加，否则无法加载命名参数。-->
			    <to uri="jdbc:dataSource2?useHeadersAsParameters=true"/>
            </split>
	    </route> 
</camelContext>
```

### 示例分析
通过上面的二个示例，简单的了解JDBC组件的普通查询和命名参数查询。在JDBC组件中新增，删除，修改等操作方法与查询的操作方法相同。只要修改SQL语句和参数，就能实现不同的功能。
JDBC是一个动态组件，它几乎不会向目的地发送一个消息，而是把消息体的内容作为命令来执行。这样，这些命令就是SQL语句。在企业集成模式中，这一类消息被称为命令消息。
JDBC组件也使我们在集成中对数据库操作更为简单，直接。




