# DLS语言

Apache Camel 与 Spring Boot 集成中，主要可以使用以下两种DLS语言

- JAVA DLS
- XML DLS



注意官方示例中，有写到Spring XML DLS，关于这个DLS语言，我在项目中使用失败，感兴趣可以自行研究



代码层面的区别简单展示

JAVA DLS：

```java
from("timer:demo?repeatCount=1").routeId("demo")
    .log("一个测试");
```

XML DLS：

```xml
<route id="demo">
	<from uri="timer:demo?repeatCount=1"/>
	<log message="一个测试"/>
</route>
```



## 各自的优缺点

### JAVA DLS：

优点：

- 灵活，可以通过匿名函数的方式，生成处理器，插入java代码，实现一些功能。

缺点：

- 对于choice的条件判断，由于是链式编程，所以感官上难以区分边界

### XML DLS：

优点：

- 对于choice的条件判断，由于xml是标记语言，<xx></xx>标签形式能够让choice结构相对容易观看

缺点：

- 不够灵活，想要使用java代码，只能通过创建处理器来完成。

    

## 特除的YAML DLS

实际上可以通过导入依赖，实现使用YAML DLS编写路由

```xml
<!--camel yaml dls 支持-->
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-yaml-dsl</artifactId>
    <version>${camel.version}</version>
</dependency>
```

Yaml主要配合Karavan使用



## 热部署功能

可以通过配置，让camel实现热部署能力，让XML DLS 与 YAML DLS 文件能够在编辑后实时生效

该功能在官方的文档中只建议作为方便开发而使用，不建议使用于生产环境

但是该功能可以扩展实现类似于k8s集成camel k一样，在项目上线以后，也可以灵活的添加路由，修改路由。



