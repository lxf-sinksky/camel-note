# Ref组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-03*
*相关连接:[http://camel.apache.org/ref.html](http://camel.apache.org/ref.html)*

## 概述
Ref组件用于查找在注册表中绑定的现有端点。

## URI

```
ref:someName[?options]
```

其中, someName是注册表中端点的名称 (通常但不总是 Spring 注册表)。如果您使用的是 spring 注册表, 则someName将是 spring 注册表中某个终结点的 bean ID。

## URI参数

```
暂无
```

## 示例

### Blueprint DSL 配置

```xml
<camelContext xmlns="http://camel.apache.org/schema/blueprint" id="测试">	
	      <endpoint id="fromFile" uri="file:H:/fxk?delay=30000"/>
		  <endpoint id="toFile" uri="file:H:/k3"/>
	      <route id="Ref组件测试">
			    <from uri="ref:fromFile"/>
				<to uri="ref:toFile"/>
		  </route> 	
</camelContext>
```

结果显示fxk文件夹中的文件移动到了K3文件夹，实现了文件移动的功能。

### 示例分析

上述样例，可以看到路由from和to两端中并没有直接去引用file组件的端点。而是先在注册表中定义端点，然后通过ref组件去引用，同样也实现了文件移动的功能。
大家可能觉得这样做会很多余，何不直接去引用端点？
但是若在上百条甚至更多路由线路中同时引用一个端点时，代码量就会增大。
所以Ref组件让我们可以更灵活的去引用端点，避免路由线路的臃肿。