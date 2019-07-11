# Jmeter 接口测试中使用Beanshell断言: 将接口响应报文与数据库结果对比

2018年05月10日 18:57:24 [nielinqi520](https://me.csdn.net/nielinqi520) 阅读数 1670



昨天朋友问我Jmeter怎么把数据库查询结果与接口响应JSON报文做对比。（第一段是记录自己的探索过程，读者可以直接跳至第二段）



我当时想法是

第一步：搞定接口参数

\1. 正则匹配接口响应的JSON内容。匹配-1，匹配所有符合内容 (分别匹配ID 和 NAME的值)

\2. 取ID，作为ForEach控制器的控制参数。(ID_1, ID_2, ID_3 .. 有多少会循环几次)

\3. 其他变量用${__counter(TRUE,)}计数器来拼接。例如${__V(NAME_${__counter(TRUE,)})}（取得NAME_1, NAME_2... ForEach循环几次，就取几个）

第二步：搞定数据库值

\1. JDBC PostProcessor 后置处理器，将查询结果保存为 (id_1, id_2 ... name_1, name_2 ...的形式)

第三步：断言

1.使用java请求。ResultData参数 添加接口返回的 id 和 name

2.为java请求添加相应断言，在断言中添加 数据库的ID 和 NAME 

![img](https://img-blog.csdn.net/20170914163209025?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG9tb3lhX2NoZW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



朋友说这不是他想要的。他想在一个断言里判断。但是因为json里内容是不固定的，所以响应断言肯定无法满足。

昨天回到家想了一下，觉得Beanshell断言应该可以。

实际上确实可以，还很简单！

我的思路是：

第一步：接口参数直接使用HTTP请求的响应内容，JMeter内置了获取当前请求响应的方法prev.getResponseDataAsString()

第二步：数据库逃不掉的，老老实实赋字段名

第三步：Beanshell断言

\1. for循环， i = 1，vars.get("id_" + i) 拼接变量名 获得基础填充内容 "{\"id\":" + id + ",\"latitude\":" + latitude + ",\"longitude\":" + longitude + ",\"name\":\"" + name + "\"}"

\2. 拼接首位JSON格式，获得完整JSON报文

\3. equals() 判断 2个字符串

![img](https://img-blog.csdn.net/20170914172350656?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG9tb3lhX2NoZW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![img](https://img-blog.csdn.net/20170914172405687?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG9tb3lhX2NoZW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



另外Jmeter中的BeanShell中，好像不能使用String.format()。我查了一下，暂时没有这块儿资料。如果有的话，拼接部分就方便很多了。

最后附上Beanshell断言完整代码

[java][view plain](https://blog.csdn.net/tomoya_chen/article/details/77981707#)





```java
int M = ${id_#};
int i;
String Str = "";
String id = "";
String latitude = "";
String longitude = "";
String name = "";
 
Str = "{\"data\":[";
 
for(i = 1; i <= M; i++){
	id = vars.get("id_" + i);
	latitude = vars.get("latitude_" + i);
	longitude = vars.get("longitude_" + i);
	name = vars.get("name_" + i);
	
	Str = Str + "{\"id\":" + id + ",\"latitude\":" + latitude + ",\"longitude\":" + longitude + ",\"name\":\"" + name + "\"}";
	if (i != M){
		Str = Str + ",";
		}
	}
Str = Str + "],\"message\":\"\",\"result\":0}";
System.out.println("数据库内容: \n" + Str);
 
//prev.getResponseDataAsString是Jmeter提供的方法，可以调取上次请求的响应字符串  
response = prev.getResponseDataAsString();  
System.out.println("响应内容: \n" + response);
 
if(Str == ""){
	Failure = true;
	FailureMessage = "连接数据库失败或者数据库内没有历史数据！";
	//对比数据库内容和响应内容，私用euqals方法判断是否一致
	}else if(response.equals(Str) == false){
		//把断言失败置为真 
		Failure = true;  
		String Msg = "与数据库内容不匹配！\n";
		FailureMessage = Msg + "数据库内容: \n" + Str + "\n" + "响应内容: \n" + response; 
		}
```



最后总结一下用到的Jmeter内置方法：

vars.get() #获取JMeter中的变量

prev.getResponseDataAsString() #获取当前请求的响应结果

Failure、FailureMessage 是Beanshell断言特有的参数，用于指定断言失败。

完整的Jmeter内置方法请参考这位大佬的博文

http://www.cnblogs.com/puresoul/p/4915350.html

解决问题过程中参考了这位大佬的博文，利用gson包直接对比json报文。（但我没实现，只是使用了他的部分java代码）



-----------------------

# jmeter beanshell断言接口自动化实例

2018年07月30日 10:38:03 [卖包子的小行家](https://me.csdn.net/gynumber1) 阅读数 3445



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



------------------------

# Jmeter使用BeanShell 断言进行判断

2017年04月26日 16:55:29 [上不了岸的鱼](https://me.csdn.net/zailushangbuting) 阅读数 8387



 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/zailushangbuting/article/details/70808612

> 对相应结果进行判断时，普通的响应断言满足不了需求，对于有java基础的同学来说，BeanShell 断言可以解决大部分的问题；

**1、新建BeanShell断言**

![img](https://img-blog.csdn.net/20170426165912344?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvemFpbHVzaGFuZ2J1dGluZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

2、编写断言判断代码：



```java
if("206".equals("${action_seq_1}") && "3".equals("${trans_status_1}")){	Failure = false;	FailureMessage = "交易成功！";}else if(!"206".equals("${action_seq_1}")){     Failure = true;	FailureMessage = "交易类型不正确！";}else if(!"3".equals("${trans_status_1}")){	Failure = true;	FailureMessage = "交易未成功！";}
```





![img](https://img-blog.csdn.net/20170426165858446?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvemFpbHVzaGFuZ2J1dGluZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

基本操作完成。