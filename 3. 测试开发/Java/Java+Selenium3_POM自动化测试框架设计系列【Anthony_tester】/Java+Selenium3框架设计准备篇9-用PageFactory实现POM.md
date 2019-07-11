[TOC]

前面介绍了POM的优点和非POM方式写脚本，这篇介绍利用页面工厂类（page factory）去实现POM，通过查看PageFactory类，我们可以知道它是一个初始化一个页面实例的功能，在实例化该页面对象时候，也会一起实例化该页面的元素定位。直接来看看京东登录的例子，如果用POM实现，在测试脚本中实际代码就2行。

1.在pageObjects包新建一个京东主页类，代码如下
```java
package pageObects;

import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;

public class JdHomePage {

	// 元素定位
	//登录链接
	@FindBy (id="ttbar-login")
	WebElement login_link;

	//账户登录
	@FindBy (xpath="//*/div[@class='login-form']/div[2]/a")
	WebElement login_withAccount;
   
	//输入用户名框
	@FindBy (id="loginname")
	WebElement inputBox_username;

	//输入密码
	@FindBy (id="nloginpwd")
	WebElement inputBox_password;

	//登录按钮
	@FindBy (id="loginsubmit")
	WebElement login_submitBtn;

	// 业务逻辑和操作方法
    
    //登录方法
    public void login(String username, String password){
    	
    	login_link.click();
    	login_withAccount.click();
    	inputBox_username.sendKeys(username);
    	inputBox_password.sendKeys(password);
    	login_submitBtn.click();
    	
    }
}
```
2.在testSuites包下新建一个测试类
```java
package testSuites;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.support.PageFactory;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;

import pageObects.JdHomePage;

public class TestWithPOM {
	WebDriver driver;
	
	@BeforeClass
	public void setUp() throws Exception{
		
		System.setProperty("webdriver.chrome.driver", ".\\Tools\\chromedriver.exe");
		driver = new ChromeDriver();
		driver.manage().window().maximize();
		driver.get("https://www.jd.com/");
		Thread.sleep(2000);
	}

	@Test
	public void testLogin(){
		
		JdHomePage hp = PageFactory.initElements(driver, JdHomePage.class);
		hp.login("user1", "123456");
	}
}
```
​       处理打开浏览器和打开京东主页操作，实际测试登录的代码就2行对不对。这里我们借助了PageFactory类的方法，在初始化一个页面类的时候，也会一起把该页面定义的元素定位也会初始化，然后通过页面对象调用页面类的页面操作方法。这种方式，我们把元素定位和业务逻辑操作都写在了页面对象类中，测试脚本类，直接调用页面类的方法，这样的测试脚本看起来很简洁，方便阅读和维护，如果登录相关文案发生变更，我们只需要去改登录对应页面的元素定位和业务逻辑，不需要修改这个登录测试类。
