       本篇介绍利用TestNG中的Report类来实现简单的log输出，在TestNG中有一个Report的类，这个类的作用是，在添加了@Test的方法里面，通过Report.log("log message")这样的方式，给每个步骤添加日志解释，这些日志解释会显示在TestNG生成的html报告中。

废话不多说，直接来看一个例子。

package lessons;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Reporter;
import org.testng.annotations.Test;

public class TestngReportDemo {
​	
	 @Test
	 public void testReport(){
	        
		 WebDriver driver=new ChromeDriver();
	        
	     Reporter.log("launch chrome browser instance");
	        
	     driver.manage().window().maximize();
	        
	     Reporter.log("Max the browser");
	        
	     driver.get("https://www.baidu.com");
	        
	     Reporter.log("open baidu site");
	        
	     driver.quit();
	        
	     Reporter.log("退出浏览器");
	        
	    }

}
​       运行后，找到报告，打开发现中文的日志消息会显示乱码，目前我没有找到解决这个问题的办法，所以，日志，大家还是用英文写吧，英文写日志是软件开发人员的标准规范。

![img](https://img-blog.csdn.net/20170718154738789?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

