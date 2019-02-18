一、下载工具包

百度搜索”fiddler 下载“ ，安装最新版本

![img](https://img-blog.csdn.net/20171127153738616?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpTWVuZ1JhbjEwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


下载的软件安装包为“fiddler_4.6.20171.26113_setup.exe”格式，双击安装。安装成功，在“开始”-“所有程序”，就会看见这样的图标，若是常用的话，也可以在桌面建一个快捷方式，如下图：

![img](https://img-blog.csdn.net/20171130163437307?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpTWVuZ1JhbjEwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

二、fiddler手机抓包原理

在本机开启了一个http的代理服务器，然后它会转发所有的http请求和响应。Fiddler 是以代理web 服务器的形式工作的，它使用代理地址:127.0.0.1，端口:8888。网络请求走fiddler，fiddler从中拦截数据，由于fiddler充当中间人的角色，所以可以解密https。因此，它比一般的firebug或者是chrome自带的抓包工具要好用的多。不仅如此，它还可以支持请求重放等一些高级功能。它还可以支持对手机应用进行http抓包的。本文就是手机抓包。

浏览器的代理设置默认是关着的，win7谷歌浏览器“设置--高级--系统--打开代理服务器--局域网设置--代理服务器”勾上。代理开关为开：可以抓到包，代理开关为关：抓不到包。

前提条件:
1).电脑需要安装Fiddler
2).测试手机需要支持Wifi
3).测试手机与电脑需要同一网络
4).所测APP需支持代理 

三、设置Fiddler

菜单：Tools-> Fiddler Options->Connections，勾选"Allow remote computers to connect" 

![img](https://img-blog.csdn.net/20171128105717593?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpTWVuZ1JhbjEwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

          注： 8888为默认端口号，可修改，但需注意两点，一是本机空闲端口，二是手机代理设置时端口要一致。

![img](https://img-blog.csdn.net/20171128105729816?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpTWVuZ1JhbjEwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)          

       查看本机的ip地址，在本机命令行输入：ipconfig   我的电脑有3个IP，都试了一下，172.18.53.82是有效的IP，其余2个是虚拟机的IP

![img](https://img-blog.csdn.net/20171130170101369?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpTWVuZ1JhbjEwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

      打开Fiddler,右上角有一个Online，这里也可以查看IP。如果和电脑的ip不一致时，以这里的为准。

![img](https://img-blog.csdn.net/20171130170112805?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpTWVuZ1JhbjEwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

四、 手机端进行参数配置

条件：保证手机和电脑都处于同一个网络。

Step1、给手机设置代理

![img](https://img-blog.csdn.net/20171130170301277?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpTWVuZ1JhbjEwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://img-blog.csdn.net/20171130170404697?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpTWVuZ1JhbjEwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

Step2、打开手机浏览器，输入ip:端口号=172.18.53.93:8888，点击前往，见下图：

![img](https://img-blog.csdn.net/20171130172428383?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpTWVuZ1JhbjEwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

Step3、打开一个下载证书的页面，如下，点击最下方的“FiddlerRoot certificate”按钮，下载证书。

![img](https://img-blog.csdn.net/20171130172854853?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpTWVuZ1JhbjEwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

Step4、点击按钮，进入下个页面，出现一个安装文件，点击“安装”。

![img](https://img-blog.csdn.net/20171130172820411?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpTWVuZ1JhbjEwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

Step5、跳转页面要求设置一个手机密码，就是开机密码，没有就自己设置一个，记住密码就行会。输入开机密码后，出现一个授权证书，如下，点击安装即可。

![img](https://img-blog.csdn.net/20171130173041410?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpTWVuZ1JhbjEwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

出现“已验证”字样，如下图。表示证书安装成功。

![img](https://img-blog.csdn.net/20171130173150739?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpTWVuZ1JhbjEwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

五、验证手机抓包

操作手机，电脑fiddler中会显示一些http信息，即为成功 

打开手机的浏览器，使用百度，输入“selenium”百度一下。查看Fiddler上的有请求数据。

![img](https://img-blog.csdn.net/20171130174311892?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpTWVuZ1JhbjEwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

结果如下：
![img](https://img-blog.csdn.net/20171130174843775?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvU2hpTWVuZ1JhbjEwNw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)