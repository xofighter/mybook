**前言：**

主要进行TestNG[测试](http://lib.csdn.net/base/softwaretest)环境的搭建 
所需环境： 
1、IDEA UItimate 
2、JDK 
3、Maven

**一、创建工程** 
File –>new –>Project–>next–>finish

![这里写图片描述](http://img.blog.csdn.net/20161220155917342?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTGFuZ1NhbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20161220155949218?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTGFuZ1NhbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

上面两项名称自己定义

最后工程目录 
![这里写图片描述](http://img.blog.csdn.net/20161220160040391?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTGFuZ1NhbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**二、导入相关依赖包和插件**

1）添加testng依赖包 
在pom.xml文件添加

```
   <dependencies>
        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>6.8</version>
        </dependency>
    </dependencies>
```

2）添加编译插件和执行测试插件

```
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.1</version>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                    <compilerArgs>
                        <arg>-Xlint:unchecked</arg>
                        <arg>-Xlint:deprecation </arg>
                        <!--<arg>endorseddirs=${endorsed.dir}</arg>-->
                    </compilerArgs>
                </configuration>
            </plugin>
             添加插件 关联testNg.xml
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <testFailureIgnore>true</testFailureIgnore>
                    <suiteXmlFiles>
                        <file>res/testNG.xml</file>
                    </suiteXmlFiles>
                    <!--<workingDirectory>target/</workingDirectory>-->
                </configuration>
            </plugin>
        </plugins>
    </build>
```

具体的maven插件请查看

> <http://www.infoq.com/cn/news/2011/04/xxb-maven-7-plugin/> 
> <http://www.infoq.com/cn/news/2011/05/xxb-maven-8-plugin>

**三、创建测试类** 
在新建测试类，代码如下

```
public class TestDemo  
  @Test
    public void testcase1(){
        Assert.assertTrue(false);
        System.out.println("testcase1");
    }
    @Test
    public void testcase2(){
        Assert.assertTrue(true);
        System.out.println("testcase1");
    }
}
```

IDEA执行编译

![这里写图片描述](http://img.blog.csdn.net/20161220161517833?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTGFuZ1NhbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

编译情况

![这里写图片描述](http://img.blog.csdn.net/20161220161548662?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTGFuZ1NhbmQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**四、编写testNG.xml**

创建一个新的文件夹 res，里面创建testNG.xml （该文件名自己可以定义，相应需要在pom.xml的文件修改对应文件名） 
编写testNG.xml

```
<?xml version="1.0" encoding="utf-8" ?>
<suite name="testproj" parallel="false">
    <test name="testDemo1">
        <!--<packages>-->
            <!--<package name="com.testproj.Demo"/>-->
        <!--</packages>-->
        <classes>
            <class name="com.testproj.Demo.TestDemo1"></class>
        </classes>
    </test>
</suite>
```

![img](https://images2015.cnblogs.com/blog/733625/201703/733625-20170306215703125-830672307.png)

![img](https://images2015.cnblogs.com/blog/733625/201703/733625-20170306215809297-1924468266.png)

![img](https://images2015.cnblogs.com/blog/733625/201703/733625-20170306215914328-857040859.png)

 

------

![img](https://images2015.cnblogs.com/blog/733625/201703/733625-20170306220010406-664834058.png)

 

差不多搞定了，我们来看下使用maven执行测试

在IDEA控制台Terminal输入

```
mvn -f pom.xml clean test  -DxmlFileName=testNG.xml
```

编译结果

![1546930573271](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1546930573271.png)

测试报告

target/surefire-reports/com.testproj.Demo.TestDemo1文件夹下打开报告 
![1546930602117](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1546930602117.png)

到此创建TestNG+Maven+IDEA环境完成。