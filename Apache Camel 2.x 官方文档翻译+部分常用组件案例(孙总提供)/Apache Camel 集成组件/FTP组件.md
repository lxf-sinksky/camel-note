# FTP/SFTP/FTPS 组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-22*
*相关连接:[http://camel.apache.org/ftp2.html](http://camel.apache.org/ftp2.html)*

## 概述

此组件通过 FTP 和 SFTP 协议提供对远程文件系统的访问。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-ftp</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
ftp|sftp|ftps://[username@]hostname[:port]/directoryname[?options]
```
其中directoryname表示基础目录。目录名是相对路径。绝对路径是不支持的。相对路径可以包含嵌套的文件夹。
[username@]hostname[:port]表示ftp服务器端口地址。

## URI参数

名称|默认值|描述
----|----|----
username|null|指定用于登录到远程文件系统的用户名。
password|null|指定用于登录到远程文件系统的密码。
account|null|指定用于登录到远程FTP服务器的帐户（仅用于FTP和FTP安全）。
binary|false|指定文件传输模式, 二进制或 ASCII。默认为 ASCII (false)。
disconnect|false|使用后是否立即从远程FTP服务器断开连接。 可以用于消费者和生产者。 断开连接只会断开与FTP服务器的当前连接。 如果你有一个你想要停止的消费者，那么你需要停止消费者路线。
localWorkDirectory|null|在使用时，可以使用本地工作目录将远程文件内容直接存储在本地文件中，以避免将内容加载到内存中。 如果你消耗一个非常大的远程文件，这可以节省内存，这是有益的。
passiveMode|false|仅 FTP 和 FTPS: 指定是否使用被动模式连接。默认为活动模式 (false)。
securityProtocol|TLS|仅 FTPS:设置基础安全协议。定义了以下值:TLS-安全传输层协议,SSL安全套接字层协议。
disableSecureDataChannelDefaults|false|camel 2.4: 仅 FTPS: 使用安全数据传输时是否禁用execPbsz和execProt的默认值。如果要完全控制应使用的选项execPbsz和execProt , 则可以将此选项设置为true 。
download|true|camel 2.11:FTP 使用者是否应下载该文件。如果此选项设置为false, 则邮件正文将为null, 但使用者仍将触发一个具有文件详细信息 (如文件名、文件大小等) 的骆驼交换。只是文件不会被下载。
streamDownload|false|骆驼2.11：消费者是否应该预先下载整个文件，默认行为是应该从远程资源读取InputStream而不是内存数组，作为Camel Exchange的主体。 如果下载为false或提供localWorkDirectory，则忽略此选项。 该选项对于处理大型远程文件很有用。
execProt|null|camel 2.4: 仅 FTPS: 如果安全数据通道默认值未被禁用, 则默认情况下使用选项 。可能的值为:C: 清除;S: 安全 (仅限 SSL 协议);E: 机密 (仅限 SSL 协议);P: 专用。
execPbsz|null|Camel 2.4：仅限FTPS：此选项指定安全数据通道的缓冲区大小。 如果启用了选项useSecureDataChannel并且该选项没有被明确设置，则使用值0。
isImplicit|false|仅限FTPS：设置安全模式（隐式/显式）。 默认是显式的（false）。
knownHostsFile|null|仅 SFTP:设置known_hosts文件, 以便 SFTP 端点可以执行主机密钥验证。
useUserKnownHostsFile|true|SFTP onlly：camel 2.18：如果knownHostFile没有被明确配置，那么使用System.getProperty(user.home)/.ssh /known_hosts中的主机文件。
knownHostsUri|null|仅 SFTP:camel 2.11.1:设置known_hosts文件 (默认情况下从类路径加载), 以便 SFTP 端点可以执行主机密钥验证。
keyPair|null|仅限SFTP：Camel 2.12.0：设置用于SSH公共钥匙认证的Java KeyPair，它支持DSA或RSA密钥。
privateKeyFile|null|仅 SFTP:将私钥文件设置为 SFTP 端点可以执行私钥验证。
privateKeyUri|null|仅 SFTP:camel 2.11.1:将私钥文件 (默认情况下从类路径加载) 设置为 SFTP 端点可以执行私钥验证。
privateKey|null|仅SFTP：骆驼2.11.1：将私钥设置为byte []，以使SFTP端点可以进行私钥验证。
privateKeyFilePassphrase|null|仅 SFTP:已废弃:改用privateKeyPassphrase 。将私钥文件的口令设置为 SFTP 端点可以执行私钥验证。
privateKeyPassphrase|null|仅 SFTP:骆驼 2.11.1:将私钥文件的口令设置为 SFTP 端点可以执行私钥验证。
preferredAuthentications|null|仅限SFTP：camel 2.10.7,2.11.2,2.12.0：设置SFTP端点将使用的首选认证。如果未指定，将使用JSCH的默认列表。
ciphers|null|Camel 2.8.2，2.9：仅限SFTP设置将按优先顺序使用的以逗号分隔的密码列表。 可能的密码名称由JCraft JSCH定义。 一些例子包括：aes128-ctr，aes128-cbc，3des-ctr，3des-cbc，blowfish-cbc，aes192-cbc，aes256-cbc。 如果未指定，将使用JSCH的默认列表。
fastExistsCheck|false|camel 2.8.2，2.9：如果设置这个选项为true，camel-ftp将直接使用列表文件来检查文件是否存在。 由于某些FTP服务器可能不支持直接列出文件，如果选项为false，camel-ftp将使用旧的方式列出目录并检查文件是否存在。 从camel 2.10.1开始注意，这个选项也影响readLock = changed来控制是否执行快速检查来更新文件信息。 如果FTP服务器有很多文件，这可以用来加速进程。
strictHostKeyChecking|no|仅 SFTP:骆驼 2.2:设置是否使用严格的主机密钥检查。可能的值为:no、yes和ask。ask没有意义, 注意:camel 2.1 以下的默认值是ask。
maximumReconnectAttempts|3|指定Camel在尝试连接到远程FTP服务器时执行的最大重新连接尝试次数。 使用0来禁用此行为。
reconnectDelay|1000|毫秒延迟camel将在执行重新连接尝试之前等待。
connectTimeout|10000|camel 2.4：连接超时毫秒。 这对应于使用FTP/FTPS的ftpClient.connectTimeout。 对于SFTP，在尝试连接时也使用此选项。
soTimeout|null/30000|仅限FTP和FTPS：camel 2.4：以毫秒为单位的SocketOptions.SO_TIMEOUT值。在SFTP上，此选项在JSCH Session实例上设置为超时。从camel 2.16开始，默认值是300000（300秒）。
timeout|30000|仅限FTP和FTPS:camel 2.4:是毫秒的数据超时。这对应于对 FTP/FTPS 使用ftpClient.dataTimeout 。对于SFTP, 没有数据超时。
throwExceptionOnConnectFailed|false|camel 2.5:如果无法建立成功的连接和登录, 是否引发异常。这使得自定义pollStrategy可以处理异常, 例如, 停止消费者。
siteCommand|null|仅FTP和FTPS：camel 2.5：成功登录后执行站点命令。 多个站点命令可以使用新的行字符（\n）分隔。 使用帮助站点查看您的FTP服务器支持的站点命令。
stepwise|true|camel 2.6：是否应该使用逐步遍历目录。 Stepwise的意思是它一次会对一个目录进行CD。 在下面看到更多细节。 如果你不能使用这种方法，你可以禁用它。
separator|UNIX|camel 2.6：指定在上传文件时使用什么路径分隔字符。 Auto=使用提供的路径而不改变它。 UNIX =使用unix样式路径分隔符。 Windows =使用Windows样式路径分隔符。camel 2.15.2: 默认值为Auto。
chmod|null|仅 SFTP 生产者:camel 2.9:允许您在存储的文件上设置 chmod。例如chmod=640。
compression|0|仅SFTP：camel 2.8.3/2.9：使用压缩。 指定从1到10的级别。重要：您必须手动将所需的JSCH zlib JAR添加到类路径以获得压缩支持。
receiveBufferSize|32768|仅 FTP/FTPS:camel 2.15.1:用于下载文件的缓冲区大小。默认大小为32kb。
ftpClient|null|仅限FTP和FTPS:camel 2.1:允许您使用自定义org.apache.commons.net.ftp.FTPClient实例。
ftpClientConfig|null|仅限 FTP 和 FTPS:camel 2.1:允许您使用自定义org.apache.commons.net.ftp.FTPClientConfig实例。
ftpClientConfig.XXX|暂无|仅限 FTP 和 FTPS:从 uri 配置 FTPClient 实例上的各种选项。例如: ftpClientConfig.receiveDataSocketBufferSize=8192&ftpClientConfig.sendDataSocketBufferSize=8192。
serverAliveInterval|0|仅SFTP:camel 2.8允许您设置 sftp 会话的 serverAliveInterval。
serverAliveCountMax|1|仅SFTP:camel 2.8允许您设置 sftp 会话的 serverAliveCountMax。
ftpClient.trustStore.file|null|仅 FTPS:设置信任存储文件, 以便 FTPS 客户端可以查找受信任的证书。
ftpClient.trustStore.type|JKS|仅 FTPS:设置信任存储类型。
ftpClient.trustStore.algorithm|SunX509|仅 FTPS:设置信任存储算法。
ftpClient.trustStore.password|null|仅 FTPS:设置信任存储区密码。
ftpClient.keyStore.file|null|仅 FTPS:设置密钥存储文件, 以便FTPS客户端可以查找专用证书。
ftpClient.keyStore.type|JKS|仅 FTPS:设置密钥存储类型。
ftpClient.keyStore.algorithm|SunX509|仅 FTPS:设置密钥存储算法。
ftpClient.keyStore.password|null|仅 FTPS:设置密钥存储密码。
ftpClient.keyStore.keyPassword|null|仅 FTPS:设置私钥密码。
sslContextParameters|null|仅 FTPS:camel 2.9:对注册表中的org.apache.camel.util.jsse.SSLContextParameters的引用。此引用将覆盖ftpClient上任何配置的与ssl相关的选项以及在FtpsConfiguration上设置的securityProtocol(ssl、TLS 等)。请参见使用 JSSE 配置实用程序。
proxy|null|仅 SFTP:camel 2.10.7, 2.11.1:对注册表中的com.jcraft.jsch.Proxy的引用。此代理用于使用/发送来自目标 SFTP 主机的消息。
useList|true|仅限 FTP/FTPS:camel 2.12.1:用户是否应使用FTP列表命令检索目录列表以查看哪些文件存在。如果此选项设置为false, 则必须配置stepwise=false , 并且fileName必须配置为固定名称, 因此使用者知道要检索的文件的名称。执行此项时, 只能检索单个文件。
ignoreFileNotFoundOrPermissionError|false|camel 2.12.1：消费者是否应该忽略当一个文件被试图检索，但不存在（由于某种原因），或失败，由于文件权限不足的错误。 camel 2.14.2：这个选项现在也适用于目录。
sendNoop|true|camel 2.16：只有生产者。 是否在将文件上载到FTP服务器之前发送noop命令作为预写入检查。 这是默认启用的，因为验证连接仍然有效，允许以无提示的方式重新连接以便能够上传文件。 但是，如果这导致问题，您可以关闭此选项。
jschLoggingLevel|WARN|仅 SFTP:camel 2.15.3/2.16:要用于 JSCH 活动日志记录的日志级别。由于JSCH在默认情况下是详细信息级别, 因此阈值将在默认情况下发出警告。
bulkRequest|暂无|仅SFTP:camel 2.17.1:指定在任一时间可能未完成的请求数。增加此值可能会略微提高文件传输速度, 但会增加内存使用。
disconnectOnBatchComplete|false|camel 2.18:在批处理完成后是否断开与远程FTP服务器的连接。可用于消费者和生产者。断开连接只会断开与FTP服务器的当前链接。如果您有想要停止使用的用户, 则需要停止消费者/路由。
activePortRange|暂无|camel 2.18：设置客户端端口范围在主动模式。 语法是：minPort-maxPort。 两个端口号都是包含的，例如10000-19999包括所有的1xxxx端口。

### Message Headers
下面的消息头可以用来影响组件的行为

Header|描述
----|----
CamelFileName|指定发送到端点时用于输出消息的输出文件名（相对于端点目录）。如果这不存在，也没有表达式，则生成的消息ID被用作文件名。
CamelFileNameProduced|写入的输出文件的实际文件路径（路径+名称）。这个头文件是由Camel设置的，其目的是为最终用户提供写入的文件的名称。
CamelBatchIndex|这批文件中正在使用的文件总数的当前索引。
CamelBatchSize|这批文件中正在使用的文件总数。
CamelFileHost|远程主机名。
CamelFileLocalWorkPath|本地工作文件的路径 (如果使用本地工作目录)。

### 此外，ftp/ftps消费者和生产者将使用以下的头信息来丰富骆驼消息

Header|描述
----|----
CamelFtpReplyCode|camel 2.11.1:FTP客户端应答代码 (类型为整数)。
CamelFtpReplyString|camel 2.11.1:FTP客户端应答字符串。

## 示例

#### 配置FTP服务器

在测试下面的示例时，需要配置FTP服务器。
配置FTP服务器参考链接：[https://jingyan.baidu.com/article/0bc808fc408fa91bd585b94f.html](https://jingyan.baidu.com/article/0bc808fc408fa91bd585b94f.html)

#### Blueprint DSL 配置
```xml
<blueprint xmlns="http://www.osgi.org/xmlns/blueprint/v1.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:cm="http://aries.apache.org/blueprint/xmlns/blueprint-cm/v1.0.0"
           xsi:schemaLocation="
           http://www.osgi.org/xmlns/blueprint/v1.0.0 http://www.osgi.org/xmlns/blueprint/v1.0.0/blueprint.xsd">
    
	<camelContext xmlns="http://camel.apache.org/schema/blueprint">  
		<!--FTP组件测试路由一-->
		<route>
             <!--读取FTP服务器文件-->
		     <from uri="ftp://192.168.1.190:8181/Test"/>
             <to uri="file:H:/k3"/>
		</route>
		<!--FTP组件测试路由二
        <route>
             <!--写入FTP服务器文件-->
		     <from uri="file:H:/k3"/>
             <to uri="ftp://192.168.1.190:8181/Test"/>
		</route>
        -->
	</camelContext>

</blueprint>
```

#### 示例分析

当运行上面的路由后，FTP服务器Test文件下的所有文件将会读取到K3文件夹下。而且当Test文件下有了新文件时，新文件会立刻读取到K3文件夹下。所以FTP组件和File组件都是轮询消费者模式。
FTP组件集成了File组件的所有特性和配置选项，并增加了许多新的选项。而FTP组件主要的功能是远程文件的读写操作。





