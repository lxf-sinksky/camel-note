# Browse组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-07*
*相关连接:[http://camel.apache.org/browse.html](http://camel.apache.org/browse.html)*

## 概述
Browse组件提供了一个简单的[BrowsableEndpoint](http://camel.apache.org/browsableendpoint.html)，可用于测试、可视化工具或调试。发送到端点的交换都可以被浏览。

## URI
```
browse:someName[?options]
```
其中，someName可以是任何字符串作为唯一标识端点。

## URI参数
>暂无

## 示例

### 示例分析
Browse组件类似于数据的存放站点。所以，Browse组件可以辅助大家进行更快捷的开发测试。

### Browse组件单元测试类

```java
@Test
public void Test() {
    try {
         //调用dataset组件，生成数据集
		 SimpleDataSet simpleDataSet = new SimpleDataSet(5);
         //注册表
		 SimpleRegistry registry = new SimpleRegistry();
		 registry.put("simpleDataSet", simpleDataSet);
		 CamelContext context = new DefaultCamelContext(registry);   
		 context.start();
		 context.addRoutes(new RouteBuilder() {
			 @Override
			 public void configure() throws Exception {
				 from("dataset:simpleDataSet").to("browse:changer").process(new DataSetProcessor());
			    });
		 Thread.sleep(1200000);
		} catch (InterruptedException e) {
			e.printStackTrace();
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
```

### 从 Java 代码中检查接收到的交换

```java
//读取交换中的数据方法
public void readExchangeData(CamelContext context) {
       BrowsableEndpoint browse = context.getEndpoint("browse:changer", BrowsableEndpoint.class);
       //遍历数据
       List<Exchange> exchanges = browse.getExchanges();
       for(Exchange exchange : exchanges) {
               String payload = exchange.getIn().getBody();
               ...
       }
 }
```

