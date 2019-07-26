## jmeter beanshell Typed variable declaration : Object constructor错误

```java
import org.json.JSONArray;
import org.json.JSONObject;
 
 
res_str = prev.getResponseDataAsString();
JSONObject res_json_obj = new JSONObject(res_str);
JSONArray dbd_json_array = new JSONArray("${dbd}"); // 数据库转json
JSONObject dbd_json_obj = new JSONObject();
FailureMessage = "";
Failure = true;
 
res_json_obj = new JSONObject(res_json_obj.get("data").toString());
if (!res_json_obj.isNull("list")) {
	JSONArray res_json_array = new JSONArray(res_json_obj.get("list").toString());
	log.info("响应长度:" + res_json_array.length());
	log.info("数据返回长度:" + dbd_json_array.length());
 
	if (res_json_array.length() == dbd_json_array.length()) {
		for (int i = 0; i < dbd_json_array.length(); i++) {
			Message = "\n第"+i+"条记录:";
			res_json_obj = res_json_array.getJSONObject(i);
			dbd_json_obj = dbd_json_array.getJSONObject(i);
 
			System.out.println(res_json_obj);
 
			Iterator keys = dbd_json_obj.keys();
			while (keys.hasNext()) {
				String key = (String) keys.next();
				String resvalue="";
				String dbdvalue="";
 
				System.out.println("key:" + key);
				if (key.equals(null) || key.equals("")) {
					Message += key + "不存在;";
					FailureMessage += Message;
					Failure = true;
					log.info(Message);
					break;
					}
					
						
						if(res_json_obj.has(key) && dbd_json_obj.has(key)) {
							resvalue = res_json_obj.getString(key);
							dbdvalue = dbd_json_obj.getString(key);
						}else {
							Message += key + "不存在;";
							FailureMessage += Message;
							Failure = true;
							log.info(Message);
							break;
						}
 
						
 
						if (resvalue.equals(dbdvalue)) {
							Message += key + "匹配成功;";
							Failure = false;
						} else {
							Message += key + "匹配不成功,数据库值：" + dbdvalue + ",响应值:"+resvalue;
							FailureMessage += Message;
							Failure = true;
							log.info(Message);
							break;
 
						}
 
						log.info("value:" + res_json_obj.get(key));
					}
				}
 
			} else {
				Message += "接口响应与数据库数据不匹配;";
			     Message += "响应数据：" + res_json_array + "|数据库查询结果：" + dbd_json_array;
			     FailureMessage += Message;
				Failure = true;
				log.info("value:" + res_json_obj.get(key));
				break;
			}
 
		} else {
			FailureMessage = "返回数据为空";
			log.info(FailureMessage);
			Failure = true;
			return;
		}
```

这个脚本有个前提，先建立了返回值的对象，当通过jmeter从数据库获得需要验证的数值的时候，需要指定数据库查询返回值字段名与java对象字段名一致。

遇到Typed variable declaration : Object constructor

发现数据库结果数组如下：

dbd=[{cardType=1, cardTypeText=, cardNo=62260906, bankType=1}]

 

原因是cardTypeText无值，将cardTypeText赋值可以解决。

 

相同的代码在eclipse中运行没有问题，但是在jmeter中有问题，可能是对空值的默认处理方法不一样。

---------------------


## 使用org.json 中的JSONArray和JSONObject解析JSON数据

```java
import org.json.JSONArray;
import org.json.JSONObject;

public class JSONTest{
   public static void main(String[] args){
      //JSONObject
      String jsonMessage = "{\"语文\":\"88\",\"数学\":\"78\",\"计算机\":\"99\"}";
      String value1 = null;
      try{
           //将字符串转换成jsonObject对象
          JSONObject myJsonObject = new JSONObject(jsonMessage);
          //获取对应的值
          value1 = myJsonObject.getString("数学");
     } catch (JSONException e){
          System.out.println("异常");
      }
      System.out.println("value1="+value1);
//JSONArray
jsonMessage = "[{'num':'成绩', '外语':88, '历史':65, '地理':99, 'object':{'aaa':'1111','bbb':'2222','cccc':'3333'}}," +
       "{'num':'兴趣', '外语':28, '历史':45, '地理':19, 'object':{'aaa':'11a11','bbb':'2222','cccc':'3333'}}," +
       "{'num':'爱好', '外语':48, '历史':62, '地理':39, 'object':{'aaa':'11c11','bbb':'2222','cccc':'3333'}}]";
 
  JSONArray myJsonArray;
    try{
          myJsonArray = new JSONArray(jsonMessage);
          for(int i=0 ; i < myJsonArray.length() ;i++) {
           //获取每一个JsonObject对象
           JSONObject myjObject = myJsonArray.getJSONObject(i);
 
            //获取每一个对象中的值
             String numString = myjObject.getString("num");
             int englishScore = myjObject.getInt("外语");
             int historyScore = myjObject.getInt("历史");
             int geographyScore = myjObject.getInt("地理");
              //获取数组中对象的对象
             JSONObject myjObject2 = myjObject.getJSONObject("object");
             String aaaString = myjObject2.getString("aaa");
             System.out.println("aaaString="+aaaString);
             System.out.println("numString="+numString);
             System.out.println("englishScore="+englishScore);
             System.out.println("historyScore="+historyScore);
             System.out.println("geographyScore="+geographyScore);
          }
       }catch (JSONException e){
                System.out.println("异常");
       }
 
 }
}
```

---------------------
