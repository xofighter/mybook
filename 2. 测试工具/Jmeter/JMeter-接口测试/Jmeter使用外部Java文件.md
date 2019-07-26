参考文章：https://blog.csdn.net/quiet_girl/article/details/50747666

ps：首先需要将java文件生成jar包，详见https://www.cnblogs.com/mysummary/p/10144204.html

因为公司业务涉及到分表(即生成订单按照分表规则分配在128张表的一个表中)，需要调用分表的方法，

- 将分表的java文件生成jar包 --fenbiao.jar

- 在Jmeter目录下新建一个文件夹dependencies（文件夹名字可以随便取），将上一步中的jar包放到这个文件夹下面                               ![img](https://img2018.cnblogs.com/blog/1565179/201812/1565179-20181219165814981-322120906.png)

- 打开bin目录下的 jmeter.properties 文件，使用ctrl+f查找`plugin_dependency_paths` 这个属性，然后设置为  `plugin_dependency_paths=../dependencies;`保存。

- ![img](blob:https://i.cnblogs.com/9323d68d-a21c-493e-8a8b-ab5527e0d7ba)

- 接下来就是在Jmeter中调用这个方法

  1. 在线程组上右键添加“BeanShell 取样器”                                                           ![img](https://img2018.cnblogs.com/blog/1565179/201812/1565179-20181219170210360-1786811781.png)

  2. 在脚本框中写入代码（大体和Java语言差不多，可直接在Java中写好复制过来）

     import java.util.HashMap;
     import java.util.Map;

     String order_info;
     String order_key="${orderKey}"; //从下单接口取到的订单号 
     order_info = Fenbiao.test_order_info(order_key); //传入订单号，调用分表的方法，将返回值复制给order_info
     log.info("order_info=" + order_info); //打印出order_info的值，即分表后的表名

  3. 运行，可以打开日志查看选项，观察运行日志，调试即可  ，接下来就可以用拿到的返回值，进行一些操作，例如更新数据库的某个字段，或者删除数据等![img](https://img2018.cnblogs.com/blog/1565179/201812/1565179-20181219174535619-168163121.png)



------------------------

首先打开TesterHome首页地址利用json格式化工具分析一下返回的json数据，发现是否为精华帖是由excellent这个参数定义的，excellent等于1则是精华帖，等于0则不是，那么接下来要做的就很简单了。

1）获取首页返回的json

2）解析json并计算出excellent为1的帖子数量，以此得出精华帖总数。

3）解析json获取主题为"MTSC2018 早鸟票开售…"帖子对应的excellent值，以此判断是否为精华帖。

【下载json依赖包】

由于要解析json，那么就需要用到json相关的jar依赖包，下载地址： http://download.csdn.net/download/xiaoxiao_renhe/10246640 先下载下来，后面会详细说明如何使用。

【准备工作就绪，开工】

一、首先运行Jmeter（可以直接运行\apache-jmeter-3.0\bin下的jmeter.bat或在CMD直接输入jmeter然后回车）

二、在"测试计划"下依次添加线程组、Sampler-HTTP请求

三、在HTTP请求页面填写请求信息（本例中访问的地址无需设置请求参数），名称随意填写

四、接下来添加BeanShell PostProcessor，用来获取JSONArray对象并赋值给JMeter内置变量。但在此之前我们先添加依赖路径使得JMeter可以正常引用json依赖包，步骤如下：

1.在"…apache-jmeter-3.0\bin"目录下新建dependencies文件夹，并将刚才下载下来的json依赖包（json-20140107.jar）复制进去

2.打开"…apache-jmeter-3.0\bin"目录下的jmeter.properties配置文件，搜索"plugin_dependency_paths="，删除该行的注释并设置为plugin_dependency_paths=…/dependencies，最后保存；至此配置成功，在JMeter的BeanShell PostProcessor里就可以正常引用jar依赖包了。

3.不知道为什么有时候通过上述方法仍无法正常引用外部jar包，那么为了以防万一，强烈建议在测试计划中通过浏览将需要导入的jar包引入

