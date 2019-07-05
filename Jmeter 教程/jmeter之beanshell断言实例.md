# [jmeter之beanshell断言实例](https://www.cnblogs.com/lwjnicole/p/9383872.html)



.首先储存一个接口的响应结果，比如在http请求的后面添加beanshell后置处理器(BeanShell PostProcessor)来储存http请求的响应结果：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 import org.json.*;
 2 
 3 //获取上一个请求的返回值
 4 String response = prev.getResponseDataAsString();
 5 //将返回值转换为json
 6 JSONObject responseJson = new JSONObject(response);
 7 //获取responseMessage
 8 String message = responseJson.getString("responseMessage");
 9 log.info("message的值：" + message);
10 
11 //使用vars.put()方法储存变量message
12 vars.put("message",message);
13 //获取titleLink
14 String titleLink = responseJson.getJSONObject("data").getString("titleLink");
15 log.info("titleLink的值:" + titleLink);
16 //使用vars.put()方法储存变量message
17 vars.put("titleLink",titleLink);
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

在后面的其他接口中如何需要使用变量message和titleLink，可以使用${message}和${titleLink}来获取变量的值；

变量储存好后，在需要断言的接口后面添加BeanShell断言，使用Failrue来标识断言失败，FailureMessage标示断言失败的原因，如：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
1 //使用vars.get()方法获取变量的值
2 String message= vars.get("message");
3  
4 if(!message.equals("success")) {
5     Failure = true; 
6     FailureMessage = "规则解析失败";
7 }else{
8     FailureMessage = "规则解析成功";
9     }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

\------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

 

2.直接在需要断言的接口后面使用beanshell断言，使用Failrue来标识断言失败，FailureMessage标示断言失败的原因，如：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 import org.json.*;
 2 
 3 //获取上一个请求的返回值
 4 String response = prev.getResponseDataAsString();
 5 //将返回值转换为json
 6 JSONObject responseJson = new JSONObject(response);
 7 //获取responseMessage
 8 String message = responseJson.getString("responseMessage");
 9 log.info("message的值：" + message);
10 
11 if(!message.equals("success")){
12     Failure = true; 
13     FailureMessage = "规则解析失败，message不等于success";
14     return;
15 }
16 
17 //获取titleLink
18 Object titleLink = responseJson.getJSONObject("data").get("titleLink");
19 log.info("titleLink的值:" + titleLink.toString());
20 
21 
22 if(titleLink.toString().equals("null") || "".equals(titleLink)){
23     Failure = true; 
24     FailureMessage = "规则解析失败，titleLink为空";
25 }else if(!titleLink.toString().startsWith("http") && !titleLink.toString().startsWith("https")){
26     Failure = true;
27     FailureMessage = "规则解析失败，titleLink不为空，但是不是以http或者https开头的";
28 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

![img](https://images2018.cnblogs.com/blog/1280276/201807/1280276-20180728233200645-1852850928.png)

接口的响应数据为：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
 1 {
 2 :   "responseCode":"1",
 3 :   "responseMessage":"success",
 4 :   "responseType":null,
 5 :   "data":
 6 :   {
 7 :   :   "city":"上海",
 8 :   :   "rentUnit":"月",
 9 :   :   "source":"个人房源网",
10 :   :   "title":"徐盈路1188弄徐泾青浦徐泾租房",
11 :   :   "belonger":"个人",
12 :   :   "housingType":"住宅",
13 :   :   "floor":"高层",
14 :   :   "rentPrice":"17000",
15 :   :   "titleLink":"http://sh.grfy.net/rent/d-34612565.html",
16 :   :   "decoration":null,
17 :   :   "direction":null,
18 :   :   "isSplit":"否",
19 :   :   "imgs":null,
20 :   :   "publishTime":"2018-07-25T23:20:33.471",
21 :   :   "contactMobile":null,
22 :   :   "website":"http://sh.grfy.net/rent/list_2_0_0_0-0_0_0-0_0_2_0_{}_.html",
23 :   :   "address":"徐泾",
24 :   :   "contactName":"王女士",
25 :   :   "houseType":"4室2厅2卫",
26 :   :   "estate":"徐泾",
27 :   :   "roomArea":"177",
28 :   :   "collectHouseType":"住宅",
29 :   :   "collectType":"出租",
30 :   :   "district":"青浦",
31 :   :   "totalFloor":"共20层",
32 :   :   "region":"上海",
33 :   :   "isRegister":"否",
34 :   :   "desc":"仁恒西郊花园 4室2厅2卫 房屋亮点 新上 配套齐全 有阳台 首次出租 随时看房 出租要求 一家人 一年起租 租户稳定 作息正常 房源描述小区环境好，物业管理成熟，私人会所实施配套齐全，临近地铁17号徐盈站，周边多所国际学校，仁恒的房子品质有保障。无中介费。"
35 :   }
36 }
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 断言失败如下：

![img](https://images2018.cnblogs.com/blog/1280276/201807/1280276-20180728232629918-484498540.png)

---------------

# [Beanshell断言](https://www.cnblogs.com/lamdong/p/8442885.html)

Beanshell断言示例一：通过从数据库查询结果集，和从接口返回中解析json格式的返回内容，比较两者来验证sample请求是否成功

1>先通过JDBC PreProcessor把测试计划中所有接口请求涉及到的表的列名和对应的数据类型从information_schema库的COLUMNS表中查询出来存到一个变量里面

SELECT DISTINCT column_name, data_type

  FROM COLUMNS
 WHERE table_name IN ("t_bd_block",
                      "t_bd_build",
                      "t_bd_build_detail",
                      "t_bd_build_education",
                      "t_bd_build_hospital",
                      "t_bd_build_project",
                      "t_bd_build_schdist",
                      "t_bd_build_tag",
                      "t_bd_build_traffic",
                      "t_bd_floor",
                      "t_bd_house",
                      "t_bd_house_type",
                      "t_bd_picture",
                      "t_bd_project",
                      "t_bd_seo",
                      "t_bd_tag",
                      "t_bd_unit",
                      "t_bd_use",
                      "t_bd_use_sub",
                      "t_city",
                      "t_county",
                      "t_developer_company",
                      "t_idcenter",
                      "t_nation",
                      "t_poi",
                      "t_poi_education",
                      "t_poi_education_pic",
                      "t_poi_enrollment_plan",
                      "t_poi_graduation",
                      "t_poi_hospital",
                      "t_poi_station_pic",
                      "t_poi_sub",
                      "t_poi_subway_stations",
                      "t_poi_traffic_bus_station",
                      "t_poi_traffic_line",
                      "t_poi_traffic_line_station",
                      "t_poi_traffic_station",
                      "t_property_company",
                      "t_province",
                      "t_region",
                      "t_static_type");

 

![2.jpg](http://120.79.71.121/static/userImages/20180208/1518081658849054524.jpg)

 

2>把第1步中查出来的getColumnType解析出来，以键值对的形式存到变量vars供后面使用，因为json格式的数据有些类型的数据要加双引号，有些不需要，这时候就需要知道字段类型来判断下

![img](http://120.79.71.121/static/ueditor/themes/default/images/spacer.gif)![2.jpg](http://120.79.71.121/static/userImages/20180208/1518081372666003945.jpg)

ArrayList getColumnType1=vars.getObject("getColumnType");

ArrayList column_name = new ArrayList();

ArrayList data_type = new ArrayList();

for(HashMap row : getColumnType1){

   Iterator it = row.entrySet().iterator();

   while(it.hasNext()){

​     Map.Entry pair = (Map.Entry)it.next();

​    log.info(pair.getKey()+"="+pair.getValue());

​     if(pair.getKey().toString().equals("column_name")){

​        column_name.add(pair.getValue().toString());

​    }else if(pair.getKey().toString().equals("data_type")){

​           data_type.add(pair.getValue().toString());

​      }

   }

}

for(int i=0;i<column_name.size();i++){

​    vars.put(column_name.get(i).toUpperCase(),data_type.get(i));        

}

 

3>从数据库中把要验证的结果查询出来存到变量里面，变量存的是结果集

![img](http://120.79.71.121/static/ueditor/themes/default/images/spacer.gif)![1.jpg](http://120.79.71.121/static/userImages/20180208/1518081727823044473.jpg)

 

4> 对结果进行断言

ArrayList sqlResult1=vars.getObject("sqlResult");

String json = "";

if(sqlResult1.size()==0){

​    json= "[]";  //如果jdbc request查询结果为空，返回[]

}else{

​    for(HashMap row : sqlResult1){

​           String r="";

​         Iterator it = row.entrySet().iterator();

​         ArrayList keyAndValue = new ArrayList();

​        while(it.hasNext()){

​             Map.Entry pair = (Map.Entry)it.next();

​             log.info("pair:"+pair);

​             log.info(pair.getKey()+"="+pair.getValue());

​            

​                 if(pair.getValue()==null){

​                     continue;

​                     }else{

​                             String[] subKey = pair.getKey().toString().split("_");

​                                String key = subKey[0];

​                               for(int k=1; k<subKey.length; k++){

​                                    key += Character.toUpperCase(subKey[k].charAt(0))+ subKey[k].substring(1);

​                            }

​                                

​                                if("varchar".equals(vars.get(pair.getKey().toString().toUpperCase())) || "char".equals(vars.get(pair.getKey().toString().toUpperCase())) || "text".equals(vars.get(pair.getKey().toString().toUpperCase()))){

​                                    keyAndValue.add("\""+key+"\":\""+pair.getValue()+"\",");

​                                }else{

​                                    keyAndValue.add("\""+key+"\":"+pair.getValue()+",");

​                                }        

​                           

​                     }

​               

​                        

​                           

​            } //while循环结束

​             Collections.sort(keyAndValue);  

​                         for(int i=0;i<keyAndValue.size();i++){

​                             r+=keyAndValue.get(i);

​                         }

​                         r="{"+r.substring(0,r.length()-1)+"},";

​                         json+=r;

​                     

​    

  }

  if(sqlResult1.size() > 1){

​      json="{\"code\":\"0001\",\"data\":{\"A-G\":["+json.substring(0,json.length()-1)+"]},\"message\":\"ok\"}";

  }else{

​      json="{\"code\":\"0001\",\"data\":{"+json.substring(0,json.length()-1)+"},\"message\":\"ok\"}";

  }

}

log.info(json);

String response_data = prev.getResponseDataAsString();

log.info(response_data);

import org.json.*;

log.info("进去了json对象");

JSONObject sql_data_obj = new JSONObject(json);

JSONObject response_data_obj = new JSONObject(response_data);

log.info("进去了json对象");

log.info("拿到了response的json对象"+response_data_obj);

log.info("拿到了sql的json对象"+sql_data_obj);

String responses_str = response_data_obj.get("data").get("A-G").toString();

String sqls_str = sql_data_obj.get("data").get("A-G").toString();

log.info("进去了responses的A-G"+responses_str);

JSONArray responses_array = new JSONArray(responses_str);

JSONArray sqls_array = new JSONArray(sqls_str);

//String[] result = new String[responses_array.length()];

boolean flag=true;

for(int i=0;i<responses_array.length();i++){

​    JSONObject response_obj = new JSONObject(responses_array.get(i).toString());

​    JSONObject sql_obj = new JSONObject(sqls_array.get(i).toString());

//    String name = app_obj.get("cityId").toString();

​    Iterator it = response_obj.keys();  

​            while(it.hasNext()){  

​                //获取map的key  

​                String key = (String) it.next();

​                System.out.println("key: "+key);

​                //得到value的值  

​                Object response_value = response_obj.get(key);

​                Object sql_value=sql_obj.get(key);  

​                if(!(sql_value.toString().equals(response_value.toString()))){

​                    flag=false;

​                    break;

​                }

​                //递归遍历  

//                jsonObj.put(key, traveseJson(value));  

​                  

​            }  

//    result[i] = name;

}

//vars.put("result", Arrays.toString(result));

if(flag)//请求的response中有包含自定义字符，则断言结果为false

{

​    Failure=false;

​    String message="${__time(yyyy-MM-dd HH:mm:ss,)} 结果比对成功";

​    

​    FailureMessage=message;

​    log.info(FailureMessage);

​    

}else

{

​    Failure=true;

​    String message="${__time(yyyy-MM-dd HH:mm:ss,)} 结果比对失败";

​    String response="本次请求response数据："+prev.getResponseDataAsString()+"----------";

​    String need_assert="需要断言的数据："+ json;

​    FailureMessage=message+response+need_assert;

​    

}

 

 

Beanshell断言示例二：从SampleResult对象中获取请求的响应结果码，如果不是200，那就直接让断言失败，如果成功，才去获取json格式的返回内容中的某个值，然后判断后再通过Fqilure对象去设置结果是成功还是失败

![img](http://120.79.71.121/static/ueditor/themes/default/images/spacer.gif)![Image [3].png](http://120.79.71.121/static/userImages/20180208/1518081560873032576.png)