[全面超越Appium，使用Airtest超快速开发App爬虫](https://www.cnblogs.com/xieqiankun/p/use_airtest.html)

想开发网页爬虫，发现被反爬了？想对 App 抓包，发现数据被加密了？不要担心，使用 Airtest 开发 App 爬虫，只要人眼能看到，你就能抓到，最快只需要2分钟，兼容 Unity3D、Cocos2dx-*、Android 原生 App、iOS App、Windows Mobile……。

Airtest是网易开发的手机UI界面自动化测试工具，它原本的目的是通过所见即所得，截图点击等等功能，简化手机App图形界面测试代码编写工作。

爬虫开发本着天下工具为我所用，能让我获取数据的工具都能用来开发爬虫这一信念，决定使用Airtest来开发手机App爬虫。

## 安装和使用

由于本文的目的是介绍如何使用Airtest来开发App爬虫，那么Airtest作为测试开发工具的方法介绍将会一带而过，仅仅说明如何安装并进行基本的操作。

### 安装Airtest

从Airtest官网：[https://airtest.netease.com](https://airtest.netease.com/)下载Airtest，然后像安装普通软件一样安装即可。安装过程没有什么需要特别说明的地方。Airtest已经帮你打包好了开发需要的全部环境，所以安装完成Airtest以后就能够直接使用了。

Airtest运行以后的界面如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-13-10-30.png)

### 连接手机

以Android手机为例，由于Airtest会通过adb命令安装两个辅助App到手机上，再用adb命令通过控制这两个辅助App进而控制手机，因此首先需要确保手机的`adb调试`功能是打开的，并允许通过adb命令安装App到手机上。

启动Airtest以后，把Android手机连接到电脑上，点击下图方框中的`refresh ADB`：

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-13-16-45.png)

此时在Airtest界面右上角应该能够看到手机的信息，如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-13-17-51.png)

点击`connect`按钮，此时可以在界面上看到手机的界面，并且当你手动操作手机屏幕时，Airtest中的手机画面实时更新。如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-13-21-40.png)

对于某些手机，例如小米，在第一次使用Airtest时，请注意手机上将会弹出提示，询问你是否允许安装App，此时需要点击允许按钮。

### 打开微信

先通过一个简单的例子，来看看如何快速上手Airtest，稍后再来详解。

例如我现在想使用电脑控制手机，打开微信。

此时，点击下图中方框框住的`touch`按钮：

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-13-27-32.png)

此时，把鼠标移动到Airtest右边的手机屏幕区域，鼠标会变成十字型。在微信图标的左上角按下鼠标左键不放，并拖到微信右下角松开鼠标。此时请注意中间代码区域发生了什么变化，如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-13-30-15.png)

好了。以上就是你需要使用电脑打开微信所要进行的全部操作。

点击上方工具栏中的三角形图标，运行代码，如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-13-32-29.png)

代码运行完成以后，微信被打开了。

### 界面介绍

在有了一个直观的使用以后，我们再来介绍一下Airtest的界面，将会更加有针对性。

Airtest的界面如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-14-04-37.png)

这里，我把Airtest分成了A-F6个区域，他们的功能如下：

- A区：常用操作功能区
- B区：Python代码编写区
- C区：运行日志区
- D区：手机屏幕区
- E区：App页面布局信息查看区
- F区：工具栏

A区是常用的`基于图像识别`的屏幕操作功能，例如：

- `touch`: 点击屏幕元素
- `swipe`: 滑动屏幕
- `exists`: 判断屏幕元素是否存在
- `text`: 在输入框中输入文字
- `snashot`: 截图
- ……

一般来说，是点击A区里面的某一个功能，然后在D区屏幕上进行框选操作，B区就会自动生成相应的操作代码。

B区用来显示和编写Python代码。在多数情况下，不需要手动写代码，因为代码会根据你在手机屏幕上面的操作自动生成。只有一些需要特别定制化的动作才需要修改代码。

D区显示了手机屏幕，当你操作手机真机时，这个屏幕会实时刷新。你也可以直接在D区屏幕上使用鼠标操作手机，你的操作动作会被自动在真机上执行。

F区是一些常用工具，从左到右，依次为：

1. 新建项目
2. 打开项目
3. 保存项目
4. 运行代码
5. 停止代码
6. 查看运行报告

其中1-5很好理解，那么什么是查看运行报告呢？

当你至少运行了一次以后，点击这个功能，会自动给你打开一个网页。网页如下图所示，这是你的代码的运行报告，详细到每一步操作了什么元素。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/launch_report.png)

通过截图功能操作手机虽然方便，但是截图涉及到分辨率的问题，代码不能在不同的手机上通用。所以对于A区的功能，做点简单操作即可，不用深入了解。

更高级的功能，需要通过E区实现。

## 基于App布局信息操作手机

### 初始化代码

App的布局信息就像网页的HTML一样，保存了App上面各个元素的相对位置和各个参数。对于一个App而言，在不同分辨率的手机上，可能相同的元素有着不同的坐标点，但是这个元素的属性参数一般是不会变的。因此，如果使用元素的属性参数来寻找并控制这个元素，就能实现在不同分辨率手机上的精确定位。

App的布局信息的格式与App的开发环境有关。点击F区的下拉菜单，可以看到这里能够指定不同的App开发环境。其中的`Unity`、`Cocos-*`等等一般是做游戏用的，`Android`是安卓原生App，`iOS`是苹果的App……如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-14-17-32.png)

以手机版知乎为例，由于它是Android原生的App，所以在F区下拉菜单选择`Android`，此时注意B区弹出提示，询问你是否要插入poco初始代码到当前输入光标的位置，点击`Yes`，如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-14-25-17.png)

此时，B区自动插入了一段代码，如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-14-27-11.png)

### 定位并点击

现在，点击E区的锁形图标，如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-14-39-30.png)

锁形图标激活以后，你再操作D区的屏幕，点击`知乎`App下面的`知乎`两个字，会发现屏幕上被点击的App并不会打开。但E区和C区却发生了变化，如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-14-45-24.png)

其中E区显示的树状结构就是当前屏幕的布局信息，这与Chrome开发者工具里面显示的HTML结构如出一辙。C区显示的是当前被我点中的元素的信息。

请注意在这些元素信息中，有一个`text`属性，它的值为`知乎`。那么，这个属性就可以作为一个定位元素，于是可以在B区编写代码：

```
poco(text="知乎").click()
```

写完代码以后运行程序，可以看到知乎App被打开了。如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-14-48-47.png)

> 注意，如果你发现手机真机显示的界面与Airtest屏幕显示的手机界面不一致，可能是因为Airtest的屏幕被你锁定了。在F区点一下锁形图标，取消锁定，Airtest中的手机屏幕就会更新了。

### 定位并输入

打开知乎以后，我想使用知乎的搜索功能，那么继续，把锁形图标激活，然后点击知乎顶部的搜索框，如下图所示：

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-15-06-41.png)

继续看C区显示的搜索框属性，可以看到这里有一个`name`属性，它的值是`com.zhihu.android:id/input`，还有一个`text`属性，它的值为`蔡徐坤任 NBA 新春贺岁大使`。能不能像前面打开知乎一样，使用`text`这个属性呢？也行，也不行。说它行，是因为你这么做确实现在能工作；说它不行，因为这是知乎的热门搜索关键词，随时会改变。你今天使用这一句话成功了，明天热门关键词变化了，那么你的代码就无法使用了。所以此时需要使用`name`这个属性。

常见的基本上不会变化的属性包含但不限于：`name` `type` `resourceId` `package`。

另外还有一点，知乎首页的这个搜索框，实际上是不能输入内容的，当你点击以后，会跳转到另一个页面，如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-15-11-17.png)

因此你需要先点击一下这个输入框，跳转到真正的搜索界面：

```
poco(name="com.zhihu.android:id/input").click()
```

在真正的搜索界面如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-15-12-50.png)

可以看到，`name`属性的值依然是`com.zhihu.android:id/input`，此时就可以输入内容了。

输入内容使用的方法为`set_text`，用法为：

```
poco(name="com.zhihu.android:id/input").set_text('古剑奇谭三')
```

### 定位并筛选

输入了搜索关键词以后，再来看看当前页面，搜索出现了三个结果：

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-15-17-58.png)

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-15-18-36.png)

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-15-19-14.png)

通过对比这三个结果的属性信息，发现他们的`name`属性都是相同的，而`text`不同。如果像下面这样写点击动作：

```
poco(name='com.zhihu.android:id/magi_title').click()
```

那么默认就会点击第一个搜索结果。

如果我想点击第二个搜索结果怎么办呢？可以这样写代码：

```
poco(name='com.zhihu.android:id/magi_title', text='古剑奇谭（电视剧）').click()
```

或者你也可以像列表一样使用索引定位：

```
poco(name='com.zhihu.android:id/magi_title')[1].click()
```

这两种写法的前提，都是我们已经知道了每个结果分别是什么。假设现在我就想搜索`古剑奇谭三`，但我不知道搜索结果是第几项，又应该怎么办呢？此时还可以使用正则表达式：

```
poco(name='com.zhihu.android:id/magi_title', textMatches='^古剑奇谭三.*$').click()
```

### 滑动屏幕

进入搜索结果以后，需要查看下面的各种问题，此时就需要不断向上滑动屏幕。这里有一点需要特别注意，Airtest只能获取当前屏幕上的元素布局信息，不在屏幕上的内容是无法获取的。这一点和Selenium是不一样的。

滑动屏幕使用的命令为`swipe`，滑动屏幕需要使用坐标信息。但这种坐标和屏幕分辨率无关。这里的`坐标`定义为：(x, y)，其中x为横坐标，y为纵坐标。屏幕左上角为(0, 0)，屏幕右下角为(1, 1)，从左向右，横坐标从0逐渐增大到1，从上到下，纵坐标从0逐渐增大到1。

现在我要把屏幕向上滑动，那么在真机上面，我是先按住屏幕下方，然后把屏幕向上滑动，所以代码可以这样写：

```
# poco.swipe(起点坐标，终点左边)
poco.swipe([0.5, 0.8], [0.5, 0.2])
```

方向示意图如下图所示：

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-15-42-43.png)

在一般情况下：

- 向上滑动，只需要改动纵坐标，且起点值大于终点值
- 向下滑动，只需要改动纵坐标，且起点值小于终点值
- 向左滑动，只需要改动横坐标，且起点值大于终点值
- 向右滑动，只需要改动横坐标，且起点值小于终点值

在爬虫开发中，涉及到的Airtest操作基本上已经介绍完毕。

### 单独使用Python控制手机

在Airtest操作手机虽然方便，但是不可能在每一台电脑上都安装Airtest吧。所以需要想办法把代码从Airtest这个程序中分离出来。

Airtest基于Python的一个开源库Poco开发，而在Airtest的B区写的Python代码，实际上就是Poco的代码。所以只要安装Poco库，就可以在Python中直接控制手机。

安装Poco库的命令为：

```
pip install pocoui
```

这个库依赖的东西有点多，安装稍稍慢一些。安装完成以后，我们把代码复制到PyCharm中，如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-16-59-52.png)

运行这段代码，如果是Linux或者macOS的用户，请注意看运行结果是不是有报错，提示adb没有运行权限。这是因为随Poco安装的adb没有运行权限，需要给它添加权限，在终端执行命令：

```
# chmod +x 报错信息中给出的adb地址

chmod +x /Users/kingname/.local/share/virtualenvs/ZhihuSpider/lib/python3.7/site-packages/airtest/core/android/static/adb/mac/adb(实际执行时请换成你的地址)
```

命令运行完成以后再次执行代码，可以看到代码运行成功，手机被成功控制了，如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-17-00-50.png)

### 如何获取屏幕文字

由于Airtest的编辑器中的代码运行后无法正常打印出中文，因此后面的代码都直接在PyCharm中执行。

既然要做爬虫，就需要获取手机上的文字内容。回到搜索页面，我想知道“古剑奇谭”三这个关键字能搜索出多少条结果，每条结果有多少个讨论，如下图所示：

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-15-50-43.png)

此时我们需要做两件事情：

1. 分别查看每一个搜索结果
2. 获取屏幕上的文字

E区的树状结构如下图所示：

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-15-54-25.png)

每一个搜索结果的标题作为text属性的值，在`name='com.zhihu.android:id/magi_title'`对应的元素中；每一个搜索结果的讨论数作为text属性的值，在`name='com.zhihu.android:id/magi_count'`对应的元素中。

最直接的做法就是分别获取三个标题和三个讨论数，然后把它们合并在一起：

```
title_obj_list = poco(name='com.zhihu.android:id/magi_title')
title_list = [title.get_text() for title in title_obj_list]

discuss_obj_list = poco(name='com.zhihu.android:id/magi_count')
discuss_list = [discuss.get_text() for discuss in discuss_obj_list]

for title, discuss in zip(title_list, discuss_list):
    print(title, discuss)
```

运行效果如下图所示：

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-17-05-27.png)

但是这种做法实际上是很危险的，假设会有某一个很生僻的搜索结果，只有标题没有讨论数，那么这样分开抓取再组合的做法，就会导致最后匹配错位。所以合理的做法是先抓大再抓小。每一组标题和讨论数，他们都有自己的父节点，如下图箭头所指向的三个`android.widget.LinearLayout`:

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-16-07-11.png)

那么现在，使用先抓大再抓小的技巧，先把每一组结果的父节点抓下来，再到每一个结果里面分别获取标题和讨论数。

然而这个父节点又怎么获取呢？如下图所示，这个父节点每一个属性值都没有什么特殊的，写任何一个都有可能与别的节点撞上。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-17-15-07.png)

此时，最简单的办法，就是在E区，双击父节点。定位代码就会自动添加，如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-17-16-51.png)

这个定位代码看起来非常复杂，但实际上它的内在逻辑非常简单，就是从顶层一层一层往下找而已。

自动生成的定位代码如下：

```
poco("android.widget.LinearLayout").offspring("com.zhihu.android:id/action_bar_root").offspring("com.zhihu.android:id/parent_fragment_content_id").offspring("android.support.v7.widget.RecyclerView").child("android.widget.LinearLayout")[0]
```

在这个自动生成的定位代码中，我们看到了`offspring`、`child`这两种方法。其中`child`代表子节点，`offspring`代表孙节点、孙节点的子节点、孙节点的孙节点……。简言之，使用`child`只会在子节点中搜索需要的内容，而使用`offspring`会像文件夹递归一样把里面的所有节点都遍历一次，直到找到符合条件的属性为止。显然，offspring速度会比child慢。

实际上，我们可以对这个定位代码做一些精简：

```
poco("com.zhihu.android:id/parent_fragment_content_id").offspring("android.support.v7.widget.RecyclerView").child("android.widget.LinearLayout")[0]
```

这个精简的方法，与从Chrome复制的XPath中进行精简是一样的逻辑，根本原则就是找到“独一无二”的属性值，然后用这个属性值来进行定位。

由于我点击的是第一个搜索结果，所以定位代码的最后有一个`[0]`。现在由于需要获得所有搜索结果的内容，所以应该去掉`[0]`而使用for循环展开，然后获取里面的内容：

```
result_obj = poco("com.zhihu.android:id/parent_fragment_content_id").offspring("android.support.v7.widget.RecyclerView").child("android.widget.LinearLayout")
for result in result_obj:
    title = result.child(name='com.zhihu.android:id/magi_title').get_text()
    count = result.child(name='com.zhihu.android:id/magi_count').get_text()
    print(title, count)
```

运行效果如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-17-30-30.png)

## 控制多台手机

当我们在电脑上插入多个Android手机时，执行命令：

```
adb devices -l
```

运行效果如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-17-37-16.png)

每个手机都会被列出来。在最左边的编号就是手机串号。使用这个串号可以指定多个手机：

```
from airtest.core.api import auto_setup
from airtest.core.android import Android
from poco.drivers.android.uiautomation import AndroidUiautomationPoco
auto_setup(__file__)

device_1 = Android('76efadf3a7ce4')
device_2 = Android('adfasdfasf23')
device_3 = Android('adifu39ernla')

poco_1 = AndroidUiautomationPoco(device_1, use_airtest_input=True, screenshot_each_action=False)
poco_2 = AndroidUiautomationPoco(device_2, use_airtest_input=True, screenshot_each_action=False)
poco_3 = AndroidUiautomationPoco(device_3, use_airtest_input=True, screenshot_each_action=False)
```

通过这种方式，在一台电脑上使用USBHub，连上二三十台手机是完全没有问题的。

## 无线模式

Airtest支持无线模式，不需要USB，只要电脑和手机连接同一个WIFI就能控制：

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/wireless_control.gif)

如果大家对如何开启无线模式有兴趣，请留言，我就会继续写。

## 搭建手机爬虫集群

一台电脑可以连接三十台手机，那么如果有很多电脑和很多手机，就可以实现手机爬虫集群，其运行效果如下图所示。

![img](https://kingname-1257411235.cos.ap-chengdu.myqcloud.com/2019-01-19-17-44-57.png)

关于如何搭建爬虫集群，已经超出本文的范围了。如果大家有兴趣，可以阅读我的书：[Python爬虫开发 从入门到实战](https://item.jd.com/12436581.html?dist=jd)第十章对于如何搭建手机爬虫集群有详细的说明和注意事项。

如果对我的书有兴趣，请关注我的微信公众号与我交流。

