# Airtest自动化实践持续集成

2018年09月19日 15:05:16 [小小小小芈](https://me.csdn.net/Mickey220521) 阅读数 2053



做好心理准备，这是一篇长文，哈哈。。。
看到网上有比较完善的资料了，主要介绍的是安卓应用的使用，那么我这边就以iOS系统来给大家分享下我的使用心得吧！

***1：具体的airtest的使用方法，我想应该没谁写的有官网写的详细***
官网地址奉上：http://airtest.netease.com/

**Airtest简介：**
[AirtestIDE](http://airtest.netease.com/) 是一个跨平台的UI自动化测试编辑器，适用于游戏和App。

⭐️自动化脚本录制、一键回放、报告查看，轻而易举实现自动化测试流程

⭐️支持基于图像识别的Airtest框架，适用于所有Android和Windows游戏

⭐️支持基于UI控件搜索的Poco框架，适用于Unity3d，Cocos2d与Android App

⭐️访问官网通过视频查看更多特性

**Airtest在iOS上的具体使用：**

[iOS的环境搭建](https://github.com/AirtestProject/IOS-Tagent)：
可以参考：https://github.com/AirtestProject/IOS-Tagent
校验device连接是否成功，可以查看http://127.0.0.1:8100/status

成功的页面：
![在这里插入图片描述](https://img-blog.csdn.net/20180918200559199?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01pY2tleTIyMDUyMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

***接下来咱们看下iOS连接成功的具体页面：***
![在这里插入图片描述](https://img-blog.csdn.net/20180918200249918?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01pY2tleTIyMDUyMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

***脚本录制也比较简单，如下图，具体就不详细赘述，毕竟有官网大大给的非常详细的。***
![在这里插入图片描述](https://img-blog.csdn.net/20180918195619406?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01pY2tleTIyMDUyMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**Jenkins持续集成：**
讲了半天，终于讲到了重点。为什么使用jenkins集成呢？主要是因为Airtest的官网未展示持续集成的方法，AirtestIDE不能满足多条case串行，加上我司主要使用jenkins，为了方便平台一体化，所以选择自己实现持续集成。删除线格式

***1：需要在jenkins创建两个job，一个项目的，一个是测试报告的***

![在这里插入图片描述](https://img-blog.csdn.net/20180919104206687?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01pY2tleTIyMDUyMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

***2：项目job中的Excute shell需要配置***
**python /Users/mi.li/.jenkins/workspace/autotest_ui/miniapp_airtest_ios.air/miniapp.py**
**python /Users/mi.li/.jenkins/workspace/autotest_ui/miniapp_airtest_ios.air/txtnew.py**

**上面的miniapp.py文件具体封装的方法如下**

# coding=utf-8

import pandas as pd
import os

suffix_path = ‘–device ios:///[http://127.0.0.1:8100](http://127.0.0.1:8100/)’

def run_test():

```
data = pd.read_excel('/Users/mi.li/Documents/miniapp_case.xlsx')
data = pd.DataFrame(data.values)
index = 0
for rundir in data[1]:
    logdir = data[2][index]
    index += 1
    cmd = 'airtest run ' + rundir
    cmd = cmd +' --log ' + logdir
    cmd += ' ' + suffix_path
    print cmd    
    print os.system(cmd)   
1234567891011
```

run_test()

**上面的txtnew.py文件具体封装的方法如下：**

# coding=utf-8

import os

fatherPath = ‘/Users/mi.li/.jenkins/workspace/autotest_ui/miniapp_airtest_ios.air’
logDirs = os.listdir(fatherPath)
targetLogPath = ‘/Users/mi.li/.jenkins/workspace/autotest_ui/miniapp_airtest_ios.air/search.air/log/log.txt’
\#print os.getcwd()
print fatherPath
print targetLogPath

logPaths = []
for logDir in logDirs:
logPath = fatherPath + ‘/’ + logDir + ‘/log/log.txt’
if os.path.isfile(logPath):
logPaths.append(logPath)

f=open(targetLogPath, ‘aw’)
for logPath in logPaths:
lines = open(logPath).readlines()
\#遍历单个文件，读取行数
for line in lines:
f.writelines(line)
\#f.write(’\n’)

f.close()

![在这里插入图片描述](https://img-blog.csdn.net/20180919143057278?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01pY2tleTIyMDUyMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

***下面来简单介绍下上述的原理：***
第一步：我们可以把case和log的文件目录写在excel表格上；
第二步：在miniapp.py文件中附上excel的文件目录，miniapp.py封装主要是为了把excel表格中的case可以串行；
第三步：在txtnew.py文件中主要是把每个单独case生成的log.txt文件合并附在最外层的log文件中。

***上述是介绍case执行job的配置，下面我们介绍下report的合成：***

1：主要讲述下job的配置
**cd/Users/mi.li/.jenkins/workspace/autotest_ui/miniapp_airtest_ios.air/search.air**

**airtest report/Users/mi.li/.jenkins/workspace/autotest_ui/miniapp_airtest_ios.air/search.air**

**备注**：注意这样配置，report生成会出现路径问题，也就是报告中的图片路径找不到，无法正常展示，但是样式与AirtestIDE上的报告样式不一致，大家可以根据自己的需要，调整报告的样式。

***那么，现在我们讲一下图片路径解决的方案：***
![在这里插入图片描述](https://img-blog.csdn.net/20180919144950261?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01pY2tleTIyMDUyMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
***大家可以看到项目上传到gitlab上后，又一个“report”文件，下图给大家看下“report”包含的内容：***
![在这里插入图片描述](https://img-blog.csdn.net/20180919145052686?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L01pY2tleTIyMDUyMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

众所周知，css，js文件主要是报告的样式文件，把这两个文件复制到report下，并在“miniapp_airtest_ios_report”的这个job的“Execute shell”配置中增加sed命令，这样会解决图片的路径问题。

到这里，基本上就已经可以走完整个流程了，写的比较复杂，也是一个一个坑走出来的，但愿会给大家带来帮助…

（另外，如果有会用C#的大神，也可以自己写一个，认识的大佬已经自己写出来一个很完善的框架了，后续大佬应该会分享出来，由于本人比较菜，不会使用C#，所以只能用上面这个方法实现了）