[TOC]

# 一、概述
​      先说说背景吧，Selenium是一个优秀的自动化测试框架，可以模拟浏览器的各种操作来代替人工操作。不同的浏览器有 不同的driver来驱动。之前项目中使用的firefoxDriver,chromeDriver有一个缺点，就是浏览器必须和我的项目在同一台机器上。而项目一般是部署在linux机器上，这样一来，有如下几个缺点：
1. linux必须安装firefox,chrome浏览器，操作十分麻烦，而且受Linux版本限制。本人安装firefox还好，下载后解压就可以了，而chrome下载后还需要执行一些别的安装操作，由于linux是openSuse11的，缺各种包，从未安装成功过。
2. 由于是linux系统，跑UI测试的时候，如果没有安装xmanager传输图形化界面，是看不到运行效果的。
3. 在Linux上firefox浏览器运行测试总会有各种各样的问题，例如driver连接7055端口超时，在windows环境下则没有出现过。
鉴于上述问题，因此，想将测试和浏览器进行分离，通过控制远程机器的浏览器运行测试，而不仅限于在项目所在机器上。Selenium-Server-StandAlone很好的解决了这个问题。

---------------------
# 二、RemoteWebDriver
​     相比于各种浏览器Driver，RemoteWebDriver提供了控制远程浏览器能力，通过搭配Selenium-Server-standalone的服务，彻底将浏览器和测试代码分离。核心思想是：客户端代码通过RemoteWebDriver，发送浏览器操作至远程机器上的server服务，间接的操作浏览器。

---------------------

# 三、操作步骤
1. 下载Selenium-Server-standalonej，下载地址:Selenium-Server-StandAlone。下载到的是一个可执行的jar文件，例如Selenium-Server-standalone-2.46.0.jar。
2. 将Selenium-Server-standalone-2.46.0.jar拷贝至目标机器上(推荐windows操作系统)。
3. 通过命令行运行`java -jar selenium-server-standalone-2.46.0.jar -timeout=20 -browserTimeout=60 -Dwebdriver.chrome.driver=D:\chromedriver\chromedriver.exe`
这样远端服务就启动了，可以接受客户端的请求。启动后控制台会显示服务的请求地址：http://127.0.0.1:4444/wd/hub。
客户端创建remoteWebDriver的时候，就需要设置这个地址。
![img](https://img-blog.csdn.net/2018071216110170?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0dBTUVsb2Z0OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
4. 由于我们使用的是chrome浏览器需要安装chrome浏览器，请自行安装。
5. 下载chromedriver。selenium是通过chromedriver来操作chrome浏览器的，因此需要安装chromedriver。下载地址：chromedriver。此外chromedriver和chrome版本对应关系如图所示：
![img](https://img-blog.csdn.net/20180712161933777?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0dBTUVsb2Z0OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

下载完成后，将exe文件放在启动参数-Dwebdriver.chrome.driver指定的位置即可。

---------------------

# 四、客户端操作
## 1. java代码示例
```java
/**
​     * 远程driver,默认使用chrome。
​     * 远端需启动selenium-server-standalone
​     * @param remoteUrl selenium-server服务端地址
​     * */
​    public WebDriver getDefaultRemoteDriver(String remoteUrl) throws Exception{
​        DesiredCapabilities capabilities = DesiredCapabilities.chrome();
​        capabilities.setJavascriptEnabled(true);
​        URL url = new URL(remoteUrl);
​        WebDriver driver = new RemoteWebDriver(url,capabilities);
​        driver.manage().timeouts().implicitlyWait(30, TimeUnit.SECONDS);

        return driver;
    }
```
之后的操作就和原来的firefoxDriver,chromeDriver一样了。	
## 2. python代码片段示例
```java
from selenium import webdriver;
from selenium.webdriver.common.desired_capabilities import DesiredCapabilities;

# 获取远程浏览器driver，使用自己指定的selenium-server
def getChromeRemoteDriver(remoteUrl):
​    return webdriver.Remote(command_executor=remoteUrl,desired_capabilities=DesiredCapabilities.CHROME)
```
---------------------
# 五、运行效果
## 1. 自动化测试平台点击运行测试，注意该平台部署在linux机器上，通过web访问。

![img](https://img-blog.csdn.net/20180712162707976?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0dBTUVsb2Z0OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 2. 远程机器上浏览器被打开，开始测试了

![img](https://img-blog.csdn.net/20180712163028604?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0dBTUVsb2Z0OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

# 附：
官网文档：[点这里](https://github.com/SeleniumHQ/selenium/wiki/RemoteWebDriver) 