       前面我们介绍了如何设计支持不同浏览器测试，我们的解决方法是，通过切换配置文件设置的浏览器名称的值，来确定启动什么浏览器进行脚本测试。我们暂且叫浏览器引擎类，这个类负责获取浏览器类型和启动不同浏览器，并做一些前提操作，例如最大化浏览器窗口和，打开测试服务器地址。接下来，这篇我们介绍如何封装几个Selenium方法到我们的页面基类中去。在这之前，我们要解释一下页面基类，看到基类，我们想起了继承。没错，在这个框架基于POM的思想上，我们需要利用继承的特点，来实现，减少我们重复代码量。为什么要定义一个页面基类呢？我们已经知道了POM，实际上，我们每个模块或者相关功能，都能在一个个页面类上去定义和写相关业务操作方法。很多页面，我们有些方法是相同的，例如，判断一个元素是否在页面显示，还有元素点击和输入操作，还有判断页面标题和页面地址等等，甚至，有些软件web不同页面有公共的元素。这些因素，决定了我们需要写一个页面父类，来定义一些公共的方法或者公共的元素。

1.先按照如下图，创建一个BasePage的类，我们暂且叫页面基类，以后POM里面每个页面新写的类都需要继承这个BasePage类。



2.BasePage的代码内容如下。
```
package framework;
 
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
 
public class BasePage {
 
	public static WebDriver driver;
	public static String pageTitle;
	public static String pageUrl;
	
	/*
	 * 构造方法
	 */
	protected BasePage (WebDriver driver){
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
   
	       
}
```
       上面，我们实现了Selenium的元素判断是否显示，和元素清除，点击，输入等方法的二次封装。还有我们写了每个页面都存在的获取标题和url的方法。其他的方法，我先不全部放上去，以后我们，慢慢完善BasePage这个基类。接下来，我们就需要在实现POM里去测试基类的工作是否正常。
       本篇介绍就到这里，下一篇介绍POM的实现和BgasePage类的具体使用，关于POM和TestNG，有不懂的，请看前面框架设计准备篇里面的文章。