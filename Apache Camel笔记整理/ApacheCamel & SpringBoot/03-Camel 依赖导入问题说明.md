# 依赖导入

Apache Camel与Spring Boot及集成

需要导入以下依赖：

```xml
<!--版本控制-->
<properties>
	<camel.version>3.17.0</camel.version>
</properties>
...

<!--Apache Camel 主依赖-->
<dependency>
	<groupId>org.apache.camel.springboot</groupId>
	<artifactId>camel-spring-boot-starter</artifactId>
	<version>${camel.version}</version>
</dependency>

<!--按需导入的其他组件依赖-->
<!--camel sql组件-->
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-sql</artifactId>
    <version>${camel.version}</version>
</dependency>

...
```

## 注意事项：

camel依赖不管是主依赖还是组件依赖，都有对应的下面的两种依赖

- org.apache.camel.springboot

- org.apache.camel



主依赖建议使用org.apache.camel.springboot依赖

但是组件依赖则建议使用org.apache.camel依赖



如果你的camel版本为低版本，则可以使用org.apache.camel.springboot

如果你的camel版本很新，那么使用org.apache.camel.springboot可能会提示找不到依赖，因为org.apache.camel.springboot的依赖官方上传到Maven中央仓库会慢上一些



org.apache.camel.springboot的好处是可以在application.yml中写入一些配置，但是这通常不是必须的

