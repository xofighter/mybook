# [airtest环境](https://www.cnblogs.com/hyhyhy/articles/10518349.html)

\2. 安装python第三方包，终端里输入

pip3 install selenium

pip3 install python-jenkins

pip3 install requests

pip3 install pyquery

pip3 install cssselect

pip3 install lxml

pip3 install airtest

 

配置浏览器驱动

1. 安装谷歌浏览器：https://www.applex.net/downloads/google-chrome-for-mac.25/history。
2. 禁止谷歌浏览器更新：https://www.jianshu.com/p/56c370d50d0c。
3. 配置谷歌浏览器驱动：http://npm.taobao.org/mirrors/chromedriver/，把chromedriver 拷贝到  /usr/local/bin。

安装**airtest**

双击   AirtestIDE_2018-11-06_py3_Mac10-12.dmg

 

将**python3**下载的包，拷贝到**airtest**文件夹中

将用python3 下载的第三方包（selenium，jenkins，requests， pyquery，cssselect，lxml

multi_key_dict.py）copy到（/Applications/AirtestIDE.app/Contents/MacOS/）里。

 

注释：

python3 第三方包所在路径：Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/

 

替换原因说明：

（1）程序运行需要加载python3的第三方模块，Airtest 框架本身不带有这

  些包，需要后期安装。

（2）/Applications/AirtestIDE.app/Contents/MacOS/，该路径是程序运行中，加载包的路径。

  

针对**ios** 还需部署以下环境

1. 参见文档：http://airtest.netease.com/docs/docs_AirtestIDE-zh_CN/2_device_connection/4_ios_connection.html
2. 下载 ideviceinstaller：

[**https://www.cnblogs.com/lily1989/p/8383916.html**](https://www.cnblogs.com/lily1989/p/8383916.html)

下载时可能会出现的问题，参考如下：

[**https://www.jianshu.com/p/1ec36db7d5**](https://www.jianshu.com/p/1ec36db7d551)

 

使用方法：

注意事项

- 网络畅通，连接的网络必须是内网，例如：yuanfudao-inc, 否则发mission，下载安装包会失败。
- 手机与电脑的屏幕不能熄灭、锁屏。
- 通过对测试报告来判断是否通过。

第一步：连接设备

目前只支持华为，如果需要其他型号，后期补上

\1. 用数据线将手机与电脑连接

\2. 手机-->设置--搜索 “开发”-->打开USB 调试-->将usb 连接方式更改为 MIDI，如图：

 

 ![img](https://img2018.cnblogs.com/blog/1157904/201903/1157904-20190312174647193-1462470591.png)

 

 

  

 

 

 ![img](https://img2018.cnblogs.com/blog/1157904/201903/1157904-20190312174710706-121565774.png)

 

 

 

\3. 这个时候USB 可能会自动被关掉，再次启动USB，下面出现的弹框，依次点击确定，如下图：

 ![img](https://img2018.cnblogs.com/blog/1157904/201903/1157904-20190312174747524-761586111.png)

 

![img](https://img2018.cnblogs.com/blog/1157904/201903/1157904-20190312174809589-726013119.png)

 

 

 

 

 

 

 

第二步：在 **jenkins** 里进行操作

以安卓为例：

 ![img](https://img2018.cnblogs.com/blog/1157904/201903/1157904-20190312174836400-839283410.png)

![img](https://img2018.cnblogs.com/blog/1157904/201903/1157904-20190312174903279-1512266445.png)

 

 

 

 

注释：配置里的信息说明

android ：

"/Applications/AirtestIDE.app/Contents/MacOS/AirtestIDE" runner "/Users/zd/Documents/banma-android/login.air"  --device Android://127.0.0.1:5037/AYKDU18306001086

ios ：

"/Applications/AirtestIDE.app/Contents/MacOS/AirtestIDE" runner "/Users/zd/Documents/banma-ios/login.air"  --device ios:///http://127.0.0.1:8100

 

参数解释：

"/Applications/AirtestIDE.app/Contents/MacOS/AirtestIDE" 是AirtestIDE的安装路径

runner 执行脚本的方法

"/Users/zd/Documents/banma/login.air" 测试脚本的路径

device 用于连接设备

Android://127.0.0.1:5037/AYKDU18306001086 手机://IP:端口/设备号

 

登陆：

在终端输入命令后，过一会出现登陆页面，需要用户输入“用户名”，“密码”，“验证码”，点击“提交”，之后会进入自动发mission环节。

 

 

测试**mission**

android ：

"/Applications/AirtestIDE.app/Contents/MacOS/AirtestIDE" runner "/Users/zd/Documents/banma-android/NEXT.air"  --device Android://127.0.0.1:5037/AYKDU18306001086 --log "/Users/zd/Documents/banma/report"

ios ：

"/Applications/AirtestIDE.app/Contents/MacOS/AirtestIDE" runner "/Users/zd/Documents/banma-ios/NEXT.air"  --device ios:///http://127.0.0.1:8100 --log "/Users/zd/Documents/banma-ios/report"

 

--log 后面的参数是生成报告的路径, 报告的路径请指向拷贝下来文件里的report文件夹



-----------

