jmeter5.0接口测试
一、下载安装
jmeter下载地址：http://jmeter.apache.org/download_jmeter.cgi
版本：jmeter5.0
java下载地址：https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
版本：jdk-8u191-windows-x64
这里就不说明java环境的配置了。

二、将语言改为中文
找到apache-jmeter-5.0\bin 下的jmeter.properties用编辑器打开

![å¨è¿éæå¥å¾çæè¿°](https://img-blog.csdnimg.cn/20181128145404628.png)

搜索language，找到如图所示的地方，将注释去掉，zh_CN加上，重新启动JMeter，显示的语言就变为中文了。

![jmeter.properties](https://img-blog.csdnimg.cn/20181128145438390.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlYWNoc2t5TFk=,size_16,color_FFFFFF,t_70)

三、安装扩展的插件
网址：https://jmeter-plugins.org/install/Install/
点击蓝色框进行下载，文件以压缩包的形式进行下载

![ä¸è½½çå°å](https://img-blog.csdnimg.cn/20181128145117316.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlYWNoc2t5TFk=,size_16,color_FFFFFF,t_70)

根据网址下载文件将压缩包放到对应的目录下即可：apache-jmeter-5.0\lib\ext

![æ¾ç½®çç®å½](https://img-blog.csdnimg.cn/20181128145314646.png)

重新打开JMeter，选项—Plugins Manager

![å¨è¿éæå¥å¾çæè¿°](https://img-blog.csdnimg.cn/20181128152127995.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlYWNoc2t5TFk=,size_16,color_FFFFFF,t_70)

选择想要的插件，点击【Apply Changes and Restart JMeter】安装插件并重启JMeter

![å¨è¿éæå¥å¾çæè¿°](https://img-blog.csdnimg.cn/20181128151953542.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlYWNoc2t5TFk=,size_16,color_FFFFFF,t_70)

四、进行接口测试
1、建一个测试计划
右键【测试计划】—【添加】—【线程】—【线程组】

![å¨è¿éæå¥å¾çæè¿°](https://img-blog.csdnimg.cn/2018112815265637.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlYWNoc2t5TFk=,size_16,color_FFFFFF,t_70)

右键【线程组】—【添加】—【取样器】—【HTTP请求】

![å¨è¿éæå¥å¾çæè¿°](https://img-blog.csdnimg.cn/20181128153029906.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlYWNoc2t5TFk=,size_16,color_FFFFFF,t_70)

2、添加接口
1>GET请求

![å¨è¿éæå¥å¾çæè¿°](https://img-blog.csdnimg.cn/20181128171233843.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlYWNoc2t5TFk=,size_16,color_FFFFFF,t_70)

2>POST请求

![å¨è¿éæå¥å¾çæè¿°](https://img-blog.csdnimg.cn/20181128171322527.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlYWNoc2t5TFk=,size_16,color_FFFFFF,t_70)

3>添加函数—【选项】—【函数助手对话框】

![å¨è¿éæå¥å¾çæè¿°](https://img-blog.csdnimg.cn/20181128171850673.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlYWNoc2t5TFk=,size_16,color_FFFFFF,t_70)

选中一个函数，我们这里已生成当前时间的函数为例，点击生成，蓝色框内生成函数将他复制到参数值里面就可以请求就可以生成时间值了。

![å¨è¿éæå¥å¾çæè¿°](https://img-blog.csdnimg.cn/20181128171907607.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlYWNoc2t5TFk=,size_16,color_FFFFFF,t_70)

4、添加监听器—查看结果
右键【HTTP请求】—【添加】—【监听器】—【选择需要的监听器】，这里我们选择三个。

![å¨è¿éæå¥å¾çæè¿°](https://img-blog.csdnimg.cn/20181128153528218.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlYWNoc2t5TFk=,size_16,color_FFFFFF,t_70)

5、查看结果
添加监听器—【汇总报告】显示的结果

![å¨è¿éæå¥å¾çæè¿°](https://img-blog.csdnimg.cn/20181128172707144.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlYWNoc2t5TFk=,size_16,color_FFFFFF,t_70)

添加监听器—【查看结果树】显示的结果

![å¨è¿éæå¥å¾çæè¿°](https://img-blog.csdnimg.cn/20181128173004984.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RlYWNoc2t5TFk=,size_16,color_FFFFFF,t_70)

---------------------
作者：迷途-风景 
来源：CSDN 
原文：https://blog.csdn.net/teachskyLY/article/details/84584248 
版权声明：本文为博主原创文章，转载请附上博文链接！