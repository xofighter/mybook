# 基于python多线程自制快速冒烟测试工具


#### 一、前言

在项目测试的过程中，发现经常会有下面这些情况：

- 新版本升级后，有时会因为代码或部署时配置错误，某个菜单报类似404或500的错误；
- 为避免以上问题，实施部署人员升级完后，需要手动冒烟一遍所有菜单页面；
- 发布当天还有修改提测，来不及再全部手动回归一遍；
- 项目周期不长，没有时间来做业务流程的自动化。

如果所有的菜单都通过手动来冒烟一遍的话，我们假定一个菜单页面要5秒，那么如果一个项目有600个菜单页面，那么就需要3000秒，整整一个小时啊，而且是每次升级都要来这么一次。于是我就想，虽然没时间将所有功能都做成自动化，那能不能实现对所有的菜单页面都自动去访问一遍，然后根据访问结果判断是不是都是正常的呢？

 

#### 二、实现步骤分析

想法有了，那就得分析该怎么实现了。最大的问题就在于所有菜单对应的url要怎么获取了，通过各种尝试之后，发现所测项目由于框架原因，无法直接通过爬虫来获取所有的url。

但是在数据库里，对应前后台都有两张表，如eclp_A存放有域名，eclp_B则存有各个菜单页面的具体路径，那么只需要把两张表的数据取出来拼接在一起就成了一个完整的url了。

 

具体步骤：

（1）在数据库里将客户端和后台管理端（客户端是uc，后台管理端是eclp）域名和路径取出来进行拼接放到一个新表，并加入对应断言关键词；

（2）不同项目的数据库配置不一样，所以把数据库配置放在配置文件，再通过程序获取；

（3）获取到数据库的配置后，连接数据库，从表里获取到所有的url；

（4）取到所有的url之后遍历去request，但是这个需要先登录获取到cookie后做个cookie绑定，那么登录需要用到的用户密码也需要放到配置文件；

（5）遍历访问所有url，并记录其访问结果，成功与失败个数；

（6）在程序执行访问页面时，把成功与失败的日志都记录下来，方便后续查找问题；

（7）因为url可能比较多，访问所有页面会很慢，所以实现多进程或多线程的方式；

（8）将所有页面访问结果利用HTML报告输出；

 

#### 三、前期准备

**1.** **在数据库（此处是oracle****）拼接URL**

1.1 创建存放url的表

查看后台存放主域名和路径的表

```sql
select t.*,t.rowid from eclp_A t order by id desc;

select t.*,t.rowid from eclp_B t order by id desc;
```

创建存放完整url的表


```sql
drop table eclp_uc_url;

CREATE TABLE eclp_uc_url (

ID NUMBER(20) DEFAULT 0  NOT NULL ,

SUB_SYSTEM_ID  NUMBER(20) DEFAULT 0  NOT NULL ,

SUB_SYSTEM_CODE VARCHAR2(100 BYTE) DEFAULT ''  NULL ,

NAME  VARCHAR2(255 BYTE) DEFAULT ''  NULL , 

DOMAIN VARCHAR2(100 BYTE) DEFAULT ''  NULL ,

PATH VARCHAR2(100 BYTE) DEFAULT ''  NULL ,

URL  VARCHAR2(100 BYTE) DEFAULT ''  NULL ,

ASSERT_WORD  VARCHAR2(255 BYTE) DEFAULT ''  NULL

)
```


创建主键

```sql
ALTER TABLE eclp_uc_url ADD PRIMARY KEY (ID);
```

创建id自增序列


```sql
drop SEQUENCE SEQ_ECLP_UC_URL;

CREATE SEQUENCE SEQ_ECLP_UC_URL

INCREMENT BY 1

START WITH 1

MAXVALUE 9999999999999

CYCLE

CACHE 20;
```


1.2 从两个表分别插入域名和路径到存放url的新表

eclp的路径path插入表

```sql
INSERT INTO eclp_uc_url(id,SUB_SYSTEM_ID,NAME,PATH) SELECT SEQ_ECLP_UC_URL.NEXTVAL,SUB_SYSTEM_ID,NAME,URL from eclp_A;
```

eclp的域名domian插入表

```sql
update eclp_uc_url set DOMAIN = (select DOMAIN from eclp_B where eclp_uc_url.SUB_SYSTEM_ID = eclp_B.id)
where exists (select 1 from eclp_B where eclp_uc_url.SUB_SYSTEM_ID = eclp_sub_system.id);
```

将domain和path拼接成url

```sql
MERGE INTO eclp_uc_url A
USING ( select t.id, t.domain || '/' || t.path as urls from eclp_uc_url t) B ON (A.ID = B.ID)
WHEN MATCHED THEN UPDATE SET A.URL = B.URLS;
```

 

**2.** **数据库配置与前后台用户配置文件**

创建一个DbUser.ini文件：

存有数据库连接配置、前后台用户信息、浏览器选择（chrome、firefox、ie）

```ini
[db]
oracle_ip=127.0.0.1
oracle_account=test_account
oracle_password=test_password

[eclp]
center_url=http://127.0.0.1:8020/system/login.htm
center_account=test01
center_password=123456a
 
[uc]
uc_url=http://127.0.0.1:9001/login.htm
uc_account=test02
uc_password=123456a

[driver]
browser=chrome
```


**3. 前后台登录元素定位方式配置文件**

包含用户名、密码、登录按钮的定位方位，UiObjectMap.ini


```ini
[eclp]
LoginAccount=id>account
LoginPassword=id>password
LoginButton=xpath>//input[@class='btn2']

[uc]
LoginAccount=id>account
LoginPassword=id>password
LoginButton=id>button
```

#### 四、代码实现-封装

**1. 创建包和文件夹目录**

Config：存放配置文件

Driver：存放不同浏览器驱动

TestResults：存放测试结果

TestScripts：存放程序脚本

Util：存放封装方法

**2. 将所需用到目录及文件路径单独整理**

ProjVar.py

```python
import os

proj_path = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
conf_path = os.path.join(proj_path,"config")
dbuser_ini_path = os.path.join(proj_path,"config","DbUser.ini")
objectmap_ini_path = os.path.join(proj_path,"config","UiObjectMap.ini")
driver_path = os.path.join(proj_path,"Driver")
logger_path = os.path.join(proj_path,"config","Logger.conf")
result_path = os.path.join(proj_path,"Testresults")
testscript_path = os.path.join(proj_path,"TestScripts")
```

**3. 从ini配置文件读取数据方法：**

ReadConfig.py


```python
# encoding=utf-8
import configparser
import os
import platform
from Config.ProjVar import *

def read_ini_file(ini_file_path, section_name, option_name):
    #创建一个读取配置文件的实例
    cf = configparser.ConfigParser()
    #将配置文件内容加载到内存
    cf.read(ini_file_path)
    try:
        #根据section和option获取配置文件中的数据
        value = cf.get(section_name, option_name)
    except:
        print("the specific seciton or the specific option doesn't exit!")
        return None
    else:
        return value
```


**4．获取元素对象方法**

根据元素定位方式获取到对应元素对象，后面登录时会用到

ObjectMap.py


```python
#encoding=utf-8
from selenium.webdriver.support.ui import WebDriverWait
import configparser,os
from selenium import webdriver
from Config.ProjVar import *
from Util.ReadConfig import read_ini_file

class ObjectMap(object):
    def __init__(self):
        # 存放页面元素定位表达方式及定位表达式的配置文件所在绝对路径
        self.uiObjMapPath = objectmap_ini_path

    def getElementObject(self, driver, webSiteName, elementName):
        try:
            locators = read_ini_file(self.uiObjMapPath, webSiteName, elementName).split(">")
            # 得到定位方式
            locatorMethod = locators[0]
            # 得到定位表达式
            locatorExpression = locators[1]
            print(locatorMethod, locatorExpression)
            # 通过显示等待方式获取页面元素
            element = WebDriverWait(driver, 10).until(lambda x: \
                                    x.find_element(locatorMethod, locatorExpression))
        except Exception as e:
            raise e
        else:
            # 当页面元素被找到后，将该页面元素对象返回给调用者
            return element
```


**5. 建立一个绑定cookies的session对象**

从元素定位方式配置文件获取到定位方式，然后用ObjectMap方法获取到元素对象，用selenium webdriver自动登录，登录成功后得到cookies

 GetSessionOfCookie.py


```python
from selenium import webdriver
import requests,time,os
from Config.ProjVar import *
from Util.ReadConfig import read_ini_file
from Util.ObjectMap import *
#from selenium.webdriver.chrome.options import Options

def get_session_of_cookie(domain,url,account,password):
    #从配置文件获取配置的浏览器类型，并对应去登录获取cookie
    browser = read_ini_file(dbuser_ini_path, "driver", "browser")
    if browser.lower() == "chrome":
        driverpath = os.path.join(driver_path, "chromedriver.exe")
        driver = webdriver.Chrome(executable_path=driverpath)
    elif browser.lower() == "firefox":
        driverpath = os.path.join(driver_path,"geckodriver.exe")
        driver = webdriver.Firefox(executable_path=driverpath)
    elif browser.lower() == "ie":
        driverpath = os.path.join(driver_path, "IEDriverServer.exe")
        driver = webdriver.Ie(executable_path=driverpath)
    driver.maximize_window()
    time.sleep(1)
    #打开前后台登录页面
    driver.get(url)
    driver.implicitly_wait(5)
    # 获取登录页面元素传值登录
    objmap = ObjectMap()
    if domain == "eclp":
        objmap.getElementObject(driver, "eclp", "LoginAccount").send_keys(account)
        objmap.getElementObject(driver, "eclp", "LoginPassword").send_keys(password)
        objmap.getElementObject(driver, "eclp", "LoginButton").click()
    elif domain == "uc":
        objmap.getElementObject(driver, "uc", "LoginAccount").send_keys(account)
        objmap.getElementObject(driver, "uc", "LoginPassword").send_keys(password)
        objmap.getElementObject(driver, "uc", "LoginButton").click()
    time.sleep(3)
    #获取登录后的cookies
    allcookies = driver.get_cookies()
    print("获取到登录后的cookies:%s" % allcookies)
    driver.quit()
    #把上面获取的的cookies添加到s中
    s = requests.session()
    try:
        # 添加cookies到CookieJar
        c = requests.cookies.RequestsCookieJar()
        for i in allcookies:
            c.set(i["name"], i['value'])
        # 更新session里cookies
        s.cookies.update(c)  
    except Exception as e:
        print(u"添加cookies报错：%s" %str(e))
    print("查看添加后s的cookies")
    print(s.cookies)
    return s
```


**6. 从数据库表查询URL**

 这里使用的是cx_Oracle连接oracle，注意需要使用与python位数（32或64）对应的数据库instantclient客户端：

GetUrlFromOra.py

```python
import cx_Oracle
from Util.ReadConfig import read_ini_file
from Config.ProjVar import *

def get_url_from_oracle(ip,account,password,domain):
    db=cx_Oracle.connect(account+'/'+password+'@'+ip+'/orcl')
    cr = db.cursor()
    sql = ""
    #根据是eclp还是uc来获取前端还是后端的url
    if domain == "eclp":
        sql = 'select sub_system_id,name,url,assert_word from eclp_uc_url where sub_system_id != 0  order by id desc'
    elif domain == "uc":
        sql = 'select sub_system_id,name,url,assert_word from eclp_uc_url where sub_system_id = 0 order by id desc'
    cr.execute(sql)
    result = cr.fetchall()
    #返回获取到的所有结果
    return result
```

 

**7. 创建存放测试结果的文件夹方法**

先创建一个执行当天日期为名称的文件夹，若一天执行了多次，在日期文件夹下建一个“第n次”为名称的文件夹

```python
#encoding = utf-8
import os
import time
from Config.ProjVar import *

#创建日期格式文件夹
def make_date_dir(dir_path):
    if os.path.exists(dir_path):
        #获取当前时间
        timeTup = time.localtime()
        #转为xxxx年xx月xx日的格式
        currentDate = str(timeTup.tm_year) + "年" + str(timeTup.tm_mon) + "月" + str(timeTup.tm_mday) + "日"
        #用目标目录拼接日期得到绝对路径
        path = os.path.join(dir_path,currentDate)
        if not os.path.exists(path):
            os.mkdir(path)
    else:
        raise Exception("dir_path does not exist!")
    return path

#创建日期文件夹下多次执行的目录
def make_report_dir():
    #先创建一个日期格式为名称的文件夹
    date_path = make_date_dir(result_path)
    #判断当前目录已有文件夹数，加1得到新文件夹名并创建
    report_path = os.path.join(date_path, "第" + str(len(os.listdir(date_path)) + 1) + "次测试")
    os.mkdir(report_path)
    #进入到新创建文件夹并获取当前的绝对路径，作为后面存放测试结果的文件夹
    os.chdir(report_path)
    result_report_path = os.getcwd()
    return result_report_path
```

 

**8. Log日志设置**

使用python的Logger.conf配置文件，设置我们需要的日志级别和存放目录

 

**9. HTML报告模板**

创建一个htmlTemplate方法，里面先用HTML语言设置好自己想要的报告模板，后面将测试结果与模板拼接即可。

 

#### 五、主程序实现

从数据库获取url方法封装：断言关键词等数据放进队列中，并返回前台客户端uc和后台管理端eclp绑定cookie的session对象

```python
def get_urls(domain,q):
    #判断是前端uc还是后端eclp去获取对应的登录数据
    if domain == "eclp":
        login_url = read_ini_file(dbuser_ini_path, "eclp", "center_url")
        account = read_ini_file(dbuser_ini_path, "eclp", "center_account")
        password = read_ini_file(dbuser_ini_path, "eclp", "center_password")
    elif domain == "uc":
        login_url = read_ini_file(dbuser_ini_path, "uc", "uc_url")
        account = read_ini_file(dbuser_ini_path, "uc", "uc_account")
        password = read_ini_file(dbuser_ini_path, "uc", "uc_password")
    #获取eclp或uc的绑定cookie的session对象
    s = get_session_of_cookie(domain,login_url,account,password)
    #从配置文件获取数据库的连接配置
    ora_ip = read_ini_file(dbuser_ini_path, "db", "oracle_ip")
    ora_account = read_ini_file(dbuser_ini_path, "db", "oracle_account")
    ora_password = read_ini_file(dbuser_ini_path, "db", "oracle_password")
    #从数据库获取所有的url
    url_datas = get_url_from_oracle(ora_ip,ora_account,ora_password,domain)
    #将获取到的所有url放入队列中
    for data in url_datas:
        q.put(data)
    return s
```


执行获取测试结果方法封装：

在队列不为空时，循环取得url、断言关键词等数据，然后利用绑定了cookied的session对象访问得到结果；

获取资源锁记录成功与失败个数，并将结果放入一个共享的html字符串中，以便最后组装成完成的HTML报告：


```python
def get_urls_info():
    #设置测试结果为全局变量
    global share_result
    global success_case_num
    global fail_case_num
    flagDict = {0: 'red', 1: '#00AC4E'}
    #队列不为空时，循环获取数据
    while not q.empty():
        #每个测试数据的测试结果，success或fail
        test_case_result = ""
        start = time.time()
        start_time = time.strftime("%Y-%m-%d %H:%M:%S", time.localtime())
        #取队列
        data = q.get()
        #分别取出要用到的菜单名称、url、断言关键词等数据
        sub_system_id = data[0]
        url_name = data[1]
        url = data[2]
        assert_word = data[3]
        #存放断言失败的菜单的源码文件路径
        html_file_path = os.path.join(result_report_path, url_name + ".html")
        #访问菜单页面并记录结果
        try:
            判断是eclp的还是uc的菜单，用不同的session对象去访问
            if sub_system_id == 0:
                r = uc_s.get(url)
            else:
                r = eclp_s.get(url)
            #断言
            assert assert_word in r.text
            flag = 1
            debug("菜单： " + url_name + " 断言成功：")
            #记录当次是否访问成功
            test_case_result = "Success"
            #获得资源锁，记录成功个数
            lock.acquire()
            success_case_num += 1
            lock.release()
        except AssertionError as e:
            flag = 0
            error("菜单： " + url_name+" 关键词断言失败，错误信息：" + str(traceback.format_exc()))
            test_case_result = "Fail"
            #如果失败，获取资源锁，记录失败数
            lock.acquire()
            fail_case_num += 1
            lock.release()
            #将失败的菜单页面的源码保存为一个html文件，存到测试结果目录
            with open(html_file_path, "w", encoding="utf-8") as fp:
                fp.write(r.text)
        except Exception as e:
            flag = 0
            error("菜单： " + url_name+" 发生未知异常，错误信息：" + str(traceback.format_exc()))
            test_case_result = "Fail"
            lock.acquire()
            fail_case_num += 1
            lock.release()
            with open(html_file_path, "w", encoding="utf-8") as fp:
                fp.write(r.text)
        finally:
            end = time.time()
            waste_time = int((end - start) * 1000)
            #获取资源锁，将当次访问的菜单名称、url断言关键词、时间等添加到html字符串
            lock.acquire()
            # 每一组数据测试结束后，都将其测试结果信息插入表格行的HTML代码中
            share_result += '''
                  <tr>
                      <td>%s</td>
                      <td>%s</td>
                      <td>%s</td>
                      <td>%s</td>
                      <td>%s</td>
                      <td style="color:%s">%s</td>
                  </tr>''' % (url_name, url, assert_word, waste_time, start_time, flagDict[flag], test_case_result)
            lock.release()
```


由于这里更多的是文本、多IO的操作，因此我们选择多线程来实现：


```python
#定义一个继承threading.Thread线程的类
class MyThread(threading.Thread):
    def __init__(self, func):
        threading.Thread.__init__(self)
        self.func = func
    def run(self):
        self.func()
```


main程序：

```python
if __name__ == "__main__":
    # 创建用来存放日志和报告的文件夹
    result_report_path = make_report_dir()
    #建一个线程共享资源锁
    lock = threading.Lock()
    #定义一个队列
    q = Queue()
    #定义测试结果变量
    share_result = ""
    success_case_num = 0
    fail_case_num = 0
    #获取前后台的绑定了cookie的session对象
    eclp_s = get_urls("eclp",q)
    uc_s = get_urls("uc",q)

    #创建多个线程，并将get_urls_info任务传入处理
    threads = []
    for i in range(4):
        thread = MyThread(get_urls_info)
        thread.start()
        threads.append(thread)
    for thread in threads:
        thread.join()
    #将测试结果与模板拼接得到HTML报告
    htmlTemplate(share_result,result_report_path,success_case_num,fail_case_num)
```


#### 六、测试结果

在测试结果目录Testresults中有不同日期的文件夹：

![img](https://img2018.cnblogs.com/blog/1674546/201907/1674546-20190704142701947-171300057.png)

下层是一天中多次执行的文件夹，要看最新的测试结果进入“第n次”中n最大的文件夹即可：

 ![img](https://img2018.cnblogs.com/blog/1674546/201907/1674546-20190704142718747-333582011.png)

进入后可以查看到访问失败菜单的html源码，若执行后有失败的菜单，可以打开查看里面源码是什么样的；

![img](https://img2018.cnblogs.com/blog/1674546/201907/1674546-20190704142932566-1316683480.png)

另外还有当次执行的《冒烟测试报告》，打开可以查看总个数、成功个数、失败个数以及每个菜单的执行情况，

 失败的菜单页面对应测试结果会显示红色：

![img](https://img2018.cnblogs.com/blog/1674546/201907/1674546-20190704144046530-1497509465.png)

 

在程序脚本目录下会生成一个日志文件SmokingTestLog.log，每次执行的日志都会增量记录在这里：

 ![img](https://img2018.cnblogs.com/blog/1674546/201907/1674546-20190704143605718-278648839.png)

 

#### 七、总结

至此快速的冒烟测试就完成了，基本实现了可以从数据库获取所有菜单页面URL，并利用多线程遍历访问，并记录结果生成HTML报告的功能。

可应用场景：

（1）开发人员开发完成，在开发环境执行快速冒烟，检查是否有异常页面；

（2）新功能提测后，测试人眼执行快速冒烟，检查是否有异常页面再深入测试；

（3）发布UAT之前，执行快速冒烟，回归是否有页面异常

（4）实施人员升级后，执行快速冒烟，检验是否部署、配置正确，避免线上出现异常

不同的项目可能模式有所不同，如果菜单未放在表里，则需要看是否可以直接爬取或是其他方式实现了。

若文中有不正确的地方，或有更好的实现方法，欢迎指出并提宝贵意见。