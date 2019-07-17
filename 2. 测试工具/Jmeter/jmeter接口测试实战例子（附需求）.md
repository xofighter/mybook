jmeter接口测试实战例子（附需求）
2018年05月05日 15:22:30 q915730058 阅读数 3393
1.接口需求文档说明



2.打开jmeter，新建线程组，线程组里面默认配置就可以



 

3.建立http请求，我这里是http请求，所以我选择这个，跟进实际情况



4.post请求说明，post直接加参数和参数值就可以了，get请求直接在路径哪里加参数名和参数值就可以了，但要注意格式



5.配置好后，新建察看结果树



 

6.建立断言，根据自己实际情况选择，一般选择响应断言就够用了



 

6.编写接口测试用例（例如下图）



 

注：可能不太详细，如果不够详细的地方请留言，看到后我会重新编辑，让更多人一起学习

---------------


# **jmeter接口测试及详细步骤**


一、接口测试流程
1.右键点击线程组添加 HTTP请求，此时在线程组下面出现HTTP请求选项。点击该选项，出现HTTP请求界面。如下



服务器名称或IP填你的服务器名或IP地址:192.168.96.8。
端口号填接口所用的端口号:如：8081
connect表示连接时间，比如10000毫秒；response表示响应时间，比如10000毫秒。（可以不写）
Implementation选HttpClient4；（可以不写）协议填http；Content encoding填utf-8。
路径填接口地址，比如登录接口/api/user/mobileLogin.jsp、/safe/0.0.1/JFFW/urlGenerate?access_token=f4fa6e9d-4302-4b68-b365-8539c06eca3f
添加参数，比如名称为username值为13246767401，名称为:password值为liang521    (或者填写你需要的请求报文)
----------------------------------------------------------------------------------------
2.在这里的参数区域分为：parameters和BodyData

①parameters：在这个中可以分开写，名称写参数名称    值就写参数需要请求的报文。
里面是键值的格式，如：

globalInfo                          {"fwsid":"credentials-client","fwid":"fe07501238e84f7a967036eba3480abe","qyid":"110101MXB6CK9617","jrfs":"app"}
data        [{"fp_dm":"11001571071","fp_hm":"53744125","jshj":"3998.00"}]     数组的格式
          access_token 1234567890987654321123456789876543




②BodyData ：在这里可以把所有的报文写到一起，中间用逗号分开。如globalinfo和data参数。

   1.如果使用Body Data进行参数化，一定要加上http请求头信息管理器的json格式类型：如 Content-Type： application/json
   2.请求报文如：

{
    "data":[{
            "fp_dm":"111001571071",
            "fp_hm":"53744125"
    }],
    "globalInfo":{
        "fwsid":"credentials-client",
        "fwid":"fe07501238e84f7a967036eba3480abe",
        "qyid":"110101MXB6CK9617",
        "jrfs":"app"
    }
}
    3.可以吧access_token写在路径的后面，如： /dxhytest/api/0.0.1/BXFW/queryBxzt?access_token=1234567890987654321123456789876543
------------------------------------------------------------------------------------------
3.右键点击HTTP请求添加--监听器--用表格查看结果。

4.右键点击线程组添加--监听器--用查看结果树、聚合函数。

二、测试方法
 1.我们这里先是通过token接口获取到访问token的参数报文。
 2.然后在其他接口中写入访问token的报文，然后依次写入其他需要的报文。
 3.成功以后查看结果树中的内容。
三、以下是我们的接口测试脚本         
这里附上一个传送门:http://download.csdn.net/detail/qq514418695/9686509

四、相应断言：
这里的响应断言主要是针对HTTP请求返回的JSON字符串的，我们一般使用的是包括断言，就是返回的结果是否包含我们需要的信息。断言文本的截取可以在运 行结果树里进行，将结果显示类型改为Text（当然也可以JSON格式和Text格式对比着找），找出需要断言的信息。

五、定时器： 
固定定时器的延时不会计入单个sampler的响应时间，但会计入事务控制器的时间。对于“事务控制器”来说，定时器相当于loadrunner中的  think time（思考时间：实际操作中，模拟真实用户在操作过程中的等待时间）。

   

一、定时器的作用域

1、定时器是在每个sampler（采样器）之前执行的，而不是之后（无论定时器位置在sampler之前还是下面）；

2、当执行一个sampler之前时，所有当前作用域内的定时器都会被执行；

3、如果希望定时器仅应用于其中一个sampler，则把定时器作为子节点加入；

4、如果希望在sampler执行完之后再等待，则可以使用Test Action；

---------------------


# 接口测试-Jmeter 接口自动化-脚本数据分离实例

[grizz](https://testerhome.com/grizz) · 2018年04月18日 · 最后由 [grizz](https://testerhome.com/grizz) 回复于 2 月前 · 7350 次阅读

 目录 

## 一、 背景：

`` ``为了让大家更加的了解**Jmeter**，并且使用起来游刃有余。
这篇我们主要讲一下，如何优雅的使用Jmeter一步步的实现接口自动化，完成脚本与数据分离，把可能对Jmeter脚本的维护转移到csv文本中，降低接口变更时对脚本的维护，最终目标是实现写好接口自动化脚本后，接口变更的维护都只要操作csv文件。

**Jmeter脚本，数据和报告地址：**
[https://github.com/grizz/jmeter-master](https://github.com/grizz622/jmeter-master)

## 二、实例

先介绍一个Jmeter的函数-》csvRead函数，后续介绍使用的会比较多，熟悉的伙伴可以直接跳过。

#### 1、csvRead函数使用：

csvRead函数是从外部读取参数，可以从一个文件中读取多个参数。 
使用步骤：
1、先新建一个文件，例如**test.csv**(或test.txt)，里面的数据存放为
grizz,qq1111
jiezai,qq1111

文件为用户名和密码，用逗号隔开，每一列表示一种参数，每一行则表示一组参数。

2、选项-》函数助手对话框-》函数助手，打开Jmeter的函数助手，选择csvRead函数：

[![img](https://testerhome.com/uploads/photo/2018/cba36b5d-4540-462c-a972-c1b5fa11a104.png!large)](https://testerhome.com/uploads/photo/2018/cba36b5d-4540-462c-a972-c1b5fa11a104.png!large)



> 其中：
> CSV file to get values from | *alias：要读取的文件路径，为绝对路径 
> CSV文件列号| next| *alias：从第几列开始读取，注意第一列是0

即`${__CSVRead(D:/test.csv,0)}` 取到的值为grizz
即`${__CSVRead(D:/test.csv,1)}` 取到的值为qq1111

3.Jmeter执行的时候，如果有多个线程，顺序读取每行的数据，如果线程组多于文件中的行数，则循环读取。如线程数为2，则第2个线程读取的是第二行的数据，线程数为3，线程数3大于文件中的行数2，则第3个线程读取的是第一行的数据。

> PS：这一函数并不适合于读取很大的文件，因为整个文件都会被存储到内存之中。对于较大的文件，请使用配置元件CSV Data Set或者StringFromFile 。但是我们不是压测，只是接口自动化，一般没有太大的数据文件，啊哈哈哈哈。

默认情况下，函数会在遇到的每一个逗号处断行，需要换一个分隔符（通过设置属性csvread.delimiter来实现)
修改**jmeter.properties**文件:

> \#csvread.delimiter=,
> 修改为
> csvread.delimiter=?

即把分隔符修改为？问号，注意前面的#号代表注释，要去掉。**重启Jmeter生效。**

#### 2、使用的接口：

这里我们以两个接口举例，其中`Content-Type=application/json`：
1·获取token的接口**/getToken**
**入参：**
{
"flag":"test",
"appId":"001"
}
返回值：
{"returnFlag":"1000","returnMsg":"获取token成功","token":"19940622"}

2·使用token的接口**/useToken**，主要是测试useToken接口，useToken接口的token需要从getToken接口的返回值中取，其实就是参数关联。
**入参：**
{
"flag":"${token}",
"appId":"001"
}
返回值，以3个场景为例：
{"returnFlag":"1000","returnMsg":"使用token成功"}
{"returnFlag":"1001","returnMsg":"token为空"}
{"returnFlag":"1002","returnMsg":"token错误"}

对于接口的断言，我们默认`"returnFlag":"1000"`即接口业务正常返回，**1001**，**1002**代表接口针对业务的不同异常给予的返回，当然也在我们的接口测试范围内。

我们分**v1**，**v2**，**v3**，**v4**，4个版本循序渐进的讲：

#### v1版本：

刚开始入门时，我们的脚本可能会是这样的
先请求getToken接口，并获取token，用正则表达式提取如下

[![img](https://testerhome.com/uploads/photo/2018/9cc2f79c-d5ff-42ba-9f6c-295a32ffa28a.png!large)](https://testerhome.com/uploads/photo/2018/9cc2f79c-d5ff-42ba-9f6c-295a32ffa28a.png!large)



再请求useToken接口，flag的值输入${token}，使用提取到的token值。
**入参**：{"flag":"${token}","appId":"001"}
返回值：{"returnFlag":"1000","returnMsg":"使用token成功"}
断言："returnFlag":"1000"，断言成功 

[![img](https://testerhome.com/uploads/photo/2018/4243178b-c8ab-4d3f-9678-cdba6fcdd024.png!large)](https://testerhome.com/uploads/photo/2018/4243178b-c8ab-4d3f-9678-cdba6fcdd024.png!large)



再测试后续两种场景，入参如下：
**sampler**-使用tokenv1-为空：
**入参**：{"flag":"","appId":"002"}
返回值：{"returnFlag":"1001","returnMsg":"token为空"}
断言："returnFlag":"1000"，断言失败

**sampler**-使用tokenv1-错误：
**入参**：{"flag":"errorToken","appId":"003"}
返回值：{"returnFlag":"1002","returnMsg":"token错误"}
断言："returnFlag":"1000"，断言失败
**查看结果树**

[![img](https://testerhome.com/uploads/photo/2018/83acc4b4-cf85-4e27-bd2b-1f2948ff5331.png!large)](https://testerhome.com/uploads/photo/2018/83acc4b4-cf85-4e27-bd2b-1f2948ff5331.png!large)



这一顿操作下来，没啥问题，因为useToken接口的3种场景我们都覆盖了，只要把异常的场景的断言对应改一下，我们的接口脚本就写好了，可以交付。但是如果我们要实现接口自动化，那么v1版本中**sampler的重复率**比较高，我们考虑能不能把useToken的3种场景放到一个**sampler**中。
于是有了v2版本

#### v2版本：

由于聪明的我们有一定前瞻性，我们知道，想降低脚本中**sampler的重复率**，需要借助数据文件，我们可以把接口useToken的请求body直接从文件中读

```
入参1：{"flag":"${token}","appId":"001"}入参2：{"flag":"","appId":"002"}入参3：{"flag":"errorToken","appId":"003"}
```

入参2，3我们可以从文件中读取没问题，**但是入参1这样从文件中读取，”${token}”读取出来的值就是字符串”${token}”，而不是我们想要的”19940622”，怎么办呢？**
于是我们思考着，可以把接口useToken的请求分两种情况，需要正确的token和不需要正确的token，如果需要正确的token，我们就在Jmeter中传给他，其它参数还是可以在文本中读取；如果不需要正确的token，则请求全部从文件中读取。什么意思呢，继续往下看。

我们设置存放csv文件的目录 `DATA=/jmeter/testcase`，因为我们可能会多次使用到这个目录，所以可以用`${ DATA }`代表我们的文件目录。

**useToken_v2.csv**文件：

```
用例1-token正确?1?"appId":"001"}用例2-token为空?0?{"flag":"","appId":"002"}用例3-token错误?0?{"flag":"errorToken","appId":"003"}
```

举例：
`${__CSVRead(${DATA}/useToken_v2.csv,1)}`依次取到的是1，0，0
`${__CSVRead(${DATA}/useToken_v2.csv,2)}`第二次取到的是{"flag":"","appId":"002"}

如果我们**useToken_v2.csv**文件有3行，则设置auto_interface_v2线程数为3。
因为对于csvRead函数，每一个线程都有独立的内部指针指向文件数组中的当前行。当某个线程第一次引用文件时，函数会为线程在数组中分配下一个空闲行。如此一来，任何一个线程访问的文件行，都与其他线程不同（除非线程数大于数组包含的行数）。
当我们需要正确Token时，我们利用if控制器，如果文本的第二列(我这里是以问号分隔的，因为默认是逗号，但是我们接口json数据本身就有逗号，只能换一个)为1，则flag从Jmeter中自己读取；如果文本的第二列为0则代表不需要正确token，那接口入参我们就全部从文件中读取。
If：`${__CSVRead(${DATA}/useToken_v2.csv,1)}==1`

[![img](https://testerhome.com/uploads/photo/2018/333d80ae-c7c7-44ea-81c4-5c0c157036c9.png!large)](https://testerhome.com/uploads/photo/2018/333d80ae-c7c7-44ea-81c4-5c0c157036c9.png!large)



**sampler**-使用tokenv2-haveToken的请求body为：

[![img](https://testerhome.com/uploads/photo/2018/1514e685-bdfe-4bdf-8a01-5a3c410e7ed0.png!large)](https://testerhome.com/uploads/photo/2018/1514e685-bdfe-4bdf-8a01-5a3c410e7ed0.png!large)



`{"flag":"${token}",` `${__CSVRead(${DATA}/useToken_v2.csv,2)}`
即请求body由两部分组成，一部分来自Jmeter内，主要是获取正确的token，另一部分来自**useToken_v2.csv**文件的第3列。
我们这里token正确时，另一个参数好像只有"appId":"001"这一种情况，显得好像这样写起来更加冗余，其实不然，我们appId也有可能为空，也可能错误。这时候我们的**useToken_v2.csv**文件应该会是这样：

```
用例1-token正确?1?"appId":"001"}用例2-appId为空?1?"appId":""}用例3-appId错误?1?"appId":"error appId "}用例4-token为空?0?{"flag":"","appId":"002"}用例5-token错误?0?{"flag":"errorToken","appId":"003"}
```

而且一个接口的入参不可能只有两个，但一般token只会有一个，所以当接口参数多时，这样写还是减少了一定量的**sampler的重复率**。

If：`${__CSVRead(${DATA}/useToken_v2.csv,1)}==0`
请求body就为：`${__CSVRead(${DATA}/useToken_v2.csv,2)}`

[![img](https://testerhome.com/uploads/photo/2018/a73cd29b-b680-4064-bf98-fa70810c05b5.png!large)](https://testerhome.com/uploads/photo/2018/a73cd29b-b680-4064-bf98-fa70810c05b5.png!large)



再解释一下这里为什了加了一个计算器，和接口名称为什么命名为使用**tokenv2-noToken-${_number}**。
首先，当我们把线程数设置为3时，其实getToken接口也跑了3次，但是其实我们只需要它跑一次，取出正确的token就可以了，getToken接口的if控制器跟计算器一起作用，当第1个线程启动时触发if控制器的规则`${_number}==1`，第2，第3个线程是就不会触发if控制器里面的getToken接口。

[![img](https://testerhome.com/uploads/photo/2018/e776b2ea-b900-42a9-87e4-daf72a35c96c.png!large)](https://testerhome.com/uploads/photo/2018/e776b2ea-b900-42a9-87e4-daf72a35c96c.png!large)



useToken接口命名后面加一个${_number}，
使用tokenv2-noToken-${_number}和使用tokenv2-haveToken-${_number}；主要是当接口报错时，可以根据接口的名称(其后面加了${_number}，接口每个场景${_number}都是不一样的)，判断其对应**useToken_v2.csv**文件的哪一行导致报错，可快速定位并进行报错修改。
方便理解，给出运行效果图如下：

[![img](https://testerhome.com/uploads/photo/2018/b5b648af-c769-4fcc-a4fc-338b8c05a2af.png!large)](https://testerhome.com/uploads/photo/2018/b5b648af-c769-4fcc-a4fc-338b8c05a2af.png!large)



#### v3版本：

v2版本我们好像把我们能做的都给做了，但是前提是
**从文件中读取，”${token}”读取出来的值就是字符串”${token}”，而不是我们想要的”19940622”！**
随着时间的推移，楼主真的前前后后看过网上各种Jmeter教程和使用，不下40次，毕竟自己一直有信念，别人能做的自己也可以，1次看不懂的，那就看5次，5次还不懂，10次。不努力，没有办法比别人做得更好的。接着说随着时间的推移，grizz发现那个前提是可以打破的，因为Jmeter有个**eval**函数。
函数`__eval`可以用来执行一个字符串表达式，并返回执行结果。
举个栗子：
name=grizz
SQL=select * from able where name='${name}'
${ SQL }=select * from able where name='${name}'
${__eval(${SQL})}= select * from able where name='grizz'

现在我们可以设置**useToken_v3.csv**文件如下：

```
用例1-token正确?{"flag":"${token}","appId":"001"}?"returnFlag":"1000"用例2-token为空?{"flag":"","appId":"002"}?"returnFlag":"1000"用例3-token错误?{"flag":"errorToken","appId":"003"}?"returnFlag":"1000"
```

明显的，我们不需要if控制器来判断是否需要正确的Token了，脚本看起来清新了一些，而且我们把响应断言也从文件中读取。这样开发修改接口的返回提示时，我们可以直接通过修改csv文件完成对应的修改，不需要去动jmeter脚本。
**入参**：`${__eval(${__CSVRead(${DATA}/useToken_v3.csv,1)})}`
断言`${__CSVRead(${DATA}/useToken_v3.csv,2)}`

[![img](https://testerhome.com/uploads/photo/2018/5287437b-25d5-469c-96b3-30fbde85b69a.png!large)](https://testerhome.com/uploads/photo/2018/5287437b-25d5-469c-96b3-30fbde85b69a.png!large)



看到这我们可以思考一下，在v4版本还有哪些内容可以优化？

#### v4版本：

v1到v2是入门到掌握，v2到v3应该是弱鸡到熟悉，v4应该到星耀了吧，很想写个v5版本，v5(威武)，应该很强的怕。
我们想想，我们的最终目标是实现写好接口自动化脚本后，接口变更的维护都只要操作csv文件。
那么当我们的useToken接口新增了一个场景，token过期

**useToken_v4.csv**文件

```
用例个数?4用例1-token正确?{"flag":"${token}","appId":"001"}?"returnFlag":"1000"用例2-token为空?{"flag":"","appId":"002"}?"returnFlag":"1000"用例3-token错误?{"flag":"errorToken","appId":"003"}?"returnFlag":"1000"用例4-token过期?{"flag":"oldToken","appId":"003"}?"returnFlag":"1000"
```

设置线程组auto_interface_v4的线程数为
`${__CSVRead(${DATA}/useToken_v4.csv,1)}`，其实就是文件**useToken_v4.csv**第一行的第二列，就是4

[![img](https://testerhome.com/uploads/photo/2018/bd4f3529-258a-49d5-aa14-aec3ce9f07d4.png!large)](https://testerhome.com/uploads/photo/2018/bd4f3529-258a-49d5-aa14-aec3ce9f07d4.png!large)



因为我们的线程数与我们的文件行数挂钩，但这里为什么文件有5行，而线程数是4。前面说了，因为对于csvRead函数，每一个线程都有独立的内部指针指向文件数组中的当前行。当某个线程第一次引用文件时，函数会为线程在数组中分配下一个空闲行。即Jmeter会分配一个线程去保存线程的属性，如线程数，启动时间，循环次数等。即当线程数设置为`${__CSVRead(${DATA}/useToken_v4.csv,1)}`时，控制线程属性的线程就读取了**useToken_v4.csv**文件的第一行。

> PS：再说一下csvRead函数
> csvRead函数默认从文件第一行开始读取，除非你二次开发，不然这个默认就一只在(苦笑)，就是说我们不好加文件列的标题(当然可以利用v4版本的第一行也行)，降低了文件的可读性。但当对某个文件进行第一次读取时，文件将被打开并读取到一个内部数组中。如果在读取过程中找到了空行，函数就认为到达文件末尾了，即允许拖尾注释。就是我们可以在文件写完后回车一下，再写一些文件的注释，或者`${__CSVRead(D:/test.csv,next)}`了解一下，csvRead函数的第二个值为next可自行进行文件行标的切换。

其实当我们的脚本量化后，还有很多东西要考虑的，接口csv文件的命名规范，线程组和sampler的命名规范，因为线程组和sampler的名称会在报告中体现，接口入口和出口标准，怎么维护我们的数据和脚本更优雅，怎样更高效的运行脚本和生成更丰富的报告，更加的节省测试人力。

**下一篇讲一下jemter和ant，jenkins的持续集成**
[Jmeter+ant+Jenkins 接口自动化框架完整版](https://testerhome.com/topics/13389)
接口汇总报告：

[![img](https://testerhome.com/uploads/photo/2018/c9c142da-8112-43d3-929c-6008aa4d6916.png!large)](https://testerhome.com/uploads/photo/2018/c9c142da-8112-43d3-929c-6008aa4d6916.png!large)



接口详细报告：

[![img](https://testerhome.com/uploads/photo/2018/c6dd7ffd-fea6-4d4b-8082-7de982ebeb45.png!large)](https://testerhome.com/uploads/photo/2018/c6dd7ffd-fea6-4d4b-8082-7de982ebeb45.png!large)



欢迎交流指正，感谢阅读。