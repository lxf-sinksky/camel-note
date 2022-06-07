# Apache Camel 概念介绍

了解Apache Camel 最好也是最简单的方式就是看官方文档https://camel.apache.org/docs/

这里简单介绍最基础的部分概念，也可以称之为专业名称。帮助没有接触过camel的人初步上手



## CamelContext : camel上下文

camelContext是camel启动时创建的上下文，可以用自动注入的方式访问

```java
@Autowired
private CamelContext camelContext;
```

camelContext中包含很多有用的信息和方法，可以自行去探索



## Route : 路由

Camel 可以理解为一种规则引擎，而Route就是编写规则的地方。

项目启动后，camel会加载所有路由，让其运行

一个简单路由的展示：

```groovy
from("timer:demo?").routeId("demo")
	.log("一个测试");
```

项目中如何创建路由请看  [06-Camel 路由创建](./06-Camel 路由创建.md ':include') 

## DLS : DLS语言

DSL（Domain Specified Language）领域专用语言。

常见的DLS语言：HTML

DLS特点：

- 不描述解答域，仅描述问题域
- 用于专门领域，不能用于其他领域
- 表现力有限

DSL 与通用编程语言的区别：

- DSL 供非程序员使用，供领域专家使用
- DSL 有更高级的抽象，不涉及类似数据结构的细节
- DSL 表现力有限，其只能描述该领域的模型，而通用编程语言能够描述任意的模型

Apache Camel在与SpringBoot集成时，常用XML DLS或者JAVA DLS，具体介绍可以看 [05-Camel DSL语言介绍](./05-Camel DSL语言介绍.md ':include')

Camel DLS是用于编写Route路由的语言



## Components : 组件

Apache Camel有相当多的开箱即用组件，可以帮助我们快速开发。

其中相当多的组件是我们常见的一些包封装的，比如Kafka组件，ActiveMq组件，Http组件，JPA组件，Mybatis组件等

还有一些如Google，AWS，HUAWEI 等云服务厂商提供的组件

Camel帮助我们把大多数常用的包，都封装成了组件，我们可以通过定义好的URL规则，快速的使用组件的功能。

集成服务项目中的常用的组件：

集成常用组件：[0X-Camel 集成常用组件介绍](./0X-集成常用组件介绍.md ':include')



## Exchange : 交换信息

每一个路由都有一个Exchange交换信息，用于存储数据

当路由发生传递时，交换信息会传递给下一个路由(例外情况：当使用split分割消息时，只会创建一个空的新交换信息)

交换信息中的重要组成成分：

### Headers：消息头

消息头是由一个Map<String,Object>组成

**在路由中操作Headers**

```groovy
from("timer:demo?").routeId("demo")
	// 在headers中添加一条属性
	.setHeader("name",simple("lxf"))
	// 控制台打印headers中的name
	.log("${headers.name}")
	// 删除headers中key为name的属性
	.removeHeader("name");
```

**在处理器中操作Headers**

```java
.process(new Processor() {
    @Override
    public void process(Exchange exchange) throws Exception {
        exchange.getIn().setHeader("name","lxf");
        String name = exchange.getIn().getHeader("name", String.class);
        exchange.getIn().removeHeader("name");
    }
})
```



headers可以用于存放我们的数据

也会用作部分组件的功能的一部分

比如http组件，headers可以成为我们的请求头，储存如**HttpMethod**，**Uri**等信息

### Body：消息体

消息体本质上是一个Object，但是通常都会被解析为字符串

Body只能有一个

当Body是一个具体的对象时，如果有getset方法

可以通过${body.property}的方式访问属性值

**在路由中操作Body**

```groovy
from("timer:demo?").routeId("demo")
	// 设置body
	.setBody(simple("lxf"))
	// 控制台打印body
	.log("${body}");
```

**在处理器中操作Body**

```java
.process(new Processor() {
    @Override
    public void process(Exchange exchange) throws Exception {
        exchange.getIn().setBody("lxf");
        String name = exchange.getIn().getBody(String.class);
    }
})
```

