# linux+ant+jmeter+jenkins搭建接口自动化持续集成平台

在linux搭建好环境，配置好ant  jmeter  jenkins

一、Jmeter+ant
1、首先我们默认Jmeter脚本已经录制好了，并测试通过，存在（查询模块.jmx）脚本 
2、将JMeter所在目录下extras子目录里的ant-JMeter-1.1.1.jar复制到Ant所在目录lib子目录之下，这样Ant运行时才能找到"org.programmerplanet.ant.taskdefs.jmeter.JMeterTask"这个类，从而成功触发JMeter脚本      
3、jmeter默认保存的是.csv格式的文件，所以我们先要设置一下bin/jmeter.properties文件内容，保存
  jmeter.save.saveservice.output_format=xml

把javax.mail-1.4.4 复制到ant 中的lib目录中，

新建build.xml文件，脚本文件，报告文件

![img](https://img-blog.csdn.net/20170727092513799?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzAzNTMyMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

```xml
<?xml version="1.0" encoding="utf-8"?>
<project name="JmeterTest" default="all" basedir=".">
    <tstamp>
        <format property="time" pattern="yyyyMMddhhmm" />
    </tstamp>
    <!-- 需要改成自己本地的 Jmeter 目录-->
    <property name="jmeter.home" value="D:\apache-jmeter-3.0 (1)\apache-jmeter-3.0" />
    <!-- jmeter生成jtl格式的结果报告的路径-->
    <property name="jmeter.result.jtl.dir" value="D:\apache-jmeter-3.0 (1)\apache-jmeter-3.0\demoapitest\report\jtl" />
    <!-- jmeter生成html格式的结果报告的路径-->
    <property name="jmeter.result.html.dir" value="D:\apache-jmeter-3.0 (1)\apache-jmeter-3.0\demoapitest\report\html" />
    <!-- 生成的报告的前缀 -->
    <property name="ReportName" value="TestReport" />
    <property name="jmeter.result.jtlName" value="${jmeter.result.jtl.dir}/${ReportName}${time}.jtl" />
    <property name="jmeter.result.htmlName" value="${jmeter.result.html.dir}/${ReportName}${time}.html" />
    <!-- 接收测试报告的邮箱 -->
    <property name="mail_to" value="niujinliang@mocentre.com" />
    <!-- 电脑地址 -->
    <property name="ComputerName" value="Yyj" />
    <target name="all">
        <antcall target="test" />
        <antcall target="report" />
	<antcall target="sendmail" />
    </target>
    <target name="test">
        <taskdef name="jmeter" classname="org.programmerplanet.ant.taskdefs.jmeter.JMeterTask" />
        <jmeter jmeterhome="${jmeter.home}" resultlog="${jmeter.result.jtlName}">
            <!-- 声明要运行的脚本。"*.jmx"指包含此目录下的所有jmeter脚本 -->
            <testplans dir="D:\apache-jmeter-3.0 (1)\apache-jmeter-3.0\demoapitest" includes="*.jmx" />
			
			<property name="jmeter.save.saveservice.output_format" value="xml"/>
        </jmeter>
    </target>
	
	  
	
    <target name="report">
        <xslt 
		in="${jmeter.result.jtlName}" 
		out="${jmeter.result.htmlName}" 
		style="${jmeter.home}/extras/jmeter-results-detail-report_21.xsl" />
        <!-- 因为上面生成报告的时候，不会将相关的图片也一起拷贝至目标目录，所以，需要手动拷贝 -->
        <copy todir="${jmeter.result.html.dir}">
            <fileset dir="${jmeter.home}/extras">
                <include name="collapse.png" />
                <include name="expand.png" />
            </fileset>
        </copy>
    </target>
	
    <!-- 发送邮件 -->
	
	<target name="sendmail" >
		<mail mailhost="smtp.163.com"
			mailport="465"
			ssl="true"		
			user="niu2415793@163.com"
			password="**********"
			subject="接口测试报告"
			from="niu2415793@163.com"
			>
			<to address="niujinliang@mocentre.com"/>
			<message>详细报告请查看附件</message>
			  <attachments>
			    <fileset dir="${jmeter.result.html.dir}"> 
			      <include name="${ReportName}${time}.html"/>
			    </fileset> 
			  </attachments>
		</mail>
	</target>
	
	
</project>
```

一定要加这段，否则会报错，第一行不能有文字 <property name="jmeter.save.saveservice.output_format" value="xml"/>
遇到的问题 jmeter3.0中没有/extras/jmeter-results-detail-report_21.xsl，这个文件，从低版本中拷贝过来，就可以了。

jmeter-results-detail-report_21

jmeter-results-report_21

配置完成之后，cd到D:\apache-jmeter-3.0 (1)\apache-jmeter-3.0\demoapitest目录中，执行ant。ant+jmeter就会在邮箱里收到测试报告邮件。报告就会在report文件夹中生成。

![img](https://img-blog.csdn.net/20170628144410241?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzAzNTMyMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20170727100047047?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzAzNTMyMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

启动jenkins，配置jenkins

1、新建一个自由风格的项目

![img](https://img-blog.csdn.net/20170627172239423?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzAzNTMyMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



旧的构建保存了你在一次构建中的所有临时文件，如果构建没有保留的必要，就勾选丢弃旧的构建，同时设置保持构建天使和保持构建的最大个数两个参数。

注意：三个设置必须同时设置，否则无效![img](https://img-blog.csdn.net/20170727100119372?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzAzNTMyMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

设置定时运行间隔，这里，设置间隔时间为15分钟运行一次

![img](https://img-blog.csdn.net/20170627172359440?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzAzNTMyMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

设置invoke ant，完成后点击保存

![img](https://img-blog.csdn.net/20170727100140233?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzAzNTMyMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20170727100203226?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzAzNTMyMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20170727100213835?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzAzNTMyMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20170727100224606?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzAzNTMyMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20170727100233969?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzAzNTMyMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

点击立即构建，就可以调用ant，执行打包过程。在这里，也就是执行生成测试报告的步骤



[ant+jmeter+jenkins 本地搭建步骤](http://pan.baidu.com/s/1c2rgN2C)

[serverAgent](http://pan.baidu.com/s/1bpzqvy3) 用那个这个上传到被测服务器上，执行，可以监测到被测服务器cpu 内存 磁盘 使用情况



------

# linux下使用ant+Jmeter+Jenkins实现接口测试自动化

背景：后台重构，借此机会将所有接口实现自动化测试，并推送测试报告到邮箱

一.环境准备

1.安装Jmeter

wget "http://mirrors.tuna.tsinghua.edu.cn/apache//jmeter/binaries/apache-jmeter-4.0.tgz"，镜像地址可从官网找到

tar -zxvf apache-jmeter-4.0.tgz

配置环境变量（/etc/profile）：

export JMETER_HOME=/usr/apache-jmeter-4.0/

CLASSPATH=.:$JMETER_HOME\lib\ext\ApacheJMeter_core.jar:$JMETER_HOME\lib\jorphan.jar:$JMETER_HOME\lib\logkit-1.2.jar

path：$JMETER_HOME/bin



修改$JMETER_HOME/bin/jmeter.properties 保存的数据格式为xml，默认为csv

jmeter.save.saveservice.output_format=xml

2.安装JDK（略）

3.安装ANT（略）

cp $JMETER_HOME/extras/ant-jmeter-1.1.1.jar $ANT_HOME/lib/

4.安装Jenkins（使用tomcat启动，略）

二.配置

1.build.xml示例

<?xml version="1.0" encoding="UTF-8"?>  

<project name="ant-jmeter-test" default="run" basedir=".">  
      

```xml
<tstamp>  
    <format property="time" pattern="yyyyMMddhhmm" />  
</tstamp>  
<property name="basedirectory" value="/usr/apache-jmeter-4.0/extras" />  
<!-- 需要改成自己本地的 Jmeter 目录-->    
<property name="jmeter.home" value="/usr/apache-jmeter-4.0" />  
<!-- jmeter生成jtl格式的结果报告的路径-->   
<property name="jmeter.result.jtl.dir" value="${basedirectory}/report" />  
<!-- jmeter生成html格式的结果报告的路径-->  
<property name="jmeter.result.html.dir" value="${basedirectory}/report" />  
<!-- Name of test (without .jmx) -->  
<property name="test" value="store_rebuild_api"/>  
<!-- 生成的报告的前缀-->    
<property name="ReportName" value="TestReport" />  
<property name="jmeter.result.jtlName" value="${jmeter.result.jtl.dir}/${test}.jtl" />  
<property name="jmeter.result.htmlName" value="${jmeter.result.html.dir}/${test}.html" />  
  
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
         <!-- 指定要运行的脚本。"*.jmx"指包含此目录下的所有jmeter脚本-->  
        <testplans dir="/root/.jenkins/jobs/Interface_Test/workspace/jmx/" includes="*.jmx" />  
    </jmeter>  
</target>  
      
<target name="report">  
    <tstamp> <format property="report.datestamp" pattern="yyyy/MM/dd HH:mm" /></tstamp>  
    <xslt classpathref="xslt.classpath"  
          force="true"  
          in="${jmeter.result.jtlName}"  
          out="${jmeter.result.htmlName}"  
      style="${jmeter.home}/extras/jmeter-results-shanhe-me.xsl">
```



```xml
                <!--设置报告模板-->  	  
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

2.Jenkins邮箱配置（以QQ邮箱为例）

Jenkins>系统管理>系统设置

![img](https://img-blog.csdn.net/20180330142353613?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbmdqaWUwOTI1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

![img](https://img-blog.csdn.net/20180330142841325?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbmdqaWUwOTI1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



![img](https://img-blog.csdn.net/201803301429571?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbmdqaWUwOTI1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

3.配置Jenkins工程（仅列出构建和邮件，有其余需要自行配置）

构建步骤：

![img](https://img-blog.csdn.net/20180329174300480)

![img](https://img-blog.csdn.net/2018032917520690)

![img](https://img-blog.csdn.net/20180329175212236)

邮件配置：

![img](https://img-blog.csdn.net/20180329175217535)

设置发送条件：

![img](https://img-blog.csdn.net/20180329175523999?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbmdqaWUwOTI1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)





-----------

# 超详细Jmeter+Ant+Jinkens框架搭建（码农Damon）





**一、jmeter** 

我用的jmeter3.2   [**jmeter**](javascript:;)要运行，必须本地有[**java**](javascript:;)环境，所以需要配置jdk什么的，自行配置

二、ant

安装ant

第一步：下载ant  http://ant.apache.org/

**第二步：配置ant** 

**window中设置ant环境变量：**

**以下是我电脑ant配置**

ANT_HOME    C:/ apache-ant-1.9.7

path             C:/ apache-ant-1.9.7/bin

classpath      C:/apache-ant-1.9.7/lib

cmd打开 输入ant执行

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906204718507-1375286100.png)

说明ant安装成功！因为ant默认运行build.xml文件，build.xml 需要我们自己建立，等哈后面 我们再说这个

为了确定真的安装成功了  我们执行ant -version

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906204907897-1528195458.png)

 

ok了，我们继续往后面扯

**三、将\apache-jmeter-3.2\extras下面的ant-jmeter-1.1.1的jar包到ant的lib目录，如图所示：**

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906205237413-794858320.png)

 **四、建立build.xml**

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906205353179-1718985015.png)

从上图可以知道  我们创建了一个文件夹demo，然后把build.xml 丢在它下面了

![复制代码](https://common.cnblogs.com/images/copycode.gif)

```
<?xml version="1.0" encoding="UTF-8"?><projectname="ant-jmeter-test"default="run"basedir="."><tstamp><formatproperty="time"pattern="yyyyMMddhhmm"/></tstamp><!--需要改成自己本地的 Jmeter 目录--><propertyname="jmeter.home"value="D:\study\apache-jmeter-3.2"/><!--jmeter生成jtl格式的结果报告的路径--><propertyname="jmeter.result.jtl.dir"value="D:\study\apache-jmeter-3.2\demo\report\jtl"/><!--jmeter生成html格式的结果报告的路径--><propertyname="jmeter.result.html.dir"value="D:\study\apache-jmeter-3.2\demo\report\html"/><!--生成的报告的前缀--><propertyname="ReportName"value="TestReport"/><propertyname="jmeter.result.jtlName"value="${jmeter.result.jtl.dir}/${ReportName}${time}.jtl"/><propertyname="jmeter.result.htmlName"value="${jmeter.result.html.dir}/${ReportName}${time}.html"/><targetname="run"><antcalltarget="test"/><antcalltarget="report"/></target><targetname="test"><taskdefname="jmeter"classname="org.programmerplanet.ant.taskdefs.jmeter.JMeterTask"/><jmeterjmeterhome="${jmeter.home}"resultlog="${jmeter.result.jtlName}"><!--声明要运行的脚本。"*.jmx"指包含此目录下的所有jmeter脚本--><testplansdir="D:\study\apache-jmeter-3.2\demo"includes="*.jmx"/><propertyname="jmeter.save.saveservice.output_format"value="xml"/></jmeter></target><pathid="xslt.classpath"><filesetdir="${jmeter.home}/lib"includes="xalan*.jar"/><filesetdir="${jmeter.home}/lib"includes="serializer*.jar"/></path><targetname="report"><tstamp><formatproperty="report.datestamp"pattern="yyyy/MM/dd HH:mm"/></tstamp><xsltclasspathref="xslt.classpath"force="true"in="${jmeter.result.jtlName}"out="${jmeter.result.htmlName}"style="${jmeter.home}/extras/jmeter-results-report-loadtest.xsl"><paramname="dateReport"expression="${report.datestamp}"/></xslt><!--因为上面生成报告的时候，不会将相关的图片也一起拷贝至目标目录，所以，需要手动拷贝--><copytodir="${jmeter.result.html.dir}"><filesetdir="${jmeter.home}/extras"><includename="collapse.png"/><includename="expand.png"/></fileset></copy></target></project>
```



上图注释已经很清晰了，只需要配置下jmeter路径以及报告样式就可以使用我这个build.xml文件了

**五、创建一个jmx文件 然后运行下**

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906205738710-2056939576.png)

进入到该目录，并cmd执行ant

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906205858366-1390075411.png)

 

 上图的BUILD SUCCESSFUL 证明已经成功了，而且报告在report/html下面

现在我们打开报告看看

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906210019382-1637357350.png)

报告是不是很好看，其实就是\apache-jmeter-3.2\extras这个目录下jmeter-results-report-loadtest.xsl这个样式生成的报告

**六、集成jenkins**

废话不多说 ，启动jenkins

1、java -jar jenkins.war (自己去下载jenkins.war)  也可以在群里问我要

2、下载jenkins.tar 并解压 将解压之后的文件丢在tomcat下面的webapps下面      

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906210350007-552056843.png)

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906210417976-319909214.png)

双击C:\apache-tomcat-7.0.72\bin下面的startup.bat 启动  

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906210711819-1358318487.png)

这就证明jenkins已经启动了

然后随意打开一个[**浏览器**](javascript:;) 输入：http://localhost:8080/jenkins  默认端口号为8080

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906210838413-537365119.png)

我自己改成8888的，你们随意

创建一个项目。

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906211044772-358224575.png)

 

 选择第一个自由风格，然后点击OK按钮

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906211202241-828987658.png)

 

 然后配置ant，增加build路径

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906211300569-1297622866.png)

 

 ![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906211401210-119303544.png)

 

点击保存之后，我们继续配置测试报告

我们需要下载一个插件Public HTML reports 

系统管理----》管理插件----》可选插件   输入Public HTML reports 进行查询并进行安装

接下来继续配置报告

点击增加构建后操作步骤  并点击Public HTML reports 

 ![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906211934132-1494465116.png)

 

 并点击保存按钮  好了  现在我们报告也配置好了 

我们现在jenkins构建下吧

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906212027897-1801302965.png)

点击之后 出现![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906212050460-1846801619.png)

证明jenkins在构建了

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906212128366-1432959712.png)

好了到了这一步 已经构建成功了，Success ! 

我们在jenkins下面看下报告吧

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906212242757-1717252472.png)

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906212532022-1141702157.png)

![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906212551819-440634102.png)

 ![img](https://images2017.cnblogs.com/blog/707331/201709/707331-20170906213140554-1300812712.png)

 

 报告已经ok了

其实[**文章**](javascript:;)篇幅很长，配置很简单

最后可以用jenkins定时执行我们的jmeter脚本，也就是批量执行jmeter脚本了

---------------------

# Jmeter+Ant报告生成原理

  Ant生成报告主要是build.xml，这里我们就来分析一下build.xml的结构

下面是一个完整的build.xml，然后我们来一句一句的详细解释

```xml
<?xml version="1.0" encoding="Gb2312"?>
build.xml中的第一句话，主要是编码格式
<project name="ant-jmeter-test" default="run" basedir=".">
ant的所有内容必须包含在这个里边，name是你给它取的名字，basedir故名思意就是工作的根目录 .代表当前目录。default代表默认要做的事情。
    <tstamp>
        <format property="time" pattern="yyyyMMddhhmm" />
    </tstamp>
定义的时间格式
    <property environment="env"/>
    <property name="ReportName" value="TestReport" />
    <property name="jmeter.home" value="D:\apache-jmeter-2.13\apache-jmeter-2.13" />
    <property name="jmeter.result.dir" value="${env.WORKSPACE}/results/${env.BUILD_ID}" />
    <!-- 生成的报告的前缀-->  
    <property name="jmeter.result.jtlName" value="${jmeter.result.dir}/${ReportName}.jtl" />
    <property name="jmeter.result.htmlName" value="${jmeter.result.dir}/${ReportName}.html" />
 property类似程序中的变量，为什么这么做想一下变量的作用   
    <target name="run">
        <echo message="start..."/>
        <antcall target="clean" />
        <antcall target="test" />
        <antcall target="report" />
    </target>
    把你想做的每一件事情写成一个target ，它有一个名字，depends是它所依赖的target，在执行这个target 例如这里的compile之前ant会先检查init是否曾经被执行过，如果执行  
过则直接直接执行compile，如果没有则会先执行它依赖的target例如这里的init，然后在执行这个target
如我们的计划  
编译：  
<target name="compile" depends="init">  
<javac srcdir="${src}" destdir="${dest}"/>  
</target>  
做jar包:  
<target name="build" depends="compile">  
<jar jarfile="${hello_jar}" basedir="${dest}"/>  
</target>  
运行：  
<target name="run" depends="build">  
<java classname="test.ant.HelloWorld" classpath="${hello_jar}"/>  
</target>  
为了不用拷贝，我们可以在最开始定义好目标文件夹，这样ant直接把结果就放在目标文件夹中了  
新建文件夹:  
<target name="init">  
<mkdir dir="${dest}"/>  
</target>  
为了更多一点的功能体现，又加入了两个target  
删除生成的文件  
<target name="clean">  
<delete dir="${dest}" />  
<delete file="${hello_jar}" />  
</target>  
再次运行，这里显示了如何在一个target里边调用其他的target  
<target name="rerun" depends="clean,run">  
<ant target="clean" />  
<ant target="run" />  
</target>  
好了，解释完成了，下边检验一下你的ant吧  
新建一个src的文件夹，然后把HelloWorld.java按照包目录放进去  
做好build.xml文件  
在命令行下键入ant ,你会发现一个个任务都完成了。每次更改完代码只需要再次键入ant  
有的时候我们可能并不想运行程序，只想执行这些步骤中的某一两个步骤，例如我只想重新部署而不想运行，键入  
ant build  
ant中的每一个任务都可以这样调用ant + target name  
好了，这样一个简单的ant任务完成了。 
 
    <target name="clean">
        <mkdir dir="${env.WORKSPACE}/results/${env.BUILD_ID}" />
    </target>
        
    <target name="test">
        <taskdef name="jmeter" classname="org.programmerplanet.ant.taskdefs.jmeter.JMeterTask" />
        <jmeter jmeterhome="${jmeter.home}" resultlog="${jmeter.result.jtlName}">
            <!-- 声明要运行的脚本"*.jmx"指包含此目录下的所有jmeter脚本-->
            <testplans dir="D:\apache-jmeter-2.13\apache-jmeter-2.13\bin\script" includes="*.jmx" />           
            <property name="jmeter.save.saveservice.output_format" value="xml"/>
        </jmeter>
    </target>
    
	<path id="xslt.classpath">
        <fileset dir="${jmeter.home}/lib" includes="xalan*.jar"/>
        <fileset dir="${jmeter.home}/lib" includes="serializer*.jar"/>
    </path>
	
    <target name="report">
        <tstamp> <format property="report.datestamp" pattern="yyyy/MM/dd HH:mm" /></tstamp>
        <xslt 
		      classpathref="xslt.classpath"
		      in="${jmeter.result.jtlName}"
              out="${jmeter.result.htmlName}"
              style="${jmeter.home}/extras/jmeter-results-detail-report_21.xsl" />
 
        <!-- 因为上面生成报告的时候，不会将相关的图片也一起拷贝至目标目录，所以，需要手动拷贝 --> 
        <copy todir="${jmeter.result.dir}">
            <fileset dir="${jmeter.home}/extras">
                <include name="collapse.png" />
                <include name="expand.png" />
            </fileset>
        </copy>
    </target>
</project>
```



-----------

# JMETER+ANT+JENKINS（一）——环境准备&jmeter 与ant 关联

环境准备：

1.jdk        下载地址：https://www.oracle.com/technetwork/java/javase/downloads/index.html
2.jmeter  下载地址：  http://jmeter.apache.org/download_jmeter.cgi
3.ant       下载地址：  http://ant.apache.org/

4.tomcat  下载地址： https://www.cnblogs.com/beginner-boy/p/7806680.html

5.jenkins 下载地址： https://jenkins.io/download/

第一步： 准备jmeter 脚本
第二步： jmeter +ant 


想要实现jmeter与ant关联，需要做如下几个操作：

1、修改jmeter配置文件jmeter.properties，把jmeter.save.saveservice.output_format=csv修改为jmeter.save.saveservice.output_format=xml，并去掉注释
2、在jmeter的extras目录下，把ant-jmeter-1.1.1.jar文件复制到ant的lib目录下

3、在jmeter当前目录下创建TestCase文件夹，并把（jmeter 脚本）*.jmx复制到TestCase目录下
    



4、编写build.xml文件，ant执行时候，会使用到该文件
--------------------- 
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project name="ant-jmeter-test" default="run" basedir=".">
    <!-- 需要改成自己本地的 Jmeter 目录-->  
    <property name="jmeter.home" value="E:\apache-jmeter-5.0" />
    <property name="report.title" value="接口测试"/>
    <!-- jmeter生成jtl格式的结果报告的路径--> 
    <property name="jmeter.result.jtl.dir" value="E:\apache-jmeter-5.0\TestCase\JTL" />
    <!-- jmeter生成html格式的结果报告的路径-->
    <property name="jmeter.result.html.dir" value="E:\apache-jmeter-5.0\TestCase\HTML" />
    <!-- 生成的报告的前缀-->  
    <property name="ReportName" value="TestReport" />
    <property name="jmeter.result.jtlName" value="${jmeter.result.jtl.dir}/${ReportName}.jtl" />
    <property name="jmeter.result.htmlName" value="${jmeter.result.html.dir}/${ReportName}.html" />
 
    <target name="run">
        <antcall target="test" />
        <antcall target="report" />
    </target>
    
    <target name="test">
        <taskdef name="jmeter" classname="org.programmerplanet.ant.taskdefs.jmeter.JMeterTask" />
        <jmeter jmeterhome="${jmeter.home}" resultlog="${jmeter.result.jtlName}">
            <!-- 声明要运行的脚本"*.jmx"指包含此目录下的所有jmeter脚本-->
            <testplans dir="E:\apache-jmeter-5.0\TestCase\JMX" includes="*.jmx" />
            
            <property name="jmeter.save.saveservice.output_format" value="xml"/>
        </jmeter>
    </target>
        
    <path id="xslt.classpath">
        <fileset dir="${jmeter.home}/lib" includes="xalan*.jar"/>
        <fileset dir="${jmeter.home}/lib" includes="serializer*.jar"/>
    </path>
 
 
    <target name="report">
        <tstamp> <format property="report.datestamp" pattern="yyyy/MM/dd HH:mm" /></tstamp>
        <xslt 
              classpathref="xslt.classpath"
              force="true"
              in="${jmeter.result.jtlName}"
              out="${jmeter.result.htmlName}"
             
			       style="${jmeter.home}/extras/jmeter.results.shanhe.me.new.xsl">
              <param name="dateReport" expression="${report.datestamp}"/>
       </xslt>
 
                <!-- 因为上面生成报告的时候，不会将相关的图片也一起拷贝至目标目录，所以，需要手动拷贝 --> 
        <copy todir="${jmeter.result.html.dir}">
            <fileset dir="${jmeter.home}/extras">
                <include name="collapse.png" />
                <include name="expand.png" />
            </fileset>
        </copy>
    </target>
 
</project>
```

5、切换 到E:\apache-jmeter-5.0\TestCase\XML（就是放build.xml 文件的目录下）在cmd 中 执行 ant.这个时候就会生成测试报告。



 

参考文件：Jmeter&Ant构建自动化测试平台

                  windows 下tomcat 的安装配置
    
                  Jenkins的详细安装及使用
---------------------

# Jmeter4.0+Ant集成 build.xml高级配置

2018年10月15日 17:08:49 [baijingchuan](https://me.csdn.net/baijingchuan) 阅读数 1091



 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/baijingchuan/article/details/83061568

1. 批量执行某一目录下多个测试脚本，build.xml文件如下

```
<?xml version="1.0" encoding="UTF-8"?>
<project name="ant-jmeter-test" default="run" basedir=".">
      <tstamp>
        <format property="time" pattern="_yyyy_MMdd_HHmmss" />
    </tstamp>
    <!-- 需要改成自己本地的 Jmeter 目录-->  
    <property name="jmeter.home" value="E:\jmeter" />
    <property name="report.title" value="接口测试"/>
    <!-- jmeter生成jtl格式的结果报告的路径--> 
    <property name="jmeter.result.jtl.dir" value="F:\demo\jtl" />
    <!-- jmeter生成html格式的结果报告的路径-->
    <property name="jmeter.result.html.dir" value="F:\demo\html" />
    <property name="detail" value="_detail" />
    <!-- 生成的报告的前缀-->  
    <property name="ReportName" value="SmokeReport" />
    <property name="jmeter.result.jtlName" value="${jmeter.result.jtl.dir}/${ReportName}${time}.jtl" />
    <property name="jmeter.result.htmlName" value="${jmeter.result.html.dir}/${ReportName}${time}.html" />

    <target name="run">
        <antcall target="test" />
        <antcall target="report" />
    </target>

    <target name="test">
        <taskdef name="jmeter" classname="org.programmerplanet.ant.taskdefs.jmeter.JMeterTask" />
        <jmeter jmeterhome="${jmeter.home}" resultlog="${jmeter.result.jtlName}">
            <!-- 声明要运行的脚本"*.jmx"指包含此目录下的所有jmeter脚本-->
            <testplans dir="F:\demo" includes="*.jmx" />

            <property name="jmeter.save.saveservice.output_format" value="xml"/>
        </jmeter>
    </target>

    <path id="xslt.classpath">
        <fileset dir="${jmeter.home}/lib" includes="xalan*.jar"/>
        <fileset dir="${jmeter.home}/lib" includes="serializer*.jar"/>
    </path>


    <target name="report">
        <tstamp> <format property="report.datestamp" pattern="yyyy/MM/dd HH:mm" /></tstamp>
        <xslt 
              classpathref="xslt.classpath"
              force="true"
              in="${jmeter.result.jtlName}"
              out="${jmeter.result.htmlName}"
              style="${jmeter.home}/extras/jmeter-results-detail-report_21.xsl">
              <param name="showData" expression="${show-data}"/>
              <param name="titleReport" expression="${report.title}"/>
              <param name="dateReport" expression="${report.datestamp}"/> 
       </xslt>
                <!-- 因为上面生成报告的时候，不会将相关的图片也一起拷贝至目标目录，所以，需要手动拷贝 --> 
        <copy todir="${jmeter.result.html.dir}">
            <fileset dir="${jmeter.home}/extras">
                <include name="collapse.png" />
                <include name="expand.png" />
            </fileset>
        </copy>
    </target>

</project>
12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061
```

1. 执行多个目录的jmx脚本

将

```
<jmeter jmeterhome="${jmeter.home}" resultlog="${jmeter.result.jtlName}">
    <!-- 声明要运行的脚本"*.jmx"指包含此目录下的所有jmeter脚本-->
    <testplans dir="E:\jmeter\script_smoke" includes="*.jmx" />

    <property name="jmeter.save.saveservice.output_format" value="xml"/>
</jmeter>
123456
```

改为

```
<jmeter jmeterhome="${jmeter.home}" resultlog="${jmeter.result.jtlName}">
    <!-- 声明要运行的脚本"*.jmx"指包含此目录下的所有jmeter脚本-->
    <testplans dir="E:\jmeter\script_smoke" includes="*.jmx" />
    <testplans dir="E:\jmeter\script_smoke_two" includes="*.jmx" /><!--增加的这一行-->
    <property name="jmeter.save.saveservice.output_format" value="xml"/>
</jmeter>
123456
```

注：也可以通过增加`<jmeter>`标签添加更多的目录，如下图
![在这里插入图片描述](https://img-blog.csdn.net/20181015165713352?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JhaWppbmdjaHVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

1. 生成多个html报告
   这里是生成两个，一个简单的汇总报告，一个详细的报告(用于定位接口报错)
   build_smoke_report.xml文件只利用jmeter-results-detail-report_21.xsl生成了一个html报告，想利用jmeter-results-shanhe-me.xsl生成第二个
   1、 添加属性值

```
<property name="detail" value="_detail" />
<property name="jmeter.result.jtlNamedetail" value="${jmeter.result.jtl.dir}/${ReportName}${time}.jtl" />
<property name="jmeter.result.htmlNamedetail" value="${jmeter.result.html.dir}/${ReportName}${time}${detail}.html" />
123
```

新增一个标签，然后把里面用到的属性值定义好
![在这里插入图片描述](https://img-blog.csdn.net/20181015170501274?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JhaWppbmdjaHVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
PS:
使用jmeter-results-shanhe-me.xsl的一些前置操作
1、下载style文件：jmeter.results.shanhe.me.xsl
2、把下载的文件放到jmeter的extras目录下。
3、修改jmeter.properties文件如下部分，我这里都修改成true，这样执行完脚本后就会保存这些结果到.jtl文件里面，重启jmeter生效：

```
jmeter.save.saveservice.data_type=true
jmeter.save.saveservice.label=true
jmeter.save.saveservice.response_code=true
# response_data is not currently supported for CSV output
jmeter.save.saveservice.response_data=true
# Save ResponseData for failed samples
jmeter.save.saveservice.response_data.on_error=false
jmeter.save.saveservice.response_message=true
jmeter.save.saveservice.successful=true
jmeter.save.saveservice.thread_name=true
jmeter.save.saveservice.time=true
jmeter.save.saveservice.subresults=true
jmeter.save.saveservice.assertions=true
jmeter.save.saveservice.latency=true
jmeter.save.saveservice.connect_time=true
jmeter.save.saveservice.samplerData=true
jmeter.save.saveservice.responseHeaders=true
jmeter.save.saveservice.requestHeaders=true
jmeter.save.saveservice.encoding=false
jmeter.save.saveservice.bytes=true
jmeter.save.saveservice.url=true
jmeter.save.saveservice.filename=true
jmeter.save.saveservice.hostname=true
jmeter.save.saveservice.thread_counts=true
jmeter.save.saveservice.sample_count=true
jmeter.save.saveservice.idle_time=true
1234567891011121314151617181920212223242526
```

**其实XSL语法跟XML挺像的，多看看我们可以进行定制，如定义接口排序，扩展性能指标，那样我们性能测试也能利用这个生成报告，响应时间超过3秒的接口标黄显示，直观显示接口响应的性能状态，主要还是看公司的业务需求。**