       前面文章我们运行测试用例都是在main方法下，有没有什么方法不需要用main方法去执行用例，这就需要单元测试框架的支持，这篇简单介绍TestNG单元测试框架的安装和基本使用。

1.什么是TestNG

你们也许很多人听说过TestNG ,Junit,Nunit 这些工具，也肯定想它们到底有什么区别。

1）三者都是独立于测试框架，在市场上都可以免费得到。

2）TestNG和Junit在JAVA上使用，Nunit在C#上使用

3）Junit是Eclipse自带工具，TestNG需要自己安装插件

4）相对TestNG而言，Junit的注释很少

5）Junit不能生成html格式报告，TestNG能生成丰富的html报告

其他对比就不讲了，有兴趣可以到各自官网去查看。

      TestNG是一个测试框架，它能覆盖不同层级的测试，例如，单元测试，集成测试，端对端测试等。我们可以把Selenium和TestNG结合一起，在Eclipse中用Java写TestNG脚本。

2.TestNG一些特点

1）注释，很多注释提供

2）支持数据驱动测试（@DataProvider）

3）支持变量

4）自动生成测试报告

5）通过配置testng.xml，我们可以只执行测试失败的用例

6）支持很多IDE工具（Eclipse等）

7）默认JDK支持监控运行和日志

8）独立的程序服务器测试方法（API）

3.TestNG在Eclipse上的安装过程

       由于下载站点被墙了，如果你有VPN，你可以安装下面这个正常的流程来安装。

步骤一，打开Eclipse，点击Help-Install New Software

![img](https://img-blog.csdn.net/20170713160551064?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


步骤二：打开浏览器，输入testng官网：http://testng.org/doc/download.html

找到如下图界面，拷贝红框url到Eclipse中需要输入站点那个界面上。

![img](https://img-blog.csdn.net/20170713160720847?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

输入testNG的下载站点。

![img](https://img-blog.csdn.net/20170713160745196?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20170713160806393?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20170713160835475?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20170713160852409?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

跟着向导完成安装，重启Eclipse，选择一个包鼠标右键，如果看到底部倒数第二项有TestNG这个菜单，说明安装成功。如果你没有VPN环境，你去我网盘下载文件，解压出来，把这个org.testng.eclipse_6.11.0.201703011520文件夹放到eclipse的路径\eclipse\plugins

3.新建一个TestNG类

在一个包下，鼠标右键，点击TestNG,然后选择Create TestNG class

![img](https://img-blog.csdn.net/20170713161236817?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

默认TestNG没有添加到当前项目的lib中，需要安装如图添加。

![img](https://img-blog.csdn.net/20170713161425155?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

       在BeforeClass中写启动浏览器脚本。在AfterClass中写退出浏览器代码，在@Test中写我们测试脚本。记住，所有有关测试脚本，都需要写到@Test下的方法，也就是说，你写这个函数或者方法是关于测试脚本的，那么你就要在该方法前面加上@Test这个注释，可以添加多个@Test的测试函数。

举例一个完整的基于TestNG的Selenium脚本。注意下面我们把driver定义放到了类的成员变量里，所以在该类下任何地方都能调用driver和保持driver的唯一性。

package lessons;

import org.testng.annotations.Test;
import org.testng.annotations.BeforeClass;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.annotations.AfterClass;

public class FirstTestNGClass {
​	WebDriver driver;
​	
	@Test
	public void OpenBaidu() {
		driver.get("https://www.baidu.com");
		driver.findElement(By.id("kw")).sendKeys("Selenium");
		driver.findElement(By.id("su")).click();
		
	}
	
	@BeforeClass
	public void beforeClass() {
		
	    System.setProperty("webdriver.chrome.driver", ".\\Tools\\chromedriver.exe");
		
	    driver = new ChromeDriver();
		
	    driver.manage().window().maximize();
   }

    @AfterClass
    public void afterClass() {
    	driver.quit();
   }

}
​       上面我们把启动浏览器和浏览器最大化放在了setup方法里，一般setup方法是写一些用例的前置条件，而@Test注释的方法才是我们测试脚本，afterClass方法里一般写清除测试数据之类的操作，webUI自动化测试一般写退出浏览器操作。
可以右键Run as -TestNG test，运行结果控制台输出如下：

Starting ChromeDriver 2.29.461591 (62ebf098771772160f391d75e589dc567915b233) on port 36423
Only local connections are allowed.
七月 13, 2017 4:28:26 下午 org.openqa.selenium.remote.ProtocolHandshake createSession
信息: Detected dialect: OSS
PASSED: OpenBaidu

===============================================
​    Default test
    Tests run: 1, Failures: 0, Skips: 0
===============================================


===============================================
Default suite
Total tests run: 1, Failures: 0, Skips: 0
===============================================
查看测试报告

       每次运行TestNG都会生成一个测试报告，你可以在运行结束后，右键你eclipse项目-刷新，然后就可以看到项目的根目录下生成一个test-output的文件夹，你可以打开emailable-report.html，在eclipse项目中打开这个web文件格式会很难看，你可以在你workspace找到你的eclipse项目，找到这个报告文件，用本地浏览器打开，观察体验效果会更好。这里不过多介绍这个报告，后面会介绍如何生成测试报告，并保存到特定的文件夹路径下。
    
       关于TestNG的安装和简单使用就介绍到这里，下面几篇文章都会陆续介绍TestNG的使用，基本上这些使用我们在框架设计里都会涉及。
---------------------
作者：Anthony_tester 
来源：CSDN 
原文：https://blog.csdn.net/u011541946/article/details/75070425 
版权声明：本文为博主原创文章，转载请附上博文链接！