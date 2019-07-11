# 使用fiddler抓取手机上的HTTPS包

[![96](https://upload.jianshu.io/users/upload_avatars/3622259/9aab7695-65e4-413e-ae67-32e2ff13943f.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/96/h/96)](https://www.jianshu.com/u/8fd25a02e901) 

最近想抓取手机上app的数据包,在电脑上抓包可选的工具有很多, 比如**wireshark**,Linux命令行下有**tcpdump**等等工具。可是在这些工具在手机上都没法使用，这怎么搞



![img](https://upload-images.jianshu.io/upload_images/3622259-58848e89e884254d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/264/format/webp)


后来想了想能不能用网络代理抓包呢,说干就干。从网上查到，用fiddler可以代理手机的网络。

## 第一步下载fiddler

去下载一个fiddler，建议去官网下载[传送门](https://www.telerik.com/download/fiddler)。



![img](https://upload-images.jianshu.io/upload_images/3622259-f4615b4d11e4db87.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

fiddler

勾选协议,填写邮箱,然后下载就可以



## 第二步安装fiddler

双击下载的.exe文件,然后选择目录,next,next,完成安装

## 第三步安装CertMaker插件

fiddler默认生成的证书不能满足Android和iOS系统的要求,需要使用这个插件 [传送门](https://www.telerik.com/fiddler/add-ons) 往下找,找到图片的地方,点击下载,下载后,双击安装



![img](https://upload-images.jianshu.io/upload_images/3622259-4623113b3cc81f47.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

CertMaker



## 第四步配置fiddler代理

想要抓HTTPS的数据包,必须安装证书,



![img](https://upload-images.jianshu.io/upload_images/3622259-7d789925391e1cbc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

fiddler


点击tools->options,然后出现这个界面,按照我标注的1,2,3,4都勾选,中间会出现一些提示,都点**yes**



![img](https://upload-images.jianshu.io/upload_images/3622259-743eda534ce02daf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/536/format/webp)



------



![img](https://upload-images.jianshu.io/upload_images/3622259-7e8cbdaa3aa9b801.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/548/format/webp)

------



![img](https://upload-images.jianshu.io/upload_images/3622259-89aad703f4de31de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/539/format/webp)

ca2.png

------



![img](https://upload-images.jianshu.io/upload_images/3622259-bfd40396ad37918f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/349/format/webp)

ca_ok.png



最后出现这个对话框说明证书已经安装成功。

## 第五步配置代理(手机和电脑在同一个网络中)

重启fiddler，开始手机的配置。查看电脑的IP地址,我的是192.168.31.56然后打开手机的浏览器(苹果手机一定要用Safari),输入**IP地址:端口号** 我的是**192.168.31.56:8888** 这个根据具体情况设置



![img](https://upload-images.jianshu.io/upload_images/3622259-9b0486670a684700.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/750/format/webp)

iPhone





![img](https://upload-images.jianshu.io/upload_images/3622259-b5cce86fcb3de1ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/750/format/webp)

iPhone



下载证书,点击允许,安装证书.

# 重点、重点、重点

**这时候一定要把安装的证书设置为信任的证书**
去手机的设置->通用->关于本机->证书新人设置,把刚才安装的证书设置为信任的证书。再去网络设置中,给WiFi设置代理(电脑和手机一定在同一个网络中)



![img](https://upload-images.jianshu.io/upload_images/3622259-33e071e66c26266e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/750/format/webp)

iPhone


然后打开手机上的app,在fiddler中就可以看到,http的数据了,打开一个HTTPS的连接,可以看到,数据是没有加密的



![img](https://upload-images.jianshu.io/upload_images/3622259-701385010b89fdd2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

fiddler



这时候如果没有抓到数据包,或者错误,应该是证书配置有问题,把之前的证书清除,按照步骤重新配置一遍.重启fiddler,重新在手机安装证书.





![img](https://upload-images.jianshu.io/upload_images/3622259-9f53b6e646f19abc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/800/format/webp)

清除证书

## 总结

fiddler代理可以抓到大多数的数据包,但不是所有的数据包都能抓到。fiddler并不支持全部协议，目前已知的有http2、tcp、udp、websocket等，如果应用走了以上协议，那么fiddler肯定是抓不到的。因为fiddler是基于.net framework实现的，因为.net framework不支持http2，所以fiddler无法抓取http2。
还有一种情况,app使用自带的证书,我们给手机安装的证书,app不信任.这样也是无法抓到的。fiddler抓包的原理是中间人攻击，也就是说，两头瞒，欺骗客户端&&欺骗服务器端，如果https证书写死在app里，app只信任自己的证书，fiddler没法瞒客户端了，因此fiddler也就抓取不到包了。



------------------------

# [fiddler 手机抓包（含https） 完整流程](https://www.cnblogs.com/haoBo956/p/10177933.html)



第一部分：下载并安装fiddler

一.使用任一浏览器搜索【fiddler下载安装】，并下载fiddler 安装包。

![img](https://img2018.cnblogs.com/blog/833240/201812/833240-20181226104915981-1007303842.png)

二.fiddler安装包下载成功后，将下载的fiddler压缩包解压到自定义文件夹【fiddler】或者解压到当前文件夹下，双击文件夹中的【fiddler.exe】文件

![img](https://img2018.cnblogs.com/blog/833240/201812/833240-20181229105339170-292880006.png)

三.双击文件后，可以按照下面的流程进行安装。如果是汉化版的那就更简单明了了

![img](https://img2018.cnblogs.com/blog/833240/201812/833240-20181229110200668-216054439.png)

![img](https://img2018.cnblogs.com/blog/833240/201812/833240-20181229110216044-816219077.png)

四.打开已经安装好的fiddler，如果有更新提示框弹出，可以点击：NO 或者 否，关闭更新。

五.从界面上我们可以看到，fiddler了一打开就在不断地抓取网络请求了，但是这些不是我们需要的网络请求。

![img](https://img2018.cnblogs.com/blog/833240/201812/833240-20181229113309355-1226724294.png)

第二部分：配置fiddler，并进行手机抓包。

一.由于fiddler只默认抓取HTTP的请求，若想抓取HTTPS的请求，则需要设置HTTPS的各项值：

 第一步：在fiddler菜单项选择Tools  ->  Options -> HTTPS

 第二步：勾选【Decrypt HTTPS traffic 】【Ignore server certificate errors】，下拉框默认：【from all processes】 即可

第三步：点击右边的【Actions】，选择【Trust Root Certificate】点击，弹出窗点击【Yes】按钮即可。

第四步：点击【OK】保存

![img](https://img2018.cnblogs.com/blog/833240/201812/833240-20181229141250603-2039047114.png)

![img](https://img2018.cnblogs.com/blog/833240/201812/833240-20181229141419317-640976152.png)

二.设置fiddler connections的值，允许fiddler远程连接

第一步：在fiddler菜单项选择Tools  ->  Options -> connections

第二步：勾选【|Allow remote computers to connect】

第三步：点击【ok】保存

 ![img](https://img2018.cnblogs.com/blog/833240/201812/833240-20181229144321936-1480300403.png)

 

**（重点：记住设置完上述的值后，一定要重启Fiddler，不重启Fiddler 上述设置的值将不会生效）（另：Fiddler 很多地方设置了值都需要重启，如果发现自己的设置的东西没有生效，先重启Fiddler再看是否已经生效）**

 

三.在手机上进行相应的设置，为手机抓包做准备

1.查看自己本机的IP

方法一：电脑——左下角点击Windows图标——输入cmd，打开cmd面板，输入：ipconfig ，查看本机IP地址

![img](https://img2018.cnblogs.com/blog/833240/201812/833240-20181229163526937-2145086180.png)

方法二：在fiddler主界面，将鼠标移到【online】上面，就可以查看本地的IP地址了，如果你的fiddler没有显示【online】，可以通过【fiddler菜单——View——Show Toolbar】将【Show Toolbar】勾选中，就会显示【Online】信息了。![img](https://img2018.cnblogs.com/blog/833240/201812/833240-20181229164034301-409931520.png)

2.手机安装证书。（ios设置和Android设置基本一样）

前提条件：

手机和电脑要处于同一网络条件下（可以理解为：使用同一个WiFi）

fiddler的默认端口是：8888，不需要进行修改，使用默认的就可以。

一个手机可以安装多个证书，但是每安装的一个证书里面都设置有IP地址，所以：安装的证书和电脑IP是一一对应的，当前的这个证书只能针对某一台电脑使用，更换电脑后，该证书将不能使用，只能重新安装与更换的电脑的IP相同的证书才能使用。

第一步：手机下载证书。打开手机的浏览器，输入：【IP:8888】下载证书。

（注：中间的冒号一定要使用英文的冒号，中文的冒号是错误的）

例如：浏览器输入【http://192.168.xxx.xxx:8888】或者【192.168.xxx.xxx:8888】（这个地方的IP就是你电脑的IP）

![img](https://img2018.cnblogs.com/blog/833240/201901/833240-20190103110840567-1113411091.png)

 

第二步：安装证书.

 有的手机可以直接点击已下载的文件进行安装，有的手机则不行。

如果不能直接安装证书，我们可以通过以下方法来安装证书。

1.Android：安装证书。由于安装系统众多，设置的方法不尽相同，下面几个方法以供参考。

方法一：手机——设置——搜索【证书】二字——选择：安装证书或者证书管理：点击安装证书，在你的众多文件里面去选择刚刚下载的fiddler的证书，点击安装

**（注：选择安装的文件后，需要输入手机的锁屏密码。Android一定要有锁屏密码才能安装证书）**

 ![img](https://img2018.cnblogs.com/blog/833240/201812/833240-20181229175457004-1370526045.png)

方法二：在浏览器里面，直接打开已经下载的文件，安装即可，安装步骤是：先输入手机锁屏密码——后到上图为证书命名界面。

证书安装好后，查看已信任证书：具体位置在【安全——更多安全设置——加密和凭据——受信任的凭据】

![img](https://img2018.cnblogs.com/blog/833240/201812/833240-20181229183042406-1077589822.png)

 

2.ios安装证书：直接点击已下载的文件安装即可，安装文件成功后，需要在【设置——通用——关于本机——证书信任设置】开启证书信任。

第三步：为手机设置代理.（ios和安卓差不多）

设置——无线网络（WLAN）——WLAN——长按已连接的WiFi 去修改网络——在高级选项里面——选择【手动代理】——出现以下界面，按图所示操作即可。

（或者长按通知栏WiFi按钮进入WiFi界面）

（有的机型需要先添加网络才能对网络进行修改，这个看个人手机情况）

![img](https://img2018.cnblogs.com/blog/833240/201812/833240-20181229181707456-2135254223.png)

第四步：访问手机浏览器或者任一应用就可以在fiddler里面查看到抓取的请求了

最后查看已经抓取到的https的请求：

![img](https://img2018.cnblogs.com/blog/833240/201812/833240-20181229183833938-1287074937.png)

 