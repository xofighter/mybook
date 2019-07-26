# UI自动化测试工具AirTest学习笔记

[![96](https://upload.jianshu.io/users/upload_avatars/5460420/4c816313-1524-4c5f-82b3-d7ff3d7b7d5c?imageMogr2/auto-orient/strip|imageView2/1/w/96/h/96)](https://www.jianshu.com/u/25c578714905) 

\> 本篇更偏向于源码解析，适用于对airtest有一些了解，看过入门教程，写过demo的童鞋，当然初学者也可以在本章的上手环节跳转到网易官方最快5分钟教程中学习，因为我觉得那篇教程已经够好了，就不多写入门教程了。

#### **简介**

Airtest Project是最近非常火的一个ui自动化测试工具，由网易游戏内部工具团队开发并开源，获得谷歌力挺。

AirtestIDE 是一个跨平台、多端（Windows、web、android、ios、游戏）的UI自动化测试编辑器。

自动化脚本录制、一键回放、报告查看，轻而易举实现自动化测试流程，自有编辑器一站式解决

支持基于图像识别的[Airtest](https://github.com/AirtestProject/Airtest)框架，适用于所有Android和Windows游戏，会截图就能写脚本

支持基于UI控件搜索的[Poco](https://github.com/AirtestProject/Poco)框架，适用于Unity3d，Cocos2d与Android、ios App、web

能够运行在Windows和MacOS上

网易内部已成功应用在数十个项目上，利用[手机集群](http://airtest.netease.com/testlab.html)进行大规模自动化测试，手机集群没有开源，准备做收费模式吧

使用python编写，兼容2、3，尽量用3吧

#### [上手](https://www.jianshu.com/p/8931a25a716b)

[网易官方的最快五分钟上手教程](http://airtest.netease.com/tutorial/Tutorial.html)

官方教程，有演示视频，有动图，一目了然。环境搭建也相当简单，基本上安装好IDE就可以了。

AirTest IDE提供了一站式功能：脚本开发（录制、编辑）、设备管理、运行、回放、结果查看



![img](https://upload-images.jianshu.io/upload_images/5460420-13994b9da1d0ab47.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



相信通过网易的这个上手教程，很多人都能很快就可以把airtest玩起来了。

[进阶](https://www.jianshu.com/p/8931a25a716b)

当我们跟随着教程写好一条脚本，运行起来以后，一起来看看AirTest的大致框架。

首先在AirTest的定义中脚本文件名的后缀是.air，当我们在IDE中新建一个脚本文件

再来到文件管理中我们可以看到这是一个文件夹。



![img](https://upload-images.jianshu.io/upload_images/5460420-65491f948ae3d315.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/654/format/webp)

这里面有一个跟air脚本同名的py文件，其他的png图片就是在IDE里截图，录制，生成的图像文件。

打开这个py文件来看看：



![img](https://upload-images.jianshu.io/upload_images/5460420-e0d33d5bd74f2759.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

可以看出在IDE里显示的touch（图片），就是在api里的一个touch接口，里面传入一个Template，这个对象包含了图片文件的名称、录制时的相对坐标(record_pos)，分辨率(resolution)等，当然还有其他参数：目标位置(target_pos)、rgb匹配(rgb)，如果你在IDE里双击图片就会弹出窗口设置这些详细参数。

我想图像识别大概就是这样了：写脚本时截下目标图片（你想要点击的地方），这图片就跟python脚本保存在一起，touch接口传入这些目标图片，进行匹配，成功后点击目标图片的位置，有兴趣的话继续来看看这个touch接口的源码。

\```

@logwrap

def touch(v, times=1, **kwargs):

​    """

​    Perform the touch action on the device screen

​    :param v: target to touch, either a Template instance or absolute coordinates (x, y)

​    :param times: how many touches to be performed

​    :param kwargs: platform specific `kwargs`, please refer to corresponding docs

​    :return: finial position to be clicked

​    :platforms: Android, Windows, iOS

​    """

​    if isinstance(v, Template):

​        pos = loop_find(v, timeout=ST.FIND_TIMEOUT)

​    else:

​        try_log_screen()

​        pos = v

​    for _ in range(times):

​        G.DEVICE.touch(pos, **kwargs)

​        time.sleep(0.05)

​    delay_after_operation()

​    return pos

\```

入参：

v，可以是Template对象(目标截图)，或者是pos（坐标）

times，点击次数，默认为1

kwargs，平台的特殊参数

loop_find(v, timeout=ST.FIND_TIMEOUT)#通过名字大概知道，循环查找这个v，有个超时退出，返回坐标点

G.DEVICE.touch(pos, **kwargs)#点击设备的指定坐标点

G.DEVICE应该就是一个当前的设备，兼容android、ios、windows

delay_after_operation#最后点击完以后还等待一下，所以这里可以配置每步点击的等待时间

所以touch接口的逻辑是：

如传入图片信息，循环查找匹配出目标图片所在屏幕的坐标点；

传入是坐标，开始记录log信息；

循环点击指定的坐标点；

等待，然后返回目标坐标点。

再往下，看一下loop_find这个接口，我想这就是“图像识别”的“核心”部分了，哈哈

\```

@logwrap

def loop_find(query, timeout=ST.FIND_TIMEOUT, threshold=None, interval=0.5, intervalfunc=None):

​    """

​    Search for image template in the screen until timeout

​    Args:

​        query: image template to be found in screenshot

​        timeout: time interval how long to look for the image template

​        threshold: default is None

​        interval: sleep interval before next attempt to find the image template

​        intervalfunc: function that is executed after unsuccessful attempt to find the image template

​    Raises:

​        TargetNotFoundError: when image template is not found in screenshot

​    Returns:

​        TargetNotFoundError if image template not found, otherwise returns the position where the image template has

​        been found in screenshot

​    """

​    G.LOGGING.info("Try finding:\n%s", query)

​    start_time = time.time()

​    while True:

​        screen = G.DEVICE.snapshot(filename=None)

​        if screen is None:

​            G.LOGGING.warning("Screen is None, may be locked")

​        else:

​            if threshold:

​                query.threshold = threshold

​            match_pos = query.match_in(screen)

​            if match_pos:

​                try_log_screen(screen)

​                return match_pos

​        if intervalfunc is not None:

​            intervalfunc()

​        \# 超时则raise，未超时则进行下次循环:

​        if (time.time() - start_time) > timeout:

​            try_log_screen(screen)

​            raise TargetNotFoundError('Picture %s not found in screen' % query)

​        else:

​            time.sleep(interval)

\```

入参：

query：要在截图中查找的图片模板（也就是我们写脚本截的图咯）

timeout：最大匹配时间

threshold：默认是None，字面意思是阈值，也就是匹配时的相似度吧，调低点可以更容易匹配上，也更容易匹配错

interval：循环匹配的间隔时间，每次要对设备截图传入进来匹配，中间的等待时间

intervalfunc：传入一个方法，在匹配失败时调用，也就是可以在接口的外部自定义匹配失败后的动作

返参：pos：目标图片在设备屏幕中的位置

screen = G.DEVICE.snapshot(filename=None)#设备截图，所以运行完脚本以后工程路径会有很多个截图文件，就是这里产生的。

match_pos = query.match_in(screen)#在设备截图中匹配查找我们传入的目标图片

所以这loop_find的逻辑就是：一个循环，从设备中截取屏幕的图片，在屏幕图片上查找匹配我们的目标图片，匹配成功则记录日志然后返回位置坐标，失败则判断是否是否有intervalfunc方法需要执行，默认是没有的，跳过，然后接着继续循环截图、匹配，直到超时报一个TargetNotFoundError异常出去。

那么图像的匹配算法大概就是在这个match_in接口里了，接着再看一点吧，哈哈

\```

def match_in(self, screen):

​        match_result = self._cv_match(screen)

​        G.LOGGING.debug("match result: %s", match_result)

​        if not match_result:

​            return None

​        focus_pos = TargetPos().getXY(match_result, self.target_pos)

​        return focus_pos

​    @logwrap

​    def _cv_match(self, screen):

​        \# in case image file not exist in current directory:

​        image = self._imread()

​        image = self._resize_image(image, screen, ST.RESIZE_METHOD)

​        ret = None

​        for method in ST.CVSTRATEGY:

​            if method == "tpl":

​                ret = self._try_match(self._find_template, image, screen)

​            elif method == "sift":

​                ret = self._try_match(self._find_sift_in_predict_area, image, screen)

​                if not ret:

​                    ret = self._try_match(self._find_sift, image, screen)

​            else:

​                G.LOGGING.warning("Undefined method in CV_STRATEGY: %s", method)

​            if ret:

​                break

​        return ret

\```

match_in调用cv_match进行匹配，然后TargetPos().getXY(match_result, self.target_pos)就是对匹配出来的结果进行处理，在前面讲touch的时候有一个参数是target_pos，还有印象吗？根据教程和文档说明，target_pos是以123456789的数字按九宫格键盘排列，分别代表左上角，正上角，右上角，...，右下角。这个getXY就是对这个进行处理的，根据传入的target_pos对匹配到的坐标信息再做处理返回目标图片中的不同位置上的坐标，默认是返回中心点。

再看cv_match接口，

imread()#根据图片路径，将图片读取为cv2的图片处理格式

_resize_image(image, screen, ST.RESIZE_METHOD)#处理图片尺寸，这里可以在ST.RESIZE_METHOD自定义缩放规则，默认是用COCOS中的MIN策略

然后根据CVSTRATEGY（cv策略，应该不同匹配的算法），有tpl、sift，进行try_match。

其中sift策略中优先对预测的区域进行匹配，也就是用到了再touch接口中传入的record_pos，终于知道为啥要传入写脚本是截图的位置了吧。

这个try_match是转换接口，method，再调用method，也就是说匹配的算法有三个不同的，有兴趣可以继续去看看：

_find_template、_find_sift_in_predict_area、_find_sift这三个接口。

\```

@staticmethod

​    def _try_match(method, *args, **kwargs):

​        G.LOGGING.debug("try match with %s" % method.__name__)

​        try:

​            ret = method(*args, **kwargs)

​        except aircv.BaseError as err:

​            G.LOGGING.debug(repr(err))

​            return None

​        else:

​            return ret

\```

#### [总结](https://www.jianshu.com/p/8931a25a716b)

Airtest的优点

有个IDE，大大地减少了写自动化脚本的难度，搭建环境、写脚本，运行脚本，查看报告都一站式解决了；

图像识别，对不能用ui控件定位的地方的，使用图像识别来定位，对一些自定义控件、H5、小程序、游戏，都可以支持；

支持多个终端，使用图像识别的话可以一套代码兼容android和ios哦，用ui控件定位的话需要兼容一下。

本篇通过touch接口对airtest的图像识别的源码进行了初步的分析，更多图像匹配算法实现部分，下回分解。



-----------

首先进入网站下载http://airtest.netease.com/

![img](https://img-blog.csdnimg.cn/20190705091014971.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ppdWxhbmhhbw==,size_16,color_FFFFFF,t_70)

下载完成后是一个zip压缩包直接解压即可。

![img](https://img-blog.csdnimg.cn/20190705091146173.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ppdWxhbmhhbw==,size_16,color_FFFFFF,t_70)

 打开解压的目录打开图上文件即可运行（不用安装python啥的）

![img](https://img-blog.csdnimg.cn/2019070509134287.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ppdWxhbmhhbw==,size_16,color_FFFFFF,t_70)

首先现去看自己的谷歌浏览器是多少版本，去下载对应的驱动，然后粘贴到AirTest的根目录中。

![img](https://img-blog.csdnimg.cn/2019070509152767.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ppdWxhbmhhbw==,size_16,color_FFFFFF,t_70)

之后进入打开AirTest，进入设置，去设置浏览器所在的路径（不是驱动的路径）。

![img](https://img-blog.csdnimg.cn/20190705091740199.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ppdWxhbmhhbw==,size_16,color_FFFFFF,t_70)

配置好后去打开上面配置列表中的窗口，把selenium窗口打开。

![img](https://img-blog.csdnimg.cn/201907050921186.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ppdWxhbmhhbw==,size_16,color_FFFFFF,t_70)

之后点击小地球就会启动一个浏览器，再点击小电脑会获取鼠标指定元素的地址值，录像机就是去录制你的操作并生成语句。

![img](https://img-blog.csdnimg.cn/20190705092807795.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ppdWxhbmhhbw==,size_16,color_FFFFFF,t_70)