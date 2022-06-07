# Crypto组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-29*
*相关连接:[http://camel.apache.org/crypto-digital-signatures.html](http://camel.apache.org/crypto-digital-signatures.html)*

## 概述

Crypto组件使用Apache Camel端点和Java的加密扩展，为交换创建数字签名。Crypto组件提供了一对灵活的端点，这些端点可以用于在交换工作流程的一个部分中创建一个签名，然后在工作流的后面部分验证签名。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-crypto</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
crypto:sign:name[?options] 
crypto:verify:name[?options] 
```

- crypto:sign创建签名并将其存储在由常量`org.apache.camel.component.crypto.DigitalSignatureConstants.SIGNATURE`键入的Header中,即"CamelDigitalSignature"。
- crypto:verify将Header中"CamelDigitalSignature"的内容中读取, 并进行验证计算。

为了正确地运行，签名和验证过程需要一对密钥来共享，签名需要一个PrivateKey,而验证需要一个PublicKey(或者一个包含一个证书的证书)。使用JCE来生成这些密钥对非常简单，但是使用密钥库来存储和共享密钥通常是最安全的。DSL在提供密钥方面非常灵活，并提供了许多机制。

## URI参数

名称|类型|默认值|描述
----|----|----|----
algorithm|String|SHA1WithDSA|将使用的JCE签名算法的名称。
alias|String|null|用于从密钥库中选择密钥的别名。
bufferSize|Integer|2048|签名过程中使用的缓冲区的大小。
certificate|Certificate|null|用于验证交换有效负载的证书。此项或publicKey是必需的。
keystore|KeyStore|null|对 JCE 密钥库的引用, 用于存储用于签名和验证的密钥和证书。
keyStoreParameters(camel 2.14.1)|KeyStoreParameters|null|对KeyStoreParameters对象的引用, 它包装了Java密钥库对象。
provider|String|null|应该使用的JCE安全提供者的名称。
privateKey|PrivateKey|null|用来在交换有效载荷上签名的私钥。
publicKey|PublicKey|null|公钥用于验证交换有效负载的签名。
secureRandom|secureRandom|null|引用一个SecureRandom对象，该对象将用于初始化签名服务。
password|char[]|null|从密钥库中访问私钥的密码。
clearHeaders|String|true|在验证操作后从消息中删除骆驼加密头(值可以是“true”/“false”)。

## 示例
更改JCE提供程序和算法

>注：本例camel-crypto依赖为2.18.2，版本过低会缺失DigitalSignatureEndpoint类。

### BeanProcessor类
处理器，用于更改加密算法

```java
package com.shudi.databridge.test;

import java.security.KeyPair;
import java.security.KeyPairGenerator;
import java.security.PrivateKey;
import java.security.PublicKey;
import java.security.SecureRandom;
import org.apache.camel.Exchange;
import org.apache.camel.Processor;
import org.apache.camel.component.crypto.DigitalSignatureEndpoint;

public class BeanProcessor implements Processor {

	@Override
	public void process(Exchange exchange) throws Exception {
        //KeyPairGenerator类为钥匙生成器,返回一个对指定的算法产生公钥/私钥对的生成器对象。
		KeyPairGenerator keyGen = KeyPairGenerator.getInstance("RSA");
        //初始化参数
		keyGen.initialize(512, new SecureRandom());
		KeyPair keyPair= keyGen.generateKeyPair();
        //生成私钥
		PrivateKey privateKey = keyPair.getPrivate();
        //生成公钥
		PublicKey publicKey = keyPair.getPublic();
		//在端点实例上显式地设置键。
		exchange.getContext().getEndpoint("crypto:sign://rsa?algorithm=MD5withRSA",DigitalSignatureEndpoint.class).setPrivateKey(privateKey);
		exchange.getContext().getEndpoint("crypto:verify://rsa?algorithm=MD5withRSA&amp;clearHeaders=false",DigitalSignatureEndpoint.class).setPublicKey(publicKey);
	}

}
```

### DataSetProcessor类
打印数字签名

```java
package com.shudi.databridge.test;

import org.apache.camel.Exchange;
import org.apache.camel.Processor;

public class DataSetProcessor implements Processor{

	@Override
	public void process(Exchange exchange) throws Exception {
		System.out.println("数字签名打印-->"+exchange.getIn().getHeader("CamelDigitalSignature"));
	}
}
```

### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 
				
		<route>
            <from uri="timer://foo?period=10s"/>
            <!--加密算法扩展-->  
			<to uri="class://com.shudi.databridge.test.BeanProcessor"/>
            <!--创建存储数字签名-->
			<to uri="crypto:sign://rsa?algorithm=MD5withRSA"/>
			<to uri="direct:test"/>
	    </route> 

		<route>
            <from uri="direct:test"/>  
            <!--验证数字签名,clearHeaders=false为验证完成后不删除Header中的数字签名-->
			<to uri="crypto:verify://rsa?algorithm=MD5withRSA&amp;clearHeaders=false"/>
            <!--打印-->
			<to uri="class://com.shudi.databridge.test.DataSetProcessor"/>
	    </route> 
			
</camelContext>	
```

### 示例分析

消息通过使用私钥对消息的摘要进行加密。这个加密摘要随消息一起传送。另一方面，验证器重新计算消息摘要，并使用公钥对签名中的摘要进行解密。当验证成功则通过，失败则会抛出错误。
Crypto组件通过加密解密的方式大大提高了数据的安全性，在集成中我们可以利用该组件对数据的流入流出进行更严格的控制。





