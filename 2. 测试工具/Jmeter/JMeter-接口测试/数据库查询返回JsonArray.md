# 数据库查询返回JsonArray

2018年03月06日 23:13:33 [0爪哇人0](https://me.csdn.net/qq_34745295) 阅读数 1439



 版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/qq_34745295/article/details/79465041

## 前言

最近编写微信小程序的过程中，如何更加方便地与后台进行交互，如何使代码得到最大的重用，一直是我们不断思考的问题。尤其是在处理数据库查询时，怎麽样能将不同的查询语句用一个函数实现，怎样装载返回信息，值得思考。

## Json介绍

JSON(JavaScript Object Notation, JS 对象标记) 是一种轻量级的数据交换格式。它基于 ECMAScript (w3c制定的js规范)的一个子集，采用完全独立于编程语言的文本格式来存储和表示数据。简洁和清晰的层次结构使得 JSON 成为理想的数据交换语言。 易于人阅读和编写，同时也易于机器解析和生成，并有效地提升网络传输效率。

JSON 语法是 JavaScript 对象表示法语法的子集，JSON包含的规则为：

- 数据在名称/值对中
- 数据由逗号分隔
- 花括号保存对象
- 方括号保存数组

### [JsonObject](http://json-lib.sourceforge.net/apidocs/jdk15/net/sf/json/JSONObject.html)

JSONObject key、value创建方法

```
//JSONObject key、value创建方法
JSONObject json_object=new JSONObject();

String columnName=data.getColumnName(i);
String rst=rs.getString(i);
json_object.put(columnName, rst);123456
```

### [JsonArray](http://json-lib.sourceforge.net/apidocs/jdk15/net/sf/json/JSONArray.html)

用JSONObject 生成JSONArray的方法

```
//新建JsonArray
JSONArray json_array=new JSONArray();
json_array.put(json_object);123
```

## sql查询生成JsonArray

获取sql查询数据库返回数据，将属性名作为key，将对应的属性值作为key，利用每一个元组生成一个JSONObject，将JSONobject加入JSONArray，打包输出。

```
/**
     * 连接属性
     */
    private static Connection conn=null;


    public  static JSONArray query_sql(String sql){

        PreparedStatement pre =null;
        ResultSet rs = null;
        conn=(Connection) JdbcUtils.getConnection();

        //新建JsonArray
        JSONArray json_array=new JSONArray();

        try {
            pre=conn.prepareStatement(sql);
            rs=pre.executeQuery();

            ResultSetMetaData data=rs.getMetaData();


            while(rs.next()){
                JSONObject json_object=new JSONObject();
                for(int i=1;i<=data.getColumnCount();++i){
                    //列名
                    String columnName=data.getColumnName(i);
                    String rst=rs.getString(i);
                    json_object.put(columnName, rst);
                }
                json_array.put(json_object);
            }
//          System.out.println(json_array.toString());

        } catch (SQLException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (JSONException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        return json_array;
    }
```