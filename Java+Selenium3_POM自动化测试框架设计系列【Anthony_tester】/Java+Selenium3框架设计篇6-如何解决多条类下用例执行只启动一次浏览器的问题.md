       前面我们实现了在setUp方法中添加登录的用例。今天，还是为了解决我们遗留的问题，如果执行多条测试用例，这些用例在不同类文件下，我们只需要启动一次浏览器，登录一次，然后根据顺序去执行多个类文件下的用例，最后退出并关闭浏览器。

解决思路：

1.在一个类文件中执行多个用例，我们现在没有问题

2.假如我有两个类文件，每个类文件都包含一个测试用例。如何保证用例A执行完后的driver如何传递给用例B

这个如何传递是我们的重点，我之前犯了一个错误，在用例A和用例B都写了一个类变量，那就是WebDriver driver。当时执行完了用例A之后，我也思考过如何把driver传递给用例B中去。正确的做法是，用例A执行完后，@AfterClass里面不能执行driver.quit()语句，这里我们替换成时间等待语句。在用例B类文件中，千万不要再定义WebDriver driver这样的变量，在B中，我们先在@BeforeClass中什么也不写，也就是没有前提条件，这个时候不用去管driver如何传递过来。接下来在我们的@Test方法中，直接用A.driver来传递我们的driver实例对象，所以，我们在A中需要把driver定义为static变量。说了这么多，我们来看具体文件中代码如何写，你就明白。

![img](https://img-blog.csdn.net/20170818224208323?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

这篇文章，只需要上面三个文件，其他文件不变，具体参考前面文章。

1.A类，这里我们的测试京东搜索。


```java
package testSuite;

import java.io.IOException;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.support.PageFactory;
import org.testng.annotations.AfterClass;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;

import pageObject.HomePage;
import pageObject.functions.Login;

public class Test_HomePage_Search {
	
	public static WebDriver driver;
	
	@BeforeClass
	public void setUp() throws IOException{
		
		Login login = new Login();
		login.initSetup();
		login.loginValid();
		driver = login.driver;
		
	}


	
	@Test
	public void test_searchGoods(){
		
		HomePage hp = PageFactory.initElements(driver, HomePage.class);
		hp.searchWithKeyword("Java");


		
	}
	
	@AfterClass
	public void tearDown() throws InterruptedException{
		
		Thread.sleep(3000);
	}
}
```
2.B类，点击搜索结果的第一个商品，点击进入详情页，点击添加购物车按钮

 


```java
package testSuite;

import org.openqa.selenium.support.PageFactory;
import org.testng.annotations.AfterClass;
import org.testng.annotations.BeforeTest;
import org.testng.annotations.Test;

import pageObject.GoodsDetailsPage;
import pageObject.SearchResultListPage;

public class Test_AddGoodToCart {
​	
​	@BeforeTest
​	public void setUp() {     
​	 
​	    }


​	
​	@Test
​	public void testAddToCart() throws InterruptedException{
​		
​		SearchResultListPage srlp = PageFactory.initElements(Test_HomePage_Search.driver, SearchResultListPage.class);
​		srlp.clickItemImg();
​		
		// 切换窗口到商品详情页
		srlp.switchWindow();
		
		GoodsDetailsPage gdp = PageFactory.initElements(Test_HomePage_Search.driver, GoodsDetailsPage.class);
		gdp.addGoodToCart();
	}
	
	@AfterClass
	public void tearDown(){
		
		Test_HomePage_Search.driver.quit();
	}

}
```

      上面举例的是两个测试类文件，如果是N个也是这个原理，只有测试类A中定义WebDriver driver,然后其他测试类直接调用静态driver这个变量，其他测试类只要遇到了driver，就换成A.driver，执行到最后一个用例，需要A.driver.quit()。通过这个方式，我们解决了，如何只启动一次浏览器就，执行多次测试用例的目标。

 








3.TestNG.xml设置用例执行顺序

项目最终用例执行是通过设置TestNG.xml来管理和执行测试用例。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd">
<suite name="Suite">
  <test name="TestJDActions">
    <classes>

      <class name="testSuite.Test_HomePage_Search"/>
      <class name="testSuite.Test_AddGoodToCart"/>
      
    </classes>
  </test> 
</suite> 
```

2017/09/04更新

一些感受：

       最近好久没有更新这个Java+Selenium3系列文章。主要有两个原因，第一个是到目前，其实简单的框架设计已经完成，剩下的功能都是一些定制的功能，例如如何设置监听，如何测试完后，找到报告文件夹里面最新报告通过邮件发送。如何做数据驱动，如果设计关键字驱动框架，如何设计行为驱动框架，如何selenium做持续集成操作。这些话题，有些我已经实现，有些知道一些而已，如果要展开，还是有很大篇幅。
    
       第二个是有一个新的观点想分享给大家。如果你学到这里，你要不继续学下去，学点数据库相关的操作，实现一些数据初始化脚本。其实，这里，也是有点打击的。我在介绍接口测试的文章提到，现在Web UI自动化已经不受重视，已经开始向接口自动化测试和白盒测试迁移。因为Web UI是变化非常平凡的，特别是敏捷开发的项目。记住，测试三层金字塔模型，UI层的测试花再多时间，对代码的覆盖可能都不到30%，我们需要向接口测试，代码测试转移，测试更多的代码覆盖率，从而发现更多软件的缺陷。Web UI自动化真的没有什么技术含量，我们不需要成为什么selenium专家，测试领域还有很多方向和技术需要我们去掌握，例如app自动化测试，app稳定性测试。安全测试，性能测试，任何一个方向，都不比webui自动化测试要简单。
    
      所以，目前，考虑先把Web UI自动化先放一放，花时间去学习接口测试和性能测试，以及代码的技能，甚至web开发或者android开发，学web开发是更好去做接口测试和网站功能测试，学android开发是更好去学习android自动化测试。这里先把目前，java+selenium框架设计部分代码放在网盘。后面，如果有必要，会继续更新这个系列文章。

2019/01/01更新

这个系列是教你学会Selenium用java的方式写自动化测试代码，如果你要做持续集成测试，例如在jenkins上做，那么你可以继续看我的另外一个系列文章，介绍pipeline和selenium持续集成，请点击这：https://blog.csdn.net/u011541946/article/details/85221474这是第一篇关于持续集成的介绍，最好从这篇看到该系列的第九篇，这样你的持续集成才有一个入门和比较明确的掌握。
