# 更高效更灵活的Web自动化测试——Airtest-Selenium

置顶 2018年07月26日 16:46:20 [weixin_42803971](https://me.csdn.net/weixin_42803971) 阅读数 5809



> 2018年三月，在全球的游戏开发者大会(GDC)上，网易游戏和Google一起发布了开源项目，网易游戏自研的自动化测试框架——Airtest-Project。Airtest-Project一开始提供了安卓上的两个跨引擎的自动化测试框架，airtest和poco，解决了市面上绝大多数移动游戏的安卓测试问题。立刻获得了很好的反响，并获得了一大批稳定的用户。随后，Airtest-Project迅速扩张了自己的业务范围，在ios和web的自动化测试包括微信小程序都取得了突破。可以说，Airtest-Project在国内的自动化测试中已经是独树一帜的地位。这篇文章我来介绍一下，Airteste-Project是如何进行web自动化测试的。

# Airtest-Selenium

Airtest-Selenium是Airtest-Project中web自动化测试的框架。Airtest-Selenium是基于selenium语法的封装，同时添加了airtest基于图像识别的部分api。接下来就来简单看看Airtest-Selenium是如何工作的。

### 小视频展示

先用一个小视频简单看一下在Airtest-IDE中进行web自动化测试的效果。

![airtest_module](https://user-images.githubusercontent.com/33257680/43248962-fcf06ac8-90eb-11e8-8587-8a696b1be925.gif)

### 简单介绍

从小视频中已基本可以看到Airtest-Selenium的大概功能了。在Airtest-IDE中，有一个selenium辅助窗，专门用于web自动化测试脚本的编辑。用户可以在这里直接进入web的自动化录制，也可以辅助定位元素自动生成selenium代码。当然对于网页结构易于改变的情况，也可以截取网页图片，用图像识别的方式来进行模拟点击。

用Airtest-IDE可以高效率低成本地迅速生成一个web自动化测试脚本，并且有多样的实现方法，可以根据实际情况来选择更适合的情况。配置本地环境也非常方便，只需下载最新的[Airtest-IDE](http://airtest.netease.com/)已经安装好chrome浏览器即可开始编辑你的自动化测试脚本了。

同时，可能很多人会对web应用在移动端的测试感兴趣，这里Airtest-Project对移动端的webview也做了全方面的支持。包括微信小程序和web浏览器，都可以用安卓原生框架poco来实现。

官网博客： http://airtest.netease.com/blog/tutorial/WechatSmallProgram/

官网博客的第一篇博客即是对微信小程序进行自动化测试的详细分享。至于浏览器应用，直接使用Airtest-Project的Android Poco即可，可前往[官网](http://airtest.netease.com/)了解。

### 技术框架

![1_web_framework](https://user-images.githubusercontent.com/33257680/43253010-7d795308-90f6-11e8-9490-a253ac122777.png)

Airtest-Selenium的录制基于Chrome Devtools Protocol这个协议，将用户操作转换为对应的selenium语法，同时对其进行了封装。用户也可以自由地加入selenium的语法。在运行后，会对每一步操作生成对应的报告。

Airtest-Selenium的图像识别功能则是基于airtest的图像识别模块，同时利用selenium本身提供的一些定位浏览器位置、大小的接口，然后用pynput模拟点击来实现的基于图像识别的api。

### 更多信息

本篇文章简单介绍了目前Airtest-Selenium所能做的工作，以及Airtest-Project对移动端web的支持。目前也已经有相当一部分用户开始使用我们的web自动化测试工具。欢迎大家来了解和使用我们的工具。最后贴一些链接和用户交流群：

Github仓库：https://github.com/AirtestProject/airtest-selenium （欢迎踊跃提issue）

官网链接： http://airtest.netease.com/

Airtest-Selenium快速上手教程： http://airtest.netease.com/docs/docs_AirtestIDE-zh_CN/1_quick_start/5_get_started_with_web_test.html