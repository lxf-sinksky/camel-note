# Mock组件

*版权：数帝网络*
*整理：方辉*
*时间：2017-11-03*
*相关连接:[http://camel.apache.org/mock.html](http://camel.apache.org/mock.html)*

## 概述
模拟组件提供了强大的声明性测试机制，类似于[jMock](http://www.jmock.org/)，它允许在测试开始之前在任何模拟端点上创建声明性期望。然后运行测试，它通常会将消息发送到一个或多个端点，最后在测试用例中可以断言期望，以确保系统按照预期工作。

## URI

```
mock:someName[?options] 
```

其中，someName可以是唯一标识端点的任何字符串。

## URI参数

选项|默认|描述
----|----|----
reportGroup|null|用于报告的记录器的大小
retainFirst|暂无|只在内存中保留第一个X编号的消息
retainLast|暂无|只在内存中保留最后的X编号的消息

## 示例

### 示例分析

**单元测试的三个基本步骤：**
1.在测试开始前，设置期望值：应该发生什么？
2.测试开始； 
3.测试之后：验证输出是否符合期望值；

**Mock组件可以验证一个丰富多样的期望，如以下：**
- 每一个endpoint应该收到的消息个数；
- 消息到达的顺序；
- 收到的负载；
- 单元测试在预期的时间内运行； 

**Mock组件在许多情况下非常有用： **
- 当在开发或者测试阶段，真实的组件无法获取或者根本不存在的时候；
- 当真正的组件非常慢或需要多做许多额外的努力来建立和初始化的时候，比如数据库； 
- 基于测试的目的，你需要将特殊的逻辑合并到真正的组件中，而特殊的逻辑在正常情况下很少发生的时候； 
- 当你需要模拟组件遇到网络问题而引起的错误的时候；


如果没有Mock组件，你只能使用真实的组件来测试，大多数情况下，是比较困难的。Camel很重视测试,Camel的第一个版本就包含了Mock组件。Mock组件位于`camel-core.jar`包中，而不是`camel-test.jar`包中，这说明Mock组件的重要程度。 

### mock组件单元测试类

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
				from("browse:changer").to("mock:quote");
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
	}
}
```