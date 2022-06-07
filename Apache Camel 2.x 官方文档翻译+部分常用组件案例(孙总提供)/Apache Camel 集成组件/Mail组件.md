# Mail组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-23*
*相关连接:[http://camel.apache.org/mail.html](http://camel.apache.org/mail.html)*

## 概述

邮件组件通过Spring的邮件支持和底层的JavaMail系统提供对电子邮件的访问。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-mail</artifactId>
    <version>3.4.0</version>
</dependency>
```

## URI

### 邮件端点可以有下列URI格式之一(邮件协议，分别是SMTP、POP3或IMAP)
```
smtp://[username@]host[:port][?options] 
pop3://[username@]host[:port][?options] 
imap://[username@]host[:port][?options] 
```

### 邮件组件还支持这些协议的安全格式(通过SSL分层)
```
smtps://[username@]host[:port][?options] 
pop3s://[username@]host[:port][?options] 
imaps://[username@]host[:port][?options]
```
其中，[username@]host[:port]为邮箱服务器地址。

## 默认端口

协议|默认端口号
----|----
SMTP|25
SMTPS|465
POP3|110
POP3S|995
IMAP|143
IMAPS|993

## URI参数

名称|默认值|描述
----|----|----
host|暂无|连接到的主机名或IP地址。
port|请参见上面默认端口|连接上的TCP端口号。
username|暂无|电子邮件服务器上的用户名。
password|null|电子邮件服务器上的密码。
ignoreUriScheme|false|如果为false，Camel使用该方案确定传输协议（POP，IMAP，SMTP等）。
contentType|text/plain|邮件消息内容类型。为HTML邮件使用text/html。
folderName|INBOX|要轮询的文件夹。
destination|username@host|已废弃。改用 to 选项。
to|username@host|收件人 (邮件的接收者)。用逗号分隔多个电子邮件地址。包含特殊字符 (如 "&") 的电子邮件地址将需要以不同的方式处理-请参阅[http://camel.apache.org/how-do-i-configure-password-options-on-camel-endpoints-without-the-value-being-encoded.html](http://camel.apache.org/how-do-i-configure-password-options-on-camel-endpoints-without-the-value-being-encoded.html)。
replyTo|alias@host|从camel 2.8.4,2.9.1+起，回复收件人（回复邮件的收件人）。 用逗号分隔多个电子邮件地址。
cc|null|抄送收件人 (邮件的接收方)。用逗号分隔多个电子邮件地址。
bcc|null|密件抄送收件人 (邮件的接收者)。用逗号分隔多个电子邮件地址。
from|camel@localhost|发件人电子邮件地址。
subject|null|从骆驼2.3开始，消息被发送的主题。注意:在header中设置主题优先于此选项。
peek|true|骆驼2.11.3 / 2.12.2：仅限消费者。 在处理邮件消息之前，将javax.mail.Message标记为peek。 这仅适用于IMAPMessage消息类型。 通过使用peek邮件将不会急于在邮件服务器上标记为SEEN，这使得我们能够在Camel中发生错误处理时回滚邮件消息。
delete|false|消息处理完毕后删除。 这是通过设置邮件消息上的DELETED标志来完成的。 如果为false，则SEEN标志被设置。 从camel 2.10开始，您可以通过使用密钥删除来设置标题来覆盖此配置选项，以确定是否应该删除邮件。
unseen|true|可以配置消费者端点，以便仅处理看不见的消息（即新消息）或所有消息。 请注意，camel总是跳过删除的消息。 true的默认选项将仅过滤到看不见的消息。 POP3不支持SEEN标志，因此POP3不支持此选项; 改用IMAP。 重要提示：如果您还使用searchTerm选项，则此选项不被使用。 相反，如果你想禁用使用searchTerm的时候看不见，然后添加searchTerm.unseen = false作为一个术语。
copyTo|null|骆驼2.10：仅消费者。 处理邮件后，可以将其复制到具有给定名称的邮件文件夹中。 您可以使用带有密钥copyTo的标头覆盖此配置值，从而允许您将消息复制到在运行时配置的文件夹名称。
fetchSize|-1|设置轮询期间要消耗的最大消息数。如果邮箱文件夹中包含大量邮件, 则可以使用此方法避免邮件服务器超载。默认值-1表示没有提取大小, 所有消息都将被占用。将值设置为0是一个特殊的角情况, 其中骆驼根本不会消耗任何消息。
alternativeBodyHeader|CamelMailAlternativeBody|指定包含备用电子邮件正文的IN消息标题的密钥。 例如，如果您以text/html格式发送电子邮件，并且想要为非HTML电子邮件客户端提供备用邮件正文，请将此密钥的替代邮件正文设置为标题。
debugMode|false|在基础邮件框架上启用调试模式。默认情况下, SUN 邮件框架将调试消息记录到System.out。
connectionTimeout|30000|以毫秒为单位的连接超时。默认值为30秒。
consumer.initialDelay|1000|轮询开始前的毫秒数。
consumer.delay|60000|默认情况下, 骆驼只会在一分钟内轮询邮箱, 以避免邮件服务器超载。
consumer.useFixedDelay|false|设置为true以在轮询之间使用固定的延迟，否则使用固定的速率。 有关详细信息，请参阅JDK中的ScheduledExecutorService。
disconnect|false|camel 2.8.3/2.9:用户是否应在轮询后断开连接。如果启用此功能, 将强制骆驼在每次轮询中进行连接。
closeFolder|true|camel 2.10.4:用户是否应在轮询后关闭文件夹。将此选项设置为false , 并使disconnect=false , 然后使用者在轮询之间保持该文件夹处于打开状态。
mail.XXX|null|设置任何其他java邮件属性。例如, 如果要在使用 POP3 时设置特殊属性, 现在可以直接在URI中提供该选项, 如:mail.pop3.forgettopheaders=true。
mapMailMessage|true|camel 2.8：指定camel是否应该将收到的邮件消息映射到camel body/header。 如果设置为true，则邮件消息的正文将映射到Camel IN消息的正文，邮件标题将映射到IN标题。 如果此选项设置为false，那么IN消息包含一个原始的javax.mail.Message。 你可以通过调用exchange.getIn（）.getBody（javax.mail.Message.class）。
maxMessagesPerPoll|0|指定每次轮询要收集的最大消息数。默认情况下, 不设置最大值。可用于设置例如1000的限制, 以避免在服务器启动时下载数以千计的文件。将值设置为0或负数以禁用此选项。
javaMailSender|null|指定可插入的org.apache.camel.component.mail.JavaMailSender实例以使用自定义电子邮件实现。
ignoreUnsupportedCharset|false|发送邮件时，让Camel忽略本地JVM中不受支持的字符集的选项。 如果字符集不受支持，则charset = XXX（其中XXX代表不支持的字符集）将从内容类型中删除，并取决于平台默认值。
sslContextParameters|null|对注册表中的org.apache.camel.util.jsse.SSLContextParameters的引用。此引用将覆盖组件级别的任何已配置的 SSLContextParameters。
searchTerm|null|camel 2.11:是指javax.mail.search.SearchTerm , 它允许根据搜索条件 (如主题、正文、发送给、在特定日期后发送等) 筛选邮件。
searchTerm.xxx|null|camel 2.11：直接从端点uri配置搜索条件，uri支持org.apache.camel.component.mail.SimpleSearchTerm类定义的有限数量的术语。
sortTerm|null|骆驼2.16：你也可以在骆驼内部转换的URI上指定一个以逗号分隔的排序项列表。 例如，要按日期排序，可以使用sortTerm = reverse，date。 您可以使用com.sun.mail.imap.SortTerm中定义的任何排序术语。
postProcessAction|null|camel 2.15：指一般处理结束后，在邮箱上执行后处理任务的aorg.apache.camel.component.mail.MailBoxPostProcessAction。
skipFailedMessage|false|camel 2.15.1:如果邮件使用者无法检索给定的邮件, 则此选项允许跳过该邮件并继续检索下一封邮件。默认行为是使用者抛出一个异常, 并且没有来自该批的邮件能够被camel路由。
handleFailedMessage|false|camel 2.15.1：如果邮件使用者不能检索给定的邮件消息，那么这个选项允许消费者的错误处理程序处理引起的异常。 通过在使用者上启用网桥错误处理程序，骆驼路由错误处理程序可以处理异常。 默认行为是消费者抛出一个异常，并且批次的邮件将不能被camel路由。
dummyTrustManager|false|camel 2.17:使用虚拟安全设置来信任所有证书。只应用于开发模式, 而不应用于生产。
idempotentRepository|null|camel 2.17：一个可插入的仓库org.apache.camel.spi.IdempotentRepository，它允许从同一个邮箱进行集群消费，并让仓库协调邮件消息是否对消费者有效处理。
idempotentRepositoryRemoveOnCommit|true|camel 2.17：当使用idempotent存储库时，那么当邮件消息已经被成功处理并提交时，应该从idempotent存储库中删除消息id（默认）还是保存在存储库中。 默认情况下，它假定消息ID是唯一的，并且没有值保存在存储库中，因为邮件消息将被标记为可见/移动或删除，以防止其再次被消耗。 因此，将消息ID存储在idempotent存储库中没有什么价值。 但是这个选项允许存储消息ID，无论出于何种原因。
mailUidGenerator|暂无|camel 2.17:可插入的MailUidGenerator, 允许使用自定义逻辑生成邮件消息的UUID。

## SSL 支持

底层邮件框架提供SSL支持。 您可以通过完全指定必要的Java邮件API配置选项来配置SSL/TLS支持，也可以通过组件或端点配置提供已配置的SSLContextParameters

## 示例

下面二个示例采用QQ邮箱实现功能，在示例开始之前需要配置QQ邮箱服务器(开启POP3/SMTP等服务)。
配置QQ邮箱服务器相关链接：[https://jingyan.baidu.com/article/4f7d5712b1ac7c1a201927da.html](https://jingyan.baidu.com/article/4f7d5712b1ac7c1a201927da.html)

当POP3/SMTP服务和IMAP/SMTP服务开启成功后，会分别出现下图所示的授权码(授权码为下面示例中的password)。

![](https://i.imgur.com/YpGYezq.png)

POP3/SMTP服务和IMAP/SMTP服务扩展知识链接：[http://service.mail.qq.com/cgi-bin/help?subtype=1&&id=28&&no=331](http://service.mail.qq.com/cgi-bin/help?subtype=1&&id=28&&no=331);[http://service.mail.qq.com/cgi-bin/help?subtype=1&&id=28&&no=371](http://service.mail.qq.com/cgi-bin/help?subtype=1&&id=28&&no=371)

### 示例一：邮件发送
运用smtp协议

#### BeanProcessor类
向正文添加数据

```java
package com.shudi.databridge.test;
import java.util.Date;
import org.apache.camel.Exchange;
import org.apache.camel.Processor;
import com.shudi.databridge.utils.DateKit;

public class BeanProcessor implements Processor {
	@Override
	public void process(Exchange exchange) throws Exception {
		exchange.getIn().setBody("Apache Camel Mail 测试 --发送时间： "+DateKit.dateToString(new Date(), DateKit.DEFAULT_DATA_TIME_PATTERN));
	}
}
```

#### Blueprint DSL 配置
```xml
<route>
     <!--定时器，repeatCount=1表示定时器只触发一次-->
     <from uri="timer://foo?repeatCount=1"/>  
     <!--引用class组件向交换中添加消息-->
     <to uri="class:com.shudi.databridge.test.BeanProcessor"/>
     <!--设置消息头，subject为邮件主题-->
	 <setHeader headerName="subject">  
          <constant>Mail组件测试</constant>  
     </setHeader>
     <!--引用Mail组件中的smtp协议进行邮件发送-->
     <!--smtp.qq.com为QQ邮箱发送邮件服务器-->
     <to uri="smtp://smtp.qq.com?port=587&amp;username=1450802525@qq.com&amp;password=ooidjcnaueazgijj&amp;from=1450802525@qq.com&amp;to=280564872@qq.com,1390191040@qq.com"/> 
	 <log message="fang.${body}"/>
</route> 
```

#### 示例运行
查看邮箱

![](https://i.imgur.com/60SAkAJ.png)

### 示例二：邮件接收
运用imap协议

#### DataSetProcessor类
打印邮件主题和内容

```java
package com.shudi.databridge.test;
import org.apache.camel.Exchange;
import org.apache.camel.Processor;

public class DataSetProcessor implements Processor{

	@Override
	public void process(Exchange exchange) throws Exception {
 		   System.out.println("Mail组件测试打印---->："+exchange.getIn().getBody(String.class));
	}
}
```

#### Blueprint DSL 配置

```xml
<route>
     <!--运用Mail组件监控邮件服务器，imap.qq.com为QQ邮箱接收邮件服务器-->
     <from uri="imap://imap.qq.com?port=143&amp;username=1450802525@qq.com&amp;password=ooidjcnaueazgijj"/>  
     <to uri="class:com.shudi.databridge.test.DataSetProcessor"/>
	 <log message="fang.${body}"/>
</route> 
```

#### 示例运行
向示例中的邮箱回复邮件

![](https://i.imgur.com/60UCtJr.png)

### 示例分析

示例中运用IMAP/SMTP协议完成了邮件的接收/发送功能。
在接收邮件时imap端点为轮询消费者模式，所以会按规定的时间去访问邮件服务器。当接收到邮件后，邮箱客户端的相应邮件也被标记为已读邮件。当然对于接收邮件还有更多的处理，需要大家去了解。
Mail组件提供一些列的协议服务来实现对邮件的操作,使集成开发更加的便捷。









