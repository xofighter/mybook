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



-----------

# [Jenkins+Jmeter持续集成笔记（五：问题优化）](https://www.cnblogs.com/LiangHu/p/8268517.html)



通过前面的一系列文章，我的API自动化测试平台已经搭建成型，但是要投入具体项目使用时，还有以下几个问题需要优化。

 

还是接着以上一篇笔记中的“test_token”项目为例：

# 1、邮件通知问题

#### （1）问题

这里的主要问题是针对邮件中的附件（即测试报告），在前一篇配置[邮件通知](http://www.cnblogs.com/LiangHu/p/8259476.html)的文章，我对于附件的配置是这么说的：

![img](https://images2017.cnblogs.com/blog/670882/201801/670882-20180111102653488-197535407.png)

如果你能够多触发几次构建就会发现问题，随着构建次数越来越多，产生的测试报告也越来越多，即.html文件会越来越多，而每次构建的邮件通知都会把所有的.html文件添加到附件，这样用户收到的自动化测试邮件里面，不仅能看到本次构建的测试报告，还可以看到之前所有构建的测试报告，体验非常不好。

#### （2）解决思路

在每次构建前删除掉上次构建产生的测试报告文件，使得添加附件时，只能在html文件夹中搜索到本次构建的测试报告。

#### （3）解决方案

**方案一：**

job->配置->构建->增加构建步骤

在Invoke Ant构建前，我们添加一个构建步骤“Execute shell”

![img](https://images2017.cnblogs.com/blog/670882/201801/670882-20180111110707629-1942693692.png)

增加了一个新的构建步骤，在构建ant任务之前，先在服务器上执行一段shell，功能是删除测试报告文件夹下所有的测试报告文件即可。shell脚本如下：

```
rm -f /var/lib/jenkins/workspace/test_token/html/*.html
rm -f /var/lib/jenkins/workspace/test_token/jtl/*.jtl
```

看一下构建日志发现shell脚本被成功执行：

![img](https://images2017.cnblogs.com/blog/670882/201801/670882-20180111112357738-1834652723.png)

 

**方案二**

job->配置->构建环境->Delete workspace before build starts

![img](https://images2017.cnblogs.com/blog/670882/201801/670882-20180111112845019-1641780549.png)

构建环境我勾选了"Delete workspace before build starts"，在具体解释这一步配置之前，我先说一下workspace在目前这个平台中起到什么作用：

首先，我们在jenkins每创建一个新的job，系统就会在jenkins的安装目录下的workspace文件夹下创建一个以job name命名的文件夹；

接着，由于邮件通知添加附件的问题，我们手动把测试报告的输出位置移动到这个job name命名的文件夹下面，也就是经常提到的html文件夹的位置；

希望下面两张图能够加深对他的理解：

![img](https://images2017.cnblogs.com/blog/670882/201801/670882-20180111114242332-1747616087.png)

​             图一：workspace的目录树结构

**![img](https://images2017.cnblogs.com/blog/670882/201801/670882-20180111140427769-1065036794.png)**

 

​                                                                图二：test_token项目下的工作空间目录

 我们可以使用ANT语法指定一种模式来筛选出要被删除的文件：

（1）下拉选择框有两种模式Include和Exclude

Include模式：如果文件匹配语法要求删选条件，则文件会被移除

Exclude模式：如果文件匹配语法要求删选条件，则文件不会被移除

（2）规则只适用于文件，如果你想要适用于文件夹，请勾选Apply pattern also on directories参数

我这里填写的规则意思是，在workspace目录下的本项目中，筛选出所有以.html为后缀的文件并移除他们。

关于这个功能的配置大家还可以参考官方文档的原文https://wiki.jenkins.io/display/JENKINS/Workspace+Cleanup+Plugin

接下来看一下构建日志检查构建环境是否执行：

![img](https://images2017.cnblogs.com/blog/670882/201801/670882-20180111142827285-587443225.png)

 

OK，以上两种方法都可以解决邮件通知附件的问题，大家可根据实际需要进行选择。

 

# 2、丢弃旧的构建

每次构建都会保存数据，所以需要定期的清除过期文件，这时就用到了“丢弃旧的构建”这个功能。

保持构建的天数：每个构建能保留多少天

保持构建的最大个数：最多保留多少个构建

![img](https://images2017.cnblogs.com/blog/670882/201801/670882-20180111150738035-1026420226.png)

 

 

好了，优化部分就到此为止。



-------------

#  Jenkins+Jmeter+ant接口自动化框架for Linux学习

2018年06月08日 17:55:31 [o随风挥手o](https://me.csdn.net/oHuaXin1234) 阅读数 1687



 版权声明： https://blog.csdn.net/oHuaXin1234/article/details/80618051

之前看网上看了很多的教程，在自己的电脑上面搭建了一个Jenkins+jmeter+ant。

但是由于云服务器闲置并且更稳定一些，尝试了好几次并没有搭建成功。

终于在多次尝试中，昨天在Linux服务器中搭建成功了，现在已经可以正常使用了。

下面我详细的叙述如何去搞定这个些步骤。

# 一、准备工作

首先我们需要下载搭建环境过程中的安装包。如下：

jmeter——下载地址：http://jmeter.apache.org/download_jmeter.cgi

ant——下载地址：https://ant.apache.org/bindownload.cgi

jenkins  war包——下载地址：https://jenkins.io/download/      注：war包

Tomcat8  为了更好的管理Jenkins，我在这里使用了Tomcat，当然你也可以使用其他的容器。

下载地址：http://tomcat.apache.org/      下载Tomcat8

下载完成后如图所示：

![img](https://img-blog.csdn.net/20180608105202762?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29IdWFYaW4xMjM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

# 二、连接服务器并上传文件

使用xshell工具链接服务器（此工具的使用在此不做赘述）

![img](https://img-blog.csdn.net/20180608101410564?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29IdWFYaW4xMjM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我选择将下载的安装包放在opt目录。你可以自己选择自己要存放的目录。

进入opt目录：![img](https://img-blog.csdn.net/20180608101638425?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29IdWFYaW4xMjM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

输入ls命令检查里面的空间：![img](https://img-blog.csdn.net/20180608101711559?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29IdWFYaW4xMjM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

无误后我们开始上传文件：

在xshell中点击![img](https://img-blog.csdn.net/20180608103855972?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29IdWFYaW4xMjM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)这个按钮即可启动xfp。

![img](https://img-blog.csdn.net/20180608104111715?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29IdWFYaW4xMjM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

我们在左边进入电脑的上面文件的下载文件夹，右边我们进入opt目录。

选定下载的四个文件，传输到Linux服务器上面去。

![img](https://img-blog.csdn.net/20180608105223841?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29IdWFYaW4xMjM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后我们回到xshell，进入到opt目录。

执行解压命令将三个压缩包进行解压。tar -zxvf 文件名。

解压完成后生成了三个目录。

分别如下：

apache-jmeter-4.0

apache-ant-1.10.3

apache-tomcat-8.5.31

#  

# 三、Ant配置Jmeter

就是用ant构建命令来调动执行jmeter接口测试，并生成测试报告

1. 首先在Jmeter主目录下新建一个report文件夹
2. 进入report目录
3. 在report目录中新建如下所示的三个文件夹

​          jtl            测试报告存放目录（jtl）

​            html            测试报告存放目录（HTML）

​            run              测试脚本存放目录

## 3.1 配置库文件

 

将jmeter目录下的extras目录下的ant-jmeter-1.1.1.jar文件复制到ant目录下的lib文件夹中

 

##  

## 3.2  新建配置ant的编译文件 build.xml

注：将新建的build.xml文件放入在Jmeter目录中新建的report文件夹。

标红部分可根据自己的配置进行修改。

 

<?xml version="1.0" encoding="UTF-8"?>    <project name="ant-jmeter-test" default="run" basedir=".">            <tstamp>          <format property="time" pattern="yyyyMMddhhmm" />      </tstamp>      <property name="basedirectory" value="/opt/apache-jmeter-4.0/extras" />      <!-- 需要改成自己本地的 Jmeter 目录-->        <property name="jmeter.home" value="/opt/apache-jmeter-4.0" />      <!-- jmeter生成jtl格式的结果报告的路径-->       <property name="jmeter.result.jtl.dir" value="/opt/apache-jmeter-4.0/report\jtl" />      <!-- jmeter生成html格式的结果报告的路径-->      <property name="jmeter.result.html.dir" value="/opt/apache-jmeter-4.0/report\html" />      <!-- Name of test (without .jmx) -->      <property name="test" value="Test"/>      <!-- 生成的报告的前缀-->        <property name="ReportName" value="TestReport" />      <property name="jmeter.result.jtlName" value="${jmeter.result.jtl.dir}/${time}.jtl" />      <property name="jmeter.result.htmlName" value="${jmeter.result.html.dir}/${time}.html" />            <path id="xslt.classpath">          <fileset dir="${jmeter.home}/lib" includes="xalan*.jar"/>          <fileset dir="${jmeter.home}/lib" includes="serializer*.jar"/>      </path>            <target name="run">          <antcall target="test" />          <antcall target="report" />      </target>            <target name="test">          <taskdef name="jmeter" classname="org.programmerplanet.ant.taskdefs.jmeter.JMeterTask" />            <jmeter jmeterhome="${jmeter.home}" resultlog="${jmeter.result.jtlName}">               <!-- 声明要运行的脚本。"*.jmx"指包含此目录下的所有jmeter脚本-->              <testplans dir="/opt/apache-jmeter-4.0/report/run" includes="*.jmx" />          </jmeter>      </target>                <target name="report">          <tstamp> <format property="report.datestamp" pattern="yyyy/MM/dd HH:mm" /></tstamp>          <xslt classpathref="xslt.classpath"                force="true"                in="${jmeter.result.jtlName}"                out="${jmeter.result.htmlName}"                style="${jmeter.home}/extras/jmeter-results-detail-report_21.xsl">                <param name="dateReport" expression="${report.datestamp}"/>          </xslt>          <copy todir="${jmeter.result.html.dir}">              <fileset dir="${jmeter.home}/extras">                  <include name="collapse.png" />                  <include name="expand.png" />              </fileset>          </copy>      </target>        </project>

## 3.3  配置jmeter.properties文档

找到jmeter.properties文档，在/opt/apache-jmeter-4.0/bin目录下，打开该文档并编辑

修改jmeter报告输出格式为xml：

改jmeter.save.saveservice.output_format=csv 为jmeter.save.saveservice.output_format=xml，并去掉前面的注释符号#

 

## 3.4 验证配置，执行构建测试

选取一个做好的jmeter测试脚本放入report目录中的run目录。如不知此目录在哪，还请阅读上文

 

## 3.5  执行测试

第一步：cd到build.xml文件所在目录

第二步：输入   ant      回车，执行测试

![img](https://img-blog.csdn.net/20180608151212442?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29IdWFYaW4xMjM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

出现如图所示successful，成功！

 

## 3.6   查看测试结果报告

在报告输出存放路径下查看是否有jtl和html结果报告，存放路径在build文档中也有

打开html文档，测试结果展现了执行的用例名称、成功率、用例执行时间等结果参数

 ![img](https://img-blog.csdn.net/20180607115549641?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29IdWFYaW4xMjM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这样的结果是不是不太直观，因为用jmeter自带的测试报告得到的测试报告信息并不是很全，下面讲一下怎么优化测试报告

## 3.7、 优化测试报告

### ①下载优化模板 jmeter-results-shanhe-me.xsl，拷贝到jmeter的extras目录中

下载地址：

链接：https://pan.baidu.com/s/17UlGb1o0tZ37PLmtay40-g 

密码：bftq

将下载的东西移动到  /opt/apache-jmeter-4.0/extras  目录下

### ②设置测试输出报告要输出的内容：

进入/opt/apache-jmeter-4.0/bin目录。

在jmeter.properties中，设置需要输出的内容为true，并去掉前面的注释符号#

这里全部设置成true→保存。如下格式：

注：此处请尽量选择使用notepad++和xftp搭配进行更改

[html] [view plain](https://blog.csdn.net/oHuaXin1234/article/details/80606976#) [copy](https://blog.csdn.net/oHuaXin1234/article/details/80606976#)







1. jmeter.save.saveservice.data_type=true  
2.   
3. jmeter.save.saveservice.label=true  
4.   
5. jmeter.save.saveservice.response_code=true  
6.   
7. \# response_data is not currently supported for CSV output  
8.   
9. jmeter.save.saveservice.response_data=true  
10.   
11. \# Save ResponseData for failed samples  
12.   
13. jmeter.save.saveservice.response_data.on_error=true  

[html] [view plain](https://blog.csdn.net/oHuaXin1234/article/details/80606976#) [copy](https://blog.csdn.net/oHuaXin1234/article/details/80606976#)







1. jmeter.save.saveservice.response_message=true  
2.   
3. jmeter.save.saveservice.successful=true  
4.   
5. jmeter.save.saveservice.thread_name=true  
6.   
7. jmeter.save.saveservice.time=true  
8.   
9. jmeter.save.saveservice.subresults=true  
10.   
11. jmeter.save.saveservice.assertions=true  
12.   
13. jmeter.save.saveservice.latency=true  
14.   
15. jmeter.save.saveservice.connect_time=true  
16.   
17. jmeter.save.saveservice.samplerData=true  
18.   
19. jmeter.save.saveservice.responseHeaders=true  
20.   
21. jmeter.save.saveservice.requestHeaders=true  
22.   
23. jmeter.save.saveservice.encoding=true  
24.   
25. jmeter.save.saveservice.bytes=true  
26.   
27. jmeter.save.saveservice.url=true  
28.   
29. jmeter.save.saveservice.filename=true  
30.   
31. jmeter.save.saveservice.hostname=true  
32.   
33. jmeter.save.saveservice.thread_counts=true  
34.   
35. jmeter.save.saveservice.sample_count=true  
36.   
37. jmeter.save.saveservice.idle_time=true  

 

### ③在report目录设置buildxml文件的报告模板为优化后的模板jmeter-results-shanhe-me.xsl

 

 

 style="${jmeter.home}/extras/jmeter.results.shanhe.me.xsl">

此处：标红的名字已为替换后的文件名

 

### ④再次用ant构建测试，查看优化后的测试报告

![img](https://img-blog.csdn.net/20180607115837229?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29IdWFYaW4xMjM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

到了这一步Jmeter和ant之间的衔接已经完成了。接下来配置Jenkins。

# **4、配置Jenkins**

将Jenkins.war安装包。移动到Tomcat中webapps目录下。

在opt目录中执行mv jenkins.war apache-tomcat-8.5.31/webapps

![img](https://img-blog.csdn.net/20180608173409371?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29IdWFYaW4xMjM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后我们返回到opt目录下。

依次执行如下命令：

chmod -R 777 apache-jmeter-4.0

chmod -R 777 apache-ant-1.10.3

chmod -R 777 apache-tomcat=8.5.31

完成后我们进入Tomcat中的bin目录。

输入：./startup.sh运行Tomcat。

在浏览器中输入http://服务器地址:8080/，进入后出现如下界面

注：如果地址没有响应，多半是端口没有对外开放。

iptables -I INPUT 5 -m  state  --state  NEW  -m  tcp  -p  tcp  --dport  8080  -j  ACCEPT

输入上面的命令即可

![img](https://img-blog.csdn.net/20180608174529462?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29IdWFYaW4xMjM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后输入       http://服务器地址:8080/Jenkins

就可以启动Jenkins了。

 

进入后按步骤操作。并设置用户名。注：用户名必须设置。

##  

## 5、新建并配置一个任务

 ![img](https://img-blog.csdn.net/20180607141924706?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29IdWFYaW4xMjM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

设置项目名称：

 ![img](https://img-blog.csdn.net/20180607142121493?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29IdWFYaW4xMjM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

配置构建：

 ![img](https://img-blog.csdn.net/20180607143234780?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29IdWFYaW4xMjM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 基本配置完成

##  

## 6、任务主页，立即构建，执行测试

执行完成后控制台输出如下。

 ![img](https://img-blog.csdn.net/20180607143620116?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29IdWFYaW4xMjM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 

至此构建基本完成，打开测试报告存放路径会看到新生成的报告文档。

![img](https://img-blog.csdn.net/20180608175458599?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29IdWFYaW4xMjM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

HTML结果打开如下图所示：

![img](https://img-blog.csdn.net/20180607143918606?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29IdWFYaW4xMjM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

还可以配置邮件插件来发送测试报告（还在研究中）。

 

 

\#如果有人看不懂或者没有不知如何操作其中的步骤，请在评论中说明，我每天下班后都会看博客，为你解答，并更新博客细化步骤。

\#作为互联网从业者，收获使我产生快乐，很乐意记录并分享。