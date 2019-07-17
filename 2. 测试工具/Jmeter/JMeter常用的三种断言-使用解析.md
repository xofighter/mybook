# JMeter常用的三种断言-使用解析

2018年07月31日 00:18:00 [weixin_34409822](https://me.csdn.net/weixin_34409822) 阅读数 267



> **断言**: 请求成功、返回响应状态码200，并不代表结果一定正确，还需要判断返回内容的正确与否。因此在请求的返回层面增加一层判断机制，以此来提高测试的准确性（类似于LR中的检查点）

JMeter常用的的三种断言类型：

### 一、Response Assertion（响应断言）

#### 1. 添加响应断言

对Web请求的响应结果进行验证



![4866277-3aba03184e416610.png](https://upload-images.jianshu.io/upload_images/4866277-3aba03184e416610.png)



#### 2. 输入需要匹配的字符串

此处对于访问Baidu首页，需要设置匹配的字符串为“百度一下，你就知道”，表示返回的文本内容若包含有“百度一下，你就知道”，则就算Pass



![4866277-04cb8a5e1f280862.png](https://upload-images.jianshu.io/upload_images/4866277-04cb8a5e1f280862.png)



**Response Assertion配置参数**

| 模块类型                | 选项名称                    | 配置说明                                                     |
| :---------------------- | :-------------------------- | :----------------------------------------------------------- |
| Name                    |                             | Response Assertion名称                                       |
| Comments                |                             | 注释                                                         |
| Apply to                |                             | 断言应用的范围                                               |
|                         | Main sample and sub-samples | 作用于父节点取样器及其子节点取样器                           |
|                         | Main sample only            | 仅作用于父节点取样器                                         |
|                         | Sub-samples only            | 仅作用于子节点取样器                                         |
|                         | Jmeter Variable Name to use | 作用于Jmeter变量（输入框中可输入Jmeter的变量名称）           |
| Field to Test           |                             | 测试的字段                                                   |
|                         | Text Response               | 匹配从服务器返回的响应文本（不包括Response Headers）         |
|                         | Response Code               | 匹配响应状态码                                               |
|                         | Response Message            | 匹配响应信息。如：OK                                         |
|                         | Response Headers            | 匹配响应头                                                   |
|                         | Request Headers             | 匹配请求头                                                   |
|                         | URL Sampled                 | 匹配URL链接                                                  |
|                         | Document(text)              | 匹配文档内容                                                 |
|                         | Ignore Status               | 一个请求多项响应断言时，忽略某一项断言的响应结果，而继续下一项断言 |
|                         | Request Data                | 匹配请求数据                                                 |
| Pattern Mactching Rules |                             | 匹配的规则                                                   |
|                         | Contains                    | 返回的结果包括所指定的内容，支持正则匹配                     |
|                         | Matches                     | 根据指定内容进行匹配                                         |
|                         | Equals                      | 返回结果与所指定的内容一致                                   |
|                         | Substring                   | 返回结果包括所指定结果的字符串，不支持正则匹配               |
|                         | Not                         | 不进行匹配就算是Pass                                         |
|                         | Or                          | 暂不确定该模式的用法                                         |
| Patterns to Test        |                             |                                                              |
|                         | Patterns to Test            | 需要匹配的正则表达式、字符串。可以添加多项，每一项会分开进行验证，若某一项验证失败，则其后的不会再进行验证。 |

#### 3. 添加：断言结果(Assertion Results)、查看结果树(View Results Tree)



![4866277-13fb83eab84d3035.png](https://upload-images.jianshu.io/upload_images/4866277-13fb83eab84d3035.png)



#### 4. 运行Test Plan中的线程组，进行断言检查

以下可观察到响应数据中是包含所指定的验证字符串，Pass



![4866277-f6a60b8756b54110.png](https://upload-images.jianshu.io/upload_images/4866277-f6a60b8756b54110.png)



PS：若响应数据（Response Data）中出现中文乱码，则可参看此文解决==>[JMeter返回的响应数据出现中文乱码-解决方案](https://www.jianshu.com/p/bf6a618800f1)



### 二、Size Assertion（数据包字节大小断言）

判断响应结果是否包含正确数量的byte。可定义（=, !=, >, <, >=, <=）



![4866277-1d767f0d9718c823.png](https://upload-images.jianshu.io/upload_images/4866277-1d767f0d9718c823.png)







![4866277-989e8e7a9d7b6e70.png](https://upload-images.jianshu.io/upload_images/4866277-989e8e7a9d7b6e70.png)







### 三、Duration Assertion（持续时间断言）

判断是否在给定的时间内返回响应结果



![4866277-9e2acbbe84086a43.png](https://upload-images.jianshu.io/upload_images/4866277-9e2acbbe84086a43.png)







![4866277-eae1bdab98e090dc.png](https://upload-images.jianshu.io/upload_images/4866277-eae1bdab98e090dc.png)



--------

# [jmeter（八）断言](https://www.cnblogs.com/imyalost/p/6024306.html)



jmeter中有个元件叫做断言（Assertion），它的作用和loadrunner中的检查点类似；

用于检查测试中得到的响应数据等是否符合预期，用以保证性能测试过程中的数据交互与预期一致。

**使用断言的目的：**在request的返回层面增加一层判断机制；因为request成功了，并不代表结果一定正确。

**使用断言的方法：**

**△**在选择的Sampler下添加对应的断言（因为不同类型的断言检查的内容不同）；配置好响应的检查内容（根据断言情况而定，有的断言控制面板不需要添加任何内容，如XML Assertion）。

**△**添加一个断言结果的监听器（从监听器中添加），通过“断言结果”可以看到是否通过断言；对于一次请求，如果通过的话，断言结果中只会打印一行请求的名称；

  如果失败，则除了请求的名称外，还会有一行失败的原因（不同类型的断言，结果不同）。

**PS：**一个Sampler可以添加多个断言，根据你的检查需求来添加相应的断言，当Sampler下所有的断言都通过了，那么才算request成功。

最新版本的3.0jmeter中有13种不同的断言，下面简单介绍下每个断言各自拥有什么样的作用以及它们的适用场景：

 

**1、BeanShell断言**

BeanShell之前关于定时器的随笔中有介绍过，是一种松散类型的脚本语言（这点和JS类似），一种完全符合java语法的java脚本语言，并且又拥有自己的一些语法和方法；

**作用对象：**针对sampler中的Bean Shell sampler而使用的断言

![img](https://images2015.cnblogs.com/blog/983980/201611/983980-20161102202934846-1137374273.png)

**Name:**断言的名字（可以用一个比较容易理解和分辨的名称）

**Comments：**注释（对这个断言进行一个解释，备注）

**Reset bsh.interpreter before each call:**在每次调用Bean Shell之前重置bsh.interpreter类（bsh.interpreter是Bean Shell脚本语言的一种类，也可以理解为一种解析器）

**Parameters（String Parameters and String []bsh.args）:**String参数（String []bsh.args是主类main函数的形式参数,是一个String 对象数组，可以用来获取命令行用户输入进去的参数）

**Script file：**脚本文件（可以填入脚本文件路径）

**Script（see below for variables that are defined）:**参照下文定义的变量（使脚本文件参照定义的变量来运行）

 

**2、 BSF断言**

BSF(Bean Scripting Framework)之前也介绍过，是一个支持在Java应用程序内调用脚本语言 (Script)，并且支持脚本语言直接访问Java对象和方法的一个开源项目；

**作用对象：**针对sampler中的BSF sampler而使用的断言

![img](https://images2015.cnblogs.com/blog/983980/201611/983980-20161103170643627-1686895504.png)

**Script language（e.g.beanshell,javascirpt,jexl）:**脚本语言（可以从下面的下拉框中选择对应的脚本语言JavaScript、beanshell等）

**parameters to be passed to script（=> String Parameters and String []args）:**（传递给脚本的参数→可以理解为使用BSF断言脚本时候一起引用的参数 ）

**Script file（overrides script）：**重写脚本（可以通过选择脚本文件的状态，是浏览调用已有的脚本还是在在下方的输入框内写入脚本；）

**Script：**下面的输入框表示可以输入变量类型，运用的脚本（取样结果、断言结果、取样日志文件等参数）

 

**3、比较断言（compare  assertion）**

这是一种比较特殊的断言元件，针对断言进行字符串替换时使用；

**作用对象：**需要替换的字符串

![img](https://images2015.cnblogs.com/blog/983980/201611/983980-20161103184044658-1471548765.png)

**Select Comparison Operators:**选择比较运算符

**Compare Content:**可以选择比较的内容类型（true/false或者自定义，编辑）

**Compare Time：**比较时间（可以设定比较的时间，单位为秒，默认为-1）

**Comparison Fitters:**比较修改工具

**regular expression substitutions:**替换正则表达式

**Regex String:**要替换的字符串（可从断言结果中选择）

**substitutions：**替换的字符串（替换结果）

 

**4、HTML断言**

对响应类为XML类型的文件进行断言；

**作用对象：**针对sampler中的SOAP/XML-RPC Request而使用的断言

![img](https://images2015.cnblogs.com/blog/983980/201611/983980-20161103190755924-980211276.png)

**Tidy Settings:**Tidy 环境（Tidy是一个HTML语法检查器和打印工具，可以将HTML转换为XML类型的文件）

**Doctype:**文档类型（可通过下拉框选择不同文档类型→ omit疏忽遗漏的/auto动态的/strict严格的/loose宽泛的。。。。。。我也不太懂这里什么意思GG）

**Format：**文件格式（可选择HTML/XHTML/XML三种不同类型的文件格式来检查返回内容）

**Errors only：**误差校正（能接受的最大值）

**Error threshold：**误差/错误范围（可选择误差/错误数量的范围，最大值）

**Warning threshold：**警告范围（可选择误差警告的数量范围，最大值）

如果勾选“Error only”这里忽略Warning，只对误差作统计检查；如果对返回内容的检查结果不超过指定结果，则断言通过，否则失败。

**Write JTidy report to file:**写入JTidy报告的文件（JTidy是Tidy的一个java移植，可以将它当成一个处理HTML文件的DOM解析器）

 

**5、JSR223断言**

JSR223即Java 规范请求，是指向JCP(Java Community Process)提出新增一个标准化技术规范的正式请求；

**作用对象：**针对sampler中的JSR223 sampler而使用的断言

![img](https://images2015.cnblogs.com/blog/983980/201611/983980-20161103194626393-2078455205.png)

**Script language（e.g.beanshell,javascirpt,jexl）:**脚本语言（可以从下面的下拉框中选择对应的脚本语言JavaScript、beanshell等）

**parameters to be passed to script（=> String Parameters and String []args）:**（传递给脚本的参数→可以理解为使用JSR223断言脚本时候一起引用的参数 ）

**Script file（overrides script）：**重写脚本（可以通过选择脚本文件的状态，是浏览调用已有的脚本还是在在下方的输入框内写入脚本；）

**Script：**下面的输入框表示可以输入变量类型，运用的脚本（取样结果、断言结果、取样日志文件等参数）

 

**6、MD5Hex断言**

MD5是一种消息摘要算法，用以提供消息的完整性保护（具体关于MD5的知识请自行查询）；

**作用对象：**针对参数类型为MD5Hex加密的参数的断言

![img](https://images2015.cnblogs.com/blog/983980/201611/983980-20161103195335205-1127192152.png)

**MD5Hex：**将已被MD5加密的参数写入其中，添加取样器等其他元件

 

**7、Size断言**

用于判断返回内容的大小；

**作用对象：**返回信息，响应报文

![img](https://images2015.cnblogs.com/blog/983980/201611/983980-20161103195950158-1246251174.png)

**APPly to:**应用范围（返回内容的断言范围）

​         Main sample and sub-samples:作用于父节点取样器及对应子节点取样器

​         Main sample only：仅作用于父节点取样器

​         Sub-samples only:仅作用于子节点取样器

​         JMeter Variable:作用于jmeter变量(输入框内可输入jmeter的变量名称)

**Response Size Field to Test:**响应字节的测试范围（可以选择用于判断的响应范围）

​         Full Response:全部响应

​         Response Headers:响应头部

​         Response Body:响应主体

​         响应代码：响应报文相关的代码

​         响应信息：响应报文的信息

​         **Size to Assert:**断言字节范围

​         字节大小单位为：字节；比较顺序是①返回内容的大小②比较类型③指定字节大小

 

**8、SMIME断言**

SMIME是一种多用途网际邮件扩充协议，相比于之前的SMAP邮件传输协议，增加了安全性，对邮件主题进行保护；

**作用对象：**针对采用了该种邮件传输协议的信息

![img](https://images2015.cnblogs.com/blog/983980/201611/983980-20161104101345596-1138588847.png)

**signature:**签名（可选择对协议的签名验证状态）

​          Verify signature:验证签名

​          Message not signed:没有签名消息

**Signer certificate：**签名证书（因为SMIME协议增加了安全传输，需要证书验证）

​          No check：不检查

​          Check values:检查

**Signer distinguished name:**签名证书者名称（证书注册者的名称）

**Sigmer email address:**签名者的邮件地址（注册的邮件地址）

**Issuer distinguished name:**发行者名称（由谁发行的证书）

**Serial Number:**证书序号

**Certificate file:**选择证书文件

**Execute assertion message at position:**执行断言消息的位置（在返回消息的具体哪个位置执行断言）

 

**9、XML概要断言**

亦可以称为XML模型断言/XML数据类型断言；XML Schema 定义了两种主要的数据类型：①xml document schema 文档架构 ;② 文档架构xml-schema xml模式

**作用对象：**返回结果为XML概要断言的2中数据类型的消息

![img](https://images2015.cnblogs.com/blog/983980/201611/983980-20161104112639221-2131077184.png)

**XML Schema：**XML概要模型

**File Name:**文件名（写入需要断言的文件名称）

 

**10、XML断言**

XML(可扩展标记语言) 提供一种描述结构化数据的方法。与主要用于控制数据的显示和外观的 HTML 标记不同，XML 标记用于定义数据本身的结构和数据类型；

**作用对象：**判断返回结果是否和xml的格式即<></>成对出现

![img](https://images2015.cnblogs.com/blog/983980/201611/983980-20161104113718518-1890592303.png)

 

**11、XPath断言**

XPath即为XML路径语言，它是一种用来确定XML（标准通用标记语言的子集）文档中某部分位置的语言。XPath基于XML的树状结构，提供在数据结构树中找寻节点的能力。

**作用对象：**针对返回信息为XPAth的数据类型进行断言

![img](https://images2015.cnblogs.com/blog/983980/201611/983980-20161104141912440-1184689885.png)

**Apply to:**适用范围

​         Main sample and sub-samples:主要样本和次级样本

​         Main sample only：仅主要样本

​         Sub-samples only:仅次级样本

​         JMeter Variable:jmeter变量(输入框内可输入jmeter的变量名称)

**XML Parsing Options：**XML解析选项

​         Use Tidy(tolerant parser):使用Tidy（容错解析器），默认选择quiet（不显示）

​         Quiet：不显示

​         Report errors：错误报告

​         Show warnings:显示错误

​         Use Namespaces:使用名称空间

​         Validate XML:验证XML（文件包/数据）

​         Ignore Whitespace:忽略空格（这允许你指定语法分析器可以忽略哪个空格，而哪个空格是重要的）

​         Fetch external DTDs:获取外部DTDs（一些XML元素具有属性，属性包含应用程序使用的信息，属性仅在程序对元素进行读、写操作时，提供元素的额外信息，这时候需要在DTDs中声明）

**XPath Assertion:**输入框中写入xpath断言，点击Validate验证其正确性

​         True if nothing matches:确认都不匹配

 

**12、响应断言**

判断返回内容中的内容

**作用对象：**响应报文中的所有对象

![img](https://images2015.cnblogs.com/blog/983980/201611/983980-20161104145744986-218512768.png)

**APPly to:**适用范围

​         Main sample and sub-samples:作用于父节点取样器及对应子节点取样器

​         Main sample only：仅作用于父节点取样器

​         Sub-samples only:仅作用于子节点取样器

​         JMeter Variable:作用于jmeter变量(输入框内可输入jmeter的变量名称)

**要测试的响应字段：**要检查的项

​                响应报文

​                Documeng(text)：测试文件

​                URL样本

​                响应代码

​                响应信息

​                Response Headers:响应头部

​                Ignore status：忽略返回的响应报文状态码

**模式匹配规则：**

​           包括：返回结果包括你指定的内容

​           匹配：（好像跟Equals查不多，弄不明白有什么区别）   

​           Equals：返回结果与你指定结果一致

​           Substring：返回结果是指定结果的字串

​           否：不进行匹配

**要测试的模式:**即填写你指定的结果（可填写多个）,按钮【添加】、【删除】是进行指定内容的管理

 

**13、断言持续时间**

用于判断服务器的响应时间

**作用对象:**服务器

![img](https://images2015.cnblogs.com/blog/983980/201611/983980-20161104155852315-1953711455.png)

 

**APPly to:**适用范围

​         Main sample and sub-samples:作用于父节点取样器及对应子节点取样器

​         Main sample only：仅作用于父节点取样器

​         Sub-samples only:仅作用于子节点取样器

**Duration to assert：**持续断言

**Duration in milliseconds：**响应时间设置（单位：毫秒），如果响应时间大于设置的响应时间，则断言失败，否则成功！

​             

 

转载请注明出处，商用请征得作者本人同意，谢谢！！！



------------

# JMeter学习笔记5-如何添加Assertion

2017年05月01日 14:03:12 [Anthony_tester](https://me.csdn.net/u011541946) 阅读数 7633



 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/u011541946/article/details/71055629


       本文介绍如何使用断言。作为测试人员，断言的概念应该听说过吧，断言就是断定测试结果的正确性。前面演示的第一个JMeter测试用例，是一个HTTP Request,也就是常听说的web API测试，中文就是接口测试。接口测试，用一句话概括就是，通过发送HTTP请求，检查响应内容是否正确。在前面文章，我们在View Results Tree里，通过人工肉眼去观察，response code： 200这样的字段。这个响应代码等于200就是个一个断言的根据，今天就来介绍在JMeter上如何使用断言。Test Plan还是用前面访问百度首页的例子基础上，逐步添加新的介绍。
\1. 在前面例子基础上，点击Clear all，保持测试结果干净，这里就不截图了。
\2. 在Thread Group里添加一个断言

 ![img](https://img-blog.csdn.net/20170501140323623?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

​       通过上面截图，我们选择的是一个在接口测试过程中，使用频率非常高的一个断言：Response Assertion。可以看到，上面有12中支持的断言模板，我也是随便猜测一下几个断言的使用场景。Duration Assertion，持续时间断言，例如一个操作，在5秒的持续时间是认为合理的，超过了就测试失败。HTML Assertion,前端测试一些手段和技术，例如什么也没有什么tag。Response Assertion,响应断言，主要有响应状态码和响应时间，响应返回内容等，这里我们介绍响应代码断言。XML断言，有些操作返回的是XML格式数据，需要去XML格式数据进行解析，断言处理。XPath断言，例如我知道根据这个XPath可以找到一个元素，这个在web自动化元素定位经常使用。
\3. Response Assertion设置
这里选择Response Code,匹配规则选择完全相等，然后点击Add按钮，输入200
\4. 修改Thread Group界面
点击左侧面板的Users回到了Thread Group界面，修改如下参数

![img](https://img-blog.csdn.net/20170501140647188?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center) 

\5. 点击Start运行测试
如果没有清除之前results，先点击清除然后点击运行。
![img](https://img-blog.csdn.net/20170501140524902?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)运行都Pass，为了模拟错误的结果，我们回到Response Assertion，把200改成201，清除results然后再次运行。点击View Result Tree查看错误结果。![img](https://img-blog.csdn.net/20170501140716030?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)错误信息显示，我们要对比的是201，当前收到的状态码是200，所以测试失败。
6.新建一个Assertion Result
在左侧面板点击Users这个Thread Group,右键，add->Listener->Assertion Results

 ![img](https://img-blog.csdn.net/20170501140804484?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)![img](https://img-blog.csdn.net/20170501140823907?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


创建好了之后,把这个新建的Assertion Result拖拽到View Results Tree下方，如图

。![img](https://img-blog.csdn.net/20170501140941752?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)接下来，把Response Assertion里的201改成200，清除后再次运行测试，结果如下![img](https://img-blog.csdn.net/20170501141006330?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)因为这里，没有失败，没有产生其他log信息，所以显示就5次运行的HTTP Request的名称。Response Assetion就介绍到这里。