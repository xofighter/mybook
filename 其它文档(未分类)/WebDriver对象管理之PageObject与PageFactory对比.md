在之前的<a href="http://www.jianshu.com/p/71e04066be61">自动化框架搭建</a>文章中有提到过对象管理（或元素管理），而WebDriver给我们提供了两种对象管理思路PageObject与PageFactory，今天我就对比下两种方式的异同~
 



![img](https:////upload-images.jianshu.io/upload_images/2951233-c2342b3b929ef403?imageMogr2/auto-orient/strip%7CimageView2/2/w/640/format/webp)



关于PageObject

 将一个页面或一类元素看做是一个页面对象，一个页面对象对应一个类，将元素对象的获取定义在这一个类中。（解释总显得那么苍白，看代码吧）

 方式一：



```
public class HomePage {
   protected WebDriver driver
   public HomePage(WebDriver driver) {
       this.driver= driver;
   }
   public WebElement getUserName() {
     By by = By.xpath("//span[@class='name']");
     return driver.findElement(by);
   }
   public List<WebElement> getLinks() {
     By by = By.xpath("//a");
     return driver.findElements(by);
   }
   public WebElement getHomeLink() {
     By by = By.xpath("//a[contains(text(),'首页')]");
     return driver.findElement(by);
   }
}
```

方式二：

```
public class HomePage {
   protected WebDriver driver
   public HomePage(WebDriver driver) {
     this.driver= driver;
   }
   public WebElement getUserName() {
     By by = By.xpath("//span[@class='name']");
     return isElementExist(by) ? driver.findElement(by) : null;
   }
   public List<WebElement> getLinks() {
     By by = By.xpath("//a");
     return isElementsExist(by) ? driver.findElement(by) : null;
   }
   public WebElement getHomeLink() {
     By by = By.xpath("//a[contains(text(),'首页')]");
     return isElementExist(by) ? driver.findElement(by) : null;
   }
}
```

方式一直接返回findElement()方法，遇到元素未定位到等情况会抛出NoSuchElementException 异常，从而中断测试，所以我这边给出了方式二（推荐方式二），这里给出isElementExist()方法：

```
public static boolean isElementExist(By selector){
     try {
       driver.findElement(selector); 
       return true;
     } catch (NoSuchElementException e) {
       return false;
     }
}
```

**关于PageFactory**
 整体思想同PageObject，只是在表现形式上通过注解（@FindBy）的方式定义元素对象，看代码：****
 方式一：

```
public class HomePage {
 @FindBy(xpath = "//span[@class='name']")
 @CacheLookup
 public static WebElement userName;
 
 @FindBy(xpath = "//a")
 @CacheLookup
 public static List<WebElement> links;
 
 @FindBy(xpath = "//a[contains(text(),'首页')]")
 @CacheLookup
 public static WebElement homeLink;
}
```

使用对象的时候需要先初始化类：

```
PageFactory.initElements(driver ,HomePage.class);
HomePage.homeLink.click();
```

方式二：

```
public class HomePage extends BasePage{
 public HomePage(WebDriver driver) {
        super(driver);
}
 public HomePage(WebDriver driver, String titile) {
        super(driver, titile);
  }
 @FindBy(xpath = "//span[@class='name']")
 @CacheLookup
 public WebElement userName;
 
 @FindBy(xpath = "//a")
 @CacheLookup
 public List<WebElement> links;
 
 @FindBy(xpath = "//a[contains(text(),'首页')]")
 @CacheLookup
 public WebElement homeLink;
}

public class BasePage {
 public static final String MAIN_TITLE = "XXX平台";
 public WebDriver driver;
 public String title;
        private final int TIMEOUT = 10;
    
        public BasePage() {}
    
        public BasePage(WebDriver driver) {
            this.driver = driver;
            PageFactory.initElements(new AjaxElementLocatorFactory(driver, TIMEOUT) , this);
        }
    
        public BasePage(WebDriver driver, final String title) {
            this.driver = driver;
            this.title = title;
            try{
         new WebDriverWait(driver,TIMEOUT).until(new ExpectedCondition<Boolean>(){
                @Override
                public Boolean apply(WebDriver arg0) {
                    // TODO Auto-generated method stub
                    String acttitle = arg0.getTitle();
                    return acttitle.equals(title);                    
                }});
        }catch(TimeoutException te) {
            throw new IllegalStateException("当前页面title(" + driver.getTitle() + ")与预期("+ title +")不一致");
        }
        PageFactory.initElements(new AjaxElementLocatorFactory(driver, TIMEOUT) , this);
    }
}
```

方式二（推荐使用）增加了页面和元素等待时间，比方式一更加健壮，使用对象时也更加清晰：

```
HomePage home = new HomePage(driver,"XXX");
home.homeLink.click();
```

**总结**
 上面通过代码的形式给出了PageObject与PageFactory的区别，并且通过代码优化的方式给出了两种对象管理思路推荐的使用方式，两种方式我平时都有使用，有时候还混着用，总体来说并不能说谁好谁坏，各有千秋，看个人的习惯，在元素判断上我觉得PageObject方便一些，元素不存在就是null，这个思路很直接；PageFactory的好处除了使用注解使代码更整洁之外，还可以设置页面和元素加载等待时间，总体来说，还是推荐PageFactory

作者：测试开发栈

链接：https://www.jianshu.com/p/de643bbf534b

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。