[TOC]

# POM 是什么
页面对象模型（Page Object Model），在Selenium Webdriver自动化测试中使用非常流行和受欢迎，作为自动化测试工程师应该至少听说过POM这个概念。本篇介绍POM的简介，接下来一步一步告诉你如何在你Java+Selenium3自动化测试框架中实现POM。
 ![img](https://img-blog.csdn.net/20170719151934324?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

## 在Selenium中使用POM的优势(特点和优点)

1. POM是一种设计模式，它能让我们方便维护代码和减少代码的重复(这个在自动化测试领域是一个很重要的话题)。
2. POM中把一个页面看做一个页面对象，每个页面写一个类，实现封装。
3. POM中把一个页面的元素的全部定位方式和该页面的业务操作方法写在一个类文件，然后在测试脚本类文件去调用页面类的元素和方法。
这样就有一个好处，你的软件产某一个界面出现了变化，你只需要修改对应页面的元素定位和相关方法，而不需要去修改你的测试脚本，当然除非你这个页面由于需求问题，之后被砍了，你才要去修改你的测试脚本类。
4. 在POM中把测试脚本和页面对象分层。例如页面对象统一放一个包下面，测试用例脚本统一放另外一个包下，到达测试脚本和页面对象分离。

## 非 POM 方式的测试代码
来看看之前我们一直使用非 POM 方式写脚本的例子。
```java
package testSuites;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.annotations.AfterClass;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;

public class TestWithoutPOM {

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
	public void testLogin() {
		
		// 点击登录
		driver.findElement(By.id("ttbar-login")).click();
		// 点击账号登录
		driver.findElement(By.xpath("//*/div[@class='login-form']/div[2]/a")).click();
		// 输入用户名
		driver.findElement(By.id("loginname")).sendKeys("user1");
		// 输入密码
		driver.findElement(By.id("nloginpwd")).sendKeys("password");
		//点击登录
		driver.findElement(By.id("loginsubmit")).click();
		
	}
	
	@AfterClass
	public void tearDown(){
		
		driver.quit();
	}

}
```
上面脚本中，包含了元素定位和业务逻辑操作方法，两者混合在一起，如果脚本注释写得不好，其他人很难看得懂，特别是写了很多脚本的时候，根本没有层次和逻辑可言。让维护脚本的新人会很头疼，下一篇介绍POM的实现，可以看看两者对比，有点和缺点一下子就看出来。

