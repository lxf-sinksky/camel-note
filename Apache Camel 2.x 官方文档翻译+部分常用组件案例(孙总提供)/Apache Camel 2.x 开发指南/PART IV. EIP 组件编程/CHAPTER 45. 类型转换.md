# 第45章. 类型转换器
*karezflow 产品组
整理：孙勇
版本：0.1*

Apache Camel內建类型转换机制，用于将消息正文和消息头转换至不同类型。

## 45.1. 类型转换架构

### 类型转换器接口
所有的类型转换器必须继承`org.apache.camel.TypeConverter`接口。

```java
package org.apache.camel;

public interface TypeConverter {<T> T convertTo(Class<T> type, Object value);
}
```

### 主类型转换器
Apache Camel 类型转换器机制遵循`master/slave`模式。有许多*从类型转换器*有能力履行有限数量的类型转换，只有一个*主类型转换器*，负责从*从类型转换器*聚合类型转换。*主类型转换器*充当*从类型转换器*的前端。当请求*主转换器*完成一个类型转化，*主类型转换器*选择一个适当的*从类型转换器*并将转化任务委托给这个*从类型转换器*。

*主转化器*提供了类型转化机制的访问入口点。在启动期间，Apache Camle 自动把一个*主类型转换器*实例与`CamelContext`对象关联在一起。通过`CamelContext.getTypeConverter()`方法可以获得*主类型转换器*。

```java
org.apache.camel.TypeConverter tc =
exchange.getContext().getTypeConverter();
```

### 类型转化器装载器
*主类型转换器*使用一个`类型转换器装载器`填充*从类型转换器*注册。一个类型转化器加载器是一个继承`TypeConverterLoader`接口的任意类。Apache Camel当前只使用一个类型的装载器——`AnnotationTypeConverterLoader`注释类型装载器

### 类型转换过程
![](./images/Typeconversionprocess.png)

类型转换机制过程如下：
1. `CamelContext`对象保存对主`TypeConverter`实例的引用。转换过程首先调用`CamelContext.getTypeConverter()`方法获得主类型转换器。
2. 在主类型转换器上调用` convertTo () `方法开始类型转换。此方法指示类型转换器将数据对象、值从其原始类型转换至`toType`参数指定的类型。
3. 由于主类型转换器是许多不同的从属类型转换器的前端，因此它通过检查类型映射的注册表来查找适当的从属类型转化器。类型转换器注册的键值是类型映射对 **(toType, fromType)** 。如果在注册表中找到合适的类型转换器，则主类型转换器将调用从属的`convertTo()`方法并返回结果。
4. 如果在注册表中未找到合适的类型转换器，主类型转换器使用加载一个新的类型转换器。
5. 类型转换器加载程序在类路径下搜索可用的JAR库以便查找合适的类型转换器。当前，使用的加载策略由注释类型转换器加载程序实现，它尝试加载由 **org.apache.camel.Converter** 注释批注的类。
6. 如果类型转换器加载成功，一个新的从属类型将加载并登记到类型转换器注册表中。然后，此类型转换器用于将`value`参数转换为`toType`类型。
7. 如果成功转换数据则返回转换后的数值。否则，返回null。

## 45.2. 使用注释实现类型转换器
类型转换机制可以轻松实现自定义，添加一个新的从属类型转换器。

### 如何实现一个类型转换器
实现一个自定义类型转换器执行以下步骤：
1. 实现一个有注释的转换器类。
2. 创建一个类型转换器文件。
3. 打包这个类型转换器。

#### 实现一个有注释的转换器类
可以使用 **@Converter** 注释实现一个自定义类型转换器类。必须对类本身和每个用于执行类型转换的静态方法进行批注。每个转换器方法都采用一个参数来定义来源类型，第二个参数 **Exchange** 为可选的，并且具有一个非void返回值。

```java
package com.YourDomain.YourPackageName;

import org.apache.camel.Converter;
import java.io.*;

@Converter
public class IOConverter {
  private IOConverter() {
  }

  @Converter
  public static InputStream toInputStream(File file) throws FileNotFoundException {
    return new BufferedInputStream(new FileInputStream(file));
  }

  @Converter
  public static String toString(byte[] data, Exchange exchange) {
    if (exchange != null) {
      String charsetName =
      exchange.getProperty(Exchange.CHARSET_NAME, String.class);

      if (charsetName != null) {
        try {
          return new String(data, charsetName);
        } catch (UnsupportedEncodingException e) {
          LOG.warn("Can't convert the byte to String with the charset " + charsetName, e);
        }
      }
    }
  return new String(data);
  }
}
```

#### 创建一个类型转换器文件
要为自定义转换器启用发现机制（由注释类型转换器加载器实现），请在以下位置创建一个TypeConverter文件：

```
META-INF/services/org/apache/camel/TypeConverter
```

**TypeConverter** 文件必须包含以逗号分隔的包名称列表，标识包含类型转换器类的包的包名称。例如，如果您希望类型转换器加载器搜索用于注释转换器类的com.YourDomain.YourPackageName包，则TypeConverter文件将具有以下内容：

```
com.YourDomain.YourPackageName
```

#### 打包类型转换器
类型转换器被打包成JAR文件，其中包含的自定义类型转换器和META-INF目录的编译类。将此JAR文件放在类路径上，使其可用于Apache Camel应用程序。

#### 后备转换器方法
除了使用`@Converter`注释定义常规转换器方法之外，还可以使用`@FallbackConverter`注释来定义后备转换器方法。如果主类型转换器在类型注册表中找不到常规转换器方法，则只会尝试后备转换器方法。

常规转换器方法和后备转换器方法之间的本质区别在于，定义一个常规转换器来执行特定的一对类型之间的转换（例如，从byte []到String），后备转换器可能潜在地执行任何一对类型。这取决于后备转换器方法的代码，以确定哪些转换能够执行。在运行时，如果常规转换器无法执行转换，则主机类型转换器将遍历每个可用的后备转换器，直到找到可执行转换的转换器。

后备转换器的方法签名可以具有以下任一种形式：

```java
// 1. Non-generic form of signature
@FallbackConverter
public static Object MethodName(
  Class type,
  Exchange exchange,
  Object value,
  TypeConverterRegistry registry
)

// 2. Templating form of signature
@FallbackConverter
public static <T> T MethodName(
  Class<T> type,
  Exchange exchange,
  Object value,
  TypeConverterRegistry registry
)
```
其中 *MethodName* 是后备转换器的任意方法名称。

例如，以下代码提取（取自File组件的实现）显示了可以转换GenericFile对象的主体的后备转换器，利用该类型转换器注册表中已有的转换器：

```java
package org.apache.camel.component.file;
import org.apache.camel.Converter;
import org.apache.camel.FallbackConverter;
import org.apache.camel.Exchange;
import org.apache.camel.TypeConverter;
import org.apache.camel.spi.TypeConverterRegistry;

@Converter
public final class GenericFileConverter {

  private GenericFileConverter() {
  // Helper Class
  }

  @FallbackConverter
  public static <T> T convertTo(Class<T> type, Exchange exchange,Object value, TypeConverterRegistry registry) {

    // use a fallback type converter so we can convert the embedded body if the value is GenericFile
    if (GenericFile.class.isAssignableFrom(value.getClass())) {
      GenericFile file = (GenericFile) value;
      Class from = file.getBody().getClass();
      TypeConverter tc = registry.lookup(type, from);

      if (tc != null) {
        Object body = file.getBody();
        return tc.convertTo(type, exchange, body);
      }
    }
    return null;
  }
  ...
}
```

## 45.3. 直接实现类型转换器
实现类型转换器的推荐方法是使用注释类。但是，如果要完全控制类型转转器的注册，可以实现一个自定义从类型转换器，并将其直接添加到类型转换器注册表中。

### 实现 TypeConverter 接口
通过定义一个实现 **TypeConverter** 接口的类实现自定义类型转换器类。例如，下例中 **MyOrderTypeConverter** 类将整数值转换为 **MyOrder** 对象。其中，整数值用于初始化订单ID。

```java
import org.apache.camel.TypeConverter

private class MyOrderTypeConverter implements TypeConverter {
  public <T> T convertTo(Class<T> type, Object value) {
    // converter from value to the MyOrder bean
    MyOrder order = new MyOrder();
    order.setId(Integer.parseInt(value.toString()));
    return (T) order;
  } 

  public <T> T convertTo(Class<T> type, Exchange exchange, Object value) {
    // this method with the Exchange parameter will be preferd by Camel to invoke
    // this allows you to fetch information from the exchange during convertions
    // such as an encoding parameter or the likes
    return convertTo(type, value);
  }

  public <T> T mandatoryConvertTo(Class<T> type, Object value) {
    return convertTo(type, value);
  }

  public <T> T mandatoryConvertTo(Class<T> type, Exchange exchange,
    Object value) {
    return convertTo(type, value);
  }
}
```

### 添加类型转换器到注册表
可以用下列代码将自定义类型转化器直接添加到类型转换器注册表：
```java
// Add the custom type converter to the type converter registry
context.getTypeConverterRegistry().addTypeConverter(MyOrder.class,
String.class, new MyOrderTypeConverter());
```
其中 **context** 是当前的 **org.apache.camel.CamelContext** 实例。**addTypeConverter()**方法根据特定的类型转换注册 **MyOrderTypeConverter** 类，从**String.class** 到** MyOrder.class**。

可以将自定义类型转换器添加到Camel应用程序中，而无需使用 **METAINF** 文件。 如果使用的是Spring或Blueprint，那么您只需声明一个<bean>。 CamelContext自动发现bean并添加转换器。

```xml
<bean id="myOrderTypeConverters" class="..."/>
<camelContext>
...
</camelContext>
```

如果有更多的类，可以声明多个<bean>。