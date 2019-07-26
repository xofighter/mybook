# JMeter性能测试，完整入门篇

2018年06月20日 08:42:04 [sszmvb1234](https://me.csdn.net/u012111923) 阅读数 126824



#  

原文转自：https://blog.csdn.net/lovesoo/article/details/78579547

Apache JMeter是一款纯java编写负载功能测试和性能测试开源工具软件。相比Loadrunner而言，JMeter小巧轻便且免费，逐渐成为了主流的性能测试工具，是每个测试人员都必须要掌握的工具之一。

本文为JMeter性能测试完整入门篇，从Jmeter下载安装到编写一个完整性能测试脚本、最终执行性能测试并分析性能测试结果。

运行环境为Windows 10系统，JDK版本为1.8，JMeter版本为3.3。

# 2. Jmeter安装

## 2.1 JDK安装

由于Jmeter是基于java开发，首先需要下载安装JDK （目前JMeter只支持到Java 8，尚不支持 Java 9） 
\1. 官网下载地址：http://www.oracle.com/technetwork/java/javase/downloads/index.html 
\2. 选择Java SE 8u151/ 8u152，点击JDK下载 
![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027110525633-2122104455.png)
\3. 安装下载的JDK 
\4. 配置系统环境变量

## 2.2 JMeter安装

1. 官网下载地址：http://jmeter.apache.org/download_jmeter.cgi
2. 下载最新JMeter 3.3版本：apache-jmeter-3.3.zip 
   ![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027110534617-1488152581.png)
3. 下载完成后解压zip包
4. 启动JMeter 
   双击JMeter解压路径（apache-jmeter-3.3\bin）bin下面的jmeter.bat即可 
   ![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027110542851-1179701029.png)

# 3. 测试实例

我们选取最常见的百度搜索接口：

## 3.1 接口地址

http://www.baidu.com/s?ie=utf-8&wd=jmeter性能测试

## 3.2 请求参数

ie：编码方式，默认为utf-8 
wd: 搜索词

## 3.3 返回结果

搜索结果，我们可以通过校验结果中是否含有搜索词wd来判断本次请求成功或失败。

# 4. JMeter脚本编写

## 4.1 添加线程组

右键点击“测试计划” -> “添加” -> “Threads(Users)” -> “线程组” 
![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027140206133-635713654.png) 
这里可以配置线程组名称，线程数，准备时长（Ramp-Up Period(in seconds)）循环次数，调度器等参数： 
![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027140213008-328934523.png) 
线程组参数详解： 
\1. 线程数：虚拟用户数。一个虚拟用户占用一个进程或线程。设置多少虚拟用户数在这里也就是设置多少个线程数。 
\2. Ramp-Up Period(in seconds)准备时长：设置的虚拟用户数需要多长时间全部启动。如果线程数为10，准备时长为2，那么需要2秒钟启动10个线程，也就是每秒钟启动5个线程。 
\3. 循环次数：每个线程发送请求的次数。如果线程数为10，循环次数为100，那么每个线程发送100次请求。总请求数为10*100=1000 。如果勾选了“永远”，那么所有线程会一直发送请求，一到选择停止运行脚本。 
\4. Delay Thread creation until needed：直到需要时延迟线程的创建。 
\5. 调度器：设置线程组启动的开始时间和结束时间(配置调度器时，需要勾选循环次数为永远) 
持续时间（秒）：测试持续时间，会覆盖结束时间 
启动延迟（秒）：测试延迟启动时间，会覆盖启动时间 
启动时间：测试启动时间，启动延迟会覆盖它。当启动时间已过，手动只需测试时当前时间也会覆盖它。 
结束时间：测试结束时间，持续时间会覆盖它。

因为接口调试需要，我们暂时均使用默认设置，待后面真正执行性能测试时再回来配置。

## 4.2 添加HTTP请求

右键点击“线程组” -> “添加” -> “Sampler” -> “HTTP请求” 
![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027140259273-1510991394.png) 
对于我们的接口http://www.baidu.com/s?ie=utf-8&wd=jmeter性能测试，可以参考下图填写： 
![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027140307492-347021148.png) 
Http请求主要参数详解：

1. Web服务器 
   协议：向目标服务器发送HTTP请求协议，可以是HTTP或HTTPS，默认为HTTP 
   服务器名称或IP ：HTTP请求发送的目标服务器名称或IP 
   端口号：目标服务器的端口号，默认值为80 
   2.Http请求 
   方法：发送HTTP请求的方法，可用方法包括GET、POST、HEAD、PUT、OPTIONS、TRACE、DELETE等。 
   路径：目标URL路径（URL中去掉服务器地址、端口及参数后剩余部分） 
   Content encoding ：编码方式，默认为ISO-8859-1编码，这里配置为utf-8
2. 同请求一起发送参数 
   在请求中发送的URL参数，用户可以将URL中所有参数设置在本表中，表中每行为一个参数（对应URL中的 name=value），注意参数传入中文时需要勾选“编码”

## 4.3 添加察看结果树

右键点击“线程组” -> “添加” -> “监听器” -> “察看结果树” 
![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027140319258-966761370.png) 
这时，我们运行Http请求，修改响应数据格式为“HTML Source Formatted”，可以看到本次搜索返回结果页面标题为”jmeter性能测试_百度搜索“。 
![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027140532539-746436850.png)

## 4.4 添加用户自定义变量

我们可以添加用户自定义变量用以Http请求参数化，右键点击“线程组” -> “添加” -> “配置元件” -> “用户定义的变量”： 
![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027140605430-1268843149.png) 
新增一个参数wd，存放搜索词： 
![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027140612445-284376525.png) 
并在Http请求中使用该参数，格式为：${wd} 
![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027142440383-411258040.png)

## 4.5 添加断言

右键点击“HTTP请求” -> “添加”-> “断言” -> “响应断言” 
![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027140641898-37297868.png) 
我们校验返回的文本中是否包含搜索词，添加参数${wd}到要测试的模式中： 
![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027140652367-2031213364.png)

## 4.6 添加断言结果

右键点击“HTTP请求” -> “添加”-> “监听器” -> “断言结果” 
![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027140746320-1682152745.png)

这时，我们再运行一次就可以看到断言结果成功或失败了 
![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027140801117-1841738517.png)

## 4.7 添加聚合报告

右键点击“线程组” -> “添加” -> “监听器” -> “聚合报告”，用以存放性能测试报告 
![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027140821930-1938293505.png) 
这样，我们就完成了一个完整Http接口的JMeter性能测试脚本编写。

# 5. 执行性能测试

## 5.1 配置线程组

点击线程组，配置本次性能测试相关参数：线程数，循环次数，持续时间等，这里我们配置并发用户数为10，持续时间为60s 
![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027140835211-2041991555.png)

## 5.2 执行测试

点击绿色小箭头按钮即可启动测试，测试之前需要点击小扫把按钮清除之前的调试结果。 
![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027140859086-183557970.png)

# 6. 分析测试报告

待性能测试执行完成后，打开聚合报告可以看到： 
![这里写图片描述](http://images2017.cnblogs.com/blog/445074/201710/445074-20171027140910148-1035669123.png)

聚合报告参数详解： 
\1. Label：每个 JMeter 的 element（例如 HTTP Request）都有一个 Name 属性，这里显示的就是 Name 属性的值 
\2. #Samples：请求数——表示这次测试中一共发出了多少个请求，如果模拟10个用户，每个用户迭代10次，那么这里显示100 
\3. Average：平均响应时间——默认情况下是单个 Request 的平均响应时间，当使用了 Transaction Controller 时，以Transaction 为单位显示平均响应时间 
\4. Median：中位数，也就是 50％ 用户的响应时间 
\5. 90% Line：90％ 用户的响应时间 
\6. Min：最小响应时间 
\7. Max：最大响应时间 
\8. Error%：错误率——错误请求数/请求总数 
\9. Throughput：吞吐量——默认情况下表示每秒完成的请求数（Request per Second），当使用了 Transaction Controller 时，也可以表示类似 LoadRunner 的 Transaction per Second 数 
\10. KB/Sec：每秒从服务器端接收到的数据量，相当于LoadRunner中的Throughput/Sec

一般而言，性能测试中我们需要重点关注的数据有： #Samples 请求数，Average 平均响应时间，Min 最小响应时间，Max 最大响应时间，Error% 错误率及Throughput 吞吐量。

# 7. 源码下载

至此，我们使用JMeter完成了一个完整的Http接口性能测试流程，从脚本编写，执行到最终结果分析。

完整的脚本源码下载链接: https://pan.baidu.com/s/1eS90cVo 密码: st15



------------------------

# [Jmeter+jenkins接口性能测试平台实践整理（一）](https://www.cnblogs.com/ceshi2016/p/6040475.html)



最近两周在研究jmeter＋Jenkin的性能测试平台测试dubbo接口，分别尝试使用maven，ant和Shell进行构建，jmeter相关设置略。

一、Jmeter+jenkins＋Shell＋tomcat

安装Jenkins,JDK,tomcat,并设置环境变量，安装在Jenkins中performance-plugin插件。

1.准备测试脚本和测试数据，拷贝至Jenkins的workspace的对应的项目工作目录下：

![img](https://images2015.cnblogs.com/blog/733625/201611/733625-20161107195816436-49456945.png)

2.Jenkins设置

1)添加构建参数

![img](https://images2015.cnblogs.com/blog/733625/201611/733625-20161107200131795-302272225.png)

2）构建脚本

![img](https://images2015.cnblogs.com/blog/733625/201611/733625-20161107200328420-944830238.png)

```shell
##!/bin/bash
REPORT_TIME=`date '+%Y%m%d%H%M%S'`
jmeter_basedir=/home/apache-jmeter-3.0
report_icon=$jmeter_basedir/extras/collapse.png
basedir=/var/lib/jenkins/workspace/jmeter_Param
report_jtl=$basedir/report/jtl
report_html=$basedir/report/html

echo "starting..."
#脚本参与引入通过构建参数引入
echo $threadCount
echo $rampUp
echo $durationTime
echo $sleepTime1
echo $sleepTime2
#删除多余的jtl
cd $report_jtl
rm -f *.jtl

cd $basedir
for v1 in $(ls *.jmx)
do
# REPORT_TIME=`date '+%Y%m%d%H%M%S'`
# TestReport=`ls $v1|sed 's/.jmx//g'`
# echo $TestReport
cd $jmeter_basedir/bin
sh jmeter.sh -JthreadCount=$threadCount -JRampUp=$rampUp -JDurationTime=$durationTime -n -t $basedir/$v1 -l $report_jtl/TestReport_$REPORT_TIME.jtl &
# >~/logs.log &
# xsltproc $jmeter_basedir/extras/jmeter-results-detail-report_21.xsl $report_jtl/TestReport_$REPORT_TIME.jtl > $report_html/TestReport_$REPORT_TIME.html
# sed -i "s/date not defined/$REPORT_TIME/g" grep “date not defined" -l $report_html/TestReport_$REPORT_TIME.html
# if [! -f "$report_icon"];then
# cp $jmeter_basedir/extras/collapse.png $jmeter_basedir/extras/expand.png $report_html
# fi
# if [! -d "$report_jtl"];then
# cd $WORKSPACE/report && mkdir jtl
# fi
sleep $sleepTime1
#for id in $(ps -ef|grep jmeter|grep -v grep|awk '{print $2}')
#do
#echo $id
#kill -9 $id
#done
#sleep 5
done &
sleep $sleepTime2
xsltproc $jmeter_basedir/extras/jmeter-results-detail-report_21.xsl $report_jtl/TestReport_$REPORT_TIME.jtl > $report_html/TestReport_$REPORT_TIME.html &
sleep 20
cp $report_jtl/TestReport_$REPORT_TIME.jtl $basedir/report/jtl2
cp $report_html/TestReport_$REPORT_TIME.html $basedir/report/html2
#for id1 in $(ps -ef|grep JMeter|awk '{print $2}')
#do
#echo $id1
#kill -9 $id1
#done
echo "ending..."
```



3）构建后操作

![img](https://images2015.cnblogs.com/blog/733625/201611/733625-20161107200506467-2001754390.png)

4）保存并构建成功后，在workspace中查看jtl和html结果。

![img](https://images2015.cnblogs.com/blog/733625/201611/733625-20161107201329014-1758588821.png)

![img](https://images2015.cnblogs.com/blog/733625/201611/733625-20161107201456717-1397742469.png)

 

![img](https://images2015.cnblogs.com/blog/733625/201611/733625-20161107201650592-2118650245.png)

![img](https://images2015.cnblogs.com/blog/733625/201611/733625-20161107201739874-1386581924.png)

关于dubbo接口性能脚本编写，jar包导出，html模版修改，请参考前期文章。

 