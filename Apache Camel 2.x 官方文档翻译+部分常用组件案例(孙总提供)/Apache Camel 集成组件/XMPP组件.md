# XMPP组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-05*
*相关连接:[http://camel.apache.org/xmpp.html](http://camel.apache.org/xmpp.html)*

## 概述

XMPP组件实现了XMPP（Jabber）传输。
XMPP（可扩展消息处理现场协议）是基于可扩展标记语言（XML）的协议，它用于即时消息（IM）以及在线现场探测。XMPP协议采用的是客户端-服务器架构，所有从一个客户端发到另一个客户端的消息和数据都必须经过XMPP服务器转发，而且支持服务器间DNS的路由，也就是说可以构建服务器集群，XMPP的前身是一个开源组织制定的网络通信协议——Jabber，XMPP的核心是在网络上分片段发送XML流的协议，这个协议是XMPP的即时通讯指令的传递手段。

使用此组件需添加如下依赖：
```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-xmpp</artifactId>
    <version>x.x.x</version>
</dependency>
```

## URI

```
xmpp://[login@]hostname[:port][/participant][?Options]
```

该组件支持基于房间的和私人的人物对话。
该组件支持生产者和消费者（您可以从XMPP获取消息或将消息发送到XMPP）。 消费者模式支持房间启动。

## URI参数

名称|描述
----|----
room|如果指定了此选项，组件将连接到MUC（多用户聊天）。 通常，MUC的域名与登录域不同。 例如, 如果您是superman@jabber.org , 并且要加入krypton房间, 则房间的 URL 是krypton@conference.jabber.org. 组织。注意conference部分。提供整个房间JID不是必需的。 如果房间参数不包含@符号，那么域名部分将被camel发现并添加。
user|用户名（不含服务器名称）。如果未指定，则尝试匿名登录
password|密码。
resource|XMPP 资源。默认值为Camel。
createAccount|如果true，则尝试创建帐户。
participant|JID（Jabber ID）的人接收消息。 room参数优先于participant。
nickname|入房时使用昵称。如果指定了房间和昵称，则用户将使用昵称。
serviceName|您正在连接的服务的名称。 对于Google Talk，则是gmail.com。
testConnectionOnStartup|camel 2.11指定是否在启动时测试连接。 这用于确保XMPP客户端在路由启动时具有到XMPP服务器的有效连接。 如果无法建立连接，Camel会在启动时引发异常。当此选项设置为false时，Camel将尝试在生产者需要时建立“lazy”连接，并在连接建立之前轮询消费者连接。 默认是真的。
connectionPollDelay|amel 2.11轮询之间用于验证XMPP连接健康状况或尝试建立初始消费者连接的时间间隔（以秒为单位）。 如果连接失效，camel将尝试重新建立连接。 默认是10秒。
pubsub|Camel 2.15接受输入的pubsub数据包，默认为false。
doc|camel 2.15在包含传入数据包的文档形式的IN消息上设置一个doc header; 如果presence或pubsub为true，则默认为true，否则为false。
connectionConfiguration|camel 2.18：使用现有的连接配置

## 标题和设置主题或语言

Camel将消息IN消息头设置为XMPP消息的属性。 如果您需要自定义过滤标头，则可以配置HeaderFilterStategy。
如果将它们作为IN头提供，则还设置XMPP消息的主题和语言。


## 示例

示例采用Openfire服务器+spark客户端+Apache Camel XMPP组件实现功能

### 搭建Openfire服务器
Openfire是基于XMPP协议的IM的服务器端的一个实现,您可以使用它轻易的构建高效率的即时通信服务。

Openfire的安装和配置链接：[https://www.cnblogs.com/hoojo/archive/2012/05/17/2506769.html](https://www.cnblogs.com/hoojo/archive/2012/05/17/2506769.html)

打开Openfire路径的bin

![](https://i.imgur.com/fyWAhGg.png)

openfire.exe:登录Openfire管理界面
openfired.exe:启动Openfire服务器

#### Openfire管理界面

创建二个用户用于测试

![](https://i.imgur.com/Ycud4Li.png)

#### 启动Openfire服务器

![](https://i.imgur.com/AiFFYxQ.png)


### 安装spark客户端

安装spark客户端链接：[https://www.cnblogs.com/jerehedu/p/4359316.html](https://www.cnblogs.com/jerehedu/p/4359316.html)

>补充：spark登录不上解决方案[http://blog.csdn.net/oniejianjun/article/details/72673782](http://blog.csdn.net/oniejianjun/article/details/72673782)


### 示例一：发送邮件
XMPP组件-->spark客户端

#### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 			
	    <route>
            <from uri="timer://foo?period=30s"/> 
            <setBody>
                <constant>你好，Hunter。今天天气不错！！！</constant>
            </setBody>
            <!--XMMP组件生产者端点-->
            <!--发送者:admin-->
            <!--接收者：root@desktop-nj2tm7a(注意：root@desktop-nj2tm7a为用户的JID,参考图片“JID”)-->
            <!--serviceName为服务器名称，参考图片“serviceName”-->
			<to uri="xmpp://192.168.1.190:5222/root@desktop-nj2tm7a?user=admin&amp;password=qwer123456&amp;serviceName=desktop-nj2tm7a"/>
			<log message="${body}"/> 
	    </route> 			
</camelContext>
```

##### JID参考图

![](https://i.imgur.com/azlrL5p.png)

##### serviceName参考图

![](https://i.imgur.com/VDclHm2.png)

#### 示例一运行

![](https://i.imgur.com/JD5rXPS.png)

### 示例二：监听房间信息
spark客户端-->XMPP组件

#### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint"> 			
	    <route>
            <from uri="xmpp://192.168.1.190:5222?user=admin&amp;password=qwer123456&amp;serviceName=desktop-nj2tm7a"/> 
            <!--打印正文数据-->
			<to uri="class:com.shudi.databridge.test.DataSetProcessor"/>
			<log message="${body}"/> 
	    </route> 			
</camelContext>
```

#### 示例二运行

![](https://i.imgur.com/9dq0tQs.png)

### 示例分析

示例一中，向正文中添加数据，并发送到XMPP生产者端点。XMPP组件会访问Openfire服务器，最终将消息发送到spark客户端的用户。

示例二中，运用XMPP组件消费者端点监听用户房间信息。当房间的输入台有任何操作，都将会发送消息反馈给XMPP组件(示例二运行图中打印了除聊天记录以外的操作信息。当在输入台打字停顿或者震动聊天窗口时，都会有相应的操作信息反馈到camel的消息正文中)。

XMPP组件实现了XMPP协议的数据传输，使camel能够和基于XMPP协议的任何服务器进行交互。

>知识扩展：XMPP协议的原理介绍链接[http://blog.csdn.net/wbw1985/article/details/5502272](http://blog.csdn.net/wbw1985/article/details/5502272)

