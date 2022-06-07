# SQL组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-24*
*相关连接:[http://camel.apache.org/sql-component.html](http://camel.apache.org/sql-component.html)*

## 概述
SQL组件允许您使用JDBC查询来处理数据库。 这个组件和JDBC组件的区别在于，在SQL中，查询是端点的属性，它使用消息有效负载作为传递给查询的参数。
SQL组件在后台使用spring-jdbc进行实际的SQL处理,而JDBC组件在后台使用标准的JDBC API。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-sql</artifactId>
    <version>x.x.x</version>
</dependency>
```
SQL 组件还支持:
- 幂等消费者EIP模式基于JDBC的存储库。
- 用于聚合器EIP模式基于JDBC的存储库。

## URI
```
sql:sqlstatement[?options] 
```
其中，sqlstatement为sql语句。

从camel 2.11开始，这个组件可以创建消费者（例如`from（）`）和生产者端点（例如`to（）`）。
在以前的版本中，它只能作为一个生产者。

从Camel 2.17开始，您可以将SQL查询外部化到类路径或文件系统中的文件中，如下所示：`sql:classpath:sql/myquery.sql[?options]`

## URI参数

名称|类型|默认值|描述
----|----|----|----
batch|boolean|false|执行SQL批量更新语句。
dataSourceRef|String|null|已弃用, 将在camel 3.0 中删除:对DataSource的引用, 以便在注册表中查找。改用dataSource=#theName 。
dataSource|String|null|camel 2.11:对DataSource的引用, 以便在注册表中查找。
placeholder|String|#|camel 2.4：指定一个将被替换的字符？ 在SQL查询中。 请注意，这是简单的String.replaceAll（）操作，并且不涉及SQL解析（引用的字符串也会更改）。 只有在使用SqlComponent创建端点时才会发生这种替换。
usePlaceholder|boolean|true|Camel 2.17:设置是否使用占位符并替换所有占位符字符?在SQL查询中标志。
template.<xxx>|暂无|null|设置 Spring JdbcTemplate上用于执行查询的后台的其他选项。例如,template.maxRows=10。有关详细文档, 请参见JdbcTemplate javadoc文档。
allowNamedParameters|boolean|true|骆驼 2.11:是否允许在查询中使用命名参数。
processingStrategy|暂无|暂无|camel 2.11:仅 SQL 使用者:允许插件使用自定义org.apache.camel.component.sql.SqlProcessingStrategy在用户处理行/批次时执行查询。
prepareStatementStrategy|暂无|暂无|camel 2.11:允许插件使用自定义org.apache.camel.component.sql.SqlPrepareStatementStrategy来控制查询和准备语句的准备工作。
consumer.delay|long|500|camel 2.11:仅SQL消费者:每次轮询之间的延迟时间 (毫秒)。
consumer.initialDelay|long|1000|camel 2.11:仅SQL消费者:轮询开始前的毫秒数。
consumer.useFixedDelay|boolean|false|camel 2.11:仅SQL消费者:设置为true以在轮询之间使用固定延迟，否则使用固定的速率。 有关详细信息，请参阅JDK中的ScheduledExecutorService。
maxMessagesPerPoll|int|0|camel 2.11:仅SQL消费者:一个整数值, 用于定义每个轮询要收集的最大消息数。默认情况下, 不设置最大值。
useIterator|boolean|true|camel 2.11:仅SQL消费者:如果true , 则在单独处理轮询时返回的每一行。如果为false , 则将数据的整个java.util.List设置为正文。注意:在camel 2.15.x 或更旧的情况下, 您需要将此选项与消费者前缀 (如consumer.useIterator = true)。
routeEmptyResultSet|boolean|false|camel 2.11:仅SQL消费者:如果没有数据进行轮询，是否要进行单一的空交换。注意:在camel 2.15.x 或更旧的情况下, 您需要将此选项与消费者前缀 (如consumer.routeEmptyResultSet = true)。
onConsume|String|null|camel 2.11:仅SQL消费者:处理完每一行之后，如果交换成功处理，就可以执行该查询。
onConsumeFailed|String|null|camel 2.11:仅SQL消费者:处理完每一行之后，如果交换失败，就可以执行该查询。
onConsumeBatchComplete|String|null|camel 2.11:仅SQL消费者:在处理整个批处理之后，可以执行此查询以批量更新行等。查询不能有参数。
expectedUpdateCount|int|-1|camel 2.11:仅SQL消费者:如果使用consumer.onConsume，则可以使用此选项来设置要更新的预期行数。 通常，您可以将其设置为1以期望更新一行。
breakBatchOnConsumeFail|boolean|false|camel 2.11:仅SQL消费者:如果使用consumer.onConsume并失败, 则此选项将控制是否退出批处理, 或继续处理批次中的下一行。
alwaysPopulateStatement|boolean|false|camel 2.11:仅SQL生产者:如果启用，则始终调用org.apache.camel.component.sql.SqlPrepareStatementStrategy中的populateStatement方法，如果没有预期的参数需要准备。 当这个值为false时，只有当有一个或多个预期的参数需要设置时才会调用populateStatement。 例如，这样可以避免在没有参数的情况下读取SQL查询的消息body/headers。
separator|char|,|camel 2.11.1：从消息正文（如果主体是字符串类型）取得参数值时要使用的分隔符，要插入＃个占位符处。 注意:如果使用命名参数，则使用Map类型。
outputType|String|SelectList|camel 2.12.0：outputType ='SelectList'，为消费者或生产者，将输出一个映射列表。 SelectOne将以下列方式输出单个Java对象：a）如果查询只有单列，则返回JDBC Column对象。（如SELECT COUNT（*）FROM PROJECT将返回一个Long对象。b）如果查询有多个列，那么它将返回该结果的映射。c）如果outputClass被设置，那么它将通过调用与列名匹配的所有设置器将查询结果转换成Java bean对象。它会假设你的类有一个默认的构造函数来创建实例。d）如果查询结果不止一行，则会抛出一个非唯一的结果异常。从camel 2.14.1开始，SelectList还支持将SelectOne的每一行映射到一个Java对象（只有步骤c）。从camel 2.18开始，有一个新的StreamList outputType使用Iterator流查询的结果。它可以与流式传输模式下的Splitter EIP配合使用，以流式处理ResultSet。此StreamList不支持批处理模式，但可以使用outputClass将每一行映射到一个类。
outputClass|String|null|camel 2.12.0:指定在outputType=SelectOne时用作转换的完整包和类名。
outputHeader|String|null|camel 2.15:将结果存储为标题而不是消息正文。这允许保留现有的消息正文。
parametersCount|int|0|Camel 2.11.2/2.12.0:如果设置大于0，那么camel将使用这些参数的计数值代替通过JDBC元数据API进行查询。如果JDBC供应商不能返回正确的参数计数，那么用户可以重写该参数，这是非常有用的。
noop|boolean|false|Camel 2.12.0:如果设置true, 将忽略 SQL 查询的结果, 并使用现有的消息作为OUT消息, 以便继续处理
useMessageBodyForSql|boolean|false|Camel 2.16:是否将消息正文用作SQL，然后使用参数的标头。 如果启用此选项，则不使用uri中的SQL。 必须使用关键字CamelSqlParameters在头部提供SQL参数。 这个选项只适用于生产者。
transacted|boolean|false|camel 2.16.2：仅限SQL消费者：启用或禁用事务。如果启用，则如果处理交换失败，则消费者将中断处理任何进一步的交换，以导致回滚。


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

#### Blueprint DSL 配置
```xml
<!--配置数据源-->
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
	<property name="driverClassName" value="com.mysql.jdbc.Driver"/>
	<property name="url" value="jdbc:mysql://localhost:3306/changer"/>
	<property name="username" value="root"/>
	<property name="password" value="123456"/>
</bean>
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	
	<route>
        <!--引用SQL组件访问数据库-->
        <from uri="sql:select * from `user`?dataSource=#dataSource&amp;consumer.delay=5s"/>  	
	    <!--从交换中获取数据并打印-->
        <to uri="class:com.shudi.databridge.test.DataSetProcessor"/>
		<log message="fang.${body}"/>
	</route> 
	
</camelContext>	
```

### 示例二：使用命名参数查询

#### Blueprint DSL 配置
```xml
<!--此处省略数据源配置......-->
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	
	<route>
        <from uri="timer://foo?period=10s"/> 
        <!--向Header中添加参数--> 
        <setHeader headerName="id">
             <constant>2</constant>
        </setHeader>
        <!--连接SQL组件生产者端点-->
        <to uri="sql:select * from `user` where id = :#id?dataSource=#dataSource"/>
	    <!--从交换中获取数据并打印-->
        <to uri="class:com.shudi.databridge.test.DataSetProcessor"/>
		<log message="fang.${body}"/>
	</route> 
	
</camelContext>
```

### 示例三：使用useMessageBodyForSql属性查询数据

#### Blueprint DSL 配置
```xml
<!--此处省略数据源配置......-->
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	
	<route>
        <from uri="timer://foo?period=10s"/> 
        <!--向Header中添加参数--> 
        <!--当useMessageBodyForSql=true时，必须使用CamelSqlParameters作为参数名-->
        <!--多个参数使用","分开，SQL组件会按顺序去匹配参数-->
        <setHeader headerName="CamelSqlParameters">
             <constant>1,Hunter</constant>
        </setHeader>
        <!--向正文中添加SQL语句，参数占位符使用?-->
        <setBody>
			 <constant>select * from `user` where id = ? and name = ?</constant>
		</setBody>
        <!--连接SQL组件生产者端点-->
        <!--当useMessageBodyForSql=true时，上面正文中的SQL语句替换端点中sqlTest语句，并加载Header中的参数-->
        <to uri="sql:sqlTest?dataSource=#dataSource&amp;useMessageBodyForSql=true"/>
	    <!--从交换中获取数据并打印-->
        <to uri="class:com.shudi.databridge.test.DataSetProcessor"/>
		<log message="fang.${body}"/>
	</route> 
	
</camelContext>
```

### 示例四：使用useMessageBodyForSql属性和命名参数批量新增数据

#### Blueprint DSL 配置
```xml
<!--此处省略数据源配置......-->
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
	
	<route>
        <from uri="timer://foo?period=10s"/>
        <!--set查询sql-->
		<setBody>
			 <simple>select * from t_man_switch</simple>
		</setBody>
        <!--连接数据库1-->
        <to uri="sql:pandian?dataSource=#pandiandatasource&amp;useMessageBodyForSql=true"/>
        <!--body类型为ArrayList<Map<Object,Object>>-->
        <!--当useMessageBodyForSql=true时，使用sql内置属性CamelSqlParameters作为参数名-->
        <setHeader headerName="CamelSqlParameters">
             <simple>${body}</simple>
        </setHeader>
        <setBody>
			 <simple>insert into t_man_code (type,code,value_display,flag_display) values (:?type,:?code,:?value_display,:?flag_display)</simple>
		</setBody>
        <!--batch=true为批量新增操作-->
        <to uri="sql:drpnptest_batch?dataSource=#pandiandatasource&amp;useMessageBodyForSql=true&amp;batch=true"/>
	</route> 
	
</camelContext>
```

#### 示例分析
示例一中SQL组件作为轮询消费者查询全部数据，与JDBC组件的不同在于前者支持在组件的URI中直接写sql语句，而后者是在正文中获取。
示例二使用命名参数查询数据,SQL语句同样是在端点中传播。其他的配置和JDBC组件基本相同。
示例三使用useMessageBodyForSql属性查询数据,当添加参数和正文后，SQL组件端点sqlTest语句将会被正文中的SQL语句覆盖,重新加载端点。

通过上述的三个例子可以知道SQL组件同样执行了对数据库的操作。概述中也提到JDBC组件在后台是使用标准JDBC API，而SQL组件是使用spring-jdbc,所以在性能上JDBC组件要优于SQL组件。而对于简单的查询，SQL组件的配置更显得直接明了。

JDBC组件和SQL组件各有优缺点，如何选择？还是要取决于我们面临什么样的集成环境。








