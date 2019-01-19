        这篇介绍如何利用TestNG实现DDT（数据驱动测试 Data Driver Test），什么是数据驱动测试呢？想想一下这个场景，假如我有50个用户，需要测试登录。你会怎么测试呢。第一个肯定是写50个测试脚本去验证登录。第二个就是采用DDT，把用户名和密码写到一个表格里，然后依次从表格读取去运行这个登录用例。

我们来试试2个用户登录，数据存储在二维数组。
```java
package lessons;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;

public class TestDDT {
​	
	@Test(dataProvider="testdata")
	public void TestLogin(String username, String password) throws InterruptedException{
	 
		System.setProperty("webdriver.chrome.driver", ".\\Tools\\chromedriver.exe");
		WebDriver driver = new ChromeDriver();
		driver.manage().window().maximize();
		driver.get("https://www.baidu.com");
		
		// click login link
		driver.findElement(By.xpath("//*[@id='u1']/a[7]")).click();
		Thread.sleep(2000);
		
		driver.findElement(By.xpath("//*[@id='TANGRAM__PSP_8__userName']")).clear();
		driver.findElement(By.xpath("//*[@id='TANGRAM__PSP_8__userName']")).sendKeys(username);
		driver.findElement(By.xpath("//*[@id='TANGRAM__PSP_8__password']")).clear();
		driver.findElement(By.xpath("//*[@id='TANGRAM__PSP_8__password']")).sendKeys(password);
		
	}
	
	@DataProvider(name="testdata")
	public Object[][] TestDataFeed(){
		//创建一个二维数组
		Object [][] baidudata=new Object[2][2];
		
		// 设置数组的元素值，这里是设置第一个用户名
		baidudata[0][0]="Selenium1@baidu.com";
		
		// 设置第一个用户密码
		baidudata[0][1]="Password1";
		
		// 设置第二个用户名称
		baidudata[1][0]="Selenium2@baidu.com";
		
		// 设置第二个用户密码
		baidudata[1][1]="Password2";
		
		// 返回数据传给脚本
		return baidudata;
	
	}

}
```
下一篇介绍利用外部excel文件实现数据驱动测试。
--------------------- 
作者：Anthony_tester 
来源：CSDN 
原文：https://blog.csdn.net/u011541946/article/details/75126377 
版权声明：本文为博主原创文章，转载请附上博文链接！