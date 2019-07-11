          本篇开始介绍Java+Selenium+POM的简单自动化测试框架设计。第一个设计点，就是支持跨浏览器测试。从我个人角度来说，跨浏览器测试就是：同一个测试用例，支持用不同浏览器去执行。我们这里只考虑winodws平台的浏览器，而且只关注火狐和谷歌浏览器的自动化测试，我们已经知道IE执行很慢，而且容易出问题。那到底要怎么做呢，才能支持一个自动化用例能够在不同浏览器上运行？我是通过读取配置文件的值，来切换不同浏览器运行测试脚本。

开发思路：

1. 写一个配置文件，例如properties文件，里面写好浏览器的类型和测试服务器的地址，方便切换

2 .编写一个浏览器引擎类，通过读取配置文件加上if语句去控制启动不同浏览器。

3. 测试脚本调用浏览器引擎类实例，得到driver，开始测试自动化脚本

4.利用TestNG编写一个测试类文件，测试切换不同浏览器是否脚本运行成功。

eclipse项目结构如下图。



配置文件config.properties内容如下

# browser switcher
#browserName = Firefox
browserName = Chrome
#browserName = IE

# test server switcher
URL=http://re.jd.com/
#URL=https://taobao.com
通过取消前面注释标记#符号，我们可以一次只运行一个浏览器平台测试，测试地址的切换也是同样道理。

浏览器引擎类代码如下：
```
package framework;
 
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;
import java.util.concurrent.TimeUnit;
 
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.firefox.FirefoxProfile;
import org.openqa.selenium.ie.InternetExplorerDriver;
 
public class BrowserEngine {
	
	public String browserName;
	public String serverURL;
	public WebDriver driver;
	
	public void initConfigData() throws IOException{
		
		Properties p = new Properties();
		// 加载配置文件
		InputStream ips = new FileInputStream(".\\TestConfig\\config.properties");
		p.load(ips);
		
		Logger.Output(LogType.LogTypeName.INFO, "Start to select browser name from properties file");
		browserName=p.getProperty("browserName");
		Logger.Output(LogType.LogTypeName.INFO, "Your had select test browser type is: "+ browserName);
		serverURL = p.getProperty("URL");
		Logger.Output(LogType.LogTypeName.INFO, "The test server URL is: "+ serverURL);
		ips.close();
		
	}
	
	public WebDriver getBrowser(){
		
		if(browserName.equalsIgnoreCase("Firefox")){
			
			System.setProperty("webdriver.gecko.driver", ".\\Tools\\geckodriver.exe");	
			driver = createFireFoxDriver();
			
			Logger.Output(LogType.LogTypeName.INFO, "Launching Firefox ...");
			
		}else if(browserName.equalsIgnoreCase("Chrome")){
			
			System.setProperty("webdriver.chrome.driver", ".\\Tools\\chromedriver.exe");
			driver= new ChromeDriver();
			Logger.Output(LogType.LogTypeName.INFO, "Launching Chrome ...");
			
		}else if(browserName.equalsIgnoreCase("IE")){
			
			System.setProperty("webdriver.ie.driver", ".\\Tools\\IEDriverServer.exe");
			driver= new InternetExplorerDriver();
			Logger.Output(LogType.LogTypeName.INFO, "Launching IE ...");
		}
		
		driver.get(serverURL);
		Logger.Output(LogType.LogTypeName.INFO, "Open URL: "+ serverURL);
		driver.manage().window().maximize();
		Logger.Output(LogType.LogTypeName.INFO, "Maximize browser...");
		callWait(5);
		return driver;
	}
	
	/*
	 * 关闭浏览器并退出方法
	 */
	
	public void tearDown() throws InterruptedException{
		
		Logger.Output(LogType.LogTypeName.INFO, "Closing browser...");
		driver.quit();
		Thread.sleep(3000);
	}
	
	/*
	 * 隐式时间等待方法
	 */
	public void callWait(int time){
		
		driver.manage().timeouts().implicitlyWait(time, TimeUnit.SECONDS);
		Logger.Output(LogType.LogTypeName.INFO, "Wait for "+time+" seconds.");
	}
	
	
	/*
	 * createFireFox Driver
	 * @Param: null
	 * @return: WebDriver
	 */
	
	private WebDriver createFireFoxDriver() {
		
		WebDriver driver = null;
		FirefoxProfile firefoxProfile = new FirefoxProfile();
 
		firefoxProfile.setPreference("prefs.converted-to-utf8", true);
		//set download folder to default folder: TestDownload
		firefoxProfile.setPreference("browser.download.folderList", 2);
		firefoxProfile.setPreference("browser.download.dir", ".\\TestDownload");
		
		try {
				driver = new FirefoxDriver(firefoxProfile);
		} catch (Exception e) {
			Logger.Output(LogType.LogTypeName.ERROR, e.getMessage());
			Logger.Output(LogType.LogTypeName.ERROR, "Failed to initilize the Firefox driver");
		}
		return driver;
	}
 
	
}
```
日志输入，我采用java方法，没有用log4j，如果日志这块出错，请回到前面纯java方式生成日志这篇文件去学习下。
新建一个测试类，内容如下，打开京东
```
package testSuite;
 
import java.io.IOException;
 
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.testng.annotations.AfterClass;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;
 
import framework.BrowserEngine;
 
public class TestLaunchBrowser {
	
	public WebDriver driver;
	
	@BeforeClass
	public void setUp() throws IOException{
		
		BrowserEngine browserEngine = new BrowserEngine();
		browserEngine.initConfigData();
		driver=browserEngine.getBrowser();
		
	}
	
	
	@Test
	public void clickNews(){
	    
		driver.findElement(By.id("key")).sendKeys("iPhone 7");
		driver.findElement(By.xpath("//*[@id='search']/div/div[2]/button")).click();
		
	}
	
	@AfterClass
	public void tearDown(){
		
		driver.quit();
	}
 
 
}
```
       这里要强调的就是setUp方法中，如何获取浏览器driver这个实例对象并赋值给当前测试脚本中定义的driver，这个一定要理解好。一句话解释就是，你在浏览器引擎类定义了一个driver对象，在测试脚本中又定义了一个driver对象，你需要保持整个测试过程，只有一个唯一的driver，否则会报错，测试脚本不会执行查找元素和点击事件。这个建议要好好去理解一下这个为什么要保持唯一性，为什么赋值。这个是设计自动化测试框架的核心的一点思想和必须要面临和解决的问题，参考一个程序只能有一个main方法一样的道理。
