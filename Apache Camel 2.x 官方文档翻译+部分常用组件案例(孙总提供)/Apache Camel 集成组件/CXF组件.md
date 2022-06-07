# CXF组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-21*
*相关连接:[http://camel.apache.org/cxf.html](http://camel.apache.org/cxf.html)*

## 概述

camel-cxf 组件是基于CXF的，它提供了在Camel中访问WebService的能力。 

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-cxf</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
cxf:bean:cxfEndpoint[?options] 
```
其中, cxfEndpoint表示在 Spring bean 注册表中引用bean的beanID。使用此URI格式,大多数端点详细信息在bean定义中指定。

```
cxf://someAddress[?options] 
```
其中someAddress指定 CXF 端点的地址。使用此 URI 格式, 大多数端点详细信息是用选项指定的。

对于以上任一样式, 可以按如下方式向 URI 追加选项:
`cxf:bean:cxfEndpoint?wsdlURL=wsdl/hello_world.wsdl&dataFormat=PAYLOAD `

## URI参数

名称|必填|描述
----|----|----
wsdlURL|No|WSDL的位置。默认情况下, 它是从端点地址获得的。示例: file://local/wsdl/hello.wsdl或wsdl/hello.wsdl
serviceClass|Yes|SEI(服务端点接口)类的名称。这个类可以有，但是不需要jsr 181注释。此选项仅在POJO模式下需要。 如果提供wsdlURL选项，则对于PAYLOAD和MESSAGE模式，serviceClass不是必需的。 当wsdlURL选项没有使用serviceClass时，务必提供serviceName和portName（用于Spring配置的endpointName）选项。 可以使用＃符号来引用注册表中的serviceClass对象实例。 例如。服务类=＃beanName。 CXF生产者（端点）的serviceClass应该是Java接口。从2.8开始，可以为PAYLOAD和MESSAGE模式省略wsdlURL和serviceClass选项。 省略时，可以在PAYLOAD模式下将任意XML元素放入CxfPayload的主体中，以便于CXF调度模式。
serviceName|No|此服务正在实现的服务名称, 它映射到wsdl:service@name。
endpointName|No|此服务正在实现的端口名称, 它映射到wsdl:port@name。
dataFormat|No|CXF 端点支持的数据类型消息。默认值: POJO。示例: POJO、PAYLOAD、MESSAGE
relayHeaders|No|请参阅此选项的relayHeaders选项部分的描述。 CXF端点应该沿着路由中传播消息头部。 目前仅当dataFormat = POJO时可用。
wrapped|No|CXF端点生产者将调用哪种操作。默认值: false。示例: true、 false。
wrappedStyle|No|描述如何在SOAP主体中表示参数的WSDL样式。如果值为false，CXF将选择文档-文字的未包装样式，如果值为true，CXF将选择文档-文字包装样式。默认值: Null。示例: true、 false。
setDefaultBus|No|当CXF端点自行创建总线时，将设置缺省总线。该选项在骆驼2.16以后不推荐使用defaultBus。
defaultBus|No|当CXF端点自行创建总线时, 将设置默认总线。 默认值: false。示例: true, false。
bus|No|由CXF Bus Factory创建的默认总线。 使用＃符号从注册表中引用总线对象。 被引用的对象必须是org.apache.cxf.Bus的一个实例。
cxfBinding|No|使用＃符号从注册表引用CXF绑定对象。被引用的对象必须是类的一个实例,org.apache.camel.component.cxf.CxfBinding (使用org.apache.camel.component.cxf.DefaultCxfBinding一个实例)。
headerFilterStrategy|No|使用＃符号从注册表中引用头过滤器策略对象。 被引用的对象必须 org.apache.camel.spi.HeaderFilterStrategy的一个实例（使用org.apache.camel.component.cxf.CxfHeaderFilterStrategy的一个实例）。
loggingFeatureEnabled|No|2.3中的新增功能,此选项启用 CXF 日志记录功能, 它将入站和出站 SOAP 消息写入日志。默认值: false。
defaultOperationName|No|2.4中的新增功能，此选项将设置将由调用远程服务的CxfProducer使用的默认operationName。默认值: null。示例: defaultOperationName=greetMe。
defaultOperationNamespace|No|2.4中的新增功能，此选项将设置将由调用远程服务的CxfProducer使用的默认operationNamespace。默认值: null。示例: defaultOperationNamespace=http://apache.org/hello_world_soap_http。
synchronous|No|2.5中的新功能,这个选项会让cxf端点决定使用同步或异步API来做底层的工作。 默认值为false，这意味着camel-cxf端点会默认尝试使用异步API。
publishedEndpointUrl|No|2.5中的新功能 这个选项可以覆盖从WSDL发布的endpointUrl，可以通过服务地址url加上？wsdl来访问。
properties.XXX|No|Camel 2.8：允许在CXF端点uri中设置自定义属性。 例如，设置properties.mtom-enabled = true以启用MTOM。
allowStreaming|No|该选项控制CXF组件在PAYLOAD模式（见下文）中运行时是否将传入的消息解析为DOM元素，或将有效负载保留为允许在某些情况下进行流式传输的javax.xml.transform.Source对象。
skipFaultLogging|No|在Camel 2.11。这个选项控制了PhaseInterceptorChain是否跳过记录它捕获的错误。
cxfEndpointConfigurer|No|Camel 2.11。 这个选项可以应用org.apache.camel.component.cxf.CxfEndpointConfigurer的实现，它支持以编程方式配置CXF端点。 从Camel 2.15.0开始，用户可以通过实现CxfEndpointConfigurer的configure {Serve/Client}方法来配置CXF服务器和客户端。
username|No|Camel 2.12.3新增该选项用于设置CXF客户端的用户名基本认证信息。
password|No|Camel 2.12.3新增该选项用于设置CXF客户端密码的基本认证信息。
continuationTimeout|No|Camel 2.14.0中的新增功能此选项用于设置在CXF服务器使用Jetty或Servlet传输时默认情况下可以在CxfConsumer中使用的CXF延续超时。（在驼峰2.14.0之前，CxfConsumer只是将延续超时设置为0，这意味着延续暂停操作从不超时)
cookieHandler|No|配置一个cookie处理程序来维护一个HTTP会话。

### dataformats 的描述

DataFormat|描述
----|----
POJO|pojo (普通的 java 对象) 是在目标服务器上调用的方法的 java 参数。支持协议和逻辑 jax-rpc 处理程序。
PAYLOAD|PAYLOAD是应用 CXF 端点中的消息配置后的消息有效负载 (soap:body的内容)。仅支持协议 jax-rpc 处理程序。不支持逻辑 jax-rpc 处理程序。
MESSAGE|MESSAGE是从传输层收到的原始消息。 如果您正在使用这种DataFormat，则不会触及或更改Stream，否则某些CXF拦截器将被删除，因此在camel-cxf使用者和JAX-WS处理程序不受支持后，您将看不到任何soap标题。
CXF_MESSAGE|在camel 2.8.2中, CXF_MESSAGE允许通过将传输层中的消息转换为原始 SOAP 消息来调用 CXF 拦截器的全部功能

### relayHeaders 选项的说明

从JAXWS WSDL开发人员的角度来看，有写入和写出的在线头文件。
写入标头是显式定义的标头，作为端点（如SOAP标头）的WSDL绑定协议的一部分。
写出标头是通过连线序列化的标头，但不是WSDL绑定合同的显式部分。
标头传播/过滤是双向的。
当一个路由有一个CXF端点，并且开发人员需要有标头比如SOAP头）时，沿另一个JAXWS端点消耗的路由被传播，那么relayHeaders应该被设置为true，这就是默认值。

## 示例 

### pom.xml配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.shudi.databridge</groupId>
	<artifactId>databridge-camel-test</artifactId>
	<version>1.0.0-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>databridge-camel-test Blueprint Bundle</name>
	<description>databridge-camel-test OSGi blueprint bundle project.</description>

	<dependencies>
        <!--依赖JAR-->
		<dependency>
			<groupId>org.apache.camel</groupId>
			<artifactId>camel-core</artifactId>
			<version>2.19.0</version>
		</dependency>
		<dependency>
            <groupId>org.apache.camel</groupId>
            <artifactId>camel-blueprint</artifactId> 
            <version>2.19.0</version>
        </dependency> 
		<dependency>
			<groupId>org.apache.camel</groupId>
			<artifactId>camel-cxf</artifactId>
			<version>2.16.5</version>
		</dependency>
	</dependencies>
	<build>
		<plugins>
            <!--配置wsdl生成java-->
			<plugin>
				<groupId>org.apache.cxf</groupId>
				<artifactId>cxf-codegen-plugin</artifactId>
				<version>3.1.11</version>
				<executions>
					<execution>
						<id>generate-sources</id>
						<phase>generate-sources</phase>
						<configuration>
							<fork>${cxf.codegenplugin.forkmode}</fork>
							<additionalJvmArgs>${cxf.codegen.jvmArgs}</additionalJvmArgs>
							<sourceRoot>${basedir}/target/generated/src/main/java</sourceRoot>
							<wsdlOptions>
								<wsdlOption>
									<wsdl>${basedir}/src/main/resources/META-INF/wsdl/edscxf.wsdl</wsdl>
								</wsdlOption>
							</wsdlOptions>
						</configuration>
						<goals>
							<goal>wsdl2java</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
			<plugin>
				<groupId>org.apache.felix</groupId>
				<artifactId>maven-bundle-plugin</artifactId>
				<version>2.4.0</version>
				<extensions>true</extensions>
				<configuration>
					<instructions>
						<Bundle-SymbolicName>${project.artifactId}</Bundle-SymbolicName>
						<Bundle-Version>${project.version}</Bundle-Version>
						<Export-Package>
							!*
						</Export-Package>
						<Import-Package>
							*
						</Import-Package>
					</instructions>
				</configuration>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<configuration>
					<source>1.8</source>
					<target>1.8</target>
				</configuration>
			</plugin>
		</plugins>
	</build>
</project>
```

### WSDL文件配置
在项目src/mian/resources文件夹下新增META-INF/wsdl/edscxf.wsdl，本例edscxf.wsdl

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!--所有的WSDL文档的根元素均是definitions元素。该元素封装了整个文档，同时通过其name提供了一个WSDL文档。--> 
<!--除了提供一个命名空间外，该元素没有其他作用，故不作详细描述--> 
<wsdl:definitions xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/"
                  xmlns:tns="http://edscxf.esb.eds.com" 
                  xmlns:xs="http://www.w3.org/2001/XMLSchema" 
                  xmlns:http="http://schemas.xmlsoap.org/wsdl/http/" 
                  xmlns:wsdl="http://schemas.xmlsoap.org/wsdl/" 
                  targetNamespace="http://edscxf.esb.eds.com">

<!--WSDL采用了W3C XML模式内置类型作为其基本类型系统。types元素用作一个容器，用于定义XML模式内置类型中没有描述的各种数据类型。-->
<!--当声明消息部分的有效负载时，消息定义使用了在types元素中定义的数据类型和元素。-->
<!--WebService输入和输出参数类型定义 --> 
	<wsdl:types>
		<xs:schema targetNamespace="http://edscxf.esb.eds.com">
			<xs:element name="edsCxfInput">
				<xs:complexType>
					<xs:sequence>
						<xs:element type="xs:string" name="routesName" />
						<xs:element type="xs:string" name="mqType" />
						<xs:element type="xs:string" name="params" />
					</xs:sequence>
				</xs:complexType>
			</xs:element>
			<xs:element name="edsCxfOutput">
				<xs:complexType>
					<xs:sequence>
						<xs:element type="xs:string" name="mqType" />
						<xs:element type="xs:string" name="params" />
					</xs:sequence>
				</xs:complexType>
			</xs:element>
		</xs:schema>
	</wsdl:types>

   <!--message元素描述了Web服务使用消息的有效负载。message元素可以描述输出或者接受消息的有效负载；-->
   <!--还可以描述SOAP文件头和错误detail元素的内容。-->
   <!--定义message元素的方式取决于使用RPC样式还是文档样式的消息传递。在本文中的message元素的定义，本文档使用了采用文档样式的消息传递--> 
   <!-- 输入和输出的消息定义 -->

	<wsdl:message name="edsCxfInput">
		<wsdl:part name="in" element="tns:edsCxfInput" />
	</wsdl:message>
	<wsdl:message name="edsCxfOutput">
		<wsdl:part name="out" element="tns:edsCxfOutput" />
	</wsdl:message>

    <!--portType元素定义了Web服务的抽象接口。该接口有点类似Java的接口，都是定义了一个抽象类型和方法，没有定义实现。--> 
    <!--在WSDL中，portType元素是由binding和service元素来实现的，这两个元素用来说明Web服务实现使用的-->
    <!--Internet协议、编码方案以及Internet地址-->
    <!--端口（接口）定义-->
    <wsdl:portType name="EdsCxfEndpoint">

         <!--一个portType中可以定义多个operation，一个operation可以看作是一个方法--> 
         <wsdl:operation name="EdsCxf">

         <!--同时包含input和output表明该操作是一个请求／响应模式。--> 
         <!--input表示传递到Web服务的有效负载，output消息表示传递给客户的有效负载--> 
                <wsdl:input message="tns:edsCxfInput"/>
                <wsdl:output message="tns:edsCxfOutput"/>
        </wsdl:operation>
    </wsdl:portType>

    <!--binding元素将一个抽象portType映射到一组具体协议(SOAO和HTTP)、消息传递样式、编码样式。--> 
    <!--通常binding元素与协议专有的元素和在一起使用-->
    <!-- 用于传输和编码的端口绑定- HTTP -->

    <wsdl:binding name="EdsCxfBinding" type="tns:EdsCxfEndpoint"> 
          <soap:binding transport="http://schemas.xmlsoap.org/soap/http"/> 
          <wsdl:operation name="EdsCxf">
                  <soap:operation soapAction="http://edscxf.esb.eds.com/EdsCxf" style="document"/>
                  <wsdl:input>
                  <!--use属性可以为literal:literal和encoded编码方式的差别在于参数类型是否出现在生成的Soap消息中--> 
                      <soap:body parts="in" use="literal"/>
                  </wsdl:input>
                  <wsdl:output>
                      <soap:body parts="out" use="literal"/>
                  </wsdl:output>
         </wsdl:operation>
    </wsdl:binding>

    <!--service元素包含一个或者多个port元素，其中每个port元素表示一个不同的Web服务。--> 
    <!--port元素将URL赋给一个特定的binding，甚至可以使两个或者多个port元素将不同的URL赋值给相同的binding-->
    <!-- 服务定义 --> 
    <wsdl:service name="EdsCxfEndpointService">
          <wsdl:port name="EdsCxfEndpoint" binding="tns:EdsCxfBinding">
               <!-- <soap:address location="http://localhost:9595/cxf/esb-cxf/webservices/edscxf"/> -->
         </wsdl:port>
    </wsdl:service> 
</wsdl:definitions>
```

### ReturnEdsCxf类
处理传入参数，返回数据

```java
package com.eds.esb.edscxf;

public class ReturnEdsCxf {
	
	public EdsCxfOutput getOutput(String mqType, String params) {
		EdsCxfOutput out = new EdsCxfOutput();
		out.setMqType(mqType);
		out.setParams(params);
		return out;
	}
}
```

### Blueprint DSL 配置

```xml
<!--配置cxf端点:address为访问地址，serviceClass为定义接口的类路径-->
<camel-cxf:cxfEndpoint id="edsCxfEndpoint" address="http://localhost:9595/cxf/esb-cxf/webservices/edscxf" serviceClass="com.eds.esb.edscxf.EdsCxfEndpoint"> 
    <camel-cxf:properties>  
        <entry key="dataFormat" value="POJO"/>  
    </camel-cxf:properties>
</camel-cxf:cxfEndpoint>
<!--处理类-->
<bean id="returnEdsCxf" class="com.eds.esb.edscxf.ReturnEdsCxf"/>
	
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
				
	<route>
          <!--引用上面配置的cxf端点-->
          <from uri="cxf://bean:edsCxfEndpoint"/>  
          <!--将传入参数转换为EdsCxfInput类型-->
		  <convertBodyTo type="com.eds.esb.edscxf.EdsCxfInput"/>
          <!--模拟实现业务处理，并返回数据-->			
		  <transform>
                <method bean="returnEdsCxf" method="getOutput(${body.mqType},${body.params})"/>
          </transform>
	</route> 		 	
</camelContext>	
```

### 示例运行

访问：http://localhost:9595/cxf/esb-cxf/webservices/edscxf?wsdl

![](https://i.imgur.com/cDl0Xat.png)

使用SoapUI访问：http://localhost:9595/cxf/esb-cxf/webservices/edscxf（带入参数）

![](https://i.imgur.com/sw3b2NB.png)

### 示例分析

上例使用Web服务描述语言(WSDL)来定义的WebService服务（补充:还可使用java代码定义WebService服务）。
在路由中，可以使用两种方法来配置端点。1，采用camel-cxf:cxfEndpoint标签配置cxf。2，可以直接在cxf组件端点中以url的方式配置。
配置完成后通过路由的方式发布WebService服务，当访问web服务时，就会触发整个路由的运行。

cxf组件集成Apache Camel和Apache CXF。Apache CXF是一个流行的web服务框架，它支持许多web服务标准。
cxf组件提供了在路由中发布和访问web服务的机制，让我们可以在集成环境中对cxf框架运用自如。



