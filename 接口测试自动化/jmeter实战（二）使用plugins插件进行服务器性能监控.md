引用：

性能测试时，我们的关注点有两部分

**1、 服务本身：并发 响应时间 QPS**

**2 、服务器的资源使用情况：cpu memory I/O disk等**

JMeter的plugins插件可以实现对"二"的监控，具体操作步骤如下(主要记录我的实践过程)：

说明：我的jmeter是4.0版本

正文：

## **客户端（Jmeter端）** 

**通过PerfMon Metrics Collector监听器进行服务器性能数据显示**

**PerfMon Metrics Collector监听器这个工具本身是不自带的，需要通过安装插件添加：**

   访问网址https://jmeter-plugins.org/downloads/old/，下载JMeterPlugins-Standard和JMeterPlugins-Extras这两个文件，解压复制JmeterPlugins-Extras.jar、JmeterPlugins-Standard.jar到Jmeter文件夹的lib/ext路径下



![img](https:////upload-images.jianshu.io/upload_images/3089283-59debf3e9bbd322c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/616/format/webp)





![img](https:////upload-images.jianshu.io/upload_images/3089283-16960f9c50a37a19.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/373/format/webp)

第二种

重启Jmeter后，在监听器中看到Perfmon Metrics Collector，客户端配置成功



![img](https:////upload-images.jianshu.io/upload_images/3089283-0f85b755edcede0d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/745/format/webp)



## **服务端**

**1、下载    ,把下载的ServerAgent-2.2.\*.zip复制到服务器端，解压即可**

2、windows的服务器，运行文件夹中的startAgent.bat即可，linux的服务器是运行startAgent.sh（需要Java环境支持，没有安装的自行安装）默认使用4444端口，Linux需要先对脚本进行授权



![img](https:////upload-images.jianshu.io/upload_images/3089283-cb0c96a522376edb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/442/format/webp)



运行脚本，出现如下情况即**服务端成功**：



![img](https:////upload-images.jianshu.io/upload_images/3089283-03919f15ec2d5236.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/544/format/webp)



​    PS：若端口被占用  1、杀掉该端口的相关进程，然后再启动



![img](https:////upload-images.jianshu.io/upload_images/3089283-98afd976fa4c2e16.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/779/format/webp)



当前前提是必须知道该进程是否能关闭，最好是换成其他未被占用的端口，如1234则添加如下参数：

./startAgent.sh --udp-port 0 --tcp-port 1234           0代表不开启该端口 

**验证使用情况**

**-------测试服务端使用情况**

**1、 ServerAgent启动的校验**

a 、在笔记本电脑打开telnet监听(控制面板-程序-打开或关闭Windows功能-telnet客户端勾选打开)

b 、cmd进入命令框，输入如下内容：



![img](https:////upload-images.jianshu.io/upload_images/3089283-3436e8851182a4f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/331/format/webp)



c 、观察server端是否有接收到消息，出现如下情况说明ServerAgent打开成功



![img](https:////upload-images.jianshu.io/upload_images/3089283-41f752687f7cd72e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/587/format/webp)



**------测试客户端（Jmeter端）使用情况**

1、随便添加一个HTTP请求的sampler，把线程组设为无限循环

2、添加“jp@gc - PerfMon Metrics Collector”监听器

3、添加要监控的项目，如CPU、内存等，一行选择一种添加监听项

4、最后运行jmx测试计划就行啦



![img](https:////upload-images.jianshu.io/upload_images/3089283-ae480cb365d63b23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



PS：这里要说的是安装客户端**PerfMon Metrics Collector监听器，通过上述方法安装成功后，运行jmeter时，没有反应，jmeter命令窗口显示**



![img](https:////upload-images.jianshu.io/upload_images/3089283-b4efdf1320275964.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/653/format/webp)



网上找的资料说Jmeter 3.2以后的版本暂时没有setFormatter函数功能，目前只能使用Jmeter 3.1？？？表示很困惑，于是把JmeterPlugins-Extras.jar、JmeterPlugins-Standard.jar删掉，换成用安装插件管理器的方式，直接安装监听器，再去运行就OK了

具体如下：访问https://jmeter-plugins.org/install/Install/，下载**plugins-manager.jar** ，复制到Jmeter文件夹的lib/ext路径下，重启Jmeter后，在选项菜单中就可以看到plugins-manager插件管理器了



![img](https:////upload-images.jianshu.io/upload_images/3089283-7e85ed0c4b4ce38e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/328/format/webp)



2、打开，查找PerfMon勾上勾选，再点击右下角的Apply Changes...按钮即可



![img](https:////upload-images.jianshu.io/upload_images/3089283-cf21fad4172e0043.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/651/format/webp)

作者：芯W

链接：https://www.jianshu.com/p/d8bf53dba308

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。