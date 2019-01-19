使用PageFactory初始化pageobject有什么作用呢，下面举个例子来说明

```
public BaiduPage baiduPage = PageFactory.initElements(driver,
            BaiduPage.class);
```

场景：使用selenium 实现自动打开[www.baidu.com](https://link.jianshu.com?t=http://www.baidu.com)首页，然后在搜索框内输入“路易”，并点击查找
 环境：win7，X86，IE浏览器，eclipse（安装testng插件）

步骤一：
 在eclipse中创建一个maven项目，并在pom.xml中引入selenium和testng的依赖

```
pom.xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.selelium.css</groupId>
    <artifactId>mycss</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>war</packaging>
    <name>mycss</name>

    <dependencies>
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-java</artifactId>
            <version>2.48.2</version>
        </dependency>
        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>6.9.9</version>
        </dependency>
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-ie-driver</artifactId>
            <version>2.48.2</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.3.2</version>
                <configuration>
                    <source>1.6</source>
                    <target>1.6</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

步骤二：
 创建初始准备类：

```
package mycss;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.ie.InternetExplorerDriver;
import org.openqa.selenium.remote.DesiredCapabilities;

public class InitPre {

    protected  WebDriver driver;
    private final String url = "www.baidu.com";
    
    public  InitPre(){
        String IEPath = "C:\\Program Files\\Internet Explorer\\iexplore.exe";
        //String IEPath_64 = "C:\\Program Files (x86)\\Internet Explorer\\iexplore.exe";
        String pro_Path = System.getProperty("user.dir");
        String IEDriver_Path = pro_Path + "\\IEDriverServer.exe";
        System.setProperty("webdriver.ie.bin", IEPath);
        System.setProperty("webdriver.ie.driver", IEDriver_Path);

        DesiredCapabilities ieCapabilities = DesiredCapabilities
                .internetExplorer();
        ieCapabilities
                .setCapability(
                        InternetExplorerDriver.INTRODUCE_FLAKINESS_BY_IGNORING_SECURITY_DOMAINS,
                        true);
        driver = new InternetExplorerDriver(ieCapabilities);
        driver.get(url);

    }
}
```

步骤3：
 将百度首页Pageobject：

```
package mycss;

import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;

public class BaiduPage {

    @FindBy(id = "kw")
    private WebElement indexInput;

    public void clearKeys_indexInput() {
        indexInput.clear();
    }

    public void sendkeys_indexInput(String index) {
        indexInput.sendKeys(index);
    }

    public void click_indexInput() {
        indexInput.click();
    }

}
```

步骤4：
 通过PageFactory初始化PageObject对象：

```
package mycss;

import org.openqa.selenium.support.PageFactory;

public class InitPage extends InitPre {
    

    public BaiduPage baiduPage = PageFactory.initElements(driver,
            BaiduPage.class);

}
```

步骤5：
 编写测试用例：

```
package mycss;

import org.testng.annotations.BeforeTest;
import org.testng.annotations.Test;

public class IndexTest 
{

    IndexTest(){
        System.out.println("IndexTest");
    }

    public InitPage initPage=new InitPage();
    BaiduPage baiduPage=initPage.baiduPage;
    
    @BeforeTest
    public void inittest(){
        
    }
            
    @Test
    public void index_test(){
        
        baiduPage.clearKeys_indexInput();
        baiduPage.sendkeys_indexInput("路易");
        baiduPage.click_indexInput();
    }
    
    
}
```

完成上面的步骤后，执行IndexTest类，然后可以看到打开浏览器，进入百度首页，然后搜索“路易”
 查看BaiduPage类可以知道，该类中仅对BaiduPage的控件和方法进行了封装，private WebElement indexInput  indexInput并没有实例化，那么执行为什么会不报空指针呢？原因是因为下面的操作，PageFactory.initElements把driver传递给了BaiduPage的对象，当执行到WebElement indexInput  indexInput这一步的话，传递的driver会在当前页面中查找该对象。大家可以尝试去掉代码中下面的一步，在执行用例查看运行结果

```
public BaiduPage baiduPage = PageFactory.initElements(driver,
            BaiduPage.class);
```

总结：
 Selenium2 引入了PageObject的思想，将每个页面的控件和操作封装成一个个Page类，然后在实现业务逻辑时，只需要调用对应的Page类即可。
 PageFactory则是为了支持PageObject而产生的，可以通过InitElements方法给Page类传递driver，当执行到WebElement时，driver在当前的页面中查找元素，这样不需要对WebElement进行实例化。如下面代码：

```
@FindBy(id = "kw")
    private WebElement indexInput;
```

作者：流浪骑士

链接：https://www.jianshu.com/p/9704035625dc

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。