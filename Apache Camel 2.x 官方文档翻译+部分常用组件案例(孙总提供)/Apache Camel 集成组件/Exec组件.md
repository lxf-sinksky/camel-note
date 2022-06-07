# Exec组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-29*
*相关连接:[http://camel.apache.org/exec.html](http://camel.apache.org/exec.html)*

## 概述

Exec组件可以用来执行系统命令。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-exec</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
exec://executable[?options]
```

其中executable是要执行的系统命令的名称或文件路径。 如果使用可执行文件名（例如：exec：java），则可执行文件必须位于系统路径中。

## URI参数

名称|默认值|描述
----|----|----
args|null|可执行文件的参数。参数可能是一个或多个空白分隔的标记, 可以用" (如参数args="arg 1" arg2引用两个变量arg 1和arg2。
workingDir|null|应该执行该命令的目录。如果是null，将使用当前进程的工作目录。
timeout|Long.MAX_VALUE|超时，以毫秒为间隔，在此之后，可执行文件将被终止。如果在超时内执行未完成，则组件将发送一个终止请求。
outFile|null|一个由可执行文件创建的文件的名称，它应该被看作是它的输出。如果没有设置输出文件，则可使用可执行文件的标准输出(stdout)。
binding|a DefaultExecBinding instance|对注册表中的org.apache.commons.exec.ExecBinding的引用。
commandExecutor|a DefaultCommandExecutor instance|引用org.apache.commons.exec.ExecCommandExecutor在注册表中,定制命令执行。默认的命令执行器利用commons-exec库，为每个执行的命令添加一个关闭操作。
useStderrOnEmptyStdout|false|一个布尔值, 指示当标准stdout为空时, 此组件将使用stderr填充骆驼消息正文。默认情况下禁用此行为 (false)。

## Message headers

名称|类型|消息|描述
----|----|----|----
ExecBinding.EXEC_COMMAND_EXECUTABLE|String|in|将要执行的系统命令的名称。重写URI中的executable。
ExecBinding.EXEC_COMMAND_ARGS|java.util.List<String>|in|要传递到执行的进程的命令行参数。这些参数是逐字使用的, 不应用引号。重写URI中的任何现有参数args。
ExecBinding.EXEC_COMMAND_ARGS|String|in|camel 2.5:可执行文件的参数作为单个字符串, 其中每个参数都是空白分隔 (请参见URI选项中的变量)，args这些参数是逐字使用的, 不应用引用。重写 URI 中的任何现有参数args。
ExecBinding.EXEC_COMMAND_OUT_FILE|String|in|由可执行文件创建的、应被视为其输出的文件名。重写URI中的任何现有outFile。
ExecBinding.EXEC_COMMAND_TIMEOUT|long|in|应终止可执行文件的超时时间 (以毫秒为单位)。重写URI中的任何现有timeout。
ExecBinding.EXEC_COMMAND_WORKING_DIR|String|in|应在其中执行命令的目录。重写URI中的任何现有workingDir。
ExecBinding.EXEC_EXIT_VALUE|int|out|这个header的值是可执行文件的退出值。 非零出口值通常表示异常终止。 请注意，退出值取决于操作系统。
ExecBinding.EXEC_STDERR|java.io.InputStream|out|此header的值指向可执行文件的标准错误流（stderr）。 如果未写入stderr，则值为空。
ExecBinding.EXEC_USE_STDERR_ON_EMPTY_STDOUT|boolean|in|指示当标准stdout为空时, 此组件将使用stderr填充骆驼消息正文。默认情况下禁用此行为(false)。

## Message body

如果Exec组件接收到可转换为in java.io.InputStream的in消息正文, 则它将用于通过其输入到可执行文件的输出。执行后,消息正文是执行的结果, 即包含标准stdout,stderr,exit value,out file的org.apache.camel.components.exec.ExecResult实例。

为了方便，该组件支持以下ExecResult类型转换器：

From|To
----|----
ExecResult|java.io.InputStream
ExecResult|String
ExecResult|byte []
ExecResult|org.w3c.dom.Document

## 示例

### 示例一:Windows系统命令

#### DataSetProcessor类
处理正文数据

```java
package com.shudi.databridge.test;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.util.HashMap;
import java.util.Map;
import org.apache.camel.Exchange;
import org.apache.camel.Processor;
import org.apache.camel.component.exec.ExecResult;

public class DataSetProcessor implements Processor {

	@Override
	public void process(Exchange exchange) throws Exception {
        //当Exec组件执行系统命令时，返回值将以ExecResult实例的方式存在于body中
		ExecResult execResult = (ExecResult) exchange.getIn().getBody();
        //解析ExecResult，获取out消息和err消息
		Map<String, InputStream> map = new HashMap<>();
		map.put("Out", execResult.getStdout());
		map.put("Err", execResult.getStderr());
        //遍历Map,打印出非空消息
		for (Map.Entry<String, InputStream> entry : map.entrySet()) {
			if (entry.getValue() == null) {
				continue;
			} else {
				System.out.println(entry.getKey() + "输出信息-->\n" + getStringBuilder(entry.getValue()));
			}
		}
	}
    
    //处理InputStream
	private StringBuilder getStringBuilder(InputStream inputStream) {
		StringBuilder stringBuilder = new StringBuilder();
		try {
			BufferedReader reader = new BufferedReader(new InputStreamReader(inputStream, "GBK"));
			String line = null;
			while ((line = reader.readLine()) != null) {
				stringBuilder.append(line + "\n");
			}
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			try {
				inputStream.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
		return stringBuilder;
	}
}

```

#### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 				
	<route>
         <from uri="timer://foo?period=30s"/>  
         <!--执行Java系统命令，查询Java版本-->
		 <!--<to uri="exec:java?args=-version"/>-->
         <!--执行windows系统命令，查询IP-->
		 <to uri="exec:cmd?args=/c ipconfig"/>
		 <to uri="class://com.shudi.databridge.test.DataSetProcessor"/>
	</route> 			
</camelContext>	
```

#### 示例运行

![](https://i.imgur.com/oEHWnam.png)

### 示例二:Linux系统命令

#### ExecProcessor处理器
处理命令参数

```java
public class ExecProcessor implements Processor {

	private static final String EXEC_COMMAND_ARGS = "CamelExecCommandArgs";

	@Override
	public void process(Exchange exchange) throws Exception {
		Map<String, Object> headersMap = exchange.getIn().getHeaders();
		String cmdStr = (String) exchange.getIn().getBody(Object.class);
		if (!StrKit.isEmpty(cmdStr)) {
			// 拆分命令参数
			String[] cmdsStr = cmdStr.split(",");
			List<String> cmdList = new ArrayList<>();
			for (String string : cmdsStr) {
				cmdList.add(string);
			}
			headersMap.put(EXEC_COMMAND_ARGS, cmdList);
		}
		exchange.getOut().setHeaders(headersMap);
		exchange.getOut().setBody(null);
	}

}
```

#### Blueprint DSL 配置

```xml
<route id="商品信息导出">
    <from uri="direct:import_goods_info"/>
	<setBody>
		<simple>
			-c,docker exec {{dumpexport.dockerName}} mysqldump -u{{dumpexport.user}} -p{{dumpexport.password}} {{dumpexport.dbName}} t_rf_shop_cy_${headers.mktid} | awk 'NR&gt;0{print}' &gt; {{dumppath.drpnptest}}t_rf_shop_cy_${headers.mktid}.sql
		</simple>
	</setBody>
	<process ref="execProcessor"/>
	<log message="正在导出 t_rf_shop_cy_${headers.mktid} 表"/>
	<to uri="exec:/bin/bash"/>
</route>
```

### 示例分析

Exec组件提供一种机制可以在路由中执行不同的系统命令，命令的返回信息作用于交换之中。






