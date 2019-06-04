# python的ddt和csv进行数据传递

注意：按照ddt，pip install ddt

![img](https://img-blog.csdnimg.cn/20181127191857183.png)

login.csv内容如下

![img](https://img-blog.csdnimg.cn/20181127191924504.png)

测试代码：

```
import unittest
import ddt
import csv


def getCsvData(filepath):
    # 读取CSV文件
    value_rows = []
    with open(filepath, encoding='UTF-8') as f:
        f_csv = csv.reader(f)
        next(f_csv)
        for r in f_csv:
            value_rows.append(r)
    return value_rows

@ddt.ddt
class MyTestCase(unittest.TestCase):
    data =getCsvData("./login.csv")

    @ddt.data(*data)
    @ddt.unpack
    def test_logindata(self,*data):
        username,password,code=data
        print("用户名是"+username+"密码是:"+password+"验证码是:"+code
if __name__ == '__main__':
    unittest.main()
```

测试结果如下

![img](https://img-blog.csdnimg.cn/20181127192102702.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE5NzE1NTg=,size_16,color_FFFFFF,t_70)

# python驱动DDT框架应用--实战举例

## **步骤:**

## **创建一个python file—ddttest**

![这里写图片描述](https://img-blog.csdn.net/20171105224237917?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VzaHVhaTE1MDgzMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## **引入ddt**

![这里写图片描述](https://img-blog.csdn.net/20171105224643817?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VzaHVhaTE1MDgzMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## **更新ddt**

```
File-Settings-双击pip-搜索框输入:ddt1
```

![这里写图片描述](https://img-blog.csdn.net/20171105224904994?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VzaHVhaTE1MDgzMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](https://img-blog.csdn.net/20171105224916627?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VzaHVhaTE1MDgzMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

# **实例讲解:**

## **传入一个参数:**

```
import unittest
from ddt import ddt,data,unpack

@ddt
class MyTestCase(unittest.TestCase):
    #下面的1,2,3代表我们传入的参数,每次传入一个值
    @data(1,2,3)
    #定义一个value用于接收我们传入的参数
    def test_something(self,value):
        #对于传入的参数与2进行对比,相等就通过,否则就是不同过
        self.assertEqual(value, 2)
if __name__ == '__main__':
    unittest.main()
1234567891011121314
```

## **日志信息:**

![这里写图片描述](https://img-blog.csdn.net/20171105225952413?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VzaHVhaTE1MDgzMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## **传入两个参数:**

```
import unittest
from ddt import ddt,data,unpack

@ddt
class MyTestCase(unittest.TestCase):
    #下面的(1,2)(2,3)代表我们传入的参数,每次传入两个值
    @data((1,2),(2,3))
    #告诉我们的测试用例传入的是两个以上的值
    @unpack
    #定义两个参数value用于接收我们传入的参数
    def test_something(self,value1,value2):
        print value1,value2
        #对于传入的第一个参数+1与第二个参数进行对比,相等就通过,否则就是不通过
        self.assertEqual(value2, value1+1)
if __name__ == '__main__':
    unittest.main()
1234567891011121314151617
```

## **报错:–未在开头加入#encoding:utf-8**

![这里写图片描述](https://img-blog.csdn.net/20171105231408164?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VzaHVhaTE1MDgzMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

## **解决:加入即可:**

```
#usr/bin/python
#encoding:utf-8
import unittest
from ddt import ddt,data,unpack

@ddt
class MyTestCase(unittest.TestCase):
    #下面的(1,2)(2,3)代表我们传入的参数,每次传入两个值
    @data((1,2),(2,3))
    #告诉我们的测试用例传入的是两个以上的值
    @unpack
    #定义两个参数value用于接收我们传入的参数
    def test_something(self,value1,value2):
        print value1,value2
        #对于传入的第一个参数+1与第二个参数进行对比,相等就通过,否则就是不通过
        self.assertEqual(value2, value1+1)
if __name__ == '__main__':
    unittest.main()
12345678910111213141516171819
```

## **运行:–成功**

![这里写图片描述](https://img-blog.csdn.net/20171105231620549?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3VzaHVhaTE1MDgzMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)