## 使用工具：

Fiddler、Jmeter

## 测试步骤：

### 1.    确认接口

从开发人员那里获取接口文档，接口文档应该包括完整的功能接口、接口请求方式、接口请求URL、接口请求参数、接口返回参数。

如果当前项目没有接口文档，则可以使用fiddler对APP进行抓包确认。

*fiddler对手机进行抓包的方法可以参考：http://blog.csdn.net/whorus1/article/details/50673740*

在手机上对APP进行操作，然后在Fiddler中可以抓取对应的网络[交互信息](https://www.baidu.com/s?wd=%E4%BA%A4%E4%BA%92%E4%BF%A1%E6%81%AF&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)（一个功能中可能设计多个接口的交互）。在抓取的信息中可以看到接口请求方式、接口请求URL、接口请求参数、接口返回参数。

### 2.    在Jmeter中编写测试脚本

\1.      在测试计划中添加一个线程组（在测试计划上右击，添加-Threads-线程组）。

![img](https://img-blog.csdn.net/20160217172047316?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



可以将线程组的名称改成测试项目的名称。

在性能测试时可以通过配置线程属性中的参数来控制并发用户数：

线程数：虚拟用户数，即并发数，一个线程表示一个虚拟用户；

Ramp-Up Period：所有线程启动的时间，单位s。通过这个参数可以设置每个线程间的启动间隔，防止给电脑造成太大性能压力；

循环次数：测试循环的次数，如果勾选了“永远”，那么所有线程会一直发送请求，直到手动停止运行脚本；

![img](https://img-blog.csdn.net/20160218090826966?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



\2.      在线程组中添加HTTP请求（在线程组上右击，添加-Sampler-HTTP请求）。

可以将名称改为待测接口的名称；

在Web[服务器](https://www.baidu.com/s?wd=%E6%9C%8D%E5%8A%A1%E5%99%A8&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)中填入请求服务器的地址和端口号；

协议中填入HTTP请求的协议，可以是http或https，我们一般用的都是http；

在方法中选择HTTP请求的方法，可用方法包括GET、POST、HEAD、PUT、OPTIONS、TRACE、DELETE；（我们项目中的接口方法使用的主要是GET和PUT，在目前易户网的项目中由于APP不直接和服务器交互，中间有一个统一接口平台，所以GET方法的接口请求也要使用POST进行交互。）

在路径中填入接口请求的URL；

请求参数：如果是GET方法的请求，请求参数直接写在URL后面即可；如果是POST的方法，则需要在Body Data中填入包含请求参数的数据（Body Data可以从Fiddler抓的包中获取，将Fiddler抓取的到数据包中Inspectors-TextView中的数据复制到Body data中即可）；

![img](https://img-blog.csdn.net/20160218090907151?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



\3.      在线程组中添加HTTP信息头管理器（在线程组上右击，添加-配置元件-HTTP请求信息头管理器）。

配置如下：

![img](https://img-blog.csdn.net/20160218090949078?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



\4.      添加[监听器](https://www.baidu.com/s?wd=%E7%9B%91%E5%90%AC%E5%99%A8&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)（在线程组上右击，添加-监听器-XXX）。

主要配置完成了，需要添加监听器以查看测试结果。一般我们使用查看结果树和聚合报告查看。

查看结果树：在结果树中会以树形结构展示每个接口请求的测试结果，包括取样器结果、请求、响应数据，在响应数据中可看到网络侧返回的数据，可以以接口文档中期望返回的参数做比较以判断返回是否正确。在结果树中通过颜色可以直观的看到接口测试的结果，绿色为通过，红色为失败。

![img](https://img-blog.csdn.net/20160218091010729?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



聚合报告：从聚合报告中我们可以获取到比较多的信息，包括接口的响应时间、吞吐量、通过率等，在接口的性能测试中很有用。

![img](https://img-blog.csdn.net/20160218091107720?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



表中参数的含义如下：

![img](https://img-blog.csdn.net/20160218091123939?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



\5.      添加响应断言（添加-断言-响应断言）。

查看测试结果时我们会发现有时候网络侧返回数据不符合要求时Jmeter给出的结果仍是通过的。这时候我们需要通过断言来判断网络的返回数据是否符合要求。

比如，我们可以添加一个断言来检查返回信息中是否包含关键字“errMsg”来判断错误信息：

首先添加一个相应断言，在要测试的响应字段中选择响应文本，在模式匹配规则中选择包括，勾选否，在要测试的模式中添加提一条，输入关键字“errMsg”；

意思是检查网络返回结果中不包含“errMsg”的才会判定为通过。

另外我们还可以从监听器中添加一个断言结果，在断言结果中可以查看断言的详细返回信息。

![img](https://img-blog.csdn.net/20160218091221846?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20160218091237371?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)





### 3.    其他补充

使用CSV Data Set Config定义变量。

举个例子，我们想要测试多用户登陆时要怎么做？可以在线程属性中配置多个线程，但是这样只是同一个用户多次登录，多个不同的用户需要输入不同的用户名密码。当然我们可以写多个登录的HTTP请求，每个请求输入不同的参数，但是这样太傻了。

这个时候我们可以通过CSV DataSet Config来定义两个变量代表用户名和密码，然后传入不同的值即可实现多用户的登录：

1、我们在登录的case中添加一个CSV DataSet Config（添加-配置元件-CSV Data Set Config）。

![img](https://img-blog.csdn.net/20160218091502021?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



*Filename：指定读取用户名和密码的文件，我们会将所有的用户名密码写到该文件中；*

*FileEncoding:：写入用户名密码的文件编码格式，不写的时候默认为ANSI*

*VaribleNames：定义文本文件中的变量名用户名=user和密码=pwd，变量之间逗号分隔。*

 

2、我们到上面Filename的路径下创建该文件并写入用户名密码，用户名和密码之间用逗号分开，不同用户名密码之间换行。

![img](https://img-blog.csdn.net/20160218091526428?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



3、到Jmeter的脚本中将原本的用户名密码改为变量名，由于我们使用的是Post方法，所以到Body Data中修改：

![img](https://img-blog.csdn.net/20160218091543966?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)