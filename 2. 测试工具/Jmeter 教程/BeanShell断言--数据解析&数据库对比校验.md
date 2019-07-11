# 【Jmeter】BeanShell断言--数据解析&数据库对比校验

2017年06月02日 21:53:16 [sysu_lluozh](https://me.csdn.net/lluozh2015) 阅读数：5806



 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/lluozh2015/article/details/72848133

有说到使用BeanShell进行响应断言校验，可以更加自主且可定制化的实现数据的校验，现在使用BeanShell断言解析数据后与数据库的数据进行对比校验接口返回值的正确性

需要实现以上需求，首先需要数据库的连接，并取到数据库中的数据，解析相应的响应数据，并进行校验。由于Jmeter的BeanShell断言编辑器比较简陋，现在在IDEA上实现

创建项目后，我们需要导入两个jar包，一个是sql的jar包，一个是json的jar包

![这里写图片描述](https://img-blog.csdn.net/20170602215606668?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGx1b3poMjAxNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

下载OK后，使用IDEA创建项目，将这两个jar包导入到项目中（File –> Project Structure –>Modules->Dependencies-> 添加），导入成功后，开始编写java脚本

定义数据库连接字段

```
//数据库连接字段
String drive = "com.mysql.jdbc.Driver";
String url = "jdbc:mysql://xxx.xxx.xxx.xxx:xxxx/";
String dbName = "dbName";
String user = "root";
String pass = "123456";123456
```

JDBC变量声明

```
//JDBC声明  
Connection Mycon = null;  
Statement Mystmt = null;  
ResultSet Myrset = null; 1234
```

SQL查询语句

```
//查询的SQL
String query = "SELECT * FROM sr WHERE id = 123456";12
```

数据库连接

```
//数据库连接
//try{}catch 方法
try{  
    Mycon = DriverManager.getConnection(url+dbName, user, pass);
    Mystmt = Mycon.createStatement();  
    Myrset = Mystmt.executeQuery(query); 
    //如果取到库中的数据，赋值给strdb
    while (Myrset.next()){  
        strdb= Myrset.getString(1);  
    }
    //close连接 
    Myrset.close();  
    Mystmt.close(); 
}catch(SQLException e){  

}  
1234567891011121314151617
```

因为在该项目中实际为4条查询语句，故

```
//sql查询语句
 private static String query(String id){
        String query = "SELECT  * FROM sr WHERE id = '"+id+"'";
        return query;
}

//数据库连接字段
String drive = "com.mysql.jdbc.Driver";
String url = "jdbc:mysql://xxx.xxx.xxx.xxx:xxxx/";
String dbName = "dbName";
String user = "root";
String pass = "123456";

//JDBC声明
Connection Mycon = null;
Statement Mystmt = null;
ResultSet []Myrset = new ResultSet[4];
String []resourceID =new String[]{ "50356","50357","50358","66213"};

//定义String数组，如果取到库中的数据，赋值到相应的数组
String []i_DB = new String[4];
String []n_DB = new String[4];
String []f_DB = new String[4];
String []v_DB = new String[4];

//数据库连接
try{
     Mycon = DriverManager.getConnection(url + dbName, user, pass);
     Mystmt = Mycon.createStatement();

     for(int i=0;i<4;i++){
         Myrset[i] = Mystmt.executeQuery(query(resourceID[i]));
         while (Myrset[i].next()){
             i_DB[i] = Myrset[i].getString("i");
             n_DB[i] = Myrset[i].getString("n");
             f_DB[i] = Myrset[i].getString("f");
             v_DB[i] = Myrset[i].getString("v");
    }
    Myrset[i].close();
}
Mystmt.close();

}catch (SQLException e){
     System.out.println(e);
}123456789101112131415161718192021222324252627282930313233343536373839404142434445
```

完成了数据库的查询，现在我们对响应进行解析

因为在IDEA中并没有获取上一个请求的返回，故先定义一个String字符串

```
String jsonContent ="
{
    "statusCode": 200,
    "data": [
        {
            "i": "50356",
            "n": "项目一",
            "f": "abc.exe",
            "v": "2.0"
        },
        {
            "i": "50357",
            "n": "项目二",
            "f": "def.exe",
            "v": "2.2"
        },
        {
            "i": "50358",
            "n": "项目三",
            "f": "ghi.exe",
            "v": "1.2"
        },
        {
            "i": "66213",
            "n": "项目四",
            "f": "jkl.exe",
            "v": "2.6"
        }
    ]
}";123456789101112131415161718192021222324252627282930
```

接下来对该数据进行解析

```
//解析定义的数据，并赋值到相应的字符串
try {
    JSONObject responseJson = new JSONObject(jsonContent);
    JSONArray groups = responseJson.getJSONArray("data");
  for(int i = 0; i<4 ;i++){
    String i_res = groups.getJSONObject(i).getString("i").toString();
    String n_res = groups.getJSONObject(i).getString("n").toString();
    String f_res = groups.getJSONObject(i).getString("f").toString();
    String v_res = groups.getJSONObject(i).getString("v").toString();
  }
}catch (JSONException e){
     System.out.println(e);
}12345678910111213
```

数据库请求值和返回值都解析OK后，将两组数据分别对比，在IDEA中只是将数据打印出来

```
//对比i的值
if(!id_response.equals(i_DB[i])){
      System.out.println("right!!!i_res: "+i_res+"i_DB:"+i_DB[i]);
} else {
      System.out.println("wrong!!!i_res: "+i_res+"i_DB:"+i_DB[i]);
}
//对比n的值
if(!n.equals(n_DB[i])){
      System.out.println("right!!!n_res:"+n_res+"n_DB:"+n_DB[i]);
} else {
      System.out.println("wrong!!!n_res:"+n_res+"n_DB:"+n_DB[i]);
}
//对比v的值
if(!v_response.equals(v_DB[i])){
      System.out.println("right!!!v_res:"+v_res+"v_DB:"+v_DB[i]);
} else {
      System.out.println("wrong!!!v_res:"+v_res+"v_DB:"+v_DB[i]);
}123456789101112131415161718
```

发现`n` 这个变量，返回值都是`"wrong!!!` 
name的返回值是中文字符，可以使用`compareTo` 的方法

```
if(name_response.compareTo(n_DB[i])==0){
    System.out.println("wrong!!!n_res:"+n_res+"n_DB:"+n_DB[i]);
} else {
    System.out.println("right!!!n_res:"+n_res+"n_DB:"+n_DB[i]);
}12345
```

搞定，但是需要在Jmeter的BeanShell断言编译器中编译执行，将代码迁移到BeanShell断言编译器，运行.jmx脚本，这个方法报错

```
Mycon = DriverManager.getConnection(url+dbName, user, pass);1
```

了解后发现 `DriverManager.getConnection` 方法，不同的数据库调用这个函数的参数可能不一样

Oracle数据库、DB2数据库 、Sql Server数据库、PostgreSQL数据库

```
String url="jdbc:db2://xxx.xxx.xxx.xxx:xxxx/sr"; 
//sample为你的数据库名 
String user="admin"; 
String password=""; 
Connection conn= DriverManager.getConnection(url,user,password);12345
```

Sybase数据库

```
//myDB为你的数据库名 
Properties sysProps = System.getProperties(); 
SysProps.put("user","userid"); 
SysProps.put("password","user_password"); 
Connection conn= DriverManager.getConnection(url, SysProps); 12345
```

Informix数据库 、MySQL数据库、PostgreSQL数据库

```
String url = 
"jdbc:informix-sqli://xxx.xxx.xxx.xxx:xxxx/myDB:INFORMIXSERVER=myserver; 
user=testuser;password=testpassword"; 
//myDB为数据库名 
Connection conn= DriverManager.getConnection(url); 12345
```

故修改传参

```
Mycon = DriverManager.getConnection(URL);1
```

执行后OK，但碰到一个诡异的问题，成功后，将传参修改为原先的传参方式，执行成功，暂未找到问题原因