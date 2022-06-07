# Test组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-08*
*相关连接:[http://camel.apache.org/test.html](http://camel.apache.org/test.html)*

## 概述
分布式和异步处理的测试是很困难的。Mock、Test和数据集端点在骆驼测试框架中工作非常出色，以简化您的单元和集成测试，使用企业集成模式和骆驼的大量组件以及强大的Bean集成。

Test组件扩展了Mock组件，以支持在启动时从另一个端点提取消息，从而在底层的模拟端点上设置预期的消息体。也就是说，您在一个路由中使用测试端点，而到达它的消息将被默认地与从其他位置提取的一些预期消息进行比较。

### 补充：
从骆驼 2.9:测试组件直接在camel-core中提供，2.9以下需添加依赖

```xml
<dependency>
    <groupId>org.apache.camel</groupId>
    <artifactId>camel-spring</artifactId>
    <version>x.x.x</version>
    <!-- use the same version as your Camel core version -->
</dependency>
```

## URI

```
test:expectedMessagesEndpointUri
```

其中, expectedMessagesEndpointUri 引用了在启动测试之前从预期的消息正文中提取的其他组件URI。

## URI参数

名称|默认|描述
----|----|----
anyOrder|false|预期的消息是否应以相同的顺序或按任何顺序到达。
delimiter|\nl\r|split=true时要使用的分隔符。分隔符可以是正则表达式。
split|false|如果从测试端点加载的true消息将使用已定义的分隔符进行分割。
timeout|2000|从URI轮询消息体时使用的超时时间。

## 示例

```java
package com.shudi.databridge.test;

import org.apache.camel.builder.RouteBuilder;
import org.apache.camel.component.mock.MockEndpoint;
import org.apache.camel.test.junit4.CamelTestSupport;
import org.junit.Test;

public class MockTest extends CamelTestSupport {

	@Override
	protected RouteBuilder createRouteBuilder() throws Exception {
		return new RouteBuilder() {
			@Override
			public void configure() throws Exception {
				from("browse:changer").to("test:browse:changer").to("mock:quote");
			}
		};
	}

	@Test
	public void testQuote() throws Exception {
        //使用CamelTestSupport类的getMockEndpoint方法获取MockEndpoint对象
		MockEndpoint quote = getMockEndpoint("mock:quote");
        //设置期望值(这里验证的是消息个数)
		quote.expectedMessageCount(1);
        //开始测试：向browse:changer端点发送一个消息；
		template.sendBody("browse:changer", "Camel mock");
        //使用assertIsSatisfied方法验证期望是否被满足，如果某一个期望没有被满足，Camel将会抛出异常：java.lang.AssertionError；
		quote.assertIsSatisfied();
        //quote.assertIsSatisfied(context);
	}
}
```

### 示例分析

上述示例是Test组件和Mock组件一同进行测试。如果测试随后调用`MockEndpoint assertIsSatisfied (camelContext)` 方法, 则测试用例将执行必要的断言。若要了解如何在测试端点上设置其他期望, 我们要更深入的了解[Mock](http://camel.apache.org/mock.html)组件。