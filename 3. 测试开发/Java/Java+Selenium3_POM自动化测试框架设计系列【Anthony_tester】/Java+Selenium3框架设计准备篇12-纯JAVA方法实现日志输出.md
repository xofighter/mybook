       前面一篇介绍如何通过开源jar包Log4j.jar实现日志文件输出，Log4j确实很强大，能生成三种日志文件，一种是保存到磁盘的日志文件，一种是控制台输出的日志，还有一种是HTML格式的日志文件。有时候，我们不一定都需要这些文件，在我们自动化测试框架里，我们只需要把日志文件保存到磁盘文件中，所以，这里介绍一种不用Log4j来实现日志文件写入和保存。
    
       通过分析前面一篇生成的log，核心其实就是不断往xx.log文件里输入一行一行的String，对不对。只是我们可以控制输出的格式和效果。每一行log的由三部分组成“当前系统日期和时间”+“日志类型”+“动作描述”。Java中有关向文件输入的方法有outputStreamWriter，在Java IO包中，它是Writer的一个子类，将输出的字符流变成字节流。

文件结构

![img](https://img-blog.csdn.net/20170725230152022?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

先看看Logger.java的内容代码

package framework;

import java.io.File;
import java.io.FileOutputStream;
import java.io.OutputStreamWriter;
import java.text.SimpleDateFormat;
import java.util.Date;

public class Logger {
​	
​	public static String OutputFileName = getDateTimeByFormat(new Date(), "yyyyMMdd_HHmmss");
​	private static OutputStreamWriter outputStreamWriter;
​	private static String logFileName;
​	public static boolean LogFlag = true;
​	 
	public Logger() {
	 
	}
	 
	private static void WriteLog(String logEntry) {
	 
		try {
				
			        // 定义日志文件保存路径和日志文件名称
				logFileName = ".\\Log" + "\\" + OutputFileName + ".log";
				if (outputStreamWriter == null) {
					File logFile = new File(logFileName);
				
				if (!logFile.exists())
						logFile.createNewFile();
				//利用OutputStreamWriter往日志文件写内容，字符编码是unicode
				outputStreamWriter = new OutputStreamWriter(new FileOutputStream(logFileName), "utf-8");
			}
			outputStreamWriter.write(logEntry, 0, logEntry.length());
			outputStreamWriter.flush();
	 
		} catch (Exception e) {
			System.out.println(LogType.LogTypeName.ERROR.toString() + ": Failed to write the file " + logFileName);
			e.printStackTrace();
	 
		}
	 
	}
	 
	//获取当前系统时间，得到格式化时间字符串
	private static String getDateTimeByFormat(Date date, String format) {
	 
		SimpleDateFormat df = new SimpleDateFormat(format);
	 
		return df.format(date);
	 
	}
	
	public static void Output(LogType.LogTypeName logTypeName, String logMessage) {
	 
		Date date = new Date();
		String logTime = getDateTimeByFormat(date, "yyyy-MM-dd HH:mm:ss.SSS");
		String logEntry = logTime + " " + logTypeName.name() + ": " + logMessage + "\r\n";
		System.out.print(logEntry);
		// 定义一个开关，为True就输出日志，如果你不想输出，改成False
		if (LogFlag)
			WriteLog(logEntry);
		}
}
还有LogType.java的代码

package framework;

public class LogType {
​	
​	public LogType(){
​		
	}
	
	public enum LogTypeName{
		
		//
		INFO,
		//
		ERROR,
		//
		WARNING,
		//
		DEBUG;
	}
}
定义枚举类型，一般主要用到INFO和ERROR两种日志类型
我们新建一个测试类，调用Logger中静态方法输出日志，检查日志文件保存路径和内容。

package testSuite;

import java.util.concurrent.TimeUnit;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

import framework.LogType;
import framework.Logger;

public class TestBaidu {
​	
​	public static void main(String[] args) {
​		
	     System.setProperty("webdriver.chrome.driver", ".\\Tools\\chromedriver.exe");  
	     WebDriver driver = new ChromeDriver(); 
	     Logger.Output(LogType.LogTypeName.INFO, "启动chrome浏览器");
	    
	     driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
	     Logger.Output(LogType.LogTypeName.INFO, "设置隐式时间10秒");
	     
	     driver.get("https://www.baidu.com");
	     Logger.Output(LogType.LogTypeName.INFO, "打开百度首页");
	     
	     driver.findElement(By.id("kw")).sendKeys("Selenium");
	     Logger.Output(LogType.LogTypeName.INFO, "搜索输入框输入关键字selenium");


​	     
​	}

}
查看日志文件
2017-07-25 22:50:40.491 INFO: 启动chrome浏览器
2017-07-25 22:50:40.512 INFO: 设置隐式时间10秒
2017-07-25 22:50:41.899 INFO: 打开百度首页
2017-07-25 22:50:42.295 INFO: 搜索输入框输入关键字selenium
​       这里需要注意，我的eclipse设置编码是utf-8,如果你设置是GBK，可能会出现日志文件乱码的情况，你需要在菜单导航栏上Window-->Preferences 打开"首选项"对话框，左侧导航树，导航到 General-->Workspace，把编码从GBK改成utf-8.
​       在我们接下来的自动化测试框架设计中，我们就采用这种日志输出方法，如果你想丰富的日志输出，你就用Log4j,Lo4j有一个小问题就是，不太方便设置日志文件名称是当前系统的时间，所以，log4j前面写死了日志名称，每次执行都会覆盖之前的日志。但是本篇的方法就不会发生这样情况，每次自动化运行都有日志保存，方便追溯错误。
