先看下入口函数

```
     PageFactory.initElements(new AppiumFieldDecorator(mDriver, 5, TimeUnit.SECONDS),
                mQQPageObject);
```

绑定注入容器（QQPageObject）

```
public static void initElements(FieldDecorator decorator, Object page) {
    //获取所有父类
    Class<?> proxyIn = page.getClass();
    while (proxyIn != Object.class) {
      proxyFields(decorator, page, proxyIn);
      proxyIn = proxyIn.getSuperclass();
    }
  }
```

proxyFields()创建所有字段的代理类

```
  private static void proxyFields(FieldDecorator decorator, Object page, Class<?> proxyIn) {
    //获取所有声明的字段
    Field[] fields = proxyIn.getDeclaredFields();
    for (Field field : fields) {
      //获取代理对象
      Object value = decorator.decorate(page.getClass().getClassLoader(), field);
      if (value != null) {
        try {
          field.setAccessible(true);
        //重新设置字段
          field.set(page, value);
        } catch (IllegalAccessException e) {
          throw new RuntimeException(e);
        }
      }
    }
  }
```

先获取所有字段，再获取字段的代理对象，并重新赋值给注入容器

```
  public Object decorate(ClassLoader ignored, Field field) {
      //使用默认的字段包装器
        Object result = defaultElementFieldDecoracor.decorate(ignored, field);
        if (result != null) {
            return result;
        }
        //Widget包装器
        return decorateWidget(field);
    }
```

先通过默认包装器构造代理对象，如果没有再通过widget包装器构造

```
public Object decorate(ClassLoader loader, Field field) {
    //判断类型
    if (!(WebElement.class.isAssignableFrom(field.getType())
          || isDecoratableList(field))) {
      return null;
    }
    //创建一个元素定位器
    ElementLocator locator = factory.createLocator(field);
    if (locator == null) {
      return null;
    }
    //创建一个元素定位器的代理对象
    if (WebElement.class.isAssignableFrom(field.getType())) {
      return proxyForLocator(loader, locator);
    } else if (List.class.isAssignableFrom(field.getType())) {
      return proxyForListLocator(loader, locator);
    } else {
      return null;
    }
  }
```

元素定位器的创建时一个多工厂模式创建的 factory.createLocator(field);
 来看看factory的来源.我们在第一步PageFactory.initElements()传入了一个AppiumFieldDecorator字段包装器，

```
  public AppiumFieldDecorator(SearchContext context, TimeOutDuration timeOutDuration) {
      ...省略无关代码....
        defaultElementFieldDecoracor = new DefaultFieldDecorator(
          //创建了一个具体的元素定位器的工厂
            new AppiumElementLocatorFactory(context, timeOutDuration, originalDriver,
                new DefaultElementByBuilder(platform, automation))) {
          ........省略无关代码......
        widgetLocatorFactory =
            new AppiumElementLocatorFactory(context, timeOutDuration, originalDriver,
                new WidgetByBuilder(platform, automation));
    }
```

回到factory创建定位器的 ElementLocator locator = factory.createLocator(field);

```
    @Override public CacheableLocator createLocator(AnnotatedElement annotatedElement) {
         ......
        //构建一个查找元素的方法
        By by = builder.buildBy();
        if (by != null) {
        //创建一个元素定位器实现对象
            return new AppiumElementLocator(searchContext, by, builder.isLookupCached(),
                    customDuration, timeOutDuration, originalWebDriver);
        }
        return null;
    }
```

builder.buildBy()是如何构建一个查找元素具体方法的？其实builder是一个抽象类，我们是在AppiumFieldDecorator的构造方法中框架自动给创建一个DefaultElementByBuilder对象。buildBy()实现如下

```
@Override public By buildBy() {
       //断言用户使用的注解是否正确，例如AndroidFindBy 与AndroidFindBys不能同时在一个字段上使用
        assertValidAnnotations();
        //解析用户的注解并创建一个By
        //公用注解
        By defaultBy = buildDefaultBy();
        //不同系统版本的注解解析
        By mobileNativeBy = buildMobileNativeBy();
      
        String idOrName = ((Field) annotatedElementContainer.getAnnotated()).getName();

        if (defaultBy == null && mobileNativeBy == null) {
            defaultBy =
                new ByIdOrName(((Field) annotatedElementContainer.getAnnotated()).getName());
            mobileNativeBy = new By.ById(idOrName);
            return returnMappedBy(defaultBy, mobileNativeBy);
        }
      ......
    }
```

有了元素定位方式By后就创建AppiumElementLocator给DefaultFieldDecorator包装器使用了，回到DefaultFieldDecorator.decorate()方法

```
  public Object decorate(ClassLoader loader, Field field) {
    if (!(WebElement.class.isAssignableFrom(field.getType())
          || isDecoratableList(field))) {
      return null;
    }
    //创建好定位器了
    ElementLocator locator = factory.createLocator(field);
    if (locator == null) {
      return null;
    }
    //创建定位代理器
    if (WebElement.class.isAssignableFrom(field.getType())) {
      return proxyForLocator(loader, locator);
    } else if (List.class.isAssignableFrom(field.getType())) {
      return proxyForListLocator(loader, locator);
    } else {
      return null;
    }
  }
```

创建一个定位代理器

```
  protected WebElement proxyForLocator(ClassLoader loader, ElementLocator locator) {
    InvocationHandler handler = new LocatingElementHandler(locator);

    WebElement proxy;
    proxy = (WebElement) Proxy.newProxyInstance(
        loader, new Class[]{WebElement.class, WrapsElement.class, Locatable.class}, handler);
    return proxy;
  }
```

这是最核心的动态代理。看看处理器LocatingElementHandler

```
public class LocatingElementHandler implements InvocationHandler {
  private final ElementLocator locator;

  public LocatingElementHandler(ElementLocator locator) {
    this.locator = locator;
  }

  public Object invoke(Object object, Method method, Object[] objects) throws Throwable {
    WebElement element;
    try {
    //通过定位器来查找元素
      element = locator.findElement();
    } catch (NoSuchElementException e) {
      if ("toString".equals(method.getName())) {
        return "Proxy element for: " + locator.toString();
      }
      throw e;
    }
    //如果调用的是getWrappedElement方法则直接返回不代理调用方法
    if ("getWrappedElement".equals(method.getName())) {
      return element;
    }

    try {
      //再调用元素的真正方法  
      return method.invoke(element, objects);
    } catch (InvocationTargetException e) {
      // Unwrap the underlying exception
      throw e.getCause();
    }
  }
}
```

这样也就是我们在调用WebElement.click()方法的时候回进入到代理器的invoke()方法，先查找到元素，再去真正的执行click()方法。而这个代理器，最后会设置给pageobject的字段。

```
 public static void initElements(FieldDecorator decorator, Object page) {
    Class<?> proxyIn = page.getClass();
    while (proxyIn != Object.class) {
      proxyFields(decorator, page, proxyIn);
      proxyIn = proxyIn.getSuperclass();
    }
  }

  private static void proxyFields(FieldDecorator decorator, Object page, Class<?> proxyIn) {
    Field[] fields = proxyIn.getDeclaredFields();
    for (Field field : fields) {
      Object value = decorator.decorate(page.getClass().getClassLoader(), field);
      if (value != null) {
        try {
          field.setAccessible(true);
          //把动态代理对象重新设置回来
          field.set(page, value);
        } catch (IllegalAccessException e) {
          throw new RuntimeException(e);
        }
      }
    }
  }
```

所以当我们在调用字段的方法时，实际是调用一个代理对象。
 再看看代理器是怎么通过被代理对象locator来找元素的。locator.findElement()
 从上面的分析locator是一个AppiumElementLocator

```
 public WebElement findElement() {
      //如果有缓存则直接用
        if (cachedElement != null && shouldCache) {
            return cachedElement;
        }

        try {
            WebElement result =  waitFor(() ->
                    //查找元素
                    searchContext.findElement(by));
            if (shouldCache) {
                cachedElement = result;
            }
            return result;
        } catch (TimeoutException | StaleElementReferenceException e) {
            throw new NoSuchElementException(exceptionMessageIfElementNotFound, e);
        }
    }
```

searchContext.findElement(by);其实是通过RemoteWebDriver的findElement(By by) 方法查找,又转移到By方法查找

```
  public WebElement findElement(SearchContext context) {
    List<WebElement> allElements = findElements(context);
    if (allElements == null || allElements.isEmpty())
      throw new NoSuchElementException("Cannot locate an element using "
          + toString());
    return allElements.get(0);
  }
```

假如这个By是ById那么转移到ById的findElement()方法中了

```
  @Override
    public WebElement findElement(SearchContext context) {
      if (context instanceof FindsById)
        return ((FindsById) context).findElementById(id);
      return ((FindsByXPath) context).findElementByXPath(".//*[@id = '" + id
          + "']");
    }
```

最后还是转移到RemoteWebDriver中

```
  protected WebElement findElement(String by, String using) {
    if (using == null) {
      throw new IllegalArgumentException("Cannot find elements when the selector is null.");
    }
    //这里是真正执行网络通信查找控件的方法
    Response response = execute(DriverCommand.FIND_ELEMENT,
        ImmutableMap.of("using", by, "value", using));
    //接收网络返回的WebElement
    Object value = response.getValue();
    WebElement element;
    try {
      element = (WebElement) value;
    } catch (ClassCastException ex) {
      throw new WebDriverException("Returned value cannot be converted to WebElement: " + value, ex);
    }
    //标识查找的方式
    setFoundBy(this, element, by, using);
    return element;
  }
```

execute方法就是拼json参数，形成httpRequest请求，通过HttpClient发送请求，
 解析返回的HttpResponse，用selenium的Response接收，最终封装成我们需要的WebElement.

总结下：pageobject模式其实就是依赖注入+动态代理实现的，每一个pageobject都可以看成一个注入容器，而我们查找控件的操作看成一个切面，通过动态代理实现AOP切面编程。



作者：yangzai

链接：https://www.jianshu.com/p/25931b8bfb2a

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。