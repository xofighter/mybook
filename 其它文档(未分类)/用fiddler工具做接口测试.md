接口测试的原理我理解很简单，是你发送数据，看返回数据是否正确，返回值类型是否正常，主要点是多种数据的，返回结果正常

和其他应用程序相同，java中需要知道你的参数，字段有哪些，我们就去给他造数据，而先公司是给的一个请求参数和地址，开始之前先说下接口的一些规范：

### 1.前端请求接口

**接口方法：MethodName(string session, int appType, string args, DateTime timestamp, string sign)调用接口方法的地址为：http://api.m.hzins.com/MethodName调用接口方法应该POST的数据格式：session=xxx&appType=1&args={json}&timestamp=now&sign=xxx**

#### 请求参数说明：

> MethodName:需要调用的方法名
> session：用户session
> appType : 调用的app类型
> args：方法调用所需要参数（如果是多个参数，将参数写成json对象进行序列化）
> timestamp：为了让签名更加安全增加一个时间因素
> sign：签名 根据约定规则对参数进行加密后的字符串

### 2. 后端接口返回数据规范：

##### {"State":7508, "Msg":"请求成功", "Data":"Data String"}

#### 返回参数说明：

> State： 返回状态码
> Msg：返回信息
> Data：返回处理数据（Json字符串）

在没有工具帮助的情况下， 需要测试接口测试后台是c#编写的，可以借助两种方式进行接口的测试

**一：URL直接请求**



###### Request

接口地址: <http://api.m.hzins.com/Insure/GetInsureDetail> 请求方式: **POST**

### 请求参数:

```xml
<code>ProductId：产品id，
PlanId：公司Id</code>
```

**返回数据：**



```
{



"State":0,



"Msg":"操作成功",



"Data":{



    "MaxPeople":100,    //最大投保人数 （目前没什么用）



    "HaveDestinationCountry":0,     //是指定旅行目的地（目前木有用到）



    "ProductAttributeAll":[       //投保属性信息 



        {



            "Childs":[        //字段可以选择的项



                {



                    "BindEntity":"",  



                    "ControlTypeId":5,



                    "DefaultSelected":false,



                    "DisplayOrder":1,



                    "FieldName":"",



                    "Id":4,



                    "InitClass":"",



                    "IsRequired":true,



                    "IsSelected":false,



                    "Name":"身份证",



                    "ParentId":3,



                    "SettingTypeId":10,



                    "Value":"1"



                },



                {



                    "BindEntity":"",



                    "ControlTypeId":5,



                    "DefaultSelected":false,



                    "DisplayOrder":2,



                    "FieldName":"",



                    "Id":5,



                    "InitClass":"",



                    "IsRequired":true,



                    "IsSelected":false,



                    "Name":"护照",



                    "ParentId":3,



                    "SettingTypeId":10,



                    "Value":"2"



                },
 
```



1.URL直接请求 gest请求

http://api.m.hzins.com/Product/GetProuductDetailInfo?args={"ProductId":"123","PlanId":"456"}，在url后面直接添加参数，这个请求要后台允许这样的方法，成功后返回以上数据

2.利用fiddler发送请求 post请求

![img](http://images2015.cnblogs.com/blog/597371/201604/597371-20160428114250423-1673003785.png)

![img](http://images2015.cnblogs.com/blog/597371/201510/597371-20151014194646038-716858787.png)

有关其他fiddler的一些小技巧，觉得这个贴还不错：http://blog.chinaunix.net/uid-27105712-id-3738821.html