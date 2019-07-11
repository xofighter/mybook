本篇简单介绍fiddler工具，通过本篇学习，可以到达fiddler的基本使用。前面工具分类介绍，fiddler是一个独立的可以抓http请求的工具，软件小巧，功能强大。既可以用来抓HTTP请求包，也可以用来做接口测试。

1.下载和安装

这个过程很简单，百度搜索fiddler，下载直接安装就可以。



2.fiddler界面介绍

![img](https://img-blog.csdn.net/20170903155423383?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

浏览器打开我的csdn页面，打开fiddler,然后回到csdn页面刷新，大致可以的得到如上图的数据。大致分为三块，第一部分是经典的windows程序的菜单栏。左边是动态获取各种请求，这个叫协议区，可以显示每条协议的一些简单信息。右边是第三部分，主要是根据协议区的选择而变化。右边主要是左边请求的详情信息显示，主要有请求的信息和响应信息。



3. fiddler工作原理

fiddler工作原理和loadrunner其实是差不多的，fiddler就是一个代理服务，负责转发请求和转发响应，简单用下图可以描述。

![img](https://img-blog.csdn.net/20170903160354878?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

直接把fiddler看成一个代理，类似现实生活中的中介。只是负责转发请求和响应，所以，fiddler能够抓到包。



4.fiddler实现抓包过程

例如，打开一个陌生的网站，我们很想知道当前页面，调用了什么接口或者是通过什么请求方式进行发送给服务器。这里，利用csdn网站举例。

1）.打开浏览器，打开csdn页面，例如我的个人博客主页

2）.打开fiddler软件

3）.回到浏览器，刷新csdn页面，刷新，实际上就是给服务器发送了一个请求过程。

4）.观察fiddler左侧协议区，找到你刷新的页面地址，找到这个http请求，如下图

![img](https://img-blog.csdn.net/20170903160953692?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



5.fiddler做接口测试

fiddler不仅可以实现抓包，也可以做接口测试，我们来举例一个新浪天气查询的接口。这个接口的具体介绍，参考这篇文章。

1）.在fiddler点击右侧的Composer tab。

![img](https://img-blog.csdn.net/20170903161333059?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

2）.输入接口地址

![img](https://img-blog.csdn.net/20170903161530568?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

注：上面接口地址，直接把参数给拼接到URL中。

3）点击发送请求按钮，观察响应

点击右上角的Execute按钮，发送请求，在左侧抓包区域，找到这个请求，双击，可以看到请求响应内容。

![img](https://img-blog.csdn.net/20170903162108172?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU0MTk0Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

fiddler的基本使用介绍就到这里，一个很简单的抓包工具。需要提醒的是，这个工具也而已抓取手机app里面的请求数据包。具体和手机配置，请自行去百度一下，以后手机方面的测试工具，如果有机会，也会写文章出来，这里暂时不展开，下一篇，接口postman这个接口测试工具。

---------------------
作者：Anthony_tester 
来源：CSDN 
原文：https://blog.csdn.net/u011541946/article/details/77822962 
版权声明：本文为博主原创文章，转载请附上博文链接！