# Python爬虫：selenium打开新窗口和多窗口切换

2018年07月27日 16:46:45 [彭世瑜](https://me.csdn.net/mouday) 阅读数 8003



 版权声明：本文为博主原创文章，欢迎转载，请注明出处 https://blog.csdn.net/mouday/article/details/81240257

网上说可以通过发送按键事件触发，比如`ctrl+T`，不过我没成功，使用了js打开新窗口的方式

# 代码示例

```python
# -*- coding: utf-8 -*-

# @File    : switch_tab.py
# @Date    : 2018-07-27
# @Author  : Peng Shiyu

import time
from selenium import webdriver


browser = webdriver.Chrome()

# 在当前浏览器中访问百度
browser.get('https://www.baidu.com')

# 新开一个窗口，通过执行js来新开一个窗口
js = 'window.open("https://www.sogou.com");'
browser.execute_script(js)

# 输出当前窗口句柄（百度）
baidu_handle = browser.current_window_handle

# 获取当前窗口句柄集合（列表类型）
handles = browser.window_handles
print(handles)  # 输出句柄集合
# ['CDwindow-E9B85270B51D42AF7369D81B9AA70FFE',
# 'CDwindow-90004FD79A0F59EE057846B34D0E7327']

# 获取搜狗窗口
sogou_handle = None
for handle in handles:
    if handle != baidu_handle:
        sogou_handle = handle

# 输出当前窗口句柄（搜狗）
print('switch to ', handle)
browser.switch_to.window(sogou_handle)
time.sleep(10)
browser.close() #关闭当前窗口（搜狗）

# 切换回百度窗口
browser.switch_to.window(baidu_handle)

time.sleep(10)
browser.quit()
```



---------------

# selenium之浏览器弹出新窗口（新页面）

2017年09月28日 09:22:52   

个人分类： [selenium](https://blog.csdn.net/x12c345/article/category/6746882)



 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/x12c345/article/details/78119561

在一个页面上点击一个链接之后，并不是在当前页面上打开，而是重新打开一个新页面；这种情况下如何跳转到新的页面上操作？

首先，需要了解的是每个窗口都有句柄的，可以理解为浏览器窗口的唯一标识符，根据这个标识符来确定新打开的窗口：

先获取当前窗口的句柄，

`String current_handle = driver.getWindowHandle();`

打开新窗口后获取所有窗口的句柄，

`Set<String> all_handles = driver.getWindowHandles();`

通过循环判断是不是当前的窗口句柄，

```python
Iterator<String> it = all_handles.iterator();

while(it.hasnext()){

if(it.next() == current_handle){

continue;

}

//跳入新窗口

WebDriver new_driver = driver.switchTo().window(it.next());

}
```



新窗口的driver为new_driver，若要跳回之前的窗口，则使用如下：

`driver.switchTo().window(current_handle);`