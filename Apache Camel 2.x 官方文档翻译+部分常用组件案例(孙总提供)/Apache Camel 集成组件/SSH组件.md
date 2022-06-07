# SSH组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-12-05*
*相关连接:[http://camel.apache.org/ssh.html](http://camel.apache.org/ssh.html)*

## 概述

SSH组件允许访问SSH服务器，这样您就可以发送一个SSH命令，并处理响应。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-ssh</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
ssh:[username[:password]@]host[:port][?options]
```

## URI参数

名称|默认值|描述
----|----|----
host|null|SSH服务器的主机名。
port|22|SSH服务器的端口。
username|null|使用SSH服务器进行身份验证的用户名。
password|null|用于使用 SSH 服务器进行身份验证的密码。如果keyPairProvider为null, 则使用。
keyPairProvider|null|引用用于为身份验证加载密钥的org.apache.sshd.common.KeyPairProvider 。如果使用此选项, 则不使用password。
keyType|ssh-rsa|引用从keyPairProvider加载的键类型。关键类型可以例如"ssh-rsa"或"ssh-dss"。
certResource|null|camel 2.11:公用密钥证书的路径引用。带有classpath:、file:或http:的前缀路径。
certFilename|null|已弃用：请改用certResource 。引用在基于文件的keyPairProvider中使用的文件名。
timeout|30000|超时连接到SSH服务器之前等待毫秒数。

## Consumer Only Options

名称|默认值|描述
----|----|----
initialDelay|1000|轮询SSH服务器开始之前的毫秒数。
delay|500|在下次轮询SSH服务器之前的毫秒数。
useFixedDelay|true|控制是否使用固定延迟或固定速率。有关详细信息, 请参见JDK中的ScheduledExecutorService。
pollCommand|null|在每个轮询周期中，命令发送到SSH服务器。。您可能需要用一个换行符结束命令, 并且必须是 URL 编码的%0A。

## 示例

### 生产者端点用法

下面是 XML DSL 中的一个示例。请注意, 该命令具有xml编码的换行符(&#10;)。

#### Blueprint DSL 配置

```xml
<route id="camel-example-ssh-producer">
     <from uri="direct:exampleSshProducer"/>
     <!--向正文中添加SSH命令-->
     <setBody>
         <constant>features:list&#10;</constant>
     </setBody>
     <to uri="ssh://karaf:karaf@localhost:8101"/>
     <log message="${body}"/>
</route>
```

### 身份验证

ssh 组件可以使用以下两种机制之一对远程 ssh 服务器进行身份验证: 公钥证书或用户名/密码。配置SSH组件进行身份验证的方式取决于如何设置和选择哪些选项。

1. 首先, 它将查看是否已设置了certResource选项, 如果是, 则使用它来定位所引用的公钥证书, 并将其用于身份验证。
2. 如果未设置certResource , 它将查看是否已设置了keyPairProvider , 如果是, 它将使用该方法来进行基于证书的身份验证。
3. 如果没有设置certResource或keyPairProvider , 则它将使用username和password选项进行身份验证。

#### Blueprint DSL 配置

```xml
<route>
  <from uri="ssh:/ //scott@localhost:8101?certResource=classpath:test_rsa&amp;useFixedDelay=true&amp;delay=5000&amp;pollCommand=features:list%0A"/>
  <log message="${body}"/>
</route>
```
 
#### 证书依赖项

如果使用基于证书的身份验证, 则需要添加一些附加的运行时依赖项。

```xml
<dependency>
  <groupId>org.apache.sshd</groupId>
  <artifactId>sshd-core</artifactId>
  <version>0.8.0</version>
</dependency>
<dependency>
  <groupId>org.bouncycastle</groupId>
  <artifactId>bcpg-jdk15on</artifactId>
  <version>1.47</version>
</dependency>
<dependency>
  <groupId>org.bouncycastle</groupId>
  <artifactId>bcpkix-jdk15on</artifactId>
  <version>1.47</version>
</dependency>
```






