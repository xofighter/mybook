# 【Jmeter测试】如何使用BeanShell断言判断请求返回的Json相应结果

2018年10月24日 10:48:03 [哈布哥](https://me.csdn.net/nitibu) 阅读数 1731



- **脚本结构**
  ![img](https://img-blog.csdn.net/2018102410222742?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L25pdGlidQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
  上图中，queryMaterialApiDTOListByPkIds是返回Json格式响应结果的请求，然后添加BeanShell断言详细判断Json结果中的值是否正确。

- **Json格式的相应结果**

  ```json
  {
  	"code": 0,
  	"msg": "success",
  	"success": true,
  	"data": [
  		{
  			"materialCode": "6902265111719",
  			"materialName": "海天蒸鱼豉油450ml*12",
  			"materialType": 1,
  			"sixNineCode": "6902265111719",
  			"expirationDate": 720,
  			"packingSpecification": 12,
  			"basicUnit": "BOX",
  			"minSaleUnit": "BOT",
  			"importFlag": 1,
  			"transportFlag": 0,
  			"sourceSystem": 10,
  			"createrName": "MD自动转换物料",
  			"updaterName": "loms",
  			"pkId": "6902265111719",
  			"mdMaterialCode": "6902265111719",
  			"verifyStatus": 2,
  			"creater": -2,
  			"createTime": 1538984955619,
  			"updater": -2,
  			"updateTime": 1538984955619,
  			"useStatus": 1
  		},
  		{
  			"materialCode": "6902265113003",
  			"materialName": "海天特辣黄豆酱230g*15",
  			"materialType": 1,
  			"sixNineCode": "6902265113003",
  			"expirationDate": 720,
  			"packingSpecification": 15,
  			"basicUnit": "BOX",
  			"minSaleUnit": "BOT",
  			"importFlag": 1,
  			"transportFlag": 0,
  			"sourceSystem": 10,
  			"createrName": "MD自动转换物料",
  			"updaterName": "loms",
  			"pkId": "6902265113003",
  			"mdMaterialCode": "6902265113003",
  			"verifyStatus": 2,
  			"creater": -2,
  			"createTime": 1538984956726,
  			"updater": -2,
  			"updateTime": 1538984956726,
  			"useStatus": 1
  		}
  	],
  	"EnumVal": {}
  }
  ```

  

   

- **BeanShell脚本**

  ```java
   
  import org.json.JSONObject;
  import org.json.JSONArray;
   
  String result = prev.getResponseDataAsString();
   
  JSONObject response = new JSONObject(result);
  JSONArray array = response.getJSONArray("data");
   
  if (array.length() != 2) {
    Failure=true ;
    FailureMessage ="array size < 2";
    return;
  }
   
  int count = 0;
  for (int i = 0; i < 2; i++) {
    JSONObject temp = array.getJSONObject(i);
    String pkId = temp.get("pkId").toString();
    if (pkId.equals("6902265111719")) {
    	
        if (!temp.get("materialCode").equals("6902265111719")) {
            Failure=true ;
            FailureMessage ="pkId: " + pkId + ", material code error, code = " + temp.get("materialCode");
            return;
        }
   
        if (!temp.get("materialName").equals("海天蒸鱼豉油450ml*12")) {
            Failure=true ;
            FailureMessage ="pkId: " + pkId + ", material name error, name = " + temp.get("materialName");
            return;
        }
        count++;
    }
   
    if (pkId.equals("6902265113003")) {
        if (!temp.get("materialCode").equals("6902265113003")) {
            Failure=true ;
            FailureMessage ="pkId: " + pkId + ", material code error, code = " + temp.get("materialCode");
            return;
        }
   
        if (!temp.get("materialName").equals("海天特辣黄豆酱230g*15")) {
            Failure=true ;
            FailureMessage ="pkId: " + pkId + ", material name error, name = " + temp.get("materialName");
            return;
        }
        count++;
    }
  }
   
  if (count != 2) {
    log.info("count != 2");
    Failure=true ;
    FailureMessage ="pkId not in range";
    return;
  }
   
  log.info(array.toString())
   
  ```

  1、先通过prev.getResponseDataAsString获取到响应的返回结果，然后通过org.json.JSONObject和org.json.JSONArray两个类来解析返回的相应结果。
  2、解析出想要的Json对象后，在for循环中对Json对象中每一个需要检测的值和期望的进行比对，如果不正确，Failure设置为true，FailureMessage设置具体的错误信息。
  3、for循环中有可能一开始的pkId取值就和期望不一致，所以这时需要计算下遍历的计数count，如果计数和期望的不一致，说明响应结果和期望结果的数量不一致。