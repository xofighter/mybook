## JMeter 连接 数据库 —— 以MySQL为例

### 一、添加 mysql驱动

Jmeter工具中有sampler-JDBC Request，可以使用JDBC Request直接连接到数据库，不过使用前需要添加对应的驱动包，例如mysql的驱动包。

1、下载安装mysql驱动包
提供下载地址：https://dev.mysql.com/downloads/connector/j/

![img](https://img-blog.csdn.net/20180321093546245?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L0Zsb3JhQ0hZ/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



也可以自行百度搜索下载。

下载完成之后，将mysql驱动放置到...\apache-jmeter-3.2\lib目录下。

![img](https://img-blog.csdn.net/20180321093709690?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L0Zsb3JhQ0hZ/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

然后重新启动JMeter，添加jar包完成。

**注意：如果遇到jar包还是没有生效的情况，还可以在计划中添加jar包。**

打开JMeter,点击测试计划，点击“浏览...”按钮，将你的JDBC驱动包添加进来。

![img](https://img-blog.csdn.net/20180507183434681?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dsb25nbGps/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 二、Jmeter 连接 数据库的配置操作

Jmeter连接数据库需要配置两个元件：JDBC Connection Configuration和JDBC Request

#### JDBC Connection Configuration 配置

1. 添加 JDBC Connection Configuration

   右键线程组->添加->配置元件->JDBC Connection Configuration

   ![img](https://images2015.cnblogs.com/blog/1139190/201707/1139190-20170706154202394-726384234.png)

2. JDBC Connection Configuration 界面控件介绍

   ![1561607735828](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1561607735828.png)

- Varible Name: 这个是变量的名字，具有唯一性，不能重复

- Database URL: jdbc:mysql://数据库地址:数据库端口号/数据库名 ；如果要执行多条SQL语句，后面还要添加“?allowMultiQueries=true”

  ![1561608001950](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1561608001950.png)

- JDBC Driver Class ：org.gjt.mm.mysql.Driver  或者 com.mysql.jdbc.Driver   这是固定值

- Username：这个数据库的登录名

- Password： 这个是数据库的登录密码

![img](https://img-blog.csdn.net/20180321100519379?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L0Zsb3JhQ0hZ/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



3. 配置 JDBC Connection Configuration

以下画圈的地方均需填写，其他内容为默认值即可。

![img](https://images2015.cnblogs.com/blog/1139190/201707/1139190-20170706154254909-1591366443.png)

#### JDBC Request 配置
1. 添加 JDBC Request

给线程组添加Sampler>JDBC Request。

![img](https://img-blog.csdn.net/20180507183600177?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dsb25nbGps/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

2. JDBC Request 界面介绍

![img](https://img-blog.csdn.net/2018032110241583?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L0Zsb3JhQ0hZ/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

- Variable Name： 
数据库连接池的名字，需要与上面配置的JDBC Connection Configuration中Variable Name Bound Pool的Variable Name相同 
- Query：填写的sql语句未尾不要加“;” 
- Parameter valus：参数值 
- Parameter types：参数类型，可参考：Javadoc for java.sql.Types 
- Variable names：保存sql语句返回结果的变量名 
- Result variable name：创建一个对象变量，保存所有返回的结果 
- Query timeout：查询超时时间 
- Handle result set：定义如何处理由callable statements语句返回的结果




| 属性 | 描述 | 备注 |
| -- | -- | -- |
| Variable Name | 选择使用哪个配置，值与JDBC Connection Configuration 的Variable Name相同	| 不能为空。JDBC Connection Configuration 可以配置多个，这里只要选择自己想用的那个即可 |
| Query Type    |     Select Statement  Update Statement  Callable Statement Prepared Select Statement   Prepared Update Statement   Commit  Rollback   Autocommit(false)    Autocommit(true)    Edit    |   Select 选择Select Statement。Insert, Update, Delete 选择 Update Statement.2. 如果使用预编译（语句中带问号）必须选择 Prepared Select(Update) Statement(建议使用Prepared)3. 其他类型不算常用，需要的时候找下资料吧 |
| SQL Query | Sql语句 | 不能为空。语句结束建议不要带上分号（虽然有分号一般也不会有问题） |
| Parameter values | 参数值。多个值时使用逗号分割。数量必须与sql语句中的参数个数相同 | 当选择Prepared或Callable 时不能为空 |
| Parameter types | 参数类型。多个值时使用逗号分割。数量必须与参数值相同。 | 使用不同的驱动，参数类型可能会有不同。常见的比如varchar, int…, 支持大小写 |
|   Variable Names    |    查询结果变量名称，每个查询字段都可以设置一个变量，逗号分割。返回结果，比如：id_#, id表示查询字段，id_#表示查询结果的行数，id_1表示查询结果中第一行的id 的值（类推，id_2为第二行id的值） | 可以为空。建议：需要用到的话，尽量每个字段都设置一个变量名称（虽然设置少几个也不报错）；sql语句中尽量不要使用 * 号，而是写清楚每个要查询的字段  |
| Result variable name | 结果变量名称。将所有的查询结果保存到一个变量中。这是一个List<Map<String,Object>>类型的变量。查询结果每行存为一个Map, 字段名为 key, 字段内容为value.	 |   比如要bean shell 中可以这样调用：columnValue = vars.getObject(“resultObject”).get(0).get(“Column Name”);  |
| Handle ResultSet |  Store As String (默认)2. Store As Object3. Count Records | |

###  Jmeter 数据库操作的场景

#### 单sql语句

#### 操作流程

1. 添加线程组，在线程组中增加 JDBC Connection Configuration 和 JDBC Request ，

![1561689275505](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1561689275505.png)

##### 增、删、改语句

1、          创建一个存储过程，语句如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
DELIMITER $$;
DROP PROCEDURE IF EXISTS test;
create PROCEDURE test()
BEGIN
   SELECT * FROM cource;
   INSERT INTO cource VALUES(104,"java");
   UPDATE cource SET C_name="C#" WHERE ID=104;
   delete from cource where ID=104;
END
$$;
DELIMITER ;;
Call test;  //调用存储过程
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

注意：测试的要先切换到相应的数据库下才能创建存储过程成功

2、          打开JMeter,点击测试计划，点击“浏览...”按钮，将JDBC驱动添加进来

![img](https://images2017.cnblogs.com/blog/1204057/201710/1204057-20171030210752324-2124074696.png)

3、          添加一个线程组测试计划-右键添加-Threads（user）-线程组

![img](https://images2017.cnblogs.com/blog/1204057/201710/1204057-20171030210817136-256275859.png)

4、          右键点击“线程组”，在下面添加一个“JDBC Connection Configuration”

![img](https://images2017.cnblogs.com/blog/1204057/201710/1204057-20171030210835386-87344097.png)

5、          配置JDBC Connection Configuration，指定数据库URL、JDBC驱动类、数据库连接用户名和密码。其中besttest为需要连接的数据库名，密码为空，如下图：

Variable Name:mysql

Database Connection Configuration:

Database URL: jdbc:mysql://localhost:3306/besttest

JDBC Driver class: org.gjt.mm.mysql.Driver

Username:数据库用户名

Password：数据库密码

![img](https://images2017.cnblogs.com/blog/1204057/201710/1204057-20171030210910340-993515676.png)

6、          右键点击“线程组”，添加一个“JDBC request”，调用创建的存储过程test

Variable Name:mysql

Call test； //调用存储过程

![img](https://images2017.cnblogs.com/blog/1204057/201710/1204057-20171030210926136-238059316.png)

7、          右键点击线程组---->添加--->监听器---->查看结果树，运行。

![img](https://images2017.cnblogs.com/blog/1204057/201710/1204057-20171030210940199-901581963.png)

##### 查询语句
##### 查询单个字段

###### 查询多个字段

###### 获取返回结果(所有返回值)

#### 多sql语句

##### 一、环境准备：

　　1、准备包含必要数据的数据库；

　　2、JDBC驱动；

##### 二、操作步骤：

　　1、添加JDBC驱动；

 　　![img](https://images2017.cnblogs.com/blog/1123699/201712/1123699-20171215192720855-1570407567.png)

　　2、添加“线程组”，并在下面添加一个“JDBC Connection Configuration”；

 　　![img](https://images2017.cnblogs.com/blog/1123699/201712/1123699-20171215192745777-175749291.png)

　　3、配置一下JDBC Connection Configuration页面，输入数据库

　　1）Database URL：jdbc:mysql://localhost:3306/hkkTest，其中hkkTest为数据库名称；

　　2）username和password填入正确账号和密码即可；

 　　![img](https://images2017.cnblogs.com/blog/1123699/201712/1123699-20171215192846230-1454854726.png)

　　4、在线程组下添加JDBC Request；

 　　![img](https://images2017.cnblogs.com/blog/1123699/201712/1123699-20171215192902652-1684461379.png)

　　5、JDBC Request中写入sql语句，并选择正确类型即可；

 　　![img](https://images2017.cnblogs.com/blog/1123699/201712/1123699-20171215192926215-1910477978.png)

　　6、需要注意的是，该设置下只能写入单条语句，如果试图执行多种类型sql语句，可有以下两种方案：

　　方案一：

　　1）首先，建立存储过程，注意不要执行CALL语句；

 　　![img](https://images2017.cnblogs.com/blog/1123699/201712/1123699-20171215193221668-32289147.png)

　　2）在Jmeter的JDBC Request中执行下图操作；

 　　![img](https://images2017.cnblogs.com/blog/1123699/201712/1123699-20171215193237449-363035877.png)

　　点击“启动”后，发现数据库数据发生响应改变

　　方案二：在JDBC Connection Configuration配置页面，Database URL：jdbc:mysql://localhost:3306/hkkTest后添加?allowMultiQueries=true，然后将JDBC Request中的Query Type选择为Callable Statement。

　　7、添加响应断言、断言结果、图形结果、查看结果树；

　　![img](https://images2017.cnblogs.com/blog/1123699/201712/1123699-20171215193401496-43157978.png)

　　1）  Sql语句：SELECT Name FROM students where ID='801';

 　　![img](https://images2017.cnblogs.com/blog/1123699/201712/1123699-20171215193345605-864636467.png)

　　2）预期结果：

 　　![img](https://images2017.cnblogs.com/blog/1123699/201712/1123699-20171215193431121-447172809.png)

　　3）Jmeter运行结果：

 　　![img](https://images2017.cnblogs.com/blog/1123699/201712/1123699-20171215193451168-1156901211.png)

 

------------------




![img](https://img-blog.csdn.net/20180507183702297?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dsb25nbGps/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



3、配置JDBC
给线程组添加配置原件>JDBC Connection Configuration。

1)Varible Name: 这个是变量的名字，具有唯一性，不能重复

2) Database URL: jdbc:mysql://数据库地址:数据库端口号/数据库名 

3) JDBC Driver Class ：org.gjt.mm.mysql.Driver  或者 com.mysql.jdbc.Driver   这是固定值

4) Username：这个数据库的登录名

5) Password： 这个是数据库的登录密码





4、添加JDBC Request
给线程组添加Sampler>JDBC Request。

Variable Name： 
数据库连接池的名字，需要与上面配置的JDBC Connection Configuration中Variable Name Bound Pool的Variable Name相同 
Query：填写的sql语句未尾不要加“;” 
Parameter valus：参数值 
Parameter types：参数类型，可参考：Javadoc for java.sql.Types 
Variable names：保存sql语句返回结果的变量名 
Result variable name：创建一个对象变量，保存所有返回的结果 
Query timeout：查询超时时间 
Handle result set：定义如何处理由callable statements语句返回的结果





5、添加查看结果树，运行线程组，查看结果




6、其他地方引用查询结果作为参数
我们需要在JDBC Request中设置Variable names作为参数名，该参数名也具有唯一性，必须不与其他变量名重复。

上图中我们已经设置了Variable names=UserName。

我们的SQL语句只查询了UserName这一个字段，那么我们想要引用第1个UserName，可以如下书写：${UserName_1}；第2个可以如下书写：${UserName_2}，依次类推。



以下是JMeter帮助文档中的介绍：



---------------------

-----------------

# [jmeter实践之数据库参数传递](https://www.cnblogs.com/huiguniang/p/7126991.html)



###  

### 一、需求：

**1、业务需求：根据手机号到数据库中查看用户id，再根据用户id查看该注册用户下关联的健康成员。**

**2、参数化分析**

1）需要根据不同的手机号进行查询，所以手机号需要进行参数化

2）用户id要作为下一个查询的参数，所以也需要参数化

### 二、实现

**1.**   **下载数据库驱动包，并在jmeter中添加该jar包**

下载mysql-connector-java.jar，存放到本地如:D:\jmeter相关

引入jar包：测试计划->浏览->选择jar文件

![img](https://images2015.cnblogs.com/blog/1139190/201707/1139190-20170706154006519-1927236762.png)

 

**2.**   **添加** **DBC Connection Configuration**

右键线程组->添加->配置元件->JDBC Connection Configuration

![img](https://images2015.cnblogs.com/blog/1139190/201707/1139190-20170706154202394-726384234.png)

配置

 ![img](https://images2015.cnblogs.com/blog/1139190/201707/1139190-20170706154254909-1591366443.png)

说明：

Variable Name：注意该变量名，后面添加jdbc请求时都需要与该名称保持一致。

Database URL：jdbc:mysql://mysql_host_ip:mysql_port/mysql_db_name

JDBC Driver class：com.mysql.jdbc.Driver

username：数据库用户名

password：数据库密码

#### **3.  添加用户变量**

![img](https://images2015.cnblogs.com/blog/1139190/201707/1139190-20170706160227003-1200734820.png)

 

**4.** **添加****JDBC Request**

右键线程组->添加->Sampler->JDBC Request

![img](https://images2015.cnblogs.com/blog/1139190/201707/1139190-20170706154512112-394659461.png)

配置：

![img](https://images2015.cnblogs.com/blog/1139190/201707/1139190-20170706162220456-1202540635.png)

Variable Name：注意与之前配置的名称保持一致

Query Type：含变量的sql查询，选择Prepared Select Statement

Query:输入sql语句，注意不要加分号

Parameter values:引用用户定义的变量${my_phone}

Parameter types:变量的类型varchar

Variable names:保存查询结果，供后续接口调用，注意调用时加“_1”

####  5.添加HTTP请求

右键线程组->添加->Sampler->HTTP Request

![img](https://images2015.cnblogs.com/blog/1139190/201707/1139190-20170706161524050-2146742807.png)

注意：

服务器名称或IP：不要加http://

变量值：使用数据库查询结果作为变量 ${my_id_1}，注意加“_1”

#### 6.**添加查看结果树**

右键线程组->添加->监听器->查看结果树

#### 7.运行

![img](https://images2015.cnblogs.com/blog/1139190/201707/1139190-20170706162102722-1434441814.png)



---------------

5、添加查看结果树，运行线程组，查看结果




6、其他地方引用查询结果作为参数
我们需要在JDBC Request中设置Variable names作为参数名，该参数名也具有唯一性，必须不与其他变量名重复。

上图中我们已经设置了Variable names=UserName。

我们的SQL语句只查询了UserName这一个字段，那么我们想要引用第1个UserName，可以如下书写：${UserName_1}；第2个可以如下书写：${UserName_2}，依次类推。



以下是JMeter帮助文档中的介绍：







---------------------
作者：florachy 
来源：CSDN 
原文：https://blog.csdn.net/florachy/article/details/79635134 
版权声明：本文为博主原创文章，转载请附上博文链接！