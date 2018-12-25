       前面一篇，我介绍了BasePage.java的内容，解释了写这个类的好处。主要有二次封装selenium一些常见方法，还有就是创建所有页面类的父类。这篇，我们借助京东商城这个网站来举例如何实现POM并测试我们的BasePage类。

完整eclipse项目结构图如下：

![img](https://img-blog.csdn.net/20170809230707281?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

1.BasePage.java内容。

       由于点击一个商品，会在新tab打开显示商品详情页，所以，这里我们需要用到浏览器窗口之间切换，这个场景，我在之前的文章介绍过实现方法，这里我们把实现方法添加到我们BasePage.java类里面。
```
package framework;
 
import java.util.Iterator;
import java.util.Set;
 
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
 
public class BasePage {
 
	public static WebDriver driver;
	public static String pageTitle;
	public static String pageUrl;
	
	/*
	 * 构造方法
	 */
	public BasePage (WebDriver driver){
		BasePage.driver = driver;
	}
	
	/*
	 * 在文本框内输入字符
	 */
	protected void type(WebElement element , String text){
		try {
				if (element.isEnabled()) {
				    element.clear();
				    Logger.Output(LogType.LogTypeName.INFO, "Clean the value if any in "+ element.toString()+".");
					element.sendKeys(text);
					Logger.Output(LogType.LogTypeName.INFO, "Type value is: " + text+".");
				}
			} catch (Exception e) {
				Logger.Output(LogType.LogTypeName.ERROR, e.getMessage()+".");
				}
			
		}
	
	/*
	 * 点击元素，这里指点击鼠标左键
	 */	
	protected void click(WebElement element){
			
		try {
				if (element.isEnabled()) {
						element.click();
						Logger.Output(LogType.LogTypeName.INFO, "Element: "+element.toString()+" was clicked.");
					}
				} catch (Exception e) {
						Logger.Output(LogType.LogTypeName.ERROR, e.getMessage()+".");
						}
			
		}
		
	/*
	 * 在文本输入框执行清除操作
	 */
	protected void clean(WebElement element){
			
		try {
				if (element.isEnabled()) {
					element.clear();
					Logger.Output(LogType.LogTypeName.INFO, "Element "+element.toString()+" was cleaned.");
				}
			} catch (Exception e) {
				Logger.Output(LogType.LogTypeName.ERROR, e.getMessage()+".");
			}
			
		}
	
	/*
	 * 判断一个页面元素是否显示在当前页面
	 */  
	protected void verifyElementIsPresent(WebElement element){
	    	
	    try {
				if (element.isDisplayed()) {
					Logger.Output(LogType.LogTypeName.INFO, "This Element " + element.toString().trim()+" is present.");
						
				}
			} catch (Exception e) {
				Logger.Output(LogType.LogTypeName.ERROR, e.getMessage()+".");
			}
	    }
	
	/*
	 * 获取页面的标题
	 */
	protected String getCurrentPageTitle(){
	    
	    pageTitle=driver.getTitle();
	    Logger.Output(LogType.LogTypeName.INFO, "Current page title is "+pageTitle);
	    return pageTitle;
	   }
	
	/*
	 * 获取页面的url
	 */
       protected String getCurrentPageUrl(){
	    
	    pageUrl=driver.getCurrentUrl();
	    Logger.Output(LogType.LogTypeName.INFO, "Current page title is "+pageUrl);
	    return pageUrl;
	   } 
   
      /*
       * 处理多窗口之间切换
       */
      public void switchWindow(){
	 	
	  String currentWindow = driver.getWindowHandle();// 获取当前窗口句柄  
          Set<String> handles = driver.getWindowHandles();// 获取所有窗口句柄  
          Logger.Output(LogType.LogTypeName.INFO, "当前窗口数量： "+ handles.size());  
          Iterator<String> it = handles.iterator();  
          while (it.hasNext()) {  
              if (currentWindow == it.next()) {  
            	   continue;
              }  
              try {  
            	  driver.close();// 关闭旧窗口
                  WebDriver window = driver.switchTo().window(it.next());// 切换到新窗口 
                  Logger.Output(LogType.LogTypeName.INFO, "new page title is "+ window.getTitle());
              } catch (Exception e) {  
            	   Logger.Output(LogType.LogTypeName.ERROR,"法切换到新打开窗口"+ e.getMessage());  
                 
            }  
            //driver.close();//关闭当前焦点所在的窗口  
        }  
        // driver.switchTo().window(currentWindow);//回到原来页面  
	}      
}
```
2.HomePage.java内容
​      目前，所有页面类，主要两部分，第一个部分是当前页面元素的定位，第二部分就是，一些业务逻辑方法的重构，方便测试脚本中调用。
```
package pageObject;
 
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;
import org.openqa.selenium.support.PageFactory;
 
import framework.BasePage;
 
public class HomePage extends BasePage{
	/*
	 * 京东商城的首页，主要提供一些其他子模块或者页面的入口，一般点击一个元素，进入下一页面
	 */
	
	// 元素定位
	//搜索输入框
	@FindBy (id="key")
	WebElement search_inputBox;
	
	//搜索提交按钮
	@FindBy (xpath="//*[@id='search']/div/div[2]/button")
	WebElement search_submitBtn;
	
 
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
 
}
```
3.SearchResultListPage.java内容
```
package pageObject;
 
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;
 
import framework.BasePage;
 
public class SearchResultListPage extends BasePage{
	/*
	 * 本页面是搜索结果列表页，通过首页输入关键字，点击搜索，跳转过来
	 */
	
	//元素定位
	//第一页第一个商品
	@FindBy (xpath="//*[@id='J_goodsList']/ul/li[1]/div/div[1]/a/img")
	WebElement searchResult_firstItem_img;
	
	//第一页第2个商品,为了和第一个对比
	@FindBy (xpath="//*[@id='J_goodsList']/ul/li[2]/div/div[1]/a/img")
	WebElement searchResult_secondItem_img;
	
	//第一个商品的价格
	@FindBy (xpath="//*[@id='J_goodsList']/ul/li[1]/div/div[3]/strong/i")
	WebElement searchResult_firstItem_price;
 
	public SearchResultListPage(WebDriver driver) {
		super(driver);
		
	}
	
	/*
	 * 点击商品图片，进入商品详情页
	 */
	public GoodsDetailsPage clickItemImg() throws InterruptedException{
		
		click(searchResult_firstItem_img);
		Thread.sleep(1000);
		return new GoodsDetailsPage(driver);
	}
	
	/*
	 * 获取商品价格，好和详情页价格对比
	 */
	public String getGoodsPriceOnListPage(){
		
		return searchResult_firstItem_price.getText();
		
	}
	
}
```
4.GoodsDetailsPage.java内容
```
package pageObject;
 
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;
 
import framework.BasePage;
 
public class GoodsDetailsPage extends BasePage {
	
	/*
	 * 商品详情页，价格对比，下单入口等操作
	 */
	
	
	@FindBy (xpath="//*/span[@class='p-price']/span[2]")
	WebElement goods_price;
	
	@FindBy (id="InitCartUrl")
	WebElement addToCart_Btn;
 
	public GoodsDetailsPage(WebDriver driver) {
		super(driver);
		
	}
	
	/*
	 * 获取商品详情页的价格
	 */
	public String getPriceOnDetailsPage(){
	
		return goods_price.getText();
	}
	
	/*
	 * 点击添加购物车
	 */
	public void addGoodToCart(){
		
		click(addToCart_Btn);
	}
}
```
5.测试脚本类的内容。
```
package testSuite;
 
import java.io.IOException;
 
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.support.PageFactory;
import org.testng.Assert;
import org.testng.annotations.AfterClass;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;
 
import framework.BrowserEngine;
import pageObject.GoodsDetailsPage;
import pageObject.HomePage;
import pageObject.SearchResultListPage;
 
public class TestCheckGoodsPrice {
	
	WebDriver driver;
	
	@BeforeClass
	public void setUp() throws IOException{
		
		BrowserEngine browserEngine = new BrowserEngine();
		browserEngine.initConfigData();
		driver = browserEngine.getBrowser();
		
	}
	
	
	@Test
	public void checkPrice() throws InterruptedException{
	    
		HomePage homepage = PageFactory.initElements(driver, HomePage.class);
		homepage.searchWithKeyword("iPhone");
		
		//进入搜索结果列表页
		SearchResultListPage srlp = PageFactory.initElements(driver, SearchResultListPage.class);
		Thread.sleep(1000);
		String price1 = srlp.getGoodsPriceOnListPage(); // 获取列表页商品的价格
		System.out.println(price1);
		srlp.clickItemImg();
		srlp.switchWindow(); // 调用窗口切换方法
		
		//进入商品详情页
		GoodsDetailsPage gdp = PageFactory.initElements(driver, GoodsDetailsPage.class);
		Thread.sleep(1000);
		String price2 = gdp.getPriceOnDetailsPage(); //获取商品详情页价格
		System.out.println(price2);
		
		//判断 同一个商品在列表页和详情页价格是否显示一致
//		if(price1 == price2){
//			System.out.println("Pass");
//		}else
//			System.out.println("Failed");
//			
		
		// 第二种断言
		Assert.assertEquals(price2, price1);
		gdp.addGoodToCart(); // 添加到购物车
	}
	
        //如果需要看清楚结果，把@AfterClass注销就好
	@AfterClass
	public void tearDown(){
		
		driver.quit();
	}
 
}
```
我运行成功的环境：
OS： win7 64bit

Firefox:  54 32bit

Chrome: 59 64bit

Selenium: 3.4



总结：

       实际上POM框架，我们已经实现了全部功能，接下来的就是一些框架本身的一些扩展和必要的组件。本篇要好好学，好好去体会，每个页面之间，driver是如何跳转和赋值的。这个非常重要，还是前面说过的一句话，整个过程要保持driver的唯一性。我们现在的设计思想就是，一个driver实例对象，去按照你给定的执行顺序，一个一个地去执行多个测试用例。