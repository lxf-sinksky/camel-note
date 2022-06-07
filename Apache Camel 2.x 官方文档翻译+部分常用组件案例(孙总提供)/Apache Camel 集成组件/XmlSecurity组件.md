# XmlSecurity组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-05*
*相关连接:[http://camel.apache.org/xml-security-component.html](http://camel.apache.org/xml-security-component.html)*

## 概述

XmlSecurity组件用于生成和验证XML签名,如[W3C标准XML签名语法](https://www.w3.org/TR/xmldsig-core/)。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-xmlsecurity</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
xmlsecurity:sign:name[?options] or xmlsecurity:verify:name[?options]

```

- 使用signer端点，您可以为消息中的主体生成XML签名，该签名可以是XML文档，也可以是纯文本。
- 通过verifier端点，您可以验证封装的XML签名，甚至可以验证消息正文中包含的几个独立的(2.14.0)XML签名;如果验证成功，则从XML签名中提取原始内容，并将其设置为输出消息的主体。
- 用户可以选择URI中的名称部分来区分骆驼上下文中的不同签名者/验证者端点。

## URI参数

名称|类型|默认值|描述
----|----|----|----
uriDereferencer|javax.xml.crypto.URIDereferencer|null|URI解除引用。 如果要限制解引用或对解引用有特殊要求，可以在这里指定您自己的URI解引用程序。
baseUri|String|null|URI解引用器中使用的基本URI。 相对URI与基本URI连接。
cryptoContextProperties|Map<String, ? extends Object>|null|加密上下文属性。 请参见javax.xml.crypto.XMLCryptoContext.setProperty（String，Object）。 属性可以取决于提供者。 例如，JDK提供程序“XMLDSig”具有用于启用清单验证的属性“org.jcp.xml.dsig.validateManifests”。 以下属性默认设置为XML验证器的值Boolean.TRUE：“org.jcp.xml.dsig.validateManifests”，“javax.xml.crypto.dsig.cacheReference”。 如果选项secureValidation为true，那么对于XML验证器，另外将属性“org.apache.jcp.xml.dsig.secureValidation”和“org.jcp.xml.dsig.secureValidation”设置为Boolean.TRUE。 如果要关闭这些功能，必须将属性值设置为Boolean.FALSE。
disallowDoctypeDecl|Boolean|true|指示传入的XML消息中是否应禁止DTD DOCTYPE声明。
omitXmlDeclaration|Boolean|false|指示是否在输出XML消息中省略XML声明header。
clearHeaders|Boolean|true|指示是否在签名者或验证者处理结束时删除在XmlSignatureConstants中定义的XML签名消息头。
schemaResourceUri|String|null|自camel 2.14.0。 到XML模式文件的类路径。 如果设置，则XML文档将根据XML模式进行验证。 必须在分离签名的情况下设置，以确定类型ID的属性。 该值可以被头部“CamelXmlSignatureSchemaResourceUri”覆盖。 有关更多信息，请参阅“分离的XML签名作为签名元素的兄弟节”子章节。 在使用ID属性的引用URI进行封装签名的情况下，该模式也是必需的（请参阅签名选项'contentReferenceUri'）。
outputXmlEncoding|String|null|自camel 2.15.0。 输出XML文档的字符编码。 如果为空，则使用UTF-8。

## Signing Options
签名者端点具有以下选项。

名称|类型|默认值|描述
----|----|----|----
keyAccessor|KeyAccessor|null|提供签名密钥和KeyInfo实例。 有一个使用密钥库的示例实现，请参阅DefaultKeyAccessor。
addKeyInfoReference|Boolean|true|指示是否将Reference元素引用由密钥访问器提供的KeyInfo元素应添加到XML签名中。
signatureAlgorithm|String|http://www.w3.org/2000/09/xmldsig#rsa-sha1|签名算法由摘要和加密算法组成。 摘要算法用于计算SignedInfo元素的摘要，并使用加密算法对摘要进行签名。 可能的值： http://www.w3.org/2000/09/xmldsig#dsa-sha1, http://www.w3.org/2000/09/xmldsig#rsa-sha1, http://www.w3.org/2001/04/xmldsig-more#rsa-sha256, http://www.w3.org/2001/04/xmldsig-more#rsa-sha384, http://www.w3.org/2001/04/xmldsig-more#rsa-sha512。
digestAlgorithm|String|see description|用于计算消息内部摘要的摘要算法。 如果未指定，则使用签名算法的摘要算法。 可能的值：http://www.w3.org/2000/09/xmldsig#sha1，http://www.w3.org/2001/04/xmlenc#sha256，http://www.w3.org/2001 / 04 / xmldsig-more＃sha384，http://www.w3.org/2001/04/xmlenc#sha512。
parentLocalName|String|null|签名元素的父级的本地名称。签名元素将添加到父级的子项的末尾。封装的 XML 签名所必需的。如果此选项和选项parentXpath arenull, 则创建包络XML签名。另请参见选项parentNamespace。或者, 您可以通过选项 parentXpath 指定父级parentXpath。
parentNamespace|String|null|Signature元素的父级的命名空间。请参见选项parentLocalName。
parentXpath|XPathFilterParameterSpec|null|camel 2.15.0。 XPath到Signature元素的父级。 Signature元素将被添加到父级子元素的末尾。 必须有封装的XML签名。 如果此选项和选项parentLocalName为空，则将创建一个包络XML签名。 或者，您可以通过选项parentLocalName指定父级。
canonicalizationMethod|javax.xml.crypto.AlgorithmMethod|C14n|在计算摘要之前用于规范化SignedInfo元素的规范化方法。 您可以使用帮助程序方法XmlSignatureHelper.getCanonicalizationMethod（String algorithm）或getCanonicalizationMethod（String algorithm，List <String> inclusiveNamespacePrefixes）来创建一个规范化方法。
transformMethods|List<javax.xml.crypto.AlgorithmMethod>|see description|在计算摘要之前在消息正文上执行的转换。默认情况下, 添加 C14n 并在封装签名的情况下 (请参见选项parentLocalName) 也在列表的位置0添加http://www.w3.org/2000/09/xmldsig#enveloped-signature 。使用XmlSignatureHelper中的方法创建转换方法。
prefixForXmlSignatureNamespace|String|ds|XML 签名命名空间的前缀。如果指定了null或空字符串, 则不使用签名命名空间前缀。
contentReferenceUri|String|see description|对已签名内容 (消息正文) 的引用的 URI。如果为null并且我们在封装的XML签名情况下, 则 URI 被设置为 ""。如果null并且我们在包含XML签名情况下, 则 URI 被设置为 "generated_object_id", 这意味着引用指向包含消息正文的对象元素。如果不希望对完整的邮件正文进行签名, 可以使用此选项引用在邮件正文中的特定部件。此值可由标题 "CamelXmlSignatureContentReferenceUri" 覆盖。请注意, 如果要使用 xml id 属性的值 (示例: "#ID_value"), 则必须通过输入 xml 文档中包含的 doctype 定义或通过 xml 架构文档提供有关 id 属性的信息, 您可以在选项 "schemaResourceUri"。通过 XML 架构定义 ID 属性时, 选项 "schemaResourceURi" 仅在封装的签名案例中有效。如果设置了xpathsToIdAttributes 选项, 则在分离签名的情况下将忽略此选项。
contentReferenceType|String|null|内容引用的类型属性的值。这个值可以通过标题“CamelXmlSignatureContentReferenceType”覆盖。
plainText|Boolean|false|指示消息正文是否包含纯文本。 通常，签名生成器将传入消息正文视为XML。 如果消息正文是纯文本，则必须将此选项设置为true。 该值可以被头部“CamelXmlSignatureMessageIsPlainText”覆盖。
plainTextEncoding|String|null|仅在选项plainText设置为true时使用。 然后你可以指定纯文本的编码。 如果为空，则使用UTF-8。 该值可以被头部“CamelXmlSignatureMessageIsPlainTextEncoding”覆盖。
properties|XmlSignatureProperties|null|为了将额外的引用和对象添加到包含其他属性的XML签名中，可以提供一个实现XmlSignatureProperties接口的bean。
contentObjectId|String|null|对象元素的Id属性的值。 仅用于包含XML签名的情况。 如果为null，则生成唯一值。 自2.12.2起可用。
xpathsToIdAttributes|List<XPathFilterParameterSpec>|empty list|自camel 2.14.0。 XPATH表达式列表到要签名的元素的ID属性。 用于分离的XML签名。 只能与schemaResourceUri选项结合使用。 该值可以被头部“CamelXmlSignatureXpathsToIdAttributes”覆盖。 如果选项parentLocalName设置在同一时间，则抛出异常。 类XPathFilterParameterSpec具有包javax.xml.crypto.dsig.spec。
signatureId|String|null|自2.14.0。 Signature元素的Id属性的值。 如果为null，则生成唯一的Id。 如果值是空字符串（“”），那么没有Id属性被添加到Signature元素。

## Verifying Options
验证者端点有以下选项。


名称|类型|默认值|描述
----|----|----|----
keySelector|javax.xml.crypto.KeySelector|null|提供用于验证 XML 签名的密钥。有一个使用密钥库的示例实现, 请参见DefaultKeySelector。
xmlSignatureChecker|XmlSignatureChecker|null|此接口允许应用程序在执行验证之前检查 XML 签名。在http://www.w3.org/TR/xmldsig-bestpractices/#check-已签名中建议使用此步骤。
validationFailedHandler|ValidationFailedHandler|DefaultValidationFailedHandler|处理不同的验证失败情况。默认实现引发不同情况的特定异常 (所有异常都具有包名称org.apache.camel.component.xmlsecurity.api , 并且是XmlSignatureInvalidException的子。如果签名值验证失败, 将引发XmlSignatureInvalidValueException 。如果引用验证失败, 将引发XmlSignatureInvalidContentHashException 。有关详细信息, 请参阅 JavaDoc。
xmlSignature2Message|XmlSignature2Message|DefaultXmlSignature2Message|在验证后将 XML 签名映射到输出消息的 Bean。此映射的执行方式可以由选项outputNodeSearchType、 outputNodeSearch和removeSignatureElements进行配置。默认实现提供了与三输出节点搜索类型 "default"、"ElementName" 和 "XPath" 相关的三种可能性。默认实现确定一个节点, 然后将其序列化并设置为输出消息的正文。如果搜索类型是 "ElementName", 则输出节点 (在这种情况下必须是一个元素) 由搜索值中定义的本地名称和命名空间确定 (请参见选项outputNodeSearch)。如果搜索类型是 "xpath", 则输出节点由搜索值中指定的 xpath 确定 (在这种情况下, 输出节点可以是类型 "元素"、"文本" 或 "文档")。如果输出节点搜索类型是 "默认", 则适用以下规则: 在封装的 xml 签名案例中 (有一个引用与 URI = "" 和转换 "http://www.w3.org/2000/09/xmldsig#enveloped-signature"), 传入的 xml 文档没有签名元素设置为输出消息正文。在 non-enveloped XML 签名案例中, 消息正文是从引用的对象中确定的;本章将在 "包络 XML 签名情况下的输出节点确定" 一章中进行更详细的解释。
outputNodeSearchType|String|"Default"|确定输出节点的搜索类型。请参见选项xmlSignature2Message。默认实现DefaultXmlSignature2Message支持三搜索类型 "Default"、"ElementName" 和 "XPath"。
outputNodeSearch|Object|null|输出节点搜索值。类型取决于搜索类型。对于默认的搜索实现DefaultXmlSignature2Message , 可以提供以下值。如果搜索类型为 "Default", 则不使用搜索值。如果搜索类型为 "ElementName", 则搜索值包含输出元素的命名空间和本地名称。命名空间必须在方括号中接受。如果搜索类型为 "xpath", 则搜索值包含一个表示 xpath 的javax.xml.crypto.dsig.spec.XPathFilterParameterSpec实例。可以通过XmlSignatureHelper.getXpathFilter(String xpath, Map<String, String> namespaceMap)来创建这样的实例。XPath 确定可为类型元素、文本或文档的输出节点。
removeSignatureElements|Boolean|false|在封装的XML签名案例中删除输出消息中的签名元素的指示符。在xml签名2消息实例中使用。默认的实现确实使用了这两个搜索类型“ElementName”和“XPath”的指示符。
secureValidation|Boolean|true|启用安全验证。如果为 true, 则启用安全验证。

## Headers

Headers|类型|描述
----|----|----
CamelXmlSignatureXAdESQualifyingPropertiesId|String|QualifyingProperties元素的"Id"属性值。
CamelXmlSignatureXAdESSignedDataObjectPropertiesId|String|SignedDataObjectProperties元素的"Id"属性值。
CamelXmlSignatureXAdESSignedSignaturePropertiesId|String|SignedSignatureProperties元素的"Id"属性值。
CamelXmlSignatureXAdESDataObjectFormatEncoding|String|对于DataObjectFormat元素的编码元素的值。
CamelXmlSignatureXAdESNamespace|String|覆盖XAdES命名空间参数值。
CamelXmlSignatureXAdESPrefix|String|覆盖XAdES前缀参数值。

## 示例

### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 			
	    <route>
            <from uri="direct:enveloping" />
            <!--对于签名过程，需要一个私钥。您指定了一个密钥访问器bean提供此私钥。-->
            <to uri="xmlsecurity:sign://enveloping?keyAccessor=#accessor" />
            <!--对于验证，相应的公钥是必要的;你指定提供此公钥的关键选择器bean--> 
            <to uri="xmlsecurity:verify://enveloping?keySelector=#selector" />
            <to uri="mock:result" />
	    </route> 			
</camelContext>
    
```

## 封装XML签名用例的输出节点确定

验证后, 从最终返回到输出消息正文的 XML 签名文档中提取节点。在封装XML签名情况下, XmlSignature2Message的默认实现DefaultXmlSignature2Message以下列方式对节点搜索类型 "default" 执行此操作 (请参见选项xmlSignature2Message):

首先确定对象引用:
- 只考虑了相同的文档引用(URI 必须以 "#" 开头)。
- 也考虑到间接相同的文档通过清单引用对象。
- 结果的对象引用数必须是1。

然后，对象被解引用，对象只能包含一个XML元素。 该元素作为输出节点返回。

### 封装XML签名结构

```xml
<Signature>
      <SignedInfo>
         <Reference URI="#object"/>
         <!-- 进一步引用可能，但它们不能指向包含对象引用的对象或清单 -->
         ...
      </SignedInfo>
      <Object Id="object">
           <!--包含一个被提取到消息体的XML元素 -->
      <Object>
      <!-- 进一步的对象元素可能是不被引用的-->
      ...
      (<KeyInfo>)?
</Signature>
```

OR

```xml
<Signature>
      <SignedInfo>
         <Reference URI="#manifest"/>
         <!-- 进一步的引用是可能的，但是它们不能指向包含对象引用的对象或其他清单。 -->
         ...
      </SignedInfo>
 
      <Object >
         <Manifest Id="manifest">
            <Reference URI=#object/>
         </Manifest>
      </Objet>
      <Object Id="object">
          <!-- 包含被提取到消息体的DOM节点-->
      </Object>
          <!--进一步的对象元素可能是不被引用的-->
      ...
      (<KeyInfo>)?
</Signature>
```

## 分离的XML签名作为已签名元素的同级

### 分离的 XML 签名示例

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<root>
    <A ID="IDforA">
        <B>
            <C ID="IDforC">
                <D>dvalue</D>
            </C>
            <ds:Signaturexmlns:ds="http://www.w3.org/2000/09/xmldsig#"
                Id="_6bf13099-0568-4d76-8649-faf5dcb313c0">
                <ds:SignedInfo>
                    <ds:CanonicalizationMethod
                        Algorithm="http://www.w3.org/TR/2001/REC-xml-c14n-20010315" />
                    <ds:SignatureMethod
                        Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1" />
                    <ds:ReferenceURI="#IDforC">
                        ...
                    </ds:Reference>
                </ds:SignedInfo>
                <ds:SignatureValue>aUDFmiG71</ds:SignatureValue>
            </ds:Signature>
        </B>
    </A>
    <ds:Signaturexmlns:ds="http://www.w3.org/2000/09/xmldsig#"Id="_6b02fb8a-30df-42c6-ba25-76eba02c8214">
        <ds:SignedInfo>
            <ds:CanonicalizationMethod
                Algorithm="http://www.w3.org/TR/2001/REC-xml-c14n-20010315" />
            <ds:SignatureMethod
                Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1" />
            <ds:ReferenceURI="#IDforA">
                ...
            </ds:Reference>         
        </ds:SignedInfo>
        <ds:SignatureValue>q3tvRoGgc8cMUqUSzP6C21zb7tt04riPnDuk=</ds:SignatureValue>
    </ds:Signature>
<root>
```

该示例显示，您可以签署多个元素，并且对于每个元素签名被创建为兄弟。 要签名的元素必须具有类型ID的属性。 该属性的ID类型必须在XML模式中定义（请参阅选项schemaResourceUri）。 指定指向类型ID属性的XPATH表达式列表（请参阅选项xpathsToIdAttributes）。 这些属性决定了要签名的元素。 这些元素由keyAccessor bean提供的相同密钥进行签名。 首先签署具有较高（=deeper）等级的评分。 在这个例子中，元素“C”在元素“A”之前被签名。

### Blueprint DSL 配置

```xml
<beanid="xpathsToIdAttributesBean" class="java.util.ArrayList">
        <constructor-arg type="java.util.Collection">
            <list>
                <bean class="org.apache.camel.component.xmlsecurity.api.XmlSignatureHelper" factory-method="getXpathFilter">
                    <constructor-arg type="java.lang.String" value="/ns:root/a/@ID"/>
                    <constructor-arg>
                        <mapkey-type="java.lang.String" value-type="java.lang.String">
                            <entrykey="ns" value="http://test" />
                        </map>
                    </constructor-arg>
                </bean>
            </list>
        </constructor-arg>
 </bean>

<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 			
	    <route>
            <from uri="direct:enveloping"/>
            <to uri="xmlsecurity:sign://detached?keyAccessor=#keyAccessorBeant&xpathsToIdAttributes=#xpathsToIdAttributesBean&schemaResourceUri=Test.xsd"/>
            <to uri="xmlsecurity:verify://detached?keySelector=#keySelectorBean&schemaResourceUri=org/apache/camel/component/xmlsecurity/Test.xsd"/>
            <to uri="mock:result"/>
	    </route> 			
</camelContext>
```




