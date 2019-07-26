# AirTest学习一，快速上手，使用AirTest录制一个安卓测试脚本

### 我觉得还是官方文档比较靠谱

地址：http://airtest.netease.com/docs/cn/index.html

### 什么是AirTest，AirTest能做什么

Airtest Project诞生的初衷，源于我们对于游戏自动化测试框架的渴求。相比于普通的软件测试，手游测试由于手机机型繁多，对于性能测试、兼容性测试的需求很高，同时可能还有大量重复性的内容例如新手流程需要进行反复测试。自动化测试在一定程度上，可以减轻测试人员的负担、缩短bug发现周期、提高测试效率。

为了降低自动化测试的门槛，用更少的精力产出更多高效的自动化脚本，来达到提升测试效率的目的，网易推出了一整套解决方案，项目命名为Airtest Project。

现在可以使用AirTest在Windows、iOS、Android上测试游戏和各类应用

### 首先安装AirTest IDE

地址：http://airtest.netease.com/

### 为安卓app录制一个简单的登陆脚本（基于AirTest的图像识别录制）

建议去看官方文档,写的非常详细:http://airtest.netease.com/docs/docs_AirtestIDE-zh_CN/1_quick_start/1_how_to_write_the_first_script_for_your_game.html

1.安装adb
2.连接安卓手机，并开启usb调试模式
3.打开AirTest IDE,先点击refresh ADB按钮，之后可以看到已连接上的安卓设备





![img](https://upload-images.jianshu.io/upload_images/14474902-1bff1983e94f8e6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



4.选择其中一个安卓设备，点击connect按钮，可以看到ide上显示出手机的界面





![img](https://upload-images.jianshu.io/upload_images/14474902-f716717e1bdb038a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

5.通过adb成功连接上安卓设备后，就可以开始录制脚本了，这里xwallet app为例，录制一个简单的登陆脚本。

6.点击IDE 左上方的录制按钮（一个摄影机的按钮），开始录制脚本，然后在Device Screen中按照正常的手机操作就可以了，根据你的操作，代码会自动生成。



![img](https://upload-images.jianshu.io/upload_images/14474902-e1de4c4e646d20ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

7.下面是一些函数的作用

```
touch(): 点击操作，根据录制脚本时所截取的图像进行点击
wait(): 可以等待画面中某个图片出现，如果在指定的时间范围内匹配到了该图片，就会返回图片所处位置的焦点，否则抛出TargetNotFoundError异常
swipe():可以从一个位置滑动到另外一个位置
exists():判断画面中是否存在某个图片,如果匹配到图片，返回图片所处位置的焦点，否则返回False
text():输入文本
sleep():线程挂起
assert_exists():断言图片存在于当前画面上
assert_not_exists():断言图片不存在
assert_equal():断言数值相等
assert_not_equal():断言数值不等

# assert_exists()与assert_equal()的区别在于，assert_exists断言的对象是图片，assert_equal断言的对象是数值
```

8.录制好的登陆脚本解释



![img](https://upload-images.jianshu.io/upload_images/14474902-4866723aba2abc94.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



---------------------

# AirTest学习二，命令行执行脚本



### 同样，先安利一波官方文档地址

http://airtest.netease.com/docs/docs_AirtestIDE-zh_CN/4_run_script/run_script.html#

### 为什么要使用命令行来执行脚本

想同时用多个命令行运行多台手机、多个脚本等情况，以及对于一些Python开发者来说，可能需要在脚本中使用其他功能强大的Python第三方库。因此推荐在本地python环境中安装airtest和pocoui，然后用命令行运行脚本。

### 准备环境

首先必须要有python环境

使用 pip 安装Airtest模块

```
pip install airtest
```

注意：在Mac/Linux系统下，需要手动赋予adb可执行权限，否则可能在执行脚本时遇到Permission denied的报错，我就碰到了

```
# mac系统
cd {your_python_path}/site-packages/airtest/core/android/static/adb/mac
# linux系统
# cd {your_python_path}/site-packages/airtest/core/android/static/adb/linux
chmod +x adb
```

若在安装opencv-contrib-python库时，提示找不到我们指定的3.2.0.7版本时，可以考虑更新你的pip到最新之后再进行重试，官网文档提到最要不要使用pyhont3.7版本，建议使用稳定的python3.6.5版本，不过我亲测3.7可用。

使用 pip 安装poco框架

```
pip install pocoui    # 请注意库的名称为pocoui，不要填错
```

环境部署完成后，我们就能够脱离AirtestIDE，在不同的宿主机器和被测平台上运行脚本了。

### 执行命令

```
# 这两个命令行的效果是相同的，我们用airtest运行了一个叫做untitled.air的脚本
>airtest run untitled.air --device Android:///手机设备号 --log log/
>python -m airtest run untitled.air --device Android:///手机设备号 --log log/

# 可以使用python sys模块查看site-packages文件夹所在的路径
import sys
print(sys.path)
```

参数说明：
airtest run 后面接的的脚本文件的路径。
--device，是我们的手机设备。
--log，log输出目录。

### 关于--device的说明

在刚才的命令行中使用的--device参数，传入的是一个设备字符串，以安卓设备为例，字串完整定义如下：

```
Android://<adbhost[localhost]>:<adbport[5037]>/<serialno>
```

其中，adbhost是adb server所在主机的ip(本地host为127.0.0.1)，adb port默认是5037，serialno是android手机的序列号。更多adb的方面的内容请参考文档developer.google。

在平时的脚本运行中，我们一般可以这样写：

```
# 什么都不填写，会默认取当前连接中的第一台手机
Android:///
# 连接本机默认端口连的一台设备号为79d03fa的手机
Android://127.0.0.1:5037/79d03fa
# 连接一个Windows窗口，窗口句柄为123456
Windows:///123456
# 连接一个Windows窗口，窗口名称匹配某个正则表达式
Windows:///?title_re=Unity.*
# 连接iOS手机
iOS:///127.0.0.1:8100
```

### 生成测试报告

```
"""
参数说明
--log_root: 指定脚本执行完毕后生成log文件夹的路径
--outfile: 指定输出的html文件路径
--lang: 指定输出的语言，zh为中文
"""
airtest report untitled.air --log_root log/ --outfile log/log.html --lang zh
```

小礼物走一走，来简书关注我



--------------

# AirTest学习四，录制Web测试脚本



## 国际惯例，先推荐一波官方文档

http://airtest.netease.com/docs/docs_AirtestIDE-zh_CN/1_quick_start/5_get_started_with_web_test.html

## 1. Airtest-Selenium测试框架原理图

[`Selenium`](https://seleniumhq.github.io/selenium/docs/api/py/api.html)是一款web自动化测试框架，它支持各种浏览器，包括 Chrome，Safari，Firefox 等主流浏览器。

得益于Selenium对Python的良好支持，我们在Airtest Project中添加了Airtest-Selenium插件，我们可以使用AirtestIDE进行**web自动化**脚本录制、脚本运行和报告生成。本文将介绍如何使用AirtestIDE进行web自动化测试。



![img](https://upload-images.jianshu.io/upload_images/14474902-020ad367c7d2d39e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/494/format/webp)

## 录制Web测试脚本步骤

- 本地安装[最新版AirtestIDE](http://airtest.netease.com/changelog.html)以及Chrome浏览器；
- 开启Selenium辅助窗：“窗口”-“Selenium Window”



![img](https://upload-images.jianshu.io/upload_images/14474902-cdd76fdbe801268e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

- 设置chrome路径，Options - Settings - Selenium - Chrome Path



![img](https://upload-images.jianshu.io/upload_images/14474902-980e466d7bd3eb62.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



![img](https://upload-images.jianshu.io/upload_images/14474902-227809474cd39532.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

- 开启浏览器并生成初始化代码：点击一个地球图案的按钮，开启浏览器，然后点击编辑框中弹出的提示，生成初始化代码；

```
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from airtest_selenium.proxy import WebChrome
driver = WebChrome()
driver.implicitly_wait(20)
```



![img](https://upload-images.jianshu.io/upload_images/14474902-7231a93816639e5c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

- 生成访问待测试网址的代码：浏览器中进入待测试的网页-点击辅助窗的start_web；
- 开始Airtest-Selenium脚本录制：点击录制按钮在浏览器进行操作
- 结束录制，运行脚本：点击运行脚本按钮
- 运行结束后，点击报告按钮，查看报告



![img](https://upload-images.jianshu.io/upload_images/14474902-a09b6b13b95abdc8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

## selenium window中的按钮功能说明

- ### Inspect类按钮

这一类按钮点击后，不会直接生成代码；浏览器会进入inspect模式，用户可以在浏览器中选择自己想要操作的元素进行点击，然后编辑框内会生成代码定位到对应元素，并生成对应操作的代码。





![img](https://upload-images.jianshu.io/upload_images/14474902-d2fed5173066d686.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/564/format/webp)

Inspect按钮: 点击这个按钮后，选取页面元素，会生成一个定位到该元素的语句。
touch按钮：这个按钮，点击后会生成点击对应元素的代码。
text按钮：点击这个按钮，会生成一条在指定元素内输入文本的语句，生成代码后，用户需自行在内部填入需要输入的文本。
assert按钮：assert按钮在点击后，生成一条assert页面元素是否存在的语句。这是一条Airtest-Selenium封装的语句，在运行时会尝试用参数代表的方法去寻找元素，如果找到会返回True，如果没找到会返回False。

- ### Airtest图像识别类按钮（mac os无法使用该功能）

这一类按钮，点击后进入截取图像模式，对当前屏幕进行框选区域并截图。



![img](https://upload-images.jianshu.io/upload_images/14474902-bb5a2f1fd1cfc357.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/552/format/webp)

airtest_touch: 点击该按钮开启截屏模式，对当前屏幕进行截屏，可从浏览器中截取区域图片。表示对截取图片进行点击。
assert_template: 点击该按钮后，对当前屏幕截图。表示断言当前浏览器存在该图片。

注意：mac os上无法使用这两个按钮，windows系统才能使用



![img](https://upload-images.jianshu.io/upload_images/14474902-267a9818371648fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/840/format/webp)

- ### 直接生成代码按钮

这一类按钮，点击后直接生成代码。不需多余操作，不过部分按钮会根据当前浏览器实际情况生成不同的参数。



![img](https://upload-images.jianshu.io/upload_images/14474902-36c42473dc42a9cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/554/format/webp)

start_web: 点击该按钮后生成当前浏览器所访问的页面地址的语句。
snapshot: 点击该按钮后生成对当前页面进行截图的语句。
new_tab: 点击该按钮生成一条跳转在最新出现的标签页的语句，一般用在一个生成新的标签页的语句后面。
previous_tab: 点击该按钮生成一条跳转到当前标签页之前的标签页（父标签）的语句。
back: 点击该按钮，生成后退到上一个页面的语句。
forward: 点击该按钮，生成前进到下一个页面的语句。