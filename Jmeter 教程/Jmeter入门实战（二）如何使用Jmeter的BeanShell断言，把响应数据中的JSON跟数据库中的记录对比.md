# Jmeter入门实战（二）如何使用Jmeter的BeanShell断言，把响应数据中的JSON跟数据库中的记录对比

2016年11月03日 17:15:48 [喵馒头](https://me.csdn.net/drico1986) 阅读数：21652



很多时候我们需要把Response Data取到的 Json 字符串跟数据库里的对比，来验证接口的正确性，使用Beanshell断言可以比较灵活的实现这个需求。

Beanshell是一种类似JAVA的脚本语言，可以直接调用外部的jar包，可以拿现成的json解析包来用，所以笔者选择了这个方法。实现的思路是



1、历史数据存储到MYSQL数据库里，

2、Jmeter读取参数化的CSV文件，把测试用例都存入CSV文件一次执行，

3、CSV文件和数据库中数据用ID关联，

4、每次断言都用BeanShell使用JDBC连接MYSQL库，使用测试用例的ID查出MYSQL中的数据

5、调用GSON的解析JSON方法，对比两个数据，进行断言



BeanShell在Jmeter中的使用可以参考这篇文章，https://www.blazemeter.com/blog/queen-jmeters-built-componentshow-use-beanshell   在这里就不多做介绍了。

GSON是Google的一个解析JSON的工具，笔者不太懂java，但是JSON字符串的对比不能直接用字符串比较，所以引用了这个包，然后调用了GSON的JsonObject.equals()方法，来判断两个JSON的内容是否一样。大家可以多研究下，最好能完成例如断言失败时打印内容不同的KEY等功能。



一、在Jmeter中添加BeanShell断言

![img](https://blog.csdn.net/drico1986/article/details/53021071)

![img](https://img-blog.csdn.net/20161103172015288?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

二、为了方便查看断言结果，再添加一个断言结果，如果断言报错可以在这里查询具体信息

![img](https://blog.csdn.net/drico1986/article/details/53021071)![img](https://img-blog.csdn.net/20161103172020132?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)



三，选中测试计划，添加JAR包的引用地址。MYSQL的驱动和GSON包都需要下载下来，如下图直接点开窗口选择JAR文件即可。



![img](https://img-blog.csdn.net/20161103172024190?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

![img](https://blog.csdn.net/drico1986/article/details/53021071)



四、Beanshell脚本编写



```java
import java.sql.*;



import java.util.*;



import java.lang.*;



import org.apache.regexp.*;



import com.google.gson.JsonObject;  



import com.google.gson.JsonParser; 



 



 



//数据库连接字段



String drive = "com.mysql.jdbc.Driver";



String url = "jdbc:mysql://yourdatabase:yourDatabasePort/";



String dbName = "YourDataBaseName";



String user = "YourDataBaseUser";



String pass = "YourDataBasePass";



 



 



String history = "";



String response = "";



String failuer = "";



 



//vars.get是Jmeter提供的方法，可以取到变量值，这个caseno是用来关联用例和数据库中结果的



String CaseNo = vars.get("caseno");



 



//下面是查询的SQL



String query = "SELECT response_data From test_json_compare Where case_no = '" + CaseNo + "'";



 



//JDBC声明



Connection Mycon = null;



Statement Mystmt = null;



ResultSet Myrset = null;



 



//try中获取数据库连接



try{



	Mycon = DriverManager.getConnection(url+dbName, user, pass);



	



		}	catch(SQLException e){



		



	}



	Mystmt = Mycon.createStatement();



	Myrset = Mystmt.executeQuery(query);



 



//prev.getResponseDataAsString是Jmeter提供的方法，可以调取上次请求的响应字符串



	response = prev.getResponseDataAsString();



 



//如果取到库中的数据，赋值给history



	while (Myrset.next()){



	history = Myrset.getString(1);



	}



 



	Myrset.close();



	Mystmt.close();



 



 



//Gson提供的方法，原理笔者也不明白，效果是把字符串生成Json对象



 



JsonParser parser = new JsonParser();  



JsonObject responseObj = (JsonObject) parser.parse(response);  



JsonParser parser1 = new JsonParser();          



JsonObject historyObj = (JsonObject) parser1.parse(history);  



 



 



if(history == "")



{



	Failure = true;



	FailureMessage = "连接数据库失败或者数据库内没有历史数据"; 



	



//调用Gson提供的Json对象euqals方法判断是否一致



}else if(responseObj.equals(historyObj) == false)



{ 



//把断言失败置为真	



Failure = true;



FailureMessage = "和历史数据不匹配"; 



}
```