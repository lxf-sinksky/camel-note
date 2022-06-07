# 路由创建

## 用JAVA DLS创建路由

1.新建一个java文件，TestRoute

2.继承 org.apache.camel.builder.RouteBuilder

3.实现RouteBuilder类的configure方法

4.添加@Component注解，使在项目启动时路由生效

5.在configure方法中用java dls编写route路由



```java
@Component
public class DemoRoute extends RouteBuilder {
    
    @Override
    public void configure() throws Exception {
        
        from("timer:demo1?repeatCount=1").routeId("demo1")
        	.setHeader("name", constant("lxf"))
        	.setBody(simple("${headers.name}"))
        	.to("direct:logBody")
        	.setBody(constant("${headers.name}"))
        	.to("direct:logBody");
        
        from("direct:logBody").routeId("logBody")
        	.log("${body}");
        
    }
  
}

/*

控制台日志:
lxf
${headers.name}

*/
```





## 用XML DLS创建路由

1.在resources目录下创建camel文件夹

2.在camel文件夹中创建xml文件

3.用xml dls编写路由



```xml
<?xml version="1.0" encoding="UTF-8"?>
<routes xmlns="http://camel.apache.org/schema/spring">
    <route id="demo1">
        <from uri="timer:demo1?repeatCount=1"/>
        <setHeader name="name">
            <constant>lxf</constant>
        </setHeader>
        <setBody>
            <simple>${headers.name}</simple>
        </setBody>
        <to uri="direct:logBody"/>
        <setBody>
            <constant>${headers.name}</constant>
        </setBody>
        <to uri="direct:logBody"/>
    </route>

    <route id="logBody">
        <from uri="direct:logBody"/>
        <log message="${body}"/>
    </route>
</routes>
```



## 特殊:

## 使用YAML DLS创建路由

YAML DLS 可以使用karavan可视化界面生成，

具体请看karavan DOTO

YAML DLS在ApacheCamel + SpringBoot中默认不支持，需要导入相关的依赖，

具体请看  [05-Camel DSL语言介绍](./05-Camel DSL语言介绍.md ':include')



myRoute.yaml文件同样放置于resources目录下的camel文件夹中

```yaml
- route:
    from:
      uri: timer:demo1
      steps:
        - setHeader:
            name: name
            expression:
              constant:
                expression: lxf
        - setBody:
            expression:
              simple:
                expression: ${headers.name}
        - to:
            uri: direct:logBody
        - setBody:
            expression:
              constant:
                expression: ${headers.name}
        - to:
            uri: direct:logBody
      parameters:
        delay: '0'
        period: '0'
        repeatCount: 1
    id: demo1
- route:
    from:
      uri: direct:logBody
      steps:
        - to:
            uri: log:logBody
            parameters:
              plain: true
              showBodyType: false
              showExchangePattern: false
    id: logBody
```



## 如何选择：

首先XML DLS无论如何都不是首选，YAML XML是XML的上位替代



### 如果选择使用YAML DLS

yaml文件应当使用Karavan生成，而不是手写。

则应当遵循以下编程原则：

- Processor处理器的通用性
- AggregationStrategy聚合策略的通用性
- 使用Bean组件调用注册Bena的Util工具类里的方法，代替Processor处理器



### 如果选择使用JAVA DLS

则以上规则只需尽可能的遵守即可，在 JAVA DLS中，可以用隐式函数创建Processor处理器

```java
from("timer:name?repeatCount=1")
	.process(new Processor() {
		@Override
		public void process(Exchange exchange) throws Exception {
			// java 代码
		}
	});
```

这样可以随意在路由中插入java代码，实现camel本身不好实现的功能

而在YAML DLS 与 XML DLS中 ，想要插入java代码，则必须创建处理器，通过BeanName调用处理器，如果每一个单独的小需求都需要创建处理器，则项目后期会产出各种各样的处理器，不知道是哪个路由使用。

就如同一个混乱的公共仓库，里面堆积了大量不是是谁的物品，难以管理

