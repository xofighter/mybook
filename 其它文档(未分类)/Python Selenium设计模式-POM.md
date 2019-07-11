## 前言

本文就python selenium自动化测试实践中所需要的POM设计模式进行分享，以便大家在实践中对POM的特点、应用场景和核心思想有一定的理解和掌握。

## 为什么要用POM

基于python selenium2开始UI级自动化测试并不是多么艰巨的任务。**只需要定位到元素，执行对应的操作即可。**下面我们看一下这个简单的脚本实现百度搜索。

```
from selenium import webdriver
import time

driver = webdriver.Firefox()
driver.implicitly_wait(30)

# 启动浏览器，访问百度
driver.get("http://www.baidu.com")

# 定位 百度搜索框，并输入selenium
driver.find_element_by_id("kw").send_keys("selenium")

# 定位 百度一下 按钮并单击进行搜索
driver.find_element_by_id("su").click()
time.sleep(5)

driver.quit()
```

从上述代码来看，我们所能做的就是定位到元素，然后进行键盘输入或鼠标动作。就这个小程序而已，维护起来看起来是很容易的。但随着时间的迁移，测试套件将持续的增长。脚本也将变得越来越臃肿庞大。如果变成我们需要维护10个页面，100个页面，甚至1000个呢？那页面元素的任何改变都会让我们的脚本维护变得繁琐复杂，而且变得耗时易出错。

那怎么解决呢？
 在自动化测试中，引入了Page Object Model（POM）：页面对象模式来解决，POM能让我们的测试代码变得可读性更好，高可维护性，高复用性。

下图为非POM和POM对比图：



![img](https:////upload-images.jianshu.io/upload_images/5146600-925d9345853a80b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/508/format/webp)

pom.png

## POM是什么

- 页面对象模型(POM)是一种设计模式，用来管理维护一组web元素集的对象库
- 在POM下，应用程序的每一个页面都有一个对应的page class
- 每一个page class维护着该web页的元素集和操作这些元素的方法
- page class中的方法命名最好根据其对应的业务场景进行，例如通常登录后我们需要等待几秒中，我们可以这样命名该方法: waitingForLoginSuccess().

下面我们看看POM的代码目录组织示例：



![img](https:////upload-images.jianshu.io/upload_images/5146600-f6bca279904591c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/328/format/webp)

pages_dir.png

## POM的优势

1. POM提供了一种在UI层操作、业务流程与验证分离的模式，这使得测试代码变得更加清晰和高可读性
2. 对象库与用例分离，使得我们更好的复用对象，甚至能与不同的工具进行深度结合应用
3. 可复用的页面方法代码会变得更加优化
4. 更加有效的命名方式使得我们更加清晰的知道方法所操作的UI元素。例如我们要回到首页，方法名命名为: gotoHomePage(),通过方法名即可清晰的知道具体的功能实现。

## POM实现示例

下面我们看下使用POM百度搜索 POM代码示例：
 看先下代码组织结构如下：



![img](https:////upload-images.jianshu.io/upload_images/5146600-b1c565738f77dd87.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/279/format/webp)

page_demo_dir.png

```
# basePage.py代码如下
# _*_ coding:utf-8 _*_

__author__ = '苦叶子'

import sys
reload(sys)
sys.setdefaultencoding("utf-8")


# pages基类
class Page(object):
    """
        Page基类，所有page都应该继承该类
    """
    def __init__(self, driver, base_url=u"http://www.baidu.com"):
        self.driver = driver
        self.base_url = base_url
        self.timeout = 30
    
    def find_element(self, *loc):
        return self.driver.find_element(*loc)
    
    def input_text(self, loc, text):
        self.find_element(*loc).send_keys(text)
        
    def click(self, loc):
        self.find_element(*loc).click()
        
    def get_title(self):
        return self.driver.title
# searchPage.py 代码如下
# _*_ coding:utf-8 _*_

__author__ = '苦叶子'

import sys

from selenium.webdriver.common.by import By
from pages.basePage import Page

reload(sys)
sys.setdefaultencoding("utf-8")


# 百度搜索page
class SearchPage(Page):
    # 元素集
    
    # 搜索输入框
    search_input = (By.ID, u'kw')
    # 百度一下 按钮
    search_button = (By.ID, u'su')
    
    def __init__(self, driver, base_url=u"http://www.baidu.com"):
        Page.__init__(self, driver, base_url)
        
    def gotoBaiduHomePage(self):
        print u"打开首页: ", self.base_url
        self.driver.get(self.base_url)
        
    def input_search_text(self, text=u"开源优测"):
        print u"输入搜索关键字： 开源优测 "
        self.input_text(self.search_input, text)
        
    def click_search_btn(self):
        print u"点击 百度一下  按钮"
        self.click(self.search_button)
# testSearchPage.py代码如下
# _*_ coding:utf-8 _*_

__author__ = '苦叶子'

import unittest
import sys

from selenium import webdriver
from pages.searchPage import SearchPage

reload(sys)
sys.setdefaultencoding("utf-8")


# 百度搜索测试
class TestSearchPage(unittest.TestCase):
    
    def setUp(self):
        self.driver = webdriver.Ie()
        
    def testSearch(self):
        driver = self.driver
        # 百度网址
        url = u"http://www.baidu.com"
        # 搜索文本
        text = u"开源优测"
        # 期望验证的标题
        assert_title = u"开源优测_百度搜索"
        print assert_title
        
        search_Page = SearchPage(driver, url)
        
        # 启动浏览器，访问百度首页
        search_Page.gotoBaiduHomePage()
        
        # 输入 搜索词
        search_Page.input_search_text(text)
        
        # 单击 百度一下 按钮进行搜索
        search_Page.click_search_btn()
        
        # 验证标题
        self.assertEqual(search_Page.get_title(), assert_title)
        
    def tearDown(self):
        self.driver.quit()
# 主入口程序代码如下
# _*_ coding:utf-8 _*_

__author__ = '苦叶子'

import unittest
import sys

from common import HTMLTestRunner
from testcase.testSearchPage import TestSearchPage


reload(sys)
sys.setdefaultencoding("utf-8")


if __name__ == '__main__':
    testunit = unittest.TestSuite()
    testunit.addTest(TestSearchPage('testSearch'))
    
    # 定义报告输出路径
    htmlPath = u"page_demo_Report.html"
    fp = file(htmlPath, "wb")
    
    runner = HTMLTestRunner.HTMLTestRunner(stream=fp, 
           title=u"百度测试", 
           description=u"测试用例结果")
    
    runner.run(testunit)
    
    fp.close()
```

按照如图所示组织代码结构，输入如上代码，执行以下命令运行，会在当前目录生成测试报告：

```
python main.py
```

## 总结

最后做个总结，所有代码请手动输入，不要直接拷贝。再次对POM进行小结

1. POM是selenium webdriver自动化测试实践对象库设计模式
2. POM使得测试脚本更易于维护
3. POM通过对象库方式进一步优化了元素、用例、数据的维护组织

作者：苦叶子

链接：https://www.jianshu.com/p/7f2b9b638e25

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。