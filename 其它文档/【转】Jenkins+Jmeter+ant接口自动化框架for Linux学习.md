# 【转】Jenkins+Jmeter+ant接口自动化框架for Linux学习

2018年06月08日 17:55:31 [o随风挥手o](https://me.csdn.net/oHuaXin1234) 阅读数：1476



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


```xml
<?xml version="1.0" encoding="UTF-8"?>  

<project name="ant-jmeter-test" default="run" basedir=".">  
      
    <tstamp>  
        <format property="time" pattern="yyyyMMddhhmm" />  
    </tstamp>  
    <property name="basedirectory" value="/opt/apache-jmeter-4.0/extras" />  
    <!-- 需要改成自己本地的 Jmeter 目录-->    
    <property name="jmeter.home" value="/opt/apache-jmeter-4.0" />  
    <!-- jmeter生成jtl格式的结果报告的路径-->   
    <property name="jmeter.result.jtl.dir" value="/opt/apache-jmeter-4.0/report/jtl" />  
    <!-- jmeter生成html格式的结果报告的路径-->  
    <property name="jmeter.result.html.dir" value="/opt/apache-jmeter-4.0/report/html" />  
    <!-- Name of test (without .jmx) -->  
    <property name="test" value="Test"/>  
    <!-- 生成的报告的前缀-->    
    <property name="ReportName" value="TestReport" />  
    <property name="jmeter.result.jtlName" value="${jmeter.result.jtl.dir}/${time}.jtl" />  
    <property name="jmeter.result.htmlName" value="${jmeter.result.html.dir}/${time}.html" />  
      
    <path id="xslt.classpath">  
        <fileset dir="${jmeter.home}/lib" includes="xalan*.jar"/>  
        <fileset dir="${jmeter.home}/lib" includes="serializer*.jar"/>  
    </path>  
      
    <target name="run">  
        <antcall target="test" />  
        <antcall target="report" />  
    </target>  
      
    <target name="test">  
        <taskdef name="jmeter" classname="org.programmerplanet.ant.taskdefs.jmeter.JMeterTask" />  
      
    <jmeter jmeterhome="${jmeter.home}" resultlog="${jmeter.result.jtlName}">  
             <!-- 声明要运行的脚本。"*.jmx"指包含此目录下的所有jmeter脚本-->  
            <testplans dir="/opt/apache-jmeter-4.0/report/run" includes="*.jmx" />  
        </jmeter>  
    </target>  
          
    <target name="report">  
        <tstamp> <format property="report.datestamp" pattern="yyyy/MM/dd HH:mm" /></tstamp>  
        <xslt classpathref="xslt.classpath"  
              force="true"  
              in="${jmeter.result.jtlName}"  
              out="${jmeter.result.htmlName}"  
              style="${jmeter.home}/extras/jmeter-results-detail-report_21.xsl">  
              <param name="dateReport" expression="${report.datestamp}"/>  
        </xslt>  
        <copy todir="${jmeter.result.html.dir}">  
            <fileset dir="${jmeter.home}/extras">  
                <include name="collapse.png" />  
                <include name="expand.png" />  
            </fileset>  
        </copy>  
    </target>  
      
</project>
```

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