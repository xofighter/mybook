      本篇介绍TestNG中的参数的作用，通过引入参数，来实现启动不同浏览器，一般参数需要在tesgng.xml文件里设置。这里要举例的场景是，我需要同时运行火狐和谷歌浏览器测试脚本。

1. 先创建一个TestCrossBrowser.java文件，输入以下内容

package lessons;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.testng.annotations.Parameters;
import org.testng.annotations.Test;

public class TestCrossBrowser {
​	@Test
​	// 这行@Test一定要写，如果不写，表示当前没有需要执行的脚本，你可以测试不写，然后run testng.xml试试看，会发生什么
​	@Parameters("Browser")
​	public  void startBrowser(String browser){
​		
​		if(browser.equalsIgnoreCase("Firefox")){
​			
			System.setProperty("webdriver.gecko.driver", ".\\Tools\\geckodriver.exe");
			WebDriver driver = new FirefoxDriver();
			driver.manage().window().maximize();
			driver.get("https://www.baidu.com");
			driver.quit();
		}
		else if(browser.equalsIgnoreCase("Chrome")){
			
			System.setProperty("webdriver.chrome.driver", ".\\Tools\\chromedriver.exe");
			WebDriver driver = new ChromeDriver();
			driver.manage().window().maximize();
			driver.get("https://www.baidu.com");
			driver.quit();
		}
		
	} 

}
注意上面@Parmeters("Browser"),直接把外部参数Browser通过@Parmaeters注释引入到测试脚本中供方法共享这个变量。
2.如何创建testng.xml

       右键上面我们创建的TestCrossBrowser.java，选择TestNG-Convert to TestNG,根据提示，走完这个向导。完成后，会在当前项目的根目录生成一个testng.xml的文件。

![img](https://img-blog.csdn.net/20170714150621719?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

我们把生成的testng.xml文件进行调整和修改，内容如下。
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd">
<!-- Suite相关设置，这里paraller是执行的模式，有tests classes methods三种取值，thread-count表示两个线程执行用例 -->
<suite name="Suite" parallel="tests" thread-count="2">
  <test name="Test">
      <parameter name="Browser" value="Firefox" />
      <classes>
      		<class name="lessons.TestCrossBrowser"/>
      </classes>
  </test> 

  <test name="Test1">
​      <parameter name="Browser" value="chrome" />
​      <classes>
​      		<class name="lessons.TestCrossBrowser"/>
​      </classes>
  </test> 

</suite> 
```
这里需要注意每个test标签下的name不能重名，下面解释下parallel这个属性，上面提到有三种值。
1）tests

TestNG 会在相同的线程中运行相同<test>标记下的所有方法，但是每个<test>下的方法会运行在不同的线程下。

应用：可以将非线程安全的类放在同一个<test>标签下，并且使其可以利用TestNG多线程的特性的同时，让这些类运行在相同的线程中。（不太理解）

2）methods

TestNG 会在不同的线程中运行测试方法，除非那些互相依赖的方法。那些相互依赖的方法会运行在同一个线程中，并且遵照其执行顺序。

3）classes

TestNG 会在相同线程中相同类中的运行所有的方法，但是每个类都会用不同的线程运行

属性 thread-count 允许你为当前的执行指定可以运行的线程数量。

3.运行testng.xml

       右键testng.xml文件，选择Run AS->TestNG Suite，观察运行结果，在控制台输出地方可以看到运行了2个test。这里我们第一次用到testng.xml文件，以后在框架设计会介绍通过这个testng.xml来管理和运行我们成百上千的测试用例。
---------------------
作者：Anthony_tester 
来源：CSDN 
原文：https://blog.csdn.net/u011541946/article/details/75109005 
版权声明：本文为博主原创文章，转载请附上博文链接！