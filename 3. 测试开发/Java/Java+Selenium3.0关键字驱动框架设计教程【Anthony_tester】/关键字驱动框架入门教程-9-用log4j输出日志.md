[TOC]

# log4j输出日志
在执行测试用例过程中，用户想要一些信息打印输出到控制台。信息可以是很多格式，完全取决于目的。当前我们在用Selenium进行自动化测试，本框架也需要输出一些信息来告诉使用的人，当前在执行什么用例，执行用例的哪一个步骤，如果失败有没有相关报错。如果使用log4j，我们在执行selenium自动化测试是可以实现日志输出的功能。一般来说，我们发现问题，上报Bug一般要提供一下要素：
1. 一个完整的复现问题的场景步骤
2. 描述问题或者失败的原因（如果可能）
3. 记录时间戳，方便开发人员去快速调查问题。
Log4j 帮助我们实现上面的目标，当我们在使用seleniumwebdriver的时候，如果日志能够被灵活使用，它将是一个很强大的debug工具。
在方法中使用日志输出:
首先有一个问题，日志在什么地方输出，是我们执行一个步骤，就打印这个步骤的日志输出吗？显然这样可以实现，但是我们需要花大量精力去思考每次输出的内容。一般来说，我们都在方法里面实现日志输出，只要调用这个方法，就有响应的日志输出。

## 如何实现
### 添加 log4j jar包的依赖
1. 在pom.xml 文件中添加依赖：
```maven
<!-- https://mvnrepository.com/artifact/log4j/log4j -->
<dependency>
 <groupId>log4j</groupId>
 <artifactId>log4j</artifactId>
 <version>1.2.17</version>
</dependency>
```
2. 更新项目的maven - 选择项目中的pom.xml文件右击，选择maven->reimport；
3. 查看项目中的External libraries,如果有log4j文件，说明下载成功了。
当然，也可以t通过下载加载log4j jar文件，操作如下：
1. 下载log4j jar文件，下载地址（http://archive.apache.org/dist/logging/log4j/）;
2. 添加到当前项目的library（log4j-1.2.9.jar）;

### Log4j.xml文件
在当前java项目根目录下新建一个log4j.xml文件;
Log4j.xml内容如下:
```xml
<?xml version="1.0" encoding="UTF-8"?>
 
<!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
 
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/" debug="false">
 
    <appender name="fileAppender" class="org.apache.log4j.FileAppender">
 
        <param name="Threshold" value="INFO" />
 
        <param name="Append" value="false" />
 
        <param name="File" value="logfile.log"/>
 
        <layout class="org.apache.log4j.PatternLayout">
 
            <param name="ConversionPattern" value="%d %-5p [%c{1}] %m %n" />
 
        </layout>
 
    </appender>
 
    <root>
 
        <level value="INFO"/>
 
        <appender-ref ref="fileAppender"/>
 
    </root>
 
</log4j:configuration>
```
**PS：**
`<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/" debug="false">`,这里可能会报错：URI is not registered；解决方法[在这里](https://www.cnblogs.com/ttflove/p/6341469.html) ；
`<param name="Append" value="false" />`，Append是设置Log保存到本地文件，false表示每次开始运行时都清空之前的日志，然后写入本次运行测试产生的日志。如果改成true，就会在原来旧日志后边接着输出新日志。

### 添加 Log 工具类
新建一个静态Log输出方法，方便其他地方调用，在utility包下新建一个Log.java。
Log.java内容如下:
```java
package utility;
 
import org.apache.log4j.Logger;
 
/**
 * create by Anthony on 2018/2/7
 */
public class Log {
 
    // 初始化log4j log
    private static Logger Log = Logger.getLogger(Log.class.getName());
 
    // 运行测试用例之前的日志输出
    public static void startTestCase(String sTestCaseName){
        Log.info("*******************************************************");
        Log.info("$$$$$$$$$          "+sTestCaseName+ "         $$$$$$$$$$");
        Log.info("*******************************************************");
    }
 
    // 用例执行结束后日志输出
    public static void endTestCase(String sTestCaseName){
        Log.info("XXXXXXX            "+"-E---N---D-"+"          XXXXXXXXX");
        Log.info("X");
        Log.info("X");
    }
 
    // 以下是不同日志级别的方法，方便需要的时候调用，一般info和error用得最多
    public static void info(String message) {
        Log.info(message);
    }
 
    public static void warn(String message) {
        Log.warn(message);
    }
 
    public static void error(String message) {
        Log.error(message);
    }
 
    public static void fatal(String message) {
        Log.fatal(message);
    }
 
    public static void debug(String message) {
        Log.debug(message);
    }
 
}
```

## log类在代码中的使用
接下来我们在ActionsKeywords.java和DriverScript.java 插入日志输出，然后运行main方法，看看日志文件输出效果。

### 在 ActionsKeywords 中添加log
在 ActionsKeywords 的方法调用日志输出方法，修改后 ActionsKeywords.java 内容如下:
```java
package config;
 
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import utility.Log;
 
import static executionEngine.DriverScript.OR;
 
 
import java.util.concurrent.TimeUnit;
 
public class ActionsKeywords {
 
    public static WebDriver driver;
 
 
    public static void openBrowser(String object) {
 
        // 这里，我们暂时都写死用chrome来进行自动化测试
        System.setProperty("webdriver.chrome.driver",".\\libs\\chromedriver.exe");
        driver = new ChromeDriver();
        Log.info("启动chrome浏览器。");
    }
 
    public static void openUrl(String object) {
        Log.info("浏览器窗口最大化。");
        driver.manage().window().maximize();
        driver.manage().timeouts().implicitlyWait(5, TimeUnit.SECONDS);
        Log.info("打开测试服务器地址。");
        driver.get(Constants.URL);
 
    }
 
    public static void click(String object) {
        driver.findElement(By.xpath(OR.getProperty(object))).click();
        Log.info("点击元素："+object);
    }
 
    public static void input(String object) {
        driver.findElement(By.id(OR.getProperty(object))).sendKeys(Constants.UserName);
    }
 
 
    public static void waitFor() throws Exception{
        Thread.sleep(3000);
    }
 
    // 关闭浏览器并退出
    public static void closeBrowser() {
        driver.quit();
    }
}
```

### 在 DriverScript 中添加log
在main方法调用日志输出方法，修改后 DriverScript.java 内容如下:
```java
package executionEngine;
import com.sun.xml.internal.bind.v2.runtime.reflect.opt.Const;
import config.ActionsKeywords;
import config.Constants;
import org.apache.log4j.xml.DOMConfigurator;
import utility.ExcelUtils;
import utility.Log;
 
import java.io.FileInputStream;
import java.lang.reflect.Method;
import java.util.Properties;
 
 
/**
 * create by Anthony on 2018/1/30
 */
public class DriverScript {
 
    // 声明一个public static的类对象，所以我们可以在main方法范围之外去使用。
    public static ActionsKeywords actionsKeywords;
    public static String sActionKeyword;
    // 下面是返回类型是方法，这里用到反射类
    public static Method method[];
    // 新建一个Properties对象
    public static Properties OR;
    public static String sPageObject;
 
    public static int iTestStep;
    public static int iTestLastStep;
    public static String sTestCaseID;
    public static String sRunMode;
 
    // 这里我们初始化'ActionsKeywords'类的一个对象
    public DriverScript() throws NoSuchMethodException, SecurityException{
        actionsKeywords = new ActionsKeywords();
        method = actionsKeywords.getClass().getMethods();
    }
 
    public static void main(String[] args) throws Exception {
 
        // 这样一定要加，否则报log4j初始化的警告
        DOMConfigurator.configure("log4j.xml");
 
        Log.startTestCase("Login_01");
        Log.info("加载和读取Excel数据文件");
        ExcelUtils.setExcelFile(Constants.Path_TestData);
 
        // 创建一个文件输入流对象，参数来源外部OR.txt文件
        FileInputStream fs = new FileInputStream(Constants.Path_OR);
        // 创建一个Properties对象
        OR = new Properties(System.getProperties());
        // 加载全部对象仓库文件
        OR.load(fs);
        // 执行用例
        DriverScript startEngine = new DriverScript();
        Log.info("开始执行测试用例。");
        startEngine.execute_TestCase();
        Log.info("测试用例执行结束。");
    }
 
    private void execute_TestCase() throws Exception {
        //获取测试用例数量
        int iTotalTestCases = ExcelUtils.getRowCount(Constants.Sheet_TestCases);
        //外层for循环，有多少个测试用例就执行多少次循环
        for(int iTestcase=1;iTestcase<=iTotalTestCases;iTestcase++){
            //从Test Case表获取测试ID
            sTestCaseID = ExcelUtils.getCellData(iTestcase, Constants.Col_TestCaseID, Constants.Sheet_TestCases);
            //获取当前测试用例的Run Mode的值
            sRunMode = ExcelUtils.getCellData(iTestcase, Constants.Col_RunMode,Constants.Sheet_TestCases);
            // Run Mode的值控制用例是否被执行
            if (sRunMode.equals("Yes")){
                // 只有当Run Mode的单元格数据是Yes，下面代码才会被执行
                iTestStep = ExcelUtils.getRowContains(sTestCaseID, Constants.Col_TestCaseID, Constants.Sheet_TestSteps);
                iTestLastStep = ExcelUtils.getTestStepsCount(Constants.Sheet_TestSteps, sTestCaseID, iTestStep);
 
                //下面这个for循环的次数就等于测试用例的步骤数
                for (;iTestStep <= iTestLastStep;iTestStep++){
                    sActionKeyword = ExcelUtils.getCellData(iTestStep, Constants.Col_ActionKeyword,Constants.Sheet_TestSteps);
                    sPageObject = ExcelUtils.getCellData(iTestStep, Constants.Col_PageObject, Constants.Sheet_TestSteps);
                    execute_Actions();
                }
            }
        }
    }
 
    private static void execute_Actions() throws Exception {
 
        for(int i=0;i<method.length;i++){
            if(method[i].getName().equals(sActionKeyword)){
                method[i].invoke(actionsKeywords,sPageObject);
                break;
            }
        }
    }
 
}
```
## 运行结果
运行main方法，会在当前项目根目录生成一个logfile.log文件，内容如下：
```
2018-02-08 10:44:29,179 INFO  [Log] ******************************************************* 
2018-02-08 10:44:29,180 INFO  [Log] $$$$$$$$$          Login_01         $$$$$$$$$$ 
2018-02-08 10:44:29,180 INFO  [Log] ******************************************************* 
2018-02-08 10:44:29,180 INFO  [Log] 加载和读取Excel数据文件 
2018-02-08 10:44:30,014 INFO  [Log] 开始执行测试用例。 
2018-02-08 10:44:33,830 INFO  [Log] 启动chrome浏览器。 
2018-02-08 10:44:33,831 INFO  [Log] 浏览器窗口最大化。 
2018-02-08 10:44:33,956 INFO  [Log] 打开测试服务器地址。 
2018-02-08 10:44:36,339 INFO  [Log] 点击元素：loginLink 
2018-02-08 10:44:37,420 INFO  [Log] 点击元素：loginTypeByAccount 
```
