       本篇主要是回答一个网友的问题，就是他可以执行登录，但是登录之后的用例就执行不了，报空指针异常。在这里，特意用京东网站，结合testNG来举例实现这个功能，解决这个报错。我们的场景大概是这样的，我需要先登录，然后在京东首页搜索一个商品。就这么一个简单的测试场景，我们的测试用例的重点是在搜索商品，测试的前提条件是登录。

解决思路：

1. 登录是搜索的前提条件

2.登录能不能封装到一个普通类中去，然后在测试脚本中setUp方法中调用登录的脚本

3.如何把登录页面的driver实例对象传递给首页，然后进行搜索。

按照这个思路，我们先把登录页面，在POM中写好，封装好登录的方法。然后新建一个Login类，主要是调用BrowserEngine类中的浏览器和测试地址。第三，在测试搜索类中的BeforeClass下调用Login类的方法，项目结构如下。（主要是下面红圈的四个类文件）

![img](https://img-blog.csdn.net/20170815225401646?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

1.HomePage中增加点击登录事件

package pageObject;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;
import framework.BasePage;

public class HomePage extends BasePage{
​	/*
​	 * 京东商城的首页，主要提供一些其他子模块或者页面的入口，一般点击一个元素，进入下一页面
​	 */
​	
​	// 元素定位
​	//搜索输入框
​	@FindBy (id="key")
​	WebElement search_inputBox;
​	
	//搜索提交按钮
	@FindBy (xpath="//*[@id='search']/div/div[2]/button")
	WebElement search_submitBtn;
	
	// 登录链接
	@FindBy (xpath="//*[@id='ttbar-login']/a[1]")
	WebElement login_lnk;

 


	public HomePage(WebDriver driver) {
		super(driver);
		
	}
	
	/*
	 * 搜索框输入关键字，点击搜索
	 */
	public SearchResultListPage searchWithKeyword(String keyword){
		
		type(search_inputBox, keyword);
		click(search_submitBtn);
		return new SearchResultListPage(driver); 
	}
	
	/*
	 * 点击登录链接
	 */
	
	public LoginPage clickLoginLnk(){
		
		click(login_lnk);
		return new LoginPage(driver);
	}
}
2.LoginPage主要是定义登录页面的元素和方法
package pageObject;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;

import framework.BasePage;

public class LoginPage extends BasePage {
​	
​	/*
​	 * 登录页面，这里主要测试账号和密码的登录方式，第三方登录暂时不考虑
​	 */
​	
	//元素定位
	//点击 账户登录 菜单
	@FindBy (xpath="//*[@id='content']/div/div[1]/div/div[2]/a")
	WebElement loginType_byAccount;
	
	// 用户名
	@FindBy (id="loginname")
	WebElement login_userName;
	
	// 密码
	@FindBy (id="nloginpwd")
	WebElement login_password;
	
	// 密码
	@FindBy (id="loginsubmit")
	WebElement login_submitBtn;
	
	// 构造函数
	public LoginPage(WebDriver driver) {
		super(driver);
	
	}
	
	/*
	 * 用户名和密码登录
	 */
	public HomePage login(String username, String password){
		
		click(loginType_byAccount);
		type(login_userName, username);
		type(login_password, password);
		click(login_submitBtn);
		return new HomePage(driver);
	}

}
3 Login类，完全就是单独拿出来表示一个模块或者功能来使用。
package pageObject.functions;

import java.io.IOException;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.support.PageFactory;

import framework.BrowserEngine;
import pageObject.HomePage;
import pageObject.LoginPage;

public class Login {
​	
​	public WebDriver driver;
​	
	//第一步，获取浏览器类型，打开浏览器。打开测试网站
	public void initSetup() throws IOException{
		
		BrowserEngine browser = new BrowserEngine();
		browser.initConfigData();
		driver = browser.getBrowser();
	}
	
	// 用page factory类来加载初始化主页元素，点击登录这个链接
	public void loginValid(){
		
		HomePage homepage = PageFactory.initElements(driver, HomePage.class);
		//点击主页上的登录链接
		homepage.clickLoginLnk();
		//跳转到登录页面，初始化登录界面的元素
		LoginPage loginpage = PageFactory.initElements(driver, LoginPage.class);
		//测试一个有效的用户名登录
		loginpage.login("你自己的用户名", "你自己密码");
		
	}

}
4. 测试类的写法
    package testSuite;

import java.io.IOException;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.support.PageFactory;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;

import pageObject.HomePage;
import pageObject.functions.Login;

public class Test_HomePage_Search {
​	
​	public WebDriver driver;
​	
	@BeforeClass
	public void setUp() throws IOException{
		
		Login login = new Login();
		login.initSetup();
		login.loginValid();
		driver = login.driver;
		
	}


​	
​	@Test
​	public void test_searchGoods(){
​		
		HomePage hp = PageFactory.initElements(driver, HomePage.class);
		hp.searchWithKeyword("Java");


​		
​	}

 

}
​       注意上面setUp内的写法，先初始化一个Login类的实例，然后调用相关方法，最后得到driver传递给当前测试类定义的driver，这样接下来@Test的方法才能顺利执行，不会报空指针的问题。
​       这个问题暂时得到了解决，但是还是有缺陷，如果有几十个测试类文件，如何保证，只开一次浏览器，只登陆一次，测试完几十个测试用例之后，才关闭浏览器。这个问题值得思考，在当前测试脚本类中写多个@Test方法，这个笨方法就不考虑。好像利用TestNG中的group，然后配置testng.xml也达不到，启动一次浏览器，执行几百个脚本用例，然后关闭浏览器的效果，这个有待研究。我项目中，还是分模块来执行，一个模块，启动一次浏览器，执行十几个用例，然后关闭，执行下一个模块， 依然还需要启动浏览器。这个有待优化，有没有人有更好的方法，谢谢分享出来，大家一起学习。
​       

---------------------