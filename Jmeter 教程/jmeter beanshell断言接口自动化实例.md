# jmeter beanshell断言接口自动化实例

2018年07月30日 10:38:03 [卖包子的小行家](https://me.csdn.net/gynumber1) 阅读数 3467



**一、JMeter介绍**

​                                                                                                                                               

![img](https://img-blog.csdn.net/20180302113310757)

​        Apache JMeter是一款优秀的开源性能测试工具，在国外无论是在性能测试还是接口测试领域都有着非常高的使用率，但由于本身没有完善的中文文档以及典型开源工具特点（界面不美观）所以在国内应用并不广泛。先说说为什么要选择JMeter作为接口测试工具，选择它我主要基于以下几个原因：

**一、成本低并且对编码要求相对不高**。相较针对公司产品特性自主开发一套接口测试自动化框架，用JMeter实现接口测试无需具备非常专业的编码能力（对于像我这样的码渣而言是极好的![羡慕](http://static.blog.csdn.net/xheditor/xheditor_emot/default/envy.gif)），并且成本也要低的多，**很适合现如今国内各大互联网公司流行的敏捷开发流程**。

**二、JMeter的扩展性非常好**。虽然原生支持的协议有限，但我们可以根据需要制定自己的Sampler并向服务器发送请求。

**三、JMeter是开源的**。开源不仅意味着免费，更重要的是你可以通过开放的源码了解工具的所有行为，而不会像商业工具那样仅仅只能依靠有限的帮助手册以及网上教程。

 

**二、安装与环境配置**

​                                                                                                                                               

1.首先去Jmeter官网（http://jmeter.apache.org/download_jmeter.cgi）下载安装包，最新版为4.0，本篇使用的是3.0

2.将apache-jmeter-3.0.zip解压至任意位置。

3.添加环境变量：

| **变量名**  | **变量值**                                                   |
| ----------- | ------------------------------------------------------------ |
| JMETER_HOME | 填入之前安装包解压的路径。例如E:\apache-jmeter-3.0           |
| CLASSPATH   | 在尾部添加  ;%JMETER_HOME%\lib\jorphan.jar;%JMETER_HOME%\lib/logkit-2.0.jar; |
| Path        | 在尾部添加  ;%JMETER_HOME%\bin;                              |

4.验证配置是否成功：打开CMD命令行窗口，输入jmeter -v，回车，出现下图则代表配置成功。

![img](https://img-blog.csdn.net/20180302121034159)

 

**三、实例练习**

​                                                                                                                                               

**【测试对象】**TesterHome首页（[https://testerhome.com/api/v3/topics.json](http://jmeter.apache.org/download_jmeter.cgi)）

**【测试点】**

1）断言首页精华帖数量是否大于4。

2）断言主题为"MTSC2018 早鸟票开售..."的帖子是否为精华帖。

**【思路】**

首先打开TesterHome首页地址利用json格式化工具分析一下返回的json数据，发现是否为精华帖是由excellent这个参数定义的，excellent等于1则是精华帖，等于0则不是，那么接下来要做的就很简单了。

1）获取首页返回的json

2）解析json并计算出excellent为1的帖子数量，以此得出精华帖总数。

3）解析json获取主题为"MTSC2018 早鸟票开售..."帖子对应的excellent值，以此判断是否为精华帖。

![img](https://img-blog.csdn.net/20180302132211751)

 

**【下载json依赖包】**

由于要解析json，那么就需要用到json相关的jar依赖包，下载地址： http://download.csdn.net/download/xiaoxiao_renhe/10246640  先下载下来，后面会详细说明如何使用。

**【准备工作就绪，开工】**

**一、首先运行Jmeter（可以直接运行\apache-jmeter-3.0\bin下的jmeter.bat或在CMD直接输入jmeter然后回车）**![img](https://img-blog.csdn.net/20180302131555886)

**二、在"测试计划"下依次添加线程组、Sampler-HTTP请求**

![img](https://img-blog.csdn.net/20180302132710582)

**三、在HTTP请求页面填写请求信息（本例中访问的地址无需设置请求参数），名称随意填写**

![img](https://img-blog.csdn.net/20180302133440532)

**四、接下来添加BeanShell PostProcessor，用来获取JSONArray对象并赋值给JMeter内置变量。但在此之前我们先添加依赖路径使得JMeter可以正常引用json依赖包，步骤如下：**

1.在"...apache-jmeter-3.0\bin"目录下新建dependencies文件夹，并将刚才下载下来的json依赖包（json-20140107.jar）复制进去

![img](https://img-blog.csdn.net/20180302134245217)

2.打开"...apache-jmeter-3.0\bin"目录下的jmeter.properties配置文件，搜索"plugin_dependency_paths="，删除该行的注释并设置为plugin_dependency_paths=../dependencies，最后保存；至此配置成功，在JMeter的BeanShell PostProcessor里就可以正常引用jar依赖包了。

![img](https://img-blog.csdn.net/20180302134543489)

3.不知道为什么有时候通过上述方法仍无法正常引用外部jar包，那么为了以防万一，强烈建议在测试计划中通过浏览将需要导入的jar包引入

![img](https://img-blog.csdn.net/20180302142506313)

![img](https://img-blog.csdn.net/20180302142455484)

**五、****右键点击"HTTP请求"->添加->后置处理器->****BeanShell PostProcessor，并在脚本添加代码用以获取"topics"下JSONArray对象（请无视这粗鄙的代码结构。。。![害羞](http://static.blog.csdn.net/xheditor/xheditor_emot/default/shy.gif)）**

```
 
```

1. `import org.json.*;`
2.  
3. `//设置全局变量`
4. `static public int excellent_count = 0;`
5.  
6. `//获取最后一次请求返回数据 （JMeter内置方法）`
7. `String jsonResult = prev.getResponseDataAsString();`
8.  
9. `//分析:TestHome首页json数据结构为{"topics":[{...},{...}...]}，外层JSONObject内层JSONArrary`
10. `//获取"topics"下JSONArray对象`
11. `JSONObject jsonObject = new JSONObject(jsonResult);`
12. `JSONArray jsonArray = jsonObject.getJSONArray("topics");`
13. `String string_jsonArray = jsonArray.toString();`
14.  
15. `vars.put("string_jsonArray",string_jsonArray);`

![img](https://img-blog.csdn.net/20180302135633432)

**六、添加断言Case01（对应第一条测试点），****右键点击"HTTP请求"->添加->断言->****BeanShell断言，然后填入以下脚本：从****BeanShell PostProcessorh提供的内置变量中获取JSONArray对象，然后通过条件"excellent=1"得出精华帖总数，从而判断精华帖是否>4**

```
 
```

1. `import org.json.*;`
2.  
3. `//设置全局变量用于记录精华帖数量`
4. `static public int excellent_count = 0;`
5.  
6. `//从BeanShell PostProcessor获取jsonArray`
7. `var b = vars.get("string_jsonArray");`
8. `JSONArray jsonArray = new JSONArray(b);`
9.  
10. `//计算精华帖数量`
11. `for (int i = 0;i < jsonArray.length();i++) {`
12. `excellent_result = jsonArray.getJSONObject(i).getInt("excellent");`
13. `if (excellent_result == 1){`
14. `//循环遍历全部文章的excellent值，若为1则精华帖数量excellent_count+1`
15. `excellent_count = excellent_count + 1;`
16. `}`
17. `}`
18.  
19. `if (excellent_count >4){`
20. `//Failure为false代表断言成功，且结果树不显示该断言`
21. `Failure = false;`
22. `//日志输出结果`
23. `log.error("【Case01】执行成功，精华帖数量为" + excellent_count + "个。");`
24. `}else{`
25. `//Failure为true则代表断言失败`
26. `Failure = true;`
27. `//设置FailureMessage为断言失败信息`
28. `FailureMessage = "【Case01】执行失败，精华帖数量为" + excellent_count + "个。" ;`
29. `log.error("【Case01】执行失败，精华帖数量为" + excellent_count + "个。" );`
30. `}`

![img](https://img-blog.csdn.net/20180302140312770)

**七、添加断言****Case02，并填入脚本：获取主题为"MTSC2018 早鸟票开售..."帖子对应的excellent参数值，以此来判断是否为精华帖**

```
 
```

1. `import org.json.*;`
2.  
3. `//设置全局变量excellent_result用于储存excellent值`
4. `static public int excellent_result;`
5.  
6. `//从BeanShell PostProcessor获取jsonArray`
7. `var b = vars.get("string_jsonArray");`
8. `JSONArray jsonArray = new JSONArray(b);`
9.  
10. `for (int i = 0;i < jsonArray.length();i++) {`
11. `//获取titile值`
12. `String title = jsonArray.getJSONObject(i).getString("title");`
13. `//当title="2018年TesterHome 全员感恩红包发放"时获取对应excellent值`
14. `if ( "MTSC2018 早鸟票开售啦_中国移动互联网测试开发大会".equals(title)){`
15. `excellent_result = jsonArray.getJSONObject(i).getInt("excellent");`
16. `break;`
17. `}else{`
18. `continue;`
19. `}`
20. `}`
21.  
22. `//校验结果excellent为0则断言成功，若为1则断言失败`
23. `if(excellent_result == 0){`
24. `Failure = false;`
25. `//日志输出结果`
26. `log.error("【Case02】执行成功，excellent值为" + excellent_result + "，此帖非精华帖。");`
27. `}else{`
28. `Failure = true;`
29. `FailureMessage = "【Case02】执行失败，excellent值为" +excellent_result ;`
30. `log.error("【Case02】执行失败，excellent值为" + excellent_result + "，此帖为精华帖。");`
31. `}`

![img](https://img-blog.csdn.net/20180302140544664)

**八、添加"察看结果树"，用来察看断言执行结果。另外由于脚本中部分执行结果由log输出，因此需要将"选项"下的"Log Viewer"选项勾上，这样就可以察看日志信息了。（建议将jmeter.properties配置文件中的log_level.jmeter以及log_level.jmeter.junit分别设置为log_level.jmeter=ERROR、log_level.jmeter.junit=DEBUG，过滤不必要的日志信息）**

![img](https://img-blog.csdn.net/20180302141146517)

![img](https://img-blog.csdn.net/2018030214123037)

**九、最后保存文件并执行（点击操作栏中绿色三角箭头），查看执行结果**

**【结论】**从右下方的日志窗口可以看到，由于精华帖数量为3因此断言Case01失败，而上方的察看结果树中这条断言也显示为红色代表失败，Case02执行成功，因此不会显示在察看结果树中。

![img](https://img-blog.csdn.net/20180302142910295)

 

**四、总结**

​                                                                                                                                                

​        至此，一个简易的接口自动化测试框架便搭建完成，基本可以应付一些不太复杂的测试场景。

​        JMeter就好比一把瑞士军刀，麻雀虽小但五脏俱全，虽不及一些商业测试工具那样能够提供庞大的"套件"，却也凭借着自己的灵活性几乎无所不能。