# 走进Java接口测试之流式断言库AssertJ

2019年01月21日 10:02:07

 

zuozewei

 

阅读数 343

更多

所属专栏： [Java接口自动化](https://blog.csdn.net/column/details/31964.html)



 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/zuozewei/article/details/86567263



### 文章目录

- - [前言](https://blog.csdn.net/zuozewei/article/details/86567263#_1)

  - [AssertJ简介](https://blog.csdn.net/zuozewei/article/details/86567263#AssertJ_6)

  - [AssertJ使用](https://blog.csdn.net/zuozewei/article/details/86567263#AssertJ_50)

  - - [导包](https://blog.csdn.net/zuozewei/article/details/86567263#_51)
    - [入门使用](https://blog.csdn.net/zuozewei/article/details/86567263#_74)
    - [对象断言](https://blog.csdn.net/zuozewei/article/details/86567263#_84)
    - [布尔断言](https://blog.csdn.net/zuozewei/article/details/86567263#_127)
    - [Iterable/Array断言](https://blog.csdn.net/zuozewei/article/details/86567263#IterableArray_144)
    - [字符断言](https://blog.csdn.net/zuozewei/article/details/86567263#_191)
    - [类断言](https://blog.csdn.net/zuozewei/article/details/86567263#_212)
    - [文件断言](https://blog.csdn.net/zuozewei/article/details/86567263#_239)
    - [Double/Float/Integer断言](https://blog.csdn.net/zuozewei/article/details/86567263#DoubleFloatInteger_264)
    - [InputStream断言](https://blog.csdn.net/zuozewei/article/details/86567263#InputStream_275)
    - [Map断言](https://blog.csdn.net/zuozewei/article/details/86567263#Map_295)
    - [Throwable 断言](https://blog.csdn.net/zuozewei/article/details/86567263#Throwable__318)
    - [描述断言](https://blog.csdn.net/zuozewei/article/details/86567263#_337)

  - [小结](https://blog.csdn.net/zuozewei/article/details/86567263#_363)



## 前言

在设计自动化接口 Cases 时，遵守的核心原则是3A（Arrange-> Actor ->Assert）原则;

断言工具是否强大直接影响到用例的执行效率，本文将介绍目前主流的一种流式断言神器：AssertJ。

## AssertJ简介

什么是流式，常见的断言器一条断言语句只能对实际值断言一个校验点，而流式断言器，支持一条断言语句对实际值同时断言多个校验点，简单理解，即 AssertJ 断言是可以串接的。
AssertJ 是一个 Java 库，为 JDK 标准类型提供断言，可以与 JUnit，TestNG 或任何其他测试框架一起使用。
不同的 AssertJ 主要版本依赖于不同的 Java 版本：

- AssertJ 3.x 需要 Java 8或更高版本
- AssertJ 2.x 需要 Java 7或更高版本
- AssertJ 1.x 需要 Java 6或更高版本

请注意，AssertJ 3.x包含所有AssertJ 2.x功能，并添加了Java 8特定功能（如 lambdas 的异常断言）

AssertJ 支持如下模块：

- Core：AssertJ core is a Java library that provides a fluent interface for writing assertions.
- Assertions generator：Use the Assertion Generator to create assertions specific to your own classes.
- Guava：AssertJ assertions for Guava provides assertions for Guava types like Multimap, Table, Optional, Range or ByteSource.
- Joda-Time：AssertJ assertions for Joda-Time provides assertions for Joda-Time types like DateTime and LocalDateTime.
- DB：AssertJ-DB provides assertions to test data in a database.
- Neo4j：Provides assertions for Neo4j 3 or higher.
- Swing：AssertJ Swing is a Java library that provides a fluent interface for functional Swing UI testing.

官方网站上提供了所有模块的详细列表。
地址：<https://joel-costigliola.github.io/assertj/index.html>

让我们从几个例子开始，直接来自 AssertJ 的官方文档：

```java
assertThat(frodo)
  .isNotEqualTo(sauron)
  .isIn(fellowshipOfTheRing);
 
assertThat(frodo.getName())
  .startsWith("Fro")
  .endsWith("do")
  .isEqualToIgnoringCase("frodo");
 
assertThat(fellowshipOfTheRing)
  .hasSize(9)
  .contains(frodo, sam)
  .doesNotContain(sauron);
12345678910111213
```

以上的例子只是冰山一角，下面我们将介绍如何使用这个库编写断言

## AssertJ使用

### 导包

SpringBoot 内置了 AssertJ,只需要导入 `spring-boot-starter-test` 依赖包

```xml
 <dependencies>
        <!--引入效率插件-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <!--引入测试包-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>
        <!--引入testng测试框架-->
        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>6.14.3</version>
        </dependency>
    </dependencies>
12345678910111213141516171819
```

### 入门使用

为了编写一个断言，你总是需要先将对象传递给 `Assertions.assertThat()` 方法，然后再按照实际的断言进行操作。

重要的是要记住，与其他一些库不同，下面的代码实际上并没有断言任何东西，并且永远不会失败测试：

```java
assertThat(anyRefenceOrValue);
1
```

如果你使用IDE的代码完成功能，由于其描述性非常强的方法，编写AssertJ 断言变得异常简单。下图就是它在`IntelliJ IDEA` 中的样子：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190120232141122.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p1b3pld2Vp,size_16,color_FFFFFF,t_70)
如图所见，有许多可供选择的上下文方法，并且这些方法仅适用于String类型。

### 对象断言

可以以各种方式比较对象，以确定两个对象的相等性或检查对象的字段。

```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class Dog {
	private String name;
	private Float weight;

}
123456789
```

让我们看两种方法，我们可以比较两个对象的相等性。鉴于以下两个Dog对象 fido和 fidosClone

```java
@Test(description = "对象断言1")
	public void whenComparingReferences_thenNotEqual() {

		// 实例化两个对象
		Dog fidos= new Dog("fidos",5.14f);
		Dog fidosClone = new Dog("fidosClone",5.14f);

		// 断言两个对象引用
		assertThat(fidos).isNotEqualTo(fidosClone);

	}
1234567891011
isEqualTo()` 是比较对象引用，所以会执行失败。如果我们想要比较它们的内容，我们可以使用 `isEqualToComparingFieldByFieldRecursively()
@Test(description = "对象断言2")
	public void whenComparingFields_thenEqual() {
		// 实例化两个对象
		Dog fido = new Dog("Fido", 5.15f);
		Dog fidosClone = new Dog("Fido", 5.15f);

		// 断言两个对象内容
		assertThat(fido).isEqualToComparingFieldByFieldRecursively(fidosClone);
	}
123456789
```

当通过字段比较执行递归字段时，Fido和fidosClone是相等的，因为一个对象的每个字段与另一个对象中的字段进行比较。

还有许多其他断言方法提供了比较和收缩对象以及检查和断言其字段的不同方法。具体请参阅官方的 `AbstractObjectAssert` API。

### 布尔断言

真值测试有一些简单的方法：

- isTrue()
- isFalse()

举个例子：

```java
	@Test(description = "布尔断言")
	public void whenisEmpty_isTrue() {
		// 实例化对象
		Dog fido = new Dog("Fido", 5.15f);

		// 断言字段是否为空
		assertThat(fido.getName().isEmpty()).isTrue();
	}
12345678
```

### Iterable/Array断言

对于 Iterable 或 Array，有多种方法可以断言它们的内容是否存在。最常见的断言之一是检查 Iterable 或 Array 是否包含给定元素：

或者如果 List 不为空：

```java
assertThat(list).isNotEmpty();
1
```

或者如果 List 以给定字符开头。例如“1”：

```java
assertThat(list).startsWith("1");
1
```

如果要为同一对象创建多个断言，可以轻松地将它们连接在一起。
下面是一个断言示例，它检查提供的列表是否为空，包含“1”元素，不包含任何空值并包含元素序列“2”，“3”：

```java
assertThat(list)
  .isNotEmpty()
  .contains("1")
  .doesNotContainNull()
  .containsSequence("2", "3");
12345
```

当然，对于那些类型存在更多可能的断言 。具体请参阅官方的`AbstractIterableAssert` API

完整示例：

```java
@Test(description = "Iterable/Array断言1")
	public void whenCheckingForElement_thenContains(){
		List<String> list = Arrays.asList("1", "2", "3");

		// 断言是否包含给定元素
		assertThat(list).contains("1");
	}

	@Test(description = "Iterable/Array断言2")
	public void whenCheckingForElement_thenMultipleAssertions() {
		List<String> list = Arrays.asList("1", "2", "3");

		// 断言list不为空
		assertThat(list).isNotEmpty();
		// 断言list以给定字段开头
		assertThat(list).startsWith("1");
		// 断言list不包含null
		assertThat(list).doesNotContainNull();
		// 多个断言
		assertThat(list).isNotEmpty().contains("1").startsWith("1").doesNotContainNull().containsSequence("2", "3");
	}
123456789101112131415161718192021
```

### 字符断言

字符类型的断言主要涉及比较，甚至检查给定字符是否来自 Unicode 表。
下面是一个断言示例，它检查提供的字符是否不是 ‘a’，在 Unicode 表中，是否大于 ‘b’ 并且是小写的：

```java
assertThat(someCharacter)
  .isNotEqualTo('a')
  .inUnicode()
  .isGreaterThanOrEqualTo('b')
  .isLowerCase();
12345
```

有关所有字符类型断言的详细列表，请参阅 `AbstractCharacterAssert`API
完整示例：

```java
@Test(description = "字符断言")
	public void whenCheckingCharacter_thenIsUnicode() {
		char someCharacter = 'c';

		// 断言字符是否不是 'a'，在 Unicode 表中，是否大于 'b' 并且是小写的
		assertThat(someCharacter).isNotEqualTo('a').inUnicode().isGreaterThanOrEqualTo('b').isLowerCase();
	}
1234567
```

### 类断言

Class 类型的断言主要是检查其字段，类类型，注释的存在和类的最终性。

如果你想断言Runnable类是一个接口，你需要简单地写：

```java
assertThat(Runnable.class).isInterface();
1
```

或者如果你想检查一个类是否可以从另一个类中分配：

```java
assertThat(Exception.class).isAssignableFrom(NoSuchElementException.class);
1
```

可以在 `AbstractClassAssert` API 中查看所有可能的类断言。
完整示例：

```java
@Test(description = "类断言1")
	public void whenCheckingRunnable_thenIsInterface() {
		// 断言Runnable类是一个接口
		assertThat(Runnable.class).isInterface();
	}

	@Test(description = "类断言2")
	public void whenAssigningNSEExToException_thenIsAssignable(){
		// 断言一个类是否可以从另一个类中分配
		assertThat(Exception.class).isAssignableFrom(NoSuchElementException.class);
	}
1234567891011
```

### 文件断言

文件断言都是关于检查给定的文件实例是否存在，是目录还是文件，具有某些内容，是否可读或具有扩展名。

在这里断言的示例，该断言检查给定文件是否存在，是文件而不是目录，可读写的：

```java
assertThat(someFile)
  .exists()
  .isFile()
  .canRead()
  .canWrite();
12345
```

可以在 `AbstractFileAssert` API 中查看所有可能的类断言。

完整示例：

```java
@Test(description = "文件断言")
	public void whenCheckingFile_then() throws IOException {
		final File someFile = File.createTempFile("aaa", "bbb");
		someFile.deleteOnExit();

		// 断言文件是否存在，是文件而不是目录，可读写的
		assertThat(someFile).exists().isFile().canRead().canWrite();
	}
12345678
```

### Double/Float/Integer断言

数字断言都是关于比较给定偏移量内或没有给定偏移量的数值。
例如，如果要根据给定的精度检查两个值是否相等，我们可以执行以下操作：

```java
assertThat(5.1).isEqualTo(5, withPrecision(1d));
1
```

请注意，我们使用已导入的 withPrecision（双偏移）辅助方法来生成偏移对象。

有关更多断言，请访问 `AbstractDoubleAssert` API。

### InputStream断言

只有一个可用的 InputStream 特定断言：

- `hasSameContentAs`（预期的 InputStream）

使用方法：

```java
assertThat(given).hasSameContentAs(expected);
1
```

完整示例：

```java
@Test(description = "InputStream断言")
	public void whenCheckingIS_then() {
		InputStream given = new ByteArrayInputStream("foo".getBytes());
		InputStream expected = new ByteArrayInputStream("foo".getBytes());

		// 断言是否预期的 InputStream
		assertThat(given).hasSameContentAs(expected);
	}
12345678
```

### Map断言

Map 断言允许你分别检查 Map 是否包含某些条目，条目集或键/值。
你可以看到断言的示例，该断言检查给定的Map是否为空，包含key “2”，不包含数字键“10”并包含条目：key:2,value:“a”：

```java
assertThat(map)
  .isNotEmpty()
  .containsKey(2)
  .doesNotContainKeys(10)
  .contains(entry(2, "a"));
12345
```

有关更多断言，请参阅 `AbstractMapAssert` API。

完整示例：

```java
@Test(description = "Map断言")
	public void whenGivenMap_then() {
		Map<Integer, String> map = Maps.newHashMap(2, "a");

		// 断言Map是否为空，包含key “2”，不包含key “10” 并包含元素：key:2,value:“a”
		assertThat(map).isNotEmpty().containsKey(2).doesNotContainKeys(10).contains(entry(2, "a"));
	}
1234567
```

### Throwable 断言

Throwable 的断言允许例如：检查异常的信息，踪迹，原因检查或者异常被抛出已验证。

让我们看一下断言示例，该断言检查是否抛出了给定的异常并且消息以“c”结尾：

```java
assertThat(ex).hasNoCause().hasMessageEndingWith("c");
1
```

有关更多断言，请参阅 `AbstractThrowableAssert` API。
完整示例：

```java
@Test(description = "Throwable断言")
	public void whenGivenException_then() {
		Exception ex = new Exception("abc");

		// 断言是否抛出了给定的异常并且消息以“c”结尾
		assertThat(ex).hasNoCause().hasMessageEndingWith("c");
	}
1234567
```

### 描述断言

为了获得更高的详细级别，你可以为断言创建动态生成的自定义描述。
这样做的关键是 as（String description，Object … args）方法。

如果你定义这样的断言：

```java
assertThat(fidos.getWeight())
.as("%s's age should be equal to 5.15f")
.isEqualTo(5.15f);
123
```

这是运行测试时的结果：

```java
org.junit.ComparisonFailure: [%s's age should be equal to 5.15f] 
Expected :5.1[5]f
Actual   :5.1[4]f
123
```

完整示例：

```java
@Test(description = "描述断言")
	public void whenRunningAssertion_thenDescribed() throws Exception {
		Dog fidos= new Dog("fidos",5.14f);

		assertThat(fidos.getWeight()).as("%s's age should be equal to 5.15f").isEqualTo(5.15f);
	}
123456
```

## 小结

在本文中，我们简要探讨了AssertJ 为核心Java类型提供最流行的流式断言的使用方法。

本文源码：
<https://github.com/zuozewei/Java-API-Test-Examples>