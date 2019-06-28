技术小白又来写流水账了~转载请注明出处~

# 一、搭建测试环境：

## 环境搭建

**工具**：jmeter2.13、mysql-connector-java-5.1.18-bin.jar、Jenkins、ant

**流程**：

**（1****）解压jemeter2.13.zip,**

配置jmeter环境变量：

新建JMETER_HOME，变量值中输入：F:\apache-jmeter-2.13（jmeter所在文件夹）

修改CLASSPATH变量，变量值中添加如下值：

%JMETER_HOME%\lib\ext\ApacheJMeter_core.jar;%JMETER_HOME%\lib\jorphan.jar;%JMETER_HOME%\lib\logkit-2.0.jar 

​                     打开jmeter bin目录下的jmeter.bat显示如下则配置成功。

 ![img](https://images2015.cnblogs.com/blog/1065733/201703/1065733-20170324103033627-565205137.png)

 

**（2）配置ant**

**(3)** **测试jmeter是否可用**

可以先找到一个jmx文件测试下jmeter是否可用，在文件中打开jmx文件：

![img](https://images2015.cnblogs.com/blog/1065733/201703/1065733-20170324103045065-1020631430.png)

 

 

找到线程组，拉至最下点击查看结果树：

 ![img](https://images2015.cnblogs.com/blog/1065733/201703/1065733-20170324103106190-414322940.png)

 

在此处找到一个jtl文件（事先在某目录下新一个jtl），之后后点击 开始测试。

生成的结果就会在此jtl中。

 ![img](https://images2015.cnblogs.com/blog/1065733/201703/1065733-20170324103116315-983757210.png)

 

 

 

## 3、修改配置

**（1****）jmeter.properties**

如果我们想生成的文件中包含某些信息，则需要修改配置文件。

在命令行jmeter的bin目录打开jmeter.properties。想显示的信息去掉#并改成true。（命令行生成的jtl文件想包含某些信息，必须在jmeter.properties文件中修改，页面上的修改对命令行没有作用）

![img](https://images2015.cnblogs.com/blog/1065733/201703/1065733-20170324103159658-1363503613.png)

 

 ![img](https://images2015.cnblogs.com/blog/1065733/201703/1065733-20170324103145174-2110876986.png)

 

（2）因为将jtl转换成html是引用了jmeter自带的样式，可根据测试的需求更改样式。

 

 ![img](https://images2015.cnblogs.com/blog/1065733/201703/1065733-20170324103215236-1913296128.png)

 

 

 

 

 

 

# 三、测试结果生成html

 

在某目录中新建build.xml文件（jmeter的extras目录下有build.xml,可对此文件进行修改。

）

本例中将build文件放置在：D:\jenkins\workspace\API-Test

 

<?xml version="1.0" encoding="UTF-8"?>

<project name="ant-jmeter-test" default="run" basedir=".">

 <!-- 需要改成自己本地的jmeter目录--> 

<property name="jmeter.home" value="F:\apache-jmeter-2.13" />

 <!-- jmeter生成的jtl格式的结果报告的路径-->   

<property name="jmeter.result.jtl.dir" value="D:\jenkins\workspace\report\jtl" />

<!-- jmeter生成的html格式的结果报告的路径-->

<property name="jmeter.result.html.dir" value="D:\jenkins\workspace\report\html" />

 <!-- ʺ生成的报告的前缀-->  

<property name="ReportName" value="TestReport" />

<property name="jmeter.result.jtlName" value="${jmeter.result.jtl.dir}/${ReportName}.jtl" />

<property name="jmeter.result.htmlName" value="${jmeter.result.html.dir}/${ReportName}.html" />

 

<target name="run">

​    <antcall target="test" />

​    <antcall target="report" />

</target>

   

<target name="test">

   <taskdef name="jmeter" classname="org.programmerplanet.ant.taskdefs.jmeter.JMeterTask" />

   <jmeter jmeterhome="${jmeter.home}" resultlog="${jmeter.result.jtlName}">

   <!-- 声明要运行的脚本"*.jmx"指包含此目录下的所有jmeter脚本-->

<testplans dir="D:\jenkins\workspace\" includes="*.jmx" />

​           

   <property name="jmeter.save.saveservice.output_format" value="xml"/>

   </jmeter>

​    </target>

​       

​    <path id="xslt.classpath">

​        <fileset dir="${jmeter.home}/lib" includes="xalan*.jar"/>

​        <fileset dir="${jmeter.home}/lib" includes="serializer*.jar"/>

​    </path>

<target name="report">

​    <tstamp> <format property="report.datestamp" pattern="yyyy/MM/dd HH:mm" /></tstamp>

​    <xslt

​              classpathref="xslt.classpath"

​              force="true"

​              in="${jmeter.result.jtlName}"

​              out="${jmeter.result.htmlName}"

​              style="${jmeter.home}/extras/jmeter-results-detail-report_21.xsl">

​              <param name="dateReport" expression="${report.datestamp}"/>

​      </xslt>

```
 <!-- 因为上面生成报告的时候，不会将相关的图片也一起拷贝至目标目录，所以，需要手动拷贝 --> 
```

  <copy todir="${jmeter.result.html.dir}">

​            <fileset dir="${jmeter.home}/extras">

​                <include name="collapse.png" />

​                <include name="expand.png" />

​            </fileset>

​        </copy>

​    </target>

 

 

 

# 四、Jenkins集成jmeter

 

设置某服务器为slave节点，在服务器上跑测试用例。

 ![img](https://images2015.cnblogs.com/blog/1065733/201703/1065733-20170324103327783-2066755762.png)

 

获取测试脚本

 

 

在服务器上执行batch语句;

：删除上次生成的jtl文件

del /q/a/f  D:\jenkins\workspace\report\jtl

：删除上次生成的html文件

del /q/a/f  D:\jenkins\workspace\report\html

：执行build.xml文件

cd D:\jenkins\workspace

ant

 

找到HTML文件显示在Jenkins上

 ![img](https://images2015.cnblogs.com/blog/1065733/201703/1065733-20170324103449315-833194653.png)

 

 

# 五、附加

tips:

（1）在测试机器上运行jmx 脚本，查看是否正常,如果在运行时报如下错误：

No suitable driver found for

jdbc:mysql://rdsw46k48c465jzyyih9.mysql.rds.aliyuncs.com:3306/APIManager?characterEncoding=utf8&autoReconnect=true

使用如下方法

下载MySQL的官方JDBC连接器mysql-connector-java-5.1.18-bin.jar

1、将mysql-connector-java-5.1.18-bin.jar放入下面目录中

E:\apache-jmeter-2.11\lib

C:\Program Files (x86)\Java\jdk1.6.0_21\jre\lib\ext\mysql-connector-java-5.0.8-bin.jar 
C:\Program Files (x86)\Java\jdk1.6.0_21\jre\lib\mysql-connector-java-5.0.8-bin.jar 

2、将路径加入到环境变量CLASSPATH中

E:\apache-jmeter-2.11\lib\mysql-connector-java-5.0.8-bin.jar ;C:\Program Files (x86)\Java\jdk1.6.0_21\jre\lib\mysql-connector-java-5.0.8-bin.jar ;

 此问题参考：<http://blog.csdn.net/tan37lu/article/details/24649563>