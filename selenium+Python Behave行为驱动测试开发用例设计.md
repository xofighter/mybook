首先使用pip install behave安装behave包，官方文档请参考。从官网中了解到，实现一个基本的behave执行，至少需要构建如下测试目录：
```
features/
features/everything.feature
features/steps/
features/steps/steps.py
```
即我们需要新建features文件夹，在该文件夹下要包含至少一个.feature文件和steps文件夹，steps下包含对应执行的测试用例.py文件。我测试的场景是百度登录，即登录百度首页->点击登录->输入用户名/密码提交->检查登录状态。场景描述文件写在.feature文件里，这类文件可由测试、产品或任何需求相关人员完成，使用自然语言描述，如下：
```
Feature:i want to login on baidu

 Scenario Outline: Login 
​    Given I am on baidu homepage
​     When I click login button
​     Then I should see login page
​     When I enter <username> and <password> and submit
​     Then I should see user status

Examples: userinfo
​    |username       |password|
​    |1******    |********|
```

Feature用来描述此次测试，Scenario Outlines和Examples结合使用，类似于数据驱动，适用于多次重复的测试，即作为一个基本参数化的场景模板，场景大纲生成多个场景，每个场景代表一个示例/行组合。我这里使用的例子体现的不好，可以结合官网的例子理解：
```
Feature:
  Scenario Outline: Wow            # line 2
​    Given an employee "<name>"

    Examples: Araxas
      | name  | birthyear |
      | Alice |  1985     |         # line 7
      | Bob   |  1975     |         # line 8
```

其等同于：
```
Scenario Outline: Wow          # features/xxx.feature:2
  Given an employee "Alice"

Scenario Outline: Wow          # features/xxx.feature:2
  Given an employee "Bob"
```

要注意，参数化的数据使用<>,里面的参数名要和Examples里的一致。 
Given,When,Then描述了测试场景，Given表示执行的前提条件，When执行操作，Then预期结果。 
然后在steps文件夹下创建.py文件编写测试用例：
```
#encoding: utf-8
from behave import *
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions
from selenium.webdriver.common.by import By
from selenium.webdriver.common.action_chains import ActionChains
import time

@given('I am on baidu homepage')
def step_on_baidu_homepage(context):
​    context.driver.get('https://www.baidu.com/')

@when('I click login button')
def step_click_login_button(context):
​    time.sleep(3)
​    login_btn = context.driver.find_element_by_link_text('登录')

    login_btn.click()
    time.sleep(4)

@then('I should see login page')
def step_see_login_page(context):
​    name_login = context.driver.find_element_by_xpath("//p[@title='用户名登录']")
​    name_login.click()
​    time.sleep(2)

@when('I enter {username} and {password} and submit')
def step_enter_user_info(context,username,password):
​    user = context.driver.find_element_by_xpath("//input[@id='TANGRAM__PSP_10__userName']")
​    user.send_keys(username)

    pw = context.driver.find_element_by_xpath("//input[@name='password']")
    pw.send_keys(password)
    time.sleep(3)
    
    btn = context.driver.find_element_by_xpath("//input[@type='submit']")
    ActionChains(context.driver).move_to_element(btn).click(btn).perform()

@then('I should see user status')
def step_see_user_status(context):
​    user_status = WebDriverWait(context.driver,10).until(expected_conditions.visibility_of_element_located((By.ID,'s_username_top')))
​    user_status_info = user_status.text
​    print(user_status_info)
​    if user_status_info[:3] =='1**':
​        assert True
```
可以看出对应的每一个Given,When,Then场景都有一个step_方法来实现，这里参数context我理解的是在各steps间进行连接、传递的对象。同时，这里要注意的坑有：

传递的参数数据使用{}来指示，同时参数名和元素名不要重名；
点击登录时遇到了报错‘ElementClickInterceptedException’，解决方法可参考stackoverflow
百度登录时会有图片验证码进行验证，这里我采取的方法是延长等待时间手动输入，还可以采用添加cookie的方法，先获得登录后的cookie，确定登录字段，然后driver.add_cookie(键名),还有一些别的方法可以参考。 
类似于setUp()和tearDown(),我们可以添加在测试执行前/后的一些操作，如启动/退出浏览器，这类操作写在features下的environment.py文件中：
```
#encoding: utf-8
from selenium import webdriver

def before_all(context):
​    profile_dir = r'C:\Users\Firefox\Profiles\iewanxoz.default'
​    profile = webdriver.FirefoxProfile(profile_dir)
​    context.driver = webdriver.Firefox(profile)
​    context.driver.implicitly_wait(30)
​    context.driver.maximize_window()

def after_all(context):
​    context.driver.quit()
```
这样就完成了一个简单的behave测试，对于复杂的可以添加多个GivenWhenThen语句，或者多个Scenario，或者多个feature文件，执行时在cmd窗口features文件夹上级目录，输入behave即可，得到测试结果如下： 
![img](https://img-blog.csdn.net/20180915190117752?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2lhbV9lbWlseQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)