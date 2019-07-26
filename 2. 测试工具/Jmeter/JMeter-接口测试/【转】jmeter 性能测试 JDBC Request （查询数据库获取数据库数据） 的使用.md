## [jmeter 性能测试 JDBC Request （查询数据库获取数据库数据） 的使用](https://www.cnblogs.com/0201zcr/p/5280090.html)

JDBC Request

　　这个Sampler可以向数据库发送一个jdbc请求（sql语句），并获取返回的数据库数据进行操作。它经常需要和JDBC Connection Configuration配置原件（配置数据库连接的相关属性，如连接名、密码等）一起使用。

一、准备工作

**1、本文使用的是mysql数据库进行测试**，数据库的用户名为root（你自己的用户名），用户名密码为*********（你自己的密码）

**2、数据库中有表**：test，表的数据结构如下：

![img](https://images2015.cnblogs.com/blog/731178/201603/731178-20160315115457724-870053817.png)

　　表中数据如下：

```
select * from test
```

![img](https://images2015.cnblogs.com/blog/731178/201603/731178-20160315115725912-1623841462.png)

**3、添加需要的驱动jar包**

　　使用不同的数据库，我们需要引入不同的jar包。

方式1：直接将jar包复制到jmeter的lib目录

- mysql数据库：无需引入其他数据库驱动jar包。
- sql server 数据库：下载sqljdbc4.jar 放到 jmeter根目录的lib目录下
- oracle数据库：将oracle数据的安装目录下面的\product\10.2.0\db_1\jdbc\lib\ojdbc14.jar 放到jmeter根目录下的lib目录下

方式2：通过Test Plan

　　假如我们不使用将jar复制到jmeter的lib目录的方式，我们还可以使用Jmeter的Test Plan引入相应的jar包，如下面引入oracle数据的jar包

![img](https://images2015.cnblogs.com/blog/731178/201603/731178-20160315120438849-1778654273.png)

二、配置JDBC Connection Configuration

**重要参数说明：**

- **Variable Name**：数据库连接池的名称，我们可以有多个jdbc connection configuration，每个可以起个不同的名称，在jdbc request中可以通过这个名称选择合适的连接池进行使用。
- **Database URL**：数据库url，jdbc:mysql://主机ip或者机器名称:mysql监听的端口号/数据库名称， 如：jdbc:mysql://localhost:3306/test
- **JDBC Driver class**：JDBC驱动
- **username**：数据库登陆的用户名
- **passwrod**：数据库登陆的密码

　　不同数据库具体的填写方式，可以参考下面的表格：

| **Datebase**  | **Driver class**                                             | **Database URL**                                             |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| MySQL         | com.mysql.jdbc.Driver                                        | jdbc:mysql://host:port/{dbname}                              |
| PostgreSQL    | org.postgresql.Driver                                        | jdbc:postgresql:{dbname}                                     |
| Oracle        | oracle.jdbc.driver.OracleDriver                              | jdbc:oracle:thin:user/pass@//host:port/service               |
| Ingres (2006) | ingres.jdbc.IngresDriver                                     | jdbc:ingres://host:port/db[;attr=value]                      |
| MSSQL         | com.microsoft.sqlserver.jdbc.SQLServerDriver或者net.sourceforge.jtds.jdbc.Driver | jdbc:sqlserver://IP:1433;databaseName=DBname或者jdbc:jtds:sqlserver://localhost:1433/"+"library" |

　　填写后大致如下：

![img](https://images2015.cnblogs.com/blog/731178/201603/731178-20160315121305521-631208239.png)

三、配置JDBC Request

**重要的参数说明：**

- **Variable Name**：数据库连接池的名字，需要与JDBC Connection Configuration的Variable Name Bound Pool名字保持一致
- **Query**：填写的sql语句未尾不要加“;”
- **Parameter valus**：参数值
- **Parameter types**：参数类型，可参考：Javadoc for java.sql.Types
- **Variable names**：保存sql语句返回结果的变量名
- **Result variable name**：创建一个对象变量，保存所有返回的结果
- **Query timeout**：查询超时时间
- **Handle result set**：定义如何处理由callable statements语句返回的结果

![img](https://images2015.cnblogs.com/blog/731178/201603/731178-20160315145807256-500534529.png)

执行结果：

![img](https://images2015.cnblogs.com/blog/731178/201603/731178-20160315145846584-604708056.png)

　　执行到这里，我们已经将数据从数据库中原样的查出来了，但具体如何之取出我们需要的数据呢，显然，假如我们查询的sql返回的只是一个数据，上面的方式已经可以满足我们的需求的，如我们查询数据的记录数，

```
select count（*） from test
```

　　查询出来的结果就是我们需要的内容，或者通过正则表达式的获取即可获取我们的内容。

　　但假如像上面那样子，我们获取出来的是多行数据，我们需要如何来对数据进行遍历，只获取出我们需要的数据呢？请看下面的分析。

四、JDBC Request 参数化

**方法（一）、定义变量，在sql query中使用变量：**

**1、在Test Plan 中定义一个变量（当然也可以使用参数化：Jmeter参数化）：**

![img](https://images2015.cnblogs.com/blog/731178/201603/731178-20160315150637256-1503636459.png)

**2、sql query 中使用${变量名}的方式引用：**

```
select * from test where name='${name}'
```

![img](https://images2015.cnblogs.com/blog/731178/201603/731178-20160315151013365-76302969.png)

**方法（二）、在sql query中使用”？“作为占位符，并传递参数值和参数类型，如下图所示：**

1、传递的参数值是常量，如图传递2个变量，多个变量使用” ， “ 分隔。**这里假如你有数据是int类型的，也要在Parameter types 那里标示为varchar类型，否则无法运行。**

![img](https://images2015.cnblogs.com/blog/731178/201603/731178-20160315151907068-1527284747.png)

2、传递的参数值是变量，使用${变量名}的方式

![img](https://images2015.cnblogs.com/blog/731178/201603/731178-20160315152241662-1323790104.png)

五、Variables names 参数使用方法：

　　jmeter官网给的解释是：**如果给这个参数设置了值，它会保存sql语句返回的数据和返回数据的总行数**。假如，sql语句返回2行，3列，且variables names设置为A,,C，那么如下变量会被设置为：

　　A_#=2 (总行数)
　　A_1=第1列, 第1行
　　A_2=第1列, 第2行 
　　C_#=2 (总行数) 
　　C_1=第3列, 第1行
　　C_2=第3列, 第2行

- 如果返回结果为0，那么A_#和C_#会被设置为0，其它变量不会设置值。
- 如果第一次返回6行数据，第二次只返回3行数据，那么第一次那多的3行数据变量会被清除。
- **可以使用${A_#}、${A_1}...来获取相应的值**

**示例**：

　　我们还是用上面的数据库，把所有数据查出来，test表有有3个字段，5条记录（忘记了的可以回到第一步那里查看）

1、添加一个jdbc request名为”参数4“，添加一个”Debug Sampler“用来查看输出的结果，设置 variables name为column1,column2,column3：

![img](https://images2015.cnblogs.com/blog/731178/201603/731178-20160315153445599-1162481916.png)

2、执行结果：

![img](https://images2015.cnblogs.com/blog/731178/201603/731178-20160315153630974-2051692118.png)

　　解析：

　　column1代表第一列所有的数据，column1_#可以获取到第一列的行数

　　column1_n:获得第一列第n行的数据。

　　column2和column3的功能类似， 假如我们只需要第一列和第三列的数据，可以写成column1,,column3,中间的","不可以省略。

六、Result variable name 参数使用方法：

　　如果给这个参数设置值，它会创建一个对象变量，保存所有返回的结果，获取具体值的方法：columnValue = vars.getObject("resultObject").get(0).get("Column Name")

![img](https://images2015.cnblogs.com/blog/731178/201603/731178-20160315154249428-80159963.png)

　　执行结果：

![img](https://images2015.cnblogs.com/blog/731178/201603/731178-20160315154207240-1570898474.png)

jmeter 简单使用：http://www.cnblogs.com/0201zcr/p/5046193.html

jmeter 获取返回数据作为下一个请求参数：http://www.cnblogs.com/0201zcr/p/5089620.html

源文件：http://pan.baidu.com/s/1mh3Ag5M

 　　**致谢**：感谢您的阅读！





### Result variable name 的使用

如果给这个参数设置值，它会创建一个对象变量，保存所有返回的结果，获取具体值的方法：columnValue = vars.getObject(“resultObject”).get(0).get(“Column Name”)

![这里写图片描述](https://img-blog.csdn.net/20170523120316081?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjE2NzA0NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

执行结果： 
![这里写图片描述](https://img-blog.csdn.net/20170523120326363?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjE2NzA0NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



| 参数                                                         | 描述                                                         | 是否为必须                             |
| ------------------------------------------------------------ | ------------------------------------------------------------ | -------------------------------------- |
| 名称                                                         | 树中显示的这个采样器的描述性名称。                           | 否                                     |
| Variable Name of Pool declared in JDBC Connection Configuration | 连接池绑定到的JMeter变量的名称。这必须与**JDBC Connection Configuration**中的“**Variable Name**”字段一致。 | 是                                     |
| Query Type                                                   | 根据语句类型设置:  -Select Statement - Update Statement - 这也可以用于插入和删除 - Callable Statement  - Prepared Select Statement - Prepared Update Statement- 这也可以用于插入和删除  - Commit - Rollback  Autocommit(false)  Autocommit(true)  - Edit- 这应该是一个变量引用，它的值应该等于上面的值之一。 | 是                                     |
| SQL Query                                                    | SQL query.    不要输入结尾的分号。  通常不需要使用花括号**{ }**来包含可调用的语句（Callable statements）;但是，如果数据库使用非标准语法，则可以使用它们。  如果需要的话，JDBC驱动程序会自动转换包含在{}中的语句。 比如： - `select * from t_customers where id=23` - `CALL`  `SYSCS_UTIL.SYSCS_EXPORT_TABLE (null, ?, ?, null, null, null)` 参数值: `tablename,filename` 参数类型： `VARCHAR,VARCHAR` 第二个示例假设您正在使用Apache Derby。 | 是                                     |
| Parameter values                                             | 参数值的逗号分隔列表。使用**]NULL[**表示**NULL**参数。(如果需要，可以通过定义属性“**jdbcsampler.nullmarker**”来更改空字符串。)如果任何值都包含逗号或双引号，那么列表必须用双引号括起来，并且任何嵌入的双引号都必须加倍，例如: `"Dbl-Quote: "" and Comma: ,"` 即使参数是**OUT** of one，语句中也必须有与占位符一样多的值。确保设置一个值，即使该值不会被使用(例如在CallableStatement中)。  解释：如果数据库操作语句是带有参数的语句，参数值一般以问号？作为占位符，那么给操作语句的参数赋值就在这里进行。表格下方将有示例说明 | 是（如果准备的或可调用的语句具有参数） |
| Parameter types                                              | SQL参数类型的逗号分隔列表(例如** INTEGER**、** DATE**、**VARCHAR**、**DOUBLE**)或常量的整数值。当您使用自定义数据库类型驱动程序时(例如**OracleTypes.CURSOR**提出的时，可以使用这些整数值。游标可以用其整数值-10表示）。这些定义为java.sql类中的字段。类型，请参阅例如:java.sql.Types Javadoc。  注意:JMeter将使用运行时JVM定义的任何类型，所以如果您在不同的JVM上运行，一定要检查适当的文档  如果可调用语句具有INOUT或OUT参数，那么必须在适当的参数类型前加上前缀来表示这些参数，例如，使用“INOUT INTEGER”而不是“INTEGER”。 如果没有指定，则假设“IN”，即“DATE”和“IN DATE”是一样的。 如果类型不是java.sql中找到的字段之一。类型，JMeter也接受相应的整数，例如，因为OracleTypes。游标== -10，可以使用“INOUT -10”。 语句中有多少占位符，就有多少类型。  解释：数据库操作语句的数据类型分多种，上面所列出的参数值什么数据类型，在这里进行一一对应。表格下方将有示例说明。 | 是（如果准备的或可调用的语句具有参数） |
| Variable Names                                               | 用逗号分隔的变量名列表，用于保存Select语句、准备的Select语句或callable语句返回的值。注意，当与CallableStatement一起使用时，变量列表必须与调用返回的OUT参数相同。如果变量名比OUT参数少，那么在线程上下文变量中存储的结果应该与提供的变量名一样多。如果存在的变量名多于参数，则将忽略其他变量 | 否                                     |
| Result Variable Name                                         | 如果指定，将创建一个对象变量，其中包含一个行映射列表。每个映射都包含列名作为键，列数据作为值。用法: `columnValue = vars.getObject("resultObject").get(0).get("Column Name");` | 否                                     |
| Handle ResultSet                                             | 定义如何处理从callable statements返回的ResultSet:  - **Store As String**(默认)-变量名列表中的所有变量都存储为字符串，当出现在列表中时，不会迭代结果集。clob将被转换为字符串。blob将被转换为字符串，就好像它们是UTF-8编码的字节数组一样。在jdbcsampler采样器之后，clob和blob都将被切断。max_retain_result_size字节。  - **Store As Object**——变量列表上的**ResultSet**类型的变量将被存储为对象，可以在后续的测试/脚本中访问和迭代，不会在**ResultSet**中迭代。将像选择**Store As String**那样处理**CLOB**s。**BLOB**s将存储为字节数组。在**jdbcsampler.max_retain_result_size**字节之后，**CLOB**s和**BLOB**s都将被切断。max_retain_result_size字节。  - **Count Records**-结果集（**ResultSet**）类型的变量将通过显示结果的记录计数来迭代。变量将作为字符串存储。对于**BLOB**对象，将存储对象的大小。 | 否                                     |