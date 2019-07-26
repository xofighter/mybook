# Airtest搭建批量执行UI自动化脚本，并批量生成html 报告

2019年05月28日 09:47:40 [me阿木](https://me.csdn.net/tianxiexingyun) 阅读数 251



Airtest 是近几年出的，帮助文档很少，反复查看源帮助文档和其他相关资料，编写了一个安卓UI自动化的轻量小框架。

使用语言：python3.6.8  开发工具：pycharm; python安装airtest ：

```bash
 pip install airtest
```

框架结构如下：

![img](https://img-blog.csdnimg.cn/20190527165935853.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RpYW54aWV4aW5neXVu,size_16,color_FFFFFF,t_70)

![img](https://img-blog.csdnimg.cn/20190606154437494.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RpYW54aWV4aW5neXVu,size_16,color_FFFFFF,t_70)

## 1、框架简单介绍

1. air 是多个自动化脚本
2. conf,lib 配置文件
3. data 数据
4. log 日志
5. report html 报告
6. XFDAirtestCase.py  入口方法，启动器

## 2、air 脚本的开发

通过airtest工具，开发脚本，可以录制，也可以自己编写，请参考源文档，这里不做多介绍

·http://airtest.netease.com/docs/cn/index.html

## 3、配置文件，获取文件所在目录

```python
# -*- coding: utf-8 -*
import os
 
Androiddevice=["Android://127.0.0.1:5037/172.16.81.115:5555"]#连接安卓设备127.0.0.1:5037固定写法172.16.81.115安卓真机的Ip
airpath = os.path.join(os.path.dirname(os.path.dirname(os.path.abspath(__file__))),'air')#脚本目录
logpath=os.path.join(os.path.dirname(os.path.dirname(os.path.abspath(__file__))),'log')#日志目录
templatepath=os.path.join(os.path.dirname(os.path.dirname(os.path.abspath(__file__))),'templates')#模板目录
 
reportpath=os.path.join(os.path.dirname(os.path.dirname(os.path.abspath(__file__))),'report')#报告目录
 
datapath = os.path.join(os.path.dirname(os.path.dirname(os.path.abspath(__file__))), 'data')#测试数据目录
```

## 4、入口脚本，启动器

```python
from airtest.cli.runner import AirtestCase, run_script
from argparse import *
import shutil
from conf.config import *
from lib.log import logger
from airtest.report.report import simple_report
 
 
 
class XFDAirtestCase(AirtestCase):#继承AirtestCase类
 
    def setUp(self):
        logger.info("案例开始执行")
        super(XFDAirtestCase, self).setUp()#继承父类的setup方法
 
    def tearDown(self):
        logger.info("案例执行结束")
        super(XFDAirtestCase, self).tearDown()#继承父类的tearDown方法
 
    def run_air(self, root_dir, device):#本方法主要是查找脚本文件，目录文件，初始化AirtestCase所需要的参数，执行脚本，并生成报告
 
        for f in os.listdir(root_dir):#循环查找air所在的目录
            if f.endswith(".air"):#以air结尾的文件
                airName = f
                script = os.path.join(root_dir, f)#脚本目录
                logger.info(script)
                log = os.path.join(logpath +'\\'+ airName.replace('.air', ''))#日志目录
                logger.info(log)
                if os.path.isdir(log):
                    shutil.rmtree(log)#清空日志目录文件
                else:
                    os.makedirs(log)
                args = Namespace(device=device, log=log, recording=None, script=script)#初始化父类AirtestCase所需要的参数
                try:
                    run_script(args, AirtestCase)#执行air脚本文件
                except AssertionError:
                    logger.info("案例执行失败")
                finally:
                    #output_file = log + '\\' + airName.replace('.air', '') + '.html'
                    output_file = reportpath + '\\' + airName.replace('.air', '') + '.html'#生成报告目录
                    simple_report(script, logpath=log, output=output_file)#生成报告的方法
                    logger.info("案例执行成功")
 
 
if __name__ == '__main__':
    test = XFDAirtestCase()
    #device = ['Android://127.0.0.1:5037/172.16.81.115:5555']
    test.run_air(airpath, Androiddevice)
```

## 5、执行过程，以及执行结果报告

​        在python环境下执行入口脚本，命令如下：python XFDAirtestCase.py  循环执行air 文件下的air 脚本，以登录为例，每个air脚本对应生成一个html报告， 放到report 目录中；html 中有详细的点击步骤，也有截图，报告效果如下：

![img](https://img-blog.csdnimg.cn/20190606110952782.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RpYW54aWV4aW5neXVu,size_16,color_FFFFFF,t_70)

## ![img](https://img-blog.csdnimg.cn/20190606111323910.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RpYW54aWV4aW5neXVu,size_16,color_FFFFFF,t_70)

## ![img](https://img-blog.csdnimg.cn/2019060611172377.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RpYW54aWV4aW5neXVu,size_16,color_FFFFFF,t_70)

## ![img](https://img-blog.csdnimg.cn/20190606111741305.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RpYW54aWV4aW5neXVu,size_16,color_FFFFFF,t_70)![img](https://img-blog.csdnimg.cn/20190606111800244.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RpYW54aWV4aW5neXVu,size_16,color_FFFFFF,t_70)![img](https://img-blog.csdnimg.cn/20190606111729174.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RpYW54aWV4aW5neXVu,size_16,color_FFFFFF,t_70)

## 6、搭建框架中遇到的问题

(1)airtest adb 与python中的adb 冲突问题，

解决方案：把\tools\adb39 中的adb 拷贝到 pyhon 的\Lib\site-packages\airtest\core\android\static\adb\windows 目录下

(2) 入口脚本 运行问题，

直接运行XFDAirtestCase.py 报错：错误如下：

```bash
Error
Traceback (most recent call last):
  File "D:\python36\lib\site-packages\airtest\cli\runner.py", line 26, in setUpClass
    cls.args = args
NameError: name 'args' is not defined
```

解决： 通过python 命令直接执行    进到该项目下  直接执行

```bash
python XFDAirtestCase.py
```

 