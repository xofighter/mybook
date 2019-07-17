 Jmeter可以做接口测试，也能做压力测试，而且是开源软件；Ant是基于Java的构建工具，可以跨平台，Jenkins是持续集成工具。将这三者结合起来可以搭建一套Web HTTP接口测试的持续构建环境，实现接口自动化测试，pc系统是Windows。

1、安装**jmeter**

2、安装JDK

​        1、2步骤之前已经听过怎么安装配置了，故省略。。。

3、安装ANT

​      3.1 安装包下载

下载地址 http://ant.apache.org/bindownload.cgi，下载后解压到指定位置即可，我是放在D:\Program Files (x86)\apache-ant-1.10.3，与jmeter放在同一位置

3.2 配置环境变量

ANT_HOME 为   D:\Program Files (x86)\apache-ant-1.10.3

CLASSPATH为   %ANT_HOME%\lib;

PATH为  %ANT_HOME%\bin;

​       3.3 安装验证

验证安装结果,命令行输入ant -v，出现版本信息则安装成功



![img](https:////upload-images.jianshu.io/upload_images/3089283-2f0b5d03b9e59445.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/520/format/webp)



4、Ant配置Jmeter：就是用ant构建命令来调动执行jmeter接口测试，并生成测试报告

4.1 配置库文件

将jmeter extras目录下的ant-jmeter-1.1.1.jar文件拷贝到ant安装目录下的lib文件夹中



![img](https:////upload-images.jianshu.io/upload_images/3089283-7561503e1f2c78d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/598/format/webp)



4.2  配置ant的编译文件 build.xml

新建的txt文件，并将此文件改名为build.xml，将如下文本敲进在文档里，或者在网上找现成的再修改

**注意：jmeter的extras文件下也有个build.xml文档，不要用这个文档来改，是两回事，会出问题！！！。。。。这里的build文档是自己新建。。新建。。新建。。**    



![img](https:////upload-images.jianshu.io/upload_images/3089283-f3dcb96622cb5f6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/612/format/webp)



PS：已经弄好的build.xml文件和htlm样式模板jmeter-results-shanhe-me.xsl

链接：https://pan.baidu.com/s/1iI3shn_yd3oF-rNvnohmrQ 密码：7fjt

如下按实际情况修改build.xml文件



![img](https:////upload-images.jianshu.io/upload_images/3089283-28ee93ff9ec60cd0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)





![img](https:////upload-images.jianshu.io/upload_images/3089283-a23f87163cabdc48.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/895/format/webp)



4.3  配置jmeter.properties文档

找到jmeter.properties文档，在jmeter/bin目录下，打开该文档并编辑，修改jmeter报告输出格式为xml：改jmeter.save.saveservice.output_format=csv 为jmeter.save.saveservice.output_format=xml，并去掉前面的注释符号#



![img](https:////upload-images.jianshu.io/upload_images/3089283-19c2791b06584b2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/641/format/webp)



4.4 验证配置，执行构建测试

将之前jmeter的脚本保存， 并将build.xml配置文件放在与测试脚本相同的目录下



![img](https:////upload-images.jianshu.io/upload_images/3089283-42558f819918d901.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/676/format/webp)



4.5  执行测试

在build.xml所在目录打开命令窗口（鼠标在空白处按下shift键后在右键），或者命令行cd到build.xml文件所在目录，输入ant run回车，执行测试



![img](https:////upload-images.jianshu.io/upload_images/3089283-de0712808efb2e4d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/846/format/webp)





![img](https:////upload-images.jianshu.io/upload_images/3089283-68c5a27f07b455d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/694/format/webp)



看到build successful 则说明构建测试成功，离成功越来越近了。。。

4.6   查看测试结果报告

在报告输出存放路径下查看是否有jtl和html结果报告，存放路径在build文档中也有



![img](https:////upload-images.jianshu.io/upload_images/3089283-3b5ce975ab1fec19.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/708/format/webp)





![img](https:////upload-images.jianshu.io/upload_images/3089283-6b82554d07942c31.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/737/format/webp)



打开html文档，测试结果展现了执行的用例名称、成功率、用例执行时间等结果参数



![img](https:////upload-images.jianshu.io/upload_images/3089283-f727acac84b189b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



这样的结果不太直观，因为用jmeter自带的测试报告得到的测试报告信息并不是很全，下面讲一下怎么优化测试报告

4.7、 优化测试报告

①下载优化模板 jmeter-results-shanhe-me.xsl，拷贝到jmeter的extras目录中



![img](https:////upload-images.jianshu.io/upload_images/3089283-c75b06b174ecfa77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/615/format/webp)



②设置测试输出报告要输出的内容：同样在jmeter.properties中，设置需要输出的内容为true，并去掉前面的注释符号#，这里全部设置成true→保存

> jmeter.save.saveservice.data_type=true
>
> jmeter.save.saveservice.label=true
>
> jmeter.save.saveservice.response_code=true
>
> \# response_data is not currently supported for CSV output
>
> jmeter.save.saveservice.response_data=true
>
> \# Save ResponseData for failed samples
>
> jmeter.save.saveservice.response_data.on_error=false
>
> jmeter.save.saveservice.response_message=true
>
> jmeter.save.saveservice.successful=true
>
> jmeter.save.saveservice.thread_name=true
>
> jmeter.save.saveservice.time=true
>
> jmeter.save.saveservice.subresults=true
>
> jmeter.save.saveservice.assertions=true
>
> jmeter.save.saveservice.latency=true
>
> jmeter.save.saveservice.connect_time=true
>
> jmeter.save.saveservice.samplerData=true
>
> jmeter.save.saveservice.responseHeaders=true
>
> jmeter.save.saveservice.requestHeaders=true
>
> jmeter.save.saveservice.encoding=true
>
> jmeter.save.saveservice.bytes=true
>
> jmeter.save.saveservice.url=true
>
> jmeter.save.saveservice.filename=true
>
> jmeter.save.saveservice.hostname=true
>
> jmeter.save.saveservice.thread_counts=true
>
> jmeter.save.saveservice.sample_count=true
>
> jmeter.save.saveservice.idle_time=true



![img](https:////upload-images.jianshu.io/upload_images/3089283-0c40cf4bdf778af4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



③设置build文件的报告模板为优化后的模板jmeter-results-shanhe-me.xsl



![img](https:////upload-images.jianshu.io/upload_images/3089283-b49d6f805c380f85.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/787/format/webp)



④再次用ant构建测试，查看优化后的测试报告



![img](https:////upload-images.jianshu.io/upload_images/3089283-4dcb4594c3f44d39.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



5、配置Jenkins，构建持续化集成

5.1  安装Jenkins：

①前提条件：已经成功安装了JDK，因为jenkins是一款基于Java的持续集成工具。

②准备工具：下载安装包，https://jenkins.io/index.html，下载jenkins的war包。

5.2  配置环境变量

JENKINS_HOME 为   D:\Program Files (x86)\jenkins

5.3  启动jenkins

​      在jenkins.war包放置目录下，shift+右键打开cmd，输入命令：  java -jar jenkins.war  ，就可以启动jenkins了。再在浏览器中输入：http://127.0.0.1:8080/，打开jenkins，配置用户名、密码及插件,这里需要配置invoke ant插件、HTML测试报告展示的插件

5.4、新建并配置一个JOB



![img](https:////upload-images.jianshu.io/upload_images/3089283-47667382689abe79.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/740/format/webp)



设置项目名称：



![img](https:////upload-images.jianshu.io/upload_images/3089283-8c58bdd8573b5651.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



配置构建：



![img](https:////upload-images.jianshu.io/upload_images/3089283-8a9ec6d5454544d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/928/format/webp)



配置构建：invoke ant



![img](https:////upload-images.jianshu.io/upload_images/3089283-912ab991c5e6b7e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/329/format/webp)





![img](https:////upload-images.jianshu.io/upload_images/3089283-ddce6267ce26be3c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/930/format/webp)



配置测试报告插件，用于打开测试结果报告



![img](https:////upload-images.jianshu.io/upload_images/3089283-f21db10b56e553d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/935/format/webp)



配置完成



![img](https:////upload-images.jianshu.io/upload_images/3089283-e1ecb20099073525.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/835/format/webp)



PS：[Jenkins构建生成的报告无法展示 HTML 样式的问题](https://zhuanlan.zhihu.com/p/28080975)

解决方案：

**临时解决方案：Jenkins系统管理-脚本命令行，输入如下命令并进行执行：System.setProperty("hudson.model.DirectoryBrowserSupport.CSP", "")**    



![img](https:////upload-images.jianshu.io/upload_images/3089283-340e0392bc1a93d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



再次进行构建，新生成的HTML就可以正常展示样式了。需要说明的是，该操作对之前构建生成的HTML报告无效 PS：该配置只是临时生效，当重启Jenkins后，Content Security Policy又会恢复为默认值，从而HTML样式又没法展示了

**永久解决方案**

**当前，Jenkins官方还没有相应的解决方法，我们只能在每次启动或重启Jenkins时，重新修改该安全策略。**

如果手工地来重复这项工作，也是可行，但并不是一个好的解决方案。

回到刚才的脚本命令行，会发现我们执行的命令其实就是一段Groovy代码；那么，如果我们可以实现在Jenkins每次启动时自动地执行该Groovy代码，那么也就同样能解决我们的问题了。

好在Jenkins已经有相应的插件：

[Startup Trigger](https://link.zhihu.com/?target=https%3A//wiki.jenkins.io/display/JENKINS/Startup%2BTrigger): 可实现在Jenkins节点(master/slave)启动时触发构建；

[Groovy plugin](https://link.zhihu.com/?target=https%3A//wiki.jenkins.io/display/JENKINS/Groovy%2Bplugin): 可实现直接执行Groovy代码。

搜索安装startup-trigger-plugin和Groovy插件后，我们就可以进行配置了。

配置方式如下：

新建一个job，该job专门用于Jenkins启动时执行的配置命令；

在Build Triggers模块下，勾选Build when job nodes start；

在Build模块下，Add build step->Execute system Groovy script，在Groovy Script中输入配置命令，System.setProperty("hudson.model.DirectoryBrowserSupport.CSP", "")。



![img](https:////upload-images.jianshu.io/upload_images/3089283-1d2ff4d66ba7636f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



需要注意的是，添加构建步骤的时候，应该选择Execute system Groovy script，而不是Execute Groovy script。关于这两者之间的差异，简单地说，Groovy Script相当于是运行在master/slave系统JVM环境中，而system groovy script，则是运行在Jenkins master的JVM环境中，与前面提到的Jenkins Script Console功能相同。如需了解更多信息，可查看[Groovy plugin的详细说明](https://zhuanlan.zhihu.com/p/http%3C/code%3Es://wiki.jenkins.io/display/JENKINS/Groovy+plugin)。

至此，我们就彻底解决HTML样式展示异常的问题了。

但还有一点需要格外注意，在本文的演示中，我们修改CSP（Content Security Policy）配置时关闭了的所有安全保护策略，即将hudson.model.DirectoryBrowserSupport.CSP设置为空，其实这是存在很大的安全隐患的。

正确的做法，我们应该是结合项目的实际情况，选择对应的安全策略。例如，如果我们需要开启脚本文件加载，但是只限于Jenkins服务器上托管的CSS文件，那么就可以采用如下配置。

System.setProperty("hudson.model.DirectoryBrowserSupport.CSP", "sandbox; style-src 'self';")

除此之外，CSP可以实现非常精细的权限配置，详细配置可参考[Content Security Policy Reference](https://link.zhihu.com/?target=https%3A//content-security-policy.com/)。



作者：芯W

链接：https://www.jianshu.com/p/1132e36e97e7

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。