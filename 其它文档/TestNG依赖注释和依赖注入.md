## 依赖注释

**@Test中的dependsOnMethods或dependsOnGroups属性**

这两种依赖：

- **Hard dependencies（硬依赖）**

所有的被依赖方法必须成功运行。只要有一个出问题，测试就不会被调用，并且在报告中被标记为SKIP。

- **Soft dependencies（软依赖）**

即便是有些依赖方法失败了，也一样运行。如果你只是需要保证你的测试方法按照顺序执行，而不关心他们的依赖方法是否成功。那么这种机制就非常有用。可以通过添加`"alwaysRun=true"`到`@Test`来实现软依赖。

硬依赖的例子：

```
@Test
public void serverStartedOk() {}

@Test(dependsOnMethods = { "serverStartedOk" })
public void method1() {}
```

此例中，`method1()`依赖于方法`serverStartedOk()`，从而保证 `serverStartedOk()`总是先运行

也可以让若干方法依赖于组：

```
@Test(groups = { "init" })
public void serverStartedOk() {}

@Test(groups = { "init" })
public void initEnvironment() {}

@Test(dependsOnGroups = { "init.* })
public void method1() {}
```

本例中,`method1()`依赖于匹配正则表达式`"init."`的组，由此保证了`serverStartedOk()`和`initEnvironment()`总是先于`method1()`被调用。

如果使用硬依赖，并且被依赖方法失败(alwaysRun=false，即默认是硬依赖)，依赖方法则不是被标记为FAIL而是SKIP。被跳过的方法会被在最后的报告中标记出来（HTML既不用红色也不是绿色所表示），主要是被跳过的方法不是必然失败，所以被标出来做以区别。

无论`dependsOnGroups`还是`dependsOnMethods`都可以接受正则表达式作为参数。对于`dependsOnMethods`，如果被依赖的方法有多个重载，那么所有的重载方法都会被调用。如果你只希望使用这些重载中的一个，那么就应该使用 `dependsOnGroups`

另外，也可以在的testng.xml文件中指定的组依赖关系。您可以使用<dependencies>标签来实现这一点：

```
<test name="My suite">
  <groups>
    <dependencies>
      <group name="c" depends-on="a  b" />
      <group name="z" depends-on="c" />
    </dependencies>
  </groups>
</test>
```

## 依赖注入

TestNG的支持两种不同类型的依赖注入：本机（由TestNG的本身执行）和外部（通过依赖注入框架，如Guice）

#### 本地依赖注入

TestNG 允许你在自己的方法中声明额外的参数。这时，TestNG会自动使用正确的值填充这些参数。依赖注入就使用在这种地方：

- 任何`@Before`或`@Test`方法可以声明一个类型为`ITestContext`的参数。
- 任何`@After`都可以声明一个类型为`ITestResult`的单数，它代表了刚刚运行的测试方法。
- 任何`@Before`和`@After`方法都能够声明类型为`XmlTest`的参数，它包含了当前的`<test>`参数。
- 任何`@BeforeMethod`可以声明一个类型为`java.lang.reflect.Method`的参数。这个参数会接收 `@BeforeMethod`完成调用的时候马上就被调用的那个测试方法当做它的值。
- 任何`@BeforeMethod`可以声明一个类型为`Object[]`的参数。这个参数会包含要被填充到下一个测试方法中的参数的列表，它既可以又 TestNG 注入，例如`java.lang.reflect.Method`或者来自`@DataProvider`。
- 任何`@DataProvider`可以声明一个类型为`ITestContext`或`java.lang.reflect.Method`的参数。后一种类型的参数，会收到即将调用的方法作为它的值。

可以使用@NoInjection关闭注释：

```
public class NoInjectionTest {

  @DataProvider(name = "provider")
  public Object[][] provide() throws Exception {
      return new Object[][] { { CC.class.getMethod("f") } };
  }

  @Test(dataProvider = "provider")
  public void withoutInjection(@NoInjection Method m) {
      Assert.assertEquals(m.getName(), "f");
  }

  @Test(dataProvider = "provider")
  public void withInjection(Method m) {
      Assert.assertEquals(m.getName(), "withInjection");
  }
}
```

#### Guice依赖注入

estNG给一个简单的方法来与Guice模块注入测试对象：

```
@Guice(modules = GuiceExampleModule.class)
public class GuiceTest extends SimpleBaseTest {

  @Inject
  ISingleton m_singleton;

  @Test
  public void singletonShouldWork() {
    m_singleton.doSomething();
  }

}
```

在这个例子中，`GuiceExampleModule`被预期绑定到`ISingleton`接口的一些具体的类：

```
public class GuiceExampleModule implements Module {

  @Override
  public void configure(Binder binder) {
    binder.bind(ISingleton.class).to(ExampleSingleton.class).in(Singleton.class);
  }

}
```

如果在指定的模块实例化你的测试类需要更多的灵活性，可以指定一个模块工厂：

```
@Guice(moduleFactory = ModuleFactory.class)
public class GuiceModuleFactoryTest {

  @Inject
  ISingleton m_singleton;

  @Test
  public void singletonShouldWork() {
    m_singleton.doSomething();
  }
}
```

模块工厂需要实现该`IModuleFactory`接口：

```
public interface IModuleFactory {
 /**
   * @param context The current test context
   * @param testClass The test class
   *
   * @return The Guice module that should be used to get an instance of this
   * test class.
   */
  Module createModule(ITestContext context, Class<?> testClass);
}
```

工厂将会通过了测试环境和TestNG需要实例化测试类的一个实例。
 `createModule`方法应该返回一个Guice模块，就会知道如何实例化这个测试类。可以使用测试环境，以了解环境的详细信息，如在规定的testng.xml设置参数等...

作者：我为峰2014

链接：https://www.jianshu.com/p/9b782a4ae12f

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。