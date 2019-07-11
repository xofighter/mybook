# Jenkins+Jmeter+Ant自动化环境搭建及报告生成

2017年10月16日 19:04:36 [疯狂的大饼](https://me.csdn.net/u011417723) 阅读数：6151



 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/u011417723/article/details/78252963



搭建环境准备文件

JDK：jdk1.8.0_141

Ant：apache-ant-1.10.1

Jmeter: apache-jmeter-3.2

Jenkins: jenkins-2.70

 

一.jdk安装

1.系统变量→新建 JAVA_HOME 变量 。

变量值填写jdk的安装目录（本人是 C:\Progrom Files\Java\jdk1.8.0_141)；

 

2.系统变量→寻找 Path 变量→编辑

在变量值最后输入 %JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;

（注意原来Path的变量值末尾有没有;号，如果没有，先输入；号再输入上面的代码）

 

3.系统变量→新建 CLASSPATH 变量

变量值填写 .;%JAVA_HOME%\lib;%JAVA_HOME%\lib\tools.jar （注意最前面有一点）

系统变量配置完毕

 

4.检验是否配置成功 运行cmd 输入 java-version 

 

二.jmeter安装

1.解压apache-jmeter-3.2.zip到C:\software\apache-jmeter-3.2目录下。

 

2.Jmeter插件的安装。解压JMeterPlugins-Extras-1.3.0.zip、JMeterPlugins-Standard-1.3.0.zip，将对应ext目录下的文件拷贝到C:\software\apache-jmeter-3.2\lib\ext目录下；



3.配置jmeter环境变量：

1）设置环境变量，新建系统变量在变量名中输入：JMETER_HOME，变量值中输入：C:\software\apache-jmeter-3.2，点击确定即可。

2）在环境变量CLASSPATH中添

加%JMETER_HOME%\lib\ext\ApacheJMeter_core.jar;%JMETER_HOME%\lib\jorphan.jar;%JMETER_HOME%\lib\logkit-1.2.jar;

在环境变量Path中添加%JMETER_HOME%\bin
3）检验是否配置成功运行cmd 输入 jmeter -v （jmeter和 -v 之间有空格）

若如图所示显示版本信息 则说明安装和配置成功。

 

三.安装ANT

1.解压apache-ant-1.10.1-bin.zip到C:\software\apache-ant-1.10.1目录下;

2.配置Ant环境变量新建以下环境变量：

1）ANT_HOME：C:\software\apache-ant-1.10.1，此为你的ANT安装路径；

设置完成后，点击"确认"来保存。

2）在环境变量PATH的最后面添加一下内容：

;%ANT_HOME%\bin 注意前面的分号，如果PATH中最后没有分号";"的话要加上，有的话就可去除。

进入cmd命令窗口，在CMD窗口中输入ant -version,敲击回城键，会显示出你安装的ANT版本,如果成功显示，说明你的配置应该是成功了.

3.本地调试使用ant构建运行Jmeter,并且生成jtl、Html报告；

)jmeter默认保存的是.csv格式的文件，所以我们先要设置一下bin/jmeter.properties文件内容，修改jmeter.properties文件 jmeter.save.saveservice.output_format=xml;

将apache-jmeter-3.2\extras\ant-jmeter-1.1.1.jar拷贝到apache-ant-1.10.1\lib目录下；

在\apache-jmeter-3.2\extras目录下打开CMD，运行"ant.bat-file build.xml -Dtest=Test" . 查看apache-jmeter-3.2\extras目录下是否出现Test.jtl、Test.html文件，若有，则构建成功。注意：Test.jmx是默认存在的Sample脚本文件。

若构建失败，请分析cmd打印的日志消息，定位问题；

4.build.xml配置

1）找到extras目录下build.xml文件内容的如下部分：它的作用就是把生成的.jtl数据文件转换为.html格式的报告

具体属性的含义：

in：生成的.jtl文件的路径

out：生成的.html文件的路径

style：从.jtl转换成.html所使用的样式，这些文件也在extras目录下，这个是最重要的部分，如果我们想定制报告可以自行定义这个文件或者从网上找一些合适的文件。

param：参数，这几个参数可以在style中使用，下面会讲到。

path：添加jar包的路径，在下面的target中有引用到（下面第1行）。



 1   <pathid="xslt.classpath">   //jar路径

 2         <fileset dir="${jmeter.home}/lib" includes="xalan-2.7.2.jar"/>

 3         <fileset dir="${jmeter.home}/lib" includes="serializer*.jar"/>

 4   </path>

 5   //把生成的.jtl文件转换成.html格式的报告

 6   <targetname="report">

 7          <xslt

 8               classpathref="xslt.classpath"

 9               force="true"

10              in="${jmeter.result.jtlName}"out="${jmeter.result.htmlName}" style="${jmeter.home}/extras/jmeter-results-detail-report_21.xsl">

11              <param name="showData"expression="${show-data}"/>

12              <param name="titleReport"expression="${ReportName}${time}"/>

13              <param name="dateReport"expression="${ReportName}${time}"/>

14         </xslt>

15  </target>



 

2）通过上面的分析，style 文件是整成报告转换的关键，那我们就打开jmeter-results-detail-report_21.xsl（更美观的报告jmeter.results.shanhe.me.xsl））这个文件看看，找到如下几行，这里也定义了三个参数跟build.xml文件中一样，由此可猜测，我们只要在build.xml文件中定义dateReport这个参数和值，在style中就可以按如下方式使用，经过测试证明猜测是正确的，那么第一个问题就解决了：

1 <xsl:param    name="showData" select="'n'"/>

2 <xsl:param    name="titleReport" select="'LoadTest Results'"/>

3 <xsl:param    name="dateReport" select="'datenot defined'"/>

3）对于第二个问题，其实把第1步中的代码复制到我们自己的build.xml文件中后，可以一并解决这两个问题。解决第二个总是的关键代码是第8行和第9行：

　　classpathref="xslt.classpath"

　　force="true"

​      而第8行又引用了第1~4行

4）到此，两个问题都解决了，最终修改后的build.xml文件如下，标黄的内容是解决时添加的代码 

<?xml version="1.0"encoding="utf-8"?>

<project name="JmeterTest"default="all" basedir=".">

   <tstamp>

​       <format property="time" pattern="yyyyMMddhhmm"/>

   </tstamp>

   <!-- 需要改成自己本地的 Jmeter 目录-->

   <property name="jmeter.home"value="C:\software\apache-jmeter-3.2" />

   <!-- jmeter生成jtl格式的结果报告的路径 -->

   <property name="jmeter.result.jtl.dir"value="C:\software\apache-jmeter-3.2\resultlog\jtl" />

   <!-- jmeter生成html格式的结果报告的路径-->

   <property name="jmeter.result.html.dir"value="C:\software\apache-jmeter-3.2\resultlog\html" />

   <!-- 生成的报告的前缀 -->

   <property name="ReportName" value="TestReport"/>

   <property name="jmeter.result.jtlName"value="${jmeter.result.jtl.dir}/${ReportName}${time}.jtl" />

   <property name="jmeter.result.htmlName"value="${jmeter.result.html.dir}/${ReportName}${time}.html" />

   <!-- 接收测试报告的邮箱 -->

   <property name="mail_to" value="303038910@qq.com"/>

   <!-- 电脑地址 -->

   <property name="ComputerName" value="大饼测试报告"/>

   <target name="all">

​       <antcall target="testwbdata" />

​       <antcall target="report" />

   </target>

   <target name="testwbdata">

​       <taskdef name="jmeter"classname="org.programmerplanet.ant.taskdefs.jmeter.JMeterTask" />

​       <jmeter jmeterhome="${jmeter.home}"resultlog="${jmeter.result.jtlName}">

​           <!-- 声明要运行的脚本。"*.jmx"指包含此目录下的所有jmeter脚本 -->

​           <testplans dir="C:\software\apache-jmeter-3.2\script"includes="wbdata.jmx" />

​                            <propertyname="jmeter.save.saveservice.output_format" value="xml"/>

​       </jmeter>

   </target>

  <!--

   <target name="quanneng">

​       <taskdef name="jmeter"classname="org.programmerplanet.ant.taskdefs.jmeter.JMeterTask" />

​       <jmeter jmeterhome="${jmeter.home}"resultlog="${jmeter.result.jtlName}">-->

​           <!-- 声明要运行的脚本。"*.jmx"指包含此目录下的所有jmeter脚本 -->

​    <!--         <testplansdir="/softDefault/apache-jmeter-3.2/plt_quanneng_20170830_授信申请_lc/script"includes="*.jmx" />

​       </jmeter>

   </target>-->

​        

   

   <path id="xslt.classpath">

​      <fileset dir="${jmeter.home}/lib" includes="xalan-2.7.2.jar"/>

​      <fileset dir="${jmeter.home}/lib"includes="serializer*.jar"/>

   </path>

 

   <target name="report">

​         <!--

​       <xslt

​                classpathref="xslt.classpath"

​           force="true"

​                in="${jmeter.result.jtlName}"out="${jmeter.result.htmlName}"style="${jmeter.home}/extras/jmeter.results.shanhe.me.xsl" >

​                <paramname="showData" expression="${show-data}"/>

​         <param name="titleReport"expression="${ReportName}${time}"/>

​         <param name="dateReport"expression="${ReportName}${time}"/>

​       </xslt>

​                   -->

​                   <xslt

​                classpathref="xslt.classpath"

​           force="true"

​                in="${jmeter.result.jtlName}"out="${jmeter.result.htmlName}" style="${jmeter.home}/extras/jmeter-results-detail-report_21.xsl">

​                <paramname="showData" expression="${show-data}"/>

​         <param name="titleReport"expression="${ReportName}${time}"/>

​         <param name="dateReport"expression="${ReportName}${time}"/>

​        </xslt>

​       <!-- 因为上面生成报告的时候，不会将相关的图片也一起拷贝至目标目录，所以，需要手动拷贝 -->

​       <copy todir="${jmeter.result.html.dir}">

​           <fileset dir="${jmeter.home}/extras">

​                <includename="collapse.png" />

​                <include name="expand.png"/>

​           </fileset>

​       </copy>

   </target>

   <!-- 发送邮件 -->

</project>

 

四.jenkins部署

1.安装<https://jenkins.io/download/>  下载war包 

1）将war包放到tomcat的webapps

2）运行tomcat/bin下的startup.sh会在webapps下生成jenkins文件

[http://localhost:8080/jenkins/](http://localhost:8088/hudson/)这样的地址即可

2.配置jenkins

1）访问jenkins->系统管理->管理插件安装[AntPlugin](https://wiki.jenkins-ci.org/display/JENKINS/Ant+Plugin)和[PerformancePlugin](http://wiki.jenkins-ci.org/display/JENKINS/Performance+Plugin)

2）访问jenkins->系统管理-> [GlobalTool Configuration](http://localhost:8081/jenkins/configureTools)  配置Ant路径

![img](https://img-blog.csdn.net/20171016202016226?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTQxNzcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



3）选择项目->配置  添加标签和build.xml路径 

其中targets标签对应的build.xml如下图



![img](https://img-blog.csdn.net/20171016202042742?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTQxNzcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

 ![img](https://img-blog.csdn.net/20171016202110718?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTQxNzcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

五.报告输出

1.上述集成jenkins输出的报告，主要通过build.xml调用jmeter.results.shanhe.me.xsl文件格式输出

2.Jmeter3.0后自带图形格式报告，只能通过命令行输出

jmeter -n -tC:\software\apache-jmeter-3.2\script\wbdata.jmx -l result.jtl -e -oC:\software\apache-jmeter-3.2\resultlog\html\testHTML

1)     C:\software\apache-jmeter-3.2\script\wbdata.jmx脚本路径

2)     result.jtl日志文件存放在C:\software\apache-jmeter-3.2\bin（每次执行名字不能重复）

3)     C:\software\apache-jmeter-3.2\resultlog\html\testHTML报告输出路径

 

报告输出样图

jmeter-results-detail-report_21.xsl

![img](https://img-blog.csdn.net/20171016202137429?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTQxNzcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



jmeter.results.shanhe.me.xsl

![img](https://img-blog.csdn.net/20171016202157576?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTQxNzcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

命令行输出默认报告

![img](https://img-blog.csdn.net/20171016202238388?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTQxNzcyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)