#### 前提条件

1. 安装IDEA
2. 安装JDK

#### 创建工程

File-->new Project 选择maven-->Next，项目名称自己填写



![img](https:////upload-images.jianshu.io/upload_images/3830714-72f8d11c15375f6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/788/format/webp)

创建项目.png

#### 导入TestNG依赖包

打开pom.xml文件，添加如下内容

```
 <dependencies>
        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>6.8.21</version>
        </dependency>
    </dependencies>
```

#### 添加编译插件和执行测试插件

打开pom.xml文件，添加如下内容

```
<build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.1</version>
        <configuration>
          <source>1.8</source>
          <target>1.8</target>
          <encoding>UTF-8</encoding>
        </configuration>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>2.15</version>
        <configuration>
          <!--<testFailureIgnore>true</testFailureIgnore>-->
          <forkMode>never</forkMode>
          <argLine>-Dfile.encoding=UTF-8</argLine>
          <suiteXmlFiles>
            <suiteXmlFile>xml/testNG.xml</suiteXmlFile>
          </suiteXmlFiles>
        </configuration>
      </plugin>
    </plugins>
  </build>
```

#### 创建测试类，进行编译调试

在Java文件夹下new -->Java Class，输入如下代码，并编译

```
import org.testng.Assert;
import org.testng.annotations.Test;

public class TestDemo {
    @Test
    public void testcase1(){
        Assert.assertTrue(false);
        System.out.println("testcase1");
    }
}
```

#### 编写testNG.xml

xml文件用于按照需要批量执行用例，右键选择运行可独立执行
 若要在最后一步生成测试报告，需修改pom文件中的文件地址为xml文件所在地址

```
<?xml version="1.0" encoding="utf-8" ?>
<suite name="testproj" parallel="false">
    <test name="testDemo1">
        <classes>
            <class name="TestDemo"></class>
        </classes>
    </test>
</suite>
```



![img](https:////upload-images.jianshu.io/upload_images/3830714-b4bfbc2ed6bca383.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/574/format/webp)

pom文件修改xml文件地址.png

#### 生成测试报告

双击运行test，在/target/surefire-reports/emailable-report.html下可以查看报告



![img](https:////upload-images.jianshu.io/upload_images/3830714-19ae2b35c18e3f22.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/642/format/webp)

maven.png

