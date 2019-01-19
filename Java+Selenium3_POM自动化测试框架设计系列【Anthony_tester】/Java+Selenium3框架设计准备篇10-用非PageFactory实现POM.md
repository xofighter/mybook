前面一篇我们用PageFactory实现了POM，这边我们介绍如果不用PageFactory如何实现POM。

1.页面类内容如下
```java
package pageObects;

import java.util.concurrent.TimeUnit;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;

public class JdHomePage {

	WebDriver driver;
	// 元素定位
	//登录链接
	By login_link = By.id("ttbar-login");
   
	//账户登录
	By login_withAccount = By.xpath("//*/div[@class='login-form']/div[2]/a");
	   
	//输入用户名框
	By inputBox_username = By.id("loginname");
	
	//输入密码
	By inputBox_password = By.id("nloginpwd");
	
	//登录按钮
	By login_submitBtn = By.id("loginsubmit");
	
	// 业务逻辑和操作方法
	// 构造方法
	public JdHomePage(WebDriver driver){
		this.driver = driver;
	}
	
	//点击登录链接
	public void clickLoginLink(){
		driver.findElement(login_link).click();
		// 点击账户登录，不点击的话默认是二维码扫描登录
		driver.findElement(login_withAccount).click();
	}
	
	// 登录步骤拆分-输入用户名
	public void inputUsername(String username){
		driver.findElement(inputBox_username).sendKeys(username);
		
	}
	
	// 登录步骤拆分-输入密码
	public void inputPassword(String password){
		driver.findElement(inputBox_password).sendKeys(password);
		
	}
	
	// 点击登录按钮
	public void clickLoginBtn(){
		driver.findElement(login_submitBtn).click();
		driver.manage().timeouts().implicitlyWait(2, TimeUnit.SECONDS);
	}
	
	//重构一个登录方法
	public void login(String name, String pass){
		driver.findElement(login_link).click();
		// 点击账户登录，不点击的话默认是二维码扫描登录
		driver.findElement(login_withAccount).click();
		
		driver.findElement(inputBox_username).sendKeys(name);
		driver.findElement(inputBox_password).sendKeys(pass);
		driver.findElement(login_submitBtn).click();
		driver.manage().timeouts().implicitlyWait(2, TimeUnit.SECONDS);
		
	}

}
```
2.测试类内容如下
```java
package testSuites;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
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
		
		JdHomePage hp = new JdHomePage(driver);
		
		hp.clickLoginLink();
		hp.inputPassword("user1");
		hp.inputUsername("123456");
		hp.clickLoginBtn();
		
	}
}
```
和前面PageFactory对比，发现两种元素定位方法不同，在本篇中需要注意的是，在页面类中一定要写构造方法，构造方法有一个driver参数，这样页面类被初始化一次，该页面类的实例对象就有driver这个属性，所以在脚本中@Test方法中需要写driver这个参数，如果不写，运行就会报空指针异常错误。
第二个想强调的就是，上面页面类比前面一篇文章写得更详细，把登录方法给拆分不同子步骤方法进行。我强烈建议大家在实际脚本开发也这么做，不要认为代码重复，因为你一个功能，可能要出测试多条类似的测试用例，就像登录这样，有不输入用户名和密码就点击登录的用例，有输入正确用户名和错误密码的用例，这个时候子方法拆分好处就体现出来。

两个方法都实现了POM，我也说不出哪个方法的更好。但是我要说的是，接下来，我们会采取使用PageFactory来实现我们的POM自动化测试框架，所以，我建议你和我一样，使用PageFactory来实现POM。

---------------------
作者：Anthony_tester 
来源：CSDN 
原文：https://blog.csdn.net/u011541946/article/details/75413436 
版权声明：本文为博主原创文章，转载请附上博文链接！