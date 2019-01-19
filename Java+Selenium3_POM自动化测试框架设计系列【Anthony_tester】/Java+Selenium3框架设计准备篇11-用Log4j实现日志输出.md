         本篇来学习一下，Selenium自动化测试中如何输出日志文件。任何软件，都会涉及到日志输出。所以，在测试人员报bug，特别是崩溃的bug，一般都要提供软件产品的日志文件。开发通过看日志文件，知道这个崩溃产生的原因，至少知道触发崩溃的条件是什么。同样在自动化测试框架设计，日志文件输出是不可或缺的。我们习惯通过日志输出来记录我们用例执行的情况。当然测试报告更直观，但是如果出现问题，往往是日志文件更有效。这里我们介绍如何通过Log4j.jar来帮助我们输出一个日志。
    
       Log4j是开源的jar包，我们可以通过http://maven.ibiblio.org/maven/log4j/jars/下载最新版本是1.2.15，这个版本是2007年发布，目前没有更新的。下载好了之后，添加到我们的eclipse项目的lib中去。然后，在当前项目新建一个Log4j.properties的文件，内容如下。

log4j.rootLogger=INFO,CONSOLE,R,HTML,TTCC

// 定义一个appender
log4j.appender.CONSOLE=org.apache.log4j.ConsoleAppender
log4j.appender.R=org.apache.log4j.RollingFileAppender
log4j.appender.TTCC=org.apache.log4j.RollingFileAppender
log4j.appender.HTML=org.apache.log4j.FileAppender

// 定义log文件保存路径，三种日志文件格式
log4j.appender.R.File=./Log/testlog.log
log4j.appender.TTCC.File=./Log/testlog1.log
log4j.appender.HTML.File=./Log/application.html

// 定义layout和pattern
log4j.appender.CONSOLE.layout=org.apache.log4j.PatternLayout
log4j.appender.CONSOLE.layout.ConversionPattern= %5p [%t] (%F:%L)- %m%n
log4j.appender.R.layout=org.apache.log4j.PatternLayout
log4j.appender.R.layout.ConversionPattern=%d - %c -%p - %m%n
log4j.appender.TTCC.layout=org.apache.log4j.TTCCLayout
log4j.appender.TTCC.layout.DateFormat=ISO8601
log4j.appender.HTML.layout=org.apache.log4j.HTMLLayout
log4j.appender.HTML.layout.Title=Application log
log4j.appender.HTML.layout.LocationInfo=true
效果如下，并且在当前根路径新建一个Log的文件夹。

![img](https://img-blog.csdn.net/20170724220605864?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


写一个测试类，测试日志输出。

package testSuite;

import java.util.concurrent.TimeUnit;

import org.apache.log4j.Logger;
import org.apache.log4j.PropertyConfigurator;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class TestBaidu {

	public static void main(String[] args) {
		
		 Logger logger=Logger.getLogger("baidu");
		 
		 PropertyConfigurator.configure(".\\Log4j.properties");
		 
		 // 打开浏览器
		 System.setProperty("webdriver.chrome.driver", ".\\Tools\\chromedriver.exe");  
	     WebDriver driver = new ChromeDriver(); 
	     logger.info("启动浏览器");
	     
	     driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
	     logger.info("隐式等待10秒");
	     
	     driver.get("https://www.baidu.com");
	     logger.info("打开百度首页");
	     
	     driver.findElement(By.id("kw")).sendKeys("Selenium");
	     logger.info("在搜索输入框输入selenium");


​	     
	}

}
运行完后，右键刷新Log文件夹，可以看到三个日志文件。

![img](https://img-blog.csdn.net/20170724220956351?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


分别打开三种文件，截图如下

![img](https://img-blog.csdn.net/20170724221220832?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20170724221236926?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20170724221256445?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

---------------------
作者：Anthony_tester 
来源：CSDN 
原文：https://blog.csdn.net/u011541946/article/details/76038697 
版权声明：本文为博主原创文章，转载请附上博文链接！