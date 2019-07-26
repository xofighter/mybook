# [利用airTest的图像实别技术测试Web应用](https://www.cnblogs.com/testertry/p/10966017.html)



airTest的第三方类库中有图像实别功能，根据官网的介绍，这个功能是能够在Windows上用来定位元素，进行操作的。尝试过以下脚本，发现真的可以。

```python
from selenium.webdriver.chrome.options import Options
from selenium import webdriver
from selenium.webdriver.common.keys import Keys
import time
from selenium import *
import os
from selenium.webdriver.common.by import By
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.wait import WebDriverWait
import win32gui
from airtest.core.api import *
from airtest.cli.parser import cli_setup

# 打开chrome 浏览器，可以用别的方式打开，我这里用webdriver。
driver = webdriver.Chrome(executable_path="C:\\Users\\xx.xxxx\\Documents\\AutoTesting\\xxxxxxxx\\chromedriver.exe")

# 返回窗口标题为data:, - Google Chrome的句柄.这里是提前写好的，因为知道每次打开chrome的时候都会显示这个窗口标题。
hld = win32gui.FindWindow(None, "data:, - Google Chrome")
shld = str(hld)

# 这是airtest 连接chrome窗口的关键步骤。将刚刚取到的句柄传入，连接airtest
if not cli_setup():
    auto_setup(__file__, logdir=True, devices=["Windows:///" + shld])

# 下面就可以实行点击等操作了。在chrome窗口中，寻找预先截好的图--"tpl1559122892842.png" ，这张图要放在工程内。一旦寻找到，就会点击，也就是touch操作。
touch(Template(r"tpl1559122892842.png", record_pos=(0.16, -0.011), resolution=(1391, 746)))
touch(Template(r"tpl1559122886202.png", record_pos=(0.244, -0.092), resolution=(1391, 746)))

# 直接text操作，在目前光标所在的位置执行。所以执行这一步之前，要在上一步先以touch的方式，将光标移在目标输入框内。
text("www.baidu.com")
touch(Template(r"tpl1559123207157.png", record_pos=(0.315, -0.116), resolution=(1391, 746)))
```

