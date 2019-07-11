JUnit 4和TestNG都是Java中非常受欢迎的单元测试框架。两种框架在功能上看起来非常相似。 哪一个更好？ 在Java项目中应该使用哪个单元测试框架？

下面表中概括了JUnit 4和TestNG之间的功能比较。如下图所示 -

![img](http://www.yiibai.com/uploads/images/201705/0405/996120528_50644.jpg)

## 1. 注释支持

注释/注解支持在JUnit 4和TestNG中是非常类似的。

| 特点                                         | JUnit 4                                                      | TestNG                                                       |
| -------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 测试注释                                     | [@Test](https://github.com/Test)                             | [@Test](https://github.com/Test)                             |
| 在套件中的所有测试运行之前运行               | -                                                            | [@BeforeSuite](https://github.com/BeforeSuite)               |
| 在套件中的所有测试运行之后运行               | -                                                            | [@AfterSuite](https://github.com/AfterSuite)                 |
| 测试之前运行                                 | -                                                            | [@BeforeTest](https://github.com/BeforeTest)                 |
| 测试之后运行                                 | -                                                            | [@AfterTest](https://github.com/AfterTest)                   |
| 在调用属于任何这些组的第一个测试方法之前运行 | -                                                            | [@BeforeGroups](https://github.com/BeforeGroups)             |
| 在调用属于任何这些组的第一个测试方法之后运行 | -                                                            | [@AfterGroups](https://github.com/AfterGroups)               |
| 在调用当前类的第一个测试方法之前运行         | [@BeforeClass](https://github.com/BeforeClass)               | [@BeforeClass](https://github.com/BeforeClass)               |
| 在调用当前类的第一个测试方法之后运行         | [@AfterClass](https://github.com/AfterClass)                 | [@AfterClass](https://github.com/AfterClass)                 |
| 在每个测试方法之前运行                       | [@Before](https://github.com/Before)                         | [@BeforeMethod](https://github.com/BeforeMethod)             |
| 在每个测试方法之后运行                       | [@After](https://github.com/After)                           | [@AfterMethod](https://github.com/AfterMethod)               |
| 忽略测试                                     | [@ignore](https://github.com/ignore)                         | [@Test](https://github.com/Test)(enbale=false)               |
| 预期的异常                                   | [@Test](https://github.com/Test)(expected = ArithmeticException.class) | [@Test](https://github.com/Test)(expectedExceptions = ArithmeticException.class) |
| 超时测试                                     | [@Test](https://github.com/Test)(timeout = 1000)             | [@Test](https://github.com/Test)(timeout = 1000)             |

JUnit4和TestNG之间的主要注释差异是：

1. 在JUnit 4中，我们必须声明“`@BeforeClass`”和“`@AfterClass`”方法作为静态方法。 TestNG在方法声明中更灵活，它没有这个约束。
2. `3`个额外的`setUp / tearDown`级别：`suite`和`group(@Before / AfterSuite，@Before / After Test，@Before / After Group)`。

**JUnit 4**

```java
@BeforeClass
public static void oneTimeSetUp() {
    // one-time initialization code
    System.out.println("@BeforeClass - oneTimeSetUp");
}


Java
```

**TestNG**

```java
@BeforeClass
public void oneTimeSetUp() {
        // one-time initialization code
        System.out.println("@BeforeClass - oneTimeSetUp");
}


Java
```

在JUnit 4中，注释命名约定有点混乱，例如“Before”，“After”和“Expected”，我们并不真正了解“Before”和“After”之前的内容，以及要测试中的“预期” 方法。TestiNG更容易理解，它使用类似“BeforeMethod”，“AfterMethod”和“ExpectedException”就很明了。

## 2. 异常测试

“异常测试”是指从单元测试中抛出的异常，此功能在JUnit 4和TestNG中都可实现。

**JUnit 4**

```java
@Test(expected = ArithmeticException.class)
public void divisionWithException() {
  int i = 1/0;
}


Java
```

**TestNG**

```java
@Test(expectedExceptions = ArithmeticException.class)
public void divisionWithException() {
  int i = 1/0;
}


Java
```

## 3. 忽略测试

“忽略”表示是否应该忽略单元测试，该功能在JUnit 4和TestNG中均可实现。

**JUnit 4**

```java
@Ignore("Not Ready to Run")
@Test
public void divisionWithException() {
  System.out.println("Method is not ready yet");
}


Java
```

**TestNG**

```java
@Test(enabled=false)
public void divisionWithException() {
  System.out.println("Method is not ready yet");
}


Java
```

## 4. 时间测试

“时间测试”表示如果单元测试所花费的时间超过指定的毫秒数，则测试将会终止，并将其标记为失败，此功能在JUnit 4和TestNG中均可实现。

**JUnit 4**

```java
@Test(timeout = 1000)
public void infinity() {
    while (true);
}


Java
```

**TestNG**

```java
@Test(timeOut = 1000)
public void infinity() {
    while (true);
}


Java
```

## 5. 套件测试

“套件测试”是指捆绑几个单元测试并一起运行。 此功能在JUnit 4和TestNG中都可实现。 然而，两者都使用非常不同的方法来实现它。

**JUnit 4**

“[@RunWith](https://github.com/RunWith)”和“[@Suite](https://github.com/Suite)”用于运行套件测试。下面的类代码表示在`JunitTest5`执行之后，单元测试“`JunitTest1`”和“`JunitTest2`”一起运行。 所有的声明都是在类内定义的。

```java
@RunWith(Suite.class)
@Suite.SuiteClasses({
        JunitTest1.class,
        JunitTest2.class
})
public class JunitTest5 {
}


Java
```

**TestNG**

XML文件用于运行套件测试。以下XML文件表示单元测试“`TestNGTest1`”和“`TestNGTest2`”将一起运行。

```xml
<suite name="My test suite">
  <test name="testing">
    <classes>
       <class name="com.fsecure.demo.testng.TestNGTest1" />
       <class name="com.fsecure.demo.testng.TestNGTest2" />
    </classes>
  </test>
</suite>


XML
```

TestNG可以做捆绑类测试，也可以捆绑方法测试。 凭借TestNG独特的“分组”概念，每种方法都可以与一个组合相结合，可以根据功能对测试进行分类(分组)。 例如，

下面是一个有四个方法的类，三个组(`method1`，`method2`和`method3`)

```java
@Test(groups="method1")
public void testingMethod1() {
  System.out.println("Method - testingMethod1()");
}

@Test(groups="method2")
public void testingMethod2() {
    System.out.println("Method - testingMethod2()");
}

@Test(groups="method1")
public void testingMethod1_1() {
    System.out.println("Method - testingMethod1_1()");
}

@Test(groups="method4")
public void testingMethod4() {
    System.out.println("Method - testingMethod4()");
}


Java
```

使用以下XML文件，可以仅使用组“`method1`”执行单元测试。

```xml
<suite name="My test suite">
  <test name="testing">
      <groups>
      <run>
        <include name="method1"/>
      </run>
    </groups>
    <classes>
       <class name="com.fsecure.demo.testng.TestNGTest5_2_0" />
    </classes>
  </test>
</suite>


XML
```

通过“分组”测试概念，集成测试的可能性是无限制的。 例如，我们只能从所有单元测试类中测试“`DatabaseFuntion`”分组。

## 6. 参数化测试

“参数化测试”是指单位测试参数值的变化。 此功能在JUnit 4和TestNG中都实现。 然而，两者都使用非常不同的方法来实现它。

**JUnit 4**

“`@RunWith`”和“`@Parameter`”用于提供单元测试的参数值，`@Parameters`必须返回`List []`，参数将作为参数传入类构造函数。

```java
@RunWith(value = Parameterized.class)
public class JunitTest6 {

     private int number;

     public JunitTest6(int number) {
        this.number = number;
     }

     @Parameters
     public static Collection<Object[]> data() {
       Object[][] data = new Object[][] { { 1 }, { 2 }, { 3 }, { 4 } };
       return Arrays.asList(data);
     }

     @Test
     public void pushTest() {
       System.out.println("Parameterized Number is : " + number);
     }
}


Java
```

这里有很多限制，我们必须遵循“JUnit”的方式来声明参数，并且必须将参数传递给构造函数才能初始化类成员作为测试的参数值。参数类的返回类型为“`List []`”，数据已被限制为String或用于测试的原始类型值。

**TestNG**

XML文件或“`@DataProvider`”用于提供不同参数进行测试。

用于参数化测试的XML文件 -

只有“`@Parameters`”在需要参数测试的方法中声明，参数化数据将在TestNG的XML配置文件中提供。 通过这样做，我们可以使用不同数据集的单个测试用例，甚至获得不同的结果。 另外，即使是最终用户，QA还是QE都可以在XML文件中提供自己的数据进行测试。

```java
public class TestNGTest6_1_0 {

@Test
@Parameters(value="number")
public void parameterIntTest(int number) {
       System.out.println("Parameterized Number is : " + number);
    }
}


Java
```

XML文件的内容如下 -

```xml
<suite name="My test suite">
  <test name="testing">

    <parameter name="number" value="2"/>

    <classes>
       <class name="com.fsecure.demo.testng.TestNGTest6_0" />
    </classes>
  </test>
</suite>


XML
```

**@DataProvider用于参数化测试**

将数据值拉入XML文件可能非常方便，但测试偶尔会需要复杂的类型，这些类型不能被表示为一个字符串或一个原始类型值。 TestNG使用`@DataProvider`注解来处理这种情况，这有助于将复杂参数类型映射到测试方法。

[@DataProvider](https://github.com/DataProvider) for Vector，String或Integer作为参数，参考如下代码 -

```java
@Test(dataProvider = "Data-Provider-Function")
    public void parameterIntTest(Class clzz, String[] number) {
       System.out.println("Parameterized Number is : " + number[0]);
       System.out.println("Parameterized Number is : " + number[1]);
    }

    //This function will provide the patameter data
    @DataProvider(name = "Data-Provider-Function")
    public Object[][] parameterIntTestProvider() {
        return new Object[][]{
                   {Vector.class, new String[] {"java.util.AbstractList",
"java.util.AbstractCollection"}},
                   {String.class, new String[] {"1", "2"}},
                   {Integer.class, new String[] {"1", "2"}}
                  };
    }


Java
```

**@DataProvider作为对象的参数**

“TestNGTest6_3_0”是一个简单的对象，只需使用`get`/`set`方法进行演示。

```java
@Test(dataProvider = "Data-Provider-Function")
public void parameterIntTest(TestNGTest6_3_0 clzz) {
   System.out.println("Parameterized Number is : " + clzz.getMsg());
   System.out.println("Parameterized Number is : " + clzz.getNumber());
}

//This function will provide the patameter data
@DataProvider(name = "Data-Provider-Function")
public Object[][] parameterIntTestProvider() {

    TestNGTest6_3_0 obj = new TestNGTest6_3_0();
    obj.setMsg("Hello");
    obj.setNumber(123);

    return new Object[][]{
               {obj}
    };
}


Java
```

TestNG的参数化测试非常用户友好和灵活(在XML文件或类内)。 它可以支持许多复杂的数据类型作为参数值，可能性是无限的。 如上例所示，我们甚至可以传入我们自己的对象(`TestNGTest6_3_0`)进行参数化测试

## 7.依赖性测试

“参数化测试”表示方法是依赖性测试，它将在所需方法之前执行。 如果依赖方法失败，则所有后续测试将会被跳过，不会被标记为失败。

**JUnit 4**

JUnit框架着重于测试隔离; 目前它不支持此功能。

**TestNG**

它使用“`dependOnMethods`”来实现依赖测试如下 -

```java
@Test
public void method1() {
   System.out.println("This is method 1");
}

@Test(dependsOnMethods={"method1"})
public void method2() {
    System.out.println("This is method 2");
}


Java
```

“`method2()`”只有在“`method1()`”运行成功的情况下才会执行，否则“`method2()`”将跳过测试。

## 结论

在考虑所有功能比较之后，建议使用TestNG作为Java项目的核心单元测试框架，因为TestNG在参数化测试，依赖测试和套件测试(分组概念)方面更加突出。 TestNG用于高级测试和复杂集成测试。 它的灵活性对于大型测试套件尤其有用。 此外，TestNG还涵盖了整个核心的JUnit4功能。这样说来，好像也没有理由使用JUnit了。