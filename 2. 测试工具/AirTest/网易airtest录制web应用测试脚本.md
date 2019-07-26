# 网易airtest录制web应用测试脚本



[官方教程](http://airtest.netease.com/docs/cn/1_quick_start/5_get_started_with_web_test.html#id1)



### 网易airtest初体验

- [使用airtest进行web测试](https://blog.csdn.net/weixin_39913716/article/details/83652319#airtestweb_2)

# 使用airtest进行web测试

根据网易介绍，airtest project支出多种测试环境，包括Windows应用，Android和ios的APP还能够测试微信小程序。
本文将主要讲述应用airtest进行web应用测试。

- 环境准备
  airtest安装属于点开即用行，不过需要登录网易通行证，这是个对于办公网络来说相当难受。
- 界面介绍
  ![调整selenium工具窗口](https://img-blog.csdnimg.cn/20181102110426506.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTkxMzcxNg==,size_16,color_FFFFFF,t_70)
  在进行web端应用测试中需要在窗口选项中调出selenium工具栏；
  然后在选项>>设置中添加Google Chrome的安装路径
  ![路径设置](https://img-blog.csdnimg.cn/20181102110802971.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTkxMzcxNg==,size_16,color_FFFFFF,t_70)
  之后，你就可以愉快的录制脚本了
- 脚本录制演示
  ![脚本编辑](https://img-blog.csdnimg.cn/201811021111503.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTkxMzcxNg==,size_16,color_FFFFFF,t_70)
  airtest提供了脚本录制功能，但是经过试用后感觉，录制功能非常不好用，会有操作遗漏，定位页面元素没有识别XPath码等问题。
  所以建议使用手动点击指令按钮，录制加手写的方式，提高脚本的准确性。
- 首先点击![开始录制](https://img-blog.csdnimg.cn/20181102111721820.png)调用浏览器，在脚本中会生成调用脚本，点yes添加，在后续脚本修改中，如果用到还用提示，注意是否覆盖，
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20181102111932423.png)
  **注意大坑**：不管你进行什么操作，一定要注意光标位置，airtest的脚本插入会默认插入到光标位置，如果不注意，后面调试原地爆炸。
- start_web 打开网址：在浏览器打开百度首页后，点击后脚本中startweb生成driver.get(“[www.baidu.com](http://www.baidu.com/)”)。
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20181102113504467.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTkxMzcxNg==,size_16,color_FFFFFF,t_70)
- touch：点击操作，只需在IDE中点touch键，再到浏览器中点击你需要的键，就能生成对应代码
- text：输入操作，基本操作与touch相同，不过需要在生成代码后，手动填写要输入的内容
- assert：设置检查点，操作方法与touch相同
- snapshot：生成快照，直接生成代码，不需要额外操作
- 剩下的四个，非别是浏览器窗口切换和页面的前进后退

按照这个操作流程，就能够生成一个简单的测试脚本了



---------

# 网易airtest录制web应用测试脚本2

2018年12月29日 11:26:43 [告白与告别](https://me.csdn.net/weixin_39913716) 阅读数 262



 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/weixin_39913716/article/details/85335390



### airtest脚本增强

- [前言](https://blog.csdn.net/weixin_39913716/article/details/85335390#_2)

- - [几个常见的操作](https://blog.csdn.net/weixin_39913716/article/details/85335390#_6)



# 前言

目前的airtestIDE工具，已经能够实现常见的web页面操作录制生成脚本，但是在实际使用中，很多测试场景并不能实现支持，好在airtest支持python脚本，加之python语言相对简便，降低了测试人员学习成本，还实现对特殊场景的脚本增强。

## 几个常见的操作

1. 生成唯一数
   在很多场景，特别是注册、新建时，大多都会限制唯一用户名、文件名，这时候我通过读取系统时间实现唯一数，因为airtest脚本为串行执行，不存在loadrunner中并发的情况，所以我认为时间轴是最稳定可靠的唯一数生成方法：

```python
// An highlighted block
# -*- encoding=utf8 -*-
__author__ = "Administrator"
'''
新建文件夹，新建接口
通过时间函数，设置不重复唯一数
'''
from airtest.core.api import *
auto_setup(__file__)
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from airtest_selenium.proxy import WebChrome
#获取时间函数，避免文件夹名重复
#import random
import time
#a = random.randint(0,1000)
a = time.strftime("%Y%m%d%H%M%S", time.localtime())
driver = WebChrome()
driver.implicitly_wait(20)

driver.get("~~~/")
driver.find_element_by_id("username").send_keys("airtest")
driver.find_element_by_id("password").send_keys("1234")
...(略)
#新建测试文件夹，文件夹不能重复
b = "airtestx"
str1 = b + str(a)
driver.find_element_by_xpath("/html/body/div[3]/div/div[2]/div/div[2]/div/div/input").send_keys(str1)
...

driver.quit()
12345678910111213141516171819202122232425262728293031
```

1. 读取文件
   在测试中需要依据现有的数据执行操作，例如登录操作中，需要已有的注册用户时，将已经编辑好的文档读取到脚本中执行。

```python
# -*- encoding=utf8 -*-
'''
完成数据驱动测试试验
'''
from airtest.core.api import *

auto_setup(__file__)
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
from airtest_selenium.proxy import WebChrome

#读取txt文件
ufile = open("G:\\UIAuto\\read.txt", 'r')
#读取数据行
lines = ufile.readlines()
#循环遍历
for line in lines:
    driver = WebChrome()
    driver.implicitly_wait(20)
    driver.get("https://www.baidu.com/")
    #每一行第一个和第二个元素
    seach = line.split(',')[0]
    seach1 = line.split(',')[1]
    #拼接输入
    driver.find_element_by_name("wd").send_keys(seach + seach1)
    driver.find_element_by_xpath("//input[@value='百度一下']").click()
	...
    
    driver.quit()
#关闭文件
ufile.close()
12345678910111213141516171819202122232425262728293031
```

1. 页面滑动
   当页面展示不完，需要滚动条向下或其他方向移动时：

```python
# -*- encoding=utf8 -*-
__author__ = "xuezhi"
'''
光标移动试验
'''
	...
    #下拉至底部
    js = "window.scrollTo(100,1400);"
    driver.execute_script(js)
    sleep(2)

1234567891011
```

浏览器中滚动页面需要了解页面的大小，将光标移动到理想位置，可能需要多次调整