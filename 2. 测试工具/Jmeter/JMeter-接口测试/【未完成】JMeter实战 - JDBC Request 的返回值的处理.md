# JMeter实战 - JDBC Request 的返回值的处理





![1563960774178](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1563960774178.png)

通过Debug Sampler 查看到的结果如下图：

![1563960968879](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1563960968879.png)

详细信息如下：

```
JMeterVariables:
JMeterThread.last_sample_ok=true
JMeterThread.pack=org.apache.jmeter.threads.SamplePackage@5fbc4d4e
START.HMS=092002
START.MS=1563931202582
START.YMD=20190724
TESTSTART.MS=1563960652614
__jm__setUp Thread Group__idx=0
ip=192.168.30.166
mysql=org.apache.jmeter.protocol.jdbc.config.DataSourceElement$DataSourceComponentImpl@4d5aeca3
ns_date_create_#=6
ns_date_create_1=2019-06-13 18:26:08.0
ns_date_create_2=2019-06-13 13:49:26.0
ns_date_create_3=2019-06-12 11:34:59.0
ns_date_create_4=2019-04-28 11:41:51.0
ns_date_create_5=2019-04-12 15:32:34.0
ns_date_create_6=2019-04-10 13:10:50.0
ns_date_delete_#=6
ns_date_delete_1=null
ns_date_delete_2=null
ns_date_delete_3=null
ns_date_delete_4=null
ns_date_delete_5=null
ns_date_delete_6=null
ns_date_update_#=6
ns_date_update_1=2019-06-17 14:51:15.0
ns_date_update_2=2019-06-17 14:51:00.0
ns_date_update_3=2019-06-17 14:50:12.0
ns_date_update_4=2019-05-17 07:41:15.0
ns_date_update_5=2019-05-17 07:41:15.0
ns_date_update_6=2019-05-17 07:41:15.0
ns_id_#=6
ns_id_1=0c26c8ceb3ae49f0870fe9b8b8e20a1a
ns_id_2=4765582b6f7a4c3b9f35ebc2b0b85e90
ns_id_3=ba86d7c9caea43548544f7467e27e30d
ns_id_4=5cc520ff3c1925090073459e
ns_id_5=5cb03f123c19250900730137
ns_id_6=5cad7ad93c1925090072f02d
ns_is_del_#=6
ns_is_del_1=0
ns_is_del_2=0
ns_is_del_3=0
ns_is_del_4=0
ns_is_del_5=0
ns_is_del_6=0
ns_name_#=6
ns_name_1=newsBanners
ns_name_2=newsBanners
ns_name_3=newsBanners
ns_name_4=newsBanners
ns_name_5=newsBanners
ns_name_6=newsBanners
ns_res=[{date_create=2019-06-13 18:26:08.0, date_delete=null, date_update=2019-06-17 14:51:15.0, name=newsBanners, is_del=0, id=0c26c8ceb3ae49f0870fe9b8b8e20a1a, version=null, value={"img":[{"name":"320200.jpg","id":"c7c7a1a8-5afa-4f4d-92d6-2cc935748c3a","url":"http://storage.e-ports.com/group1/M00/00/2E/wKgeZ10HOF2ALDhCAABa-dUPLeQ756.jpg"}],"link":"","title":"图片小"}}, {date_create=2019-06-13 13:49:26.0, date_delete=null, date_update=2019-06-17 14:51:00.0, name=newsBanners, is_del=0, id=4765582b6f7a4c3b9f35ebc2b0b85e90, version=null, value={"img":[{"name":"海员证.jpg","id":"aa9c3453-e862-4868-9fc6-f85e1dda4125","url":"http://storage.e-ports.com/group1/M00/00/2E/wKgeZ10HOD-AVCtgAANPHCbk-IM718.jpg"}],"link":"","title":"图片大"}}, {date_create=2019-06-12 11:34:59.0, date_delete=null, date_update=2019-06-17 14:50:12.0, name=newsBanners, is_del=0, id=ba86d7c9caea43548544f7467e27e30d, version=null, value={"img":[{"name":"760-432.jpg","id":"0d6b1d42-f14b-4cdf-940e-c341c50da9a9","url":"http://storage.e-ports.com/group1/M00/00/2E/wKgeZ10HOCKAE56UAAVdz4DnPtE848.jpg"}],"link":"https://www.baidu.com/","title":"正常图片信息"}}, {date_create=2019-04-28 11:41:51.0, date_delete=null, date_update=2019-05-17 07:41:15.0, name=newsBanners, is_del=0, id=5cc520ff3c1925090073459e, version=0, value={"img":[{"name":"ys.jpg","id":"5cc520351e7f5f09009bf1fa","url":"https://old.e-ports.com/files/5cc520351e7f5f09009bf1fa"}],"link":"https://www.e-ports.com/en-US/news/5cc520b63c1925090073459d","title":"Held Orders Decrease 60% Globally Over the Past Decade"}}, {date_create=2019-04-12 15:32:34.0, date_delete=null, date_update=2019-05-17 07:41:15.0, name=newsBanners, is_del=0, id=5cb03f123c19250900730137, version=0, value={"img":[{"name":"ys.jpg","id":"5cb03e521e7f5f09009bf1cd","url":"https://old.e-ports.com/files/5cb03e521e7f5f09009bf1cd"}],"link":"https://www.e-ports.com/en-US/news/5cb03e933c19250900730133","title":"Coal Imports Suffer Fluctuation: What Will 2019 Look Like?"}}, {date_create=2019-04-10 13:10:50.0, date_delete=null, date_update=2019-05-17 07:41:15.0, name=newsBanners, is_del=0, id=5cad7ad93c1925090072f02d, version=0, value={"img":[{"name":"ys.jpg","id":"5cad79b41e7f5f09009bf1c7","url":"https://old.e-ports.com/files/5cad79b41e7f5f09009bf1c7"}],"link":"https://www.e-ports.com/en-US/news/5cad79fb3c1925090072f02c","title":"Strong Demand and Sufficient Supply: China Will Import More LNG from US"}}]
ns_value_#=6
ns_value_1={"img":[{"name":"320200.jpg","id":"c7c7a1a8-5afa-4f4d-92d6-2cc935748c3a","url":"http://storage.e-ports.com/group1/M00/00/2E/wKgeZ10HOF2ALDhCAABa-dUPLeQ756.jpg"}],"link":"","title":"图片小"}
ns_value_2={"img":[{"name":"海员证.jpg","id":"aa9c3453-e862-4868-9fc6-f85e1dda4125","url":"http://storage.e-ports.com/group1/M00/00/2E/wKgeZ10HOD-AVCtgAANPHCbk-IM718.jpg"}],"link":"","title":"图片大"}
ns_value_3={"img":[{"name":"760-432.jpg","id":"0d6b1d42-f14b-4cdf-940e-c341c50da9a9","url":"http://storage.e-ports.com/group1/M00/00/2E/wKgeZ10HOCKAE56UAAVdz4DnPtE848.jpg"}],"link":"https://www.baidu.com/","title":"正常图片信息"}
ns_value_4={"img":[{"name":"ys.jpg","id":"5cc520351e7f5f09009bf1fa","url":"https://old.e-ports.com/files/5cc520351e7f5f09009bf1fa"}],"link":"https://www.e-ports.com/en-US/news/5cc520b63c1925090073459d","title":"Held Orders Decrease 60% Globally Over the Past Decade"}
ns_value_5={"img":[{"name":"ys.jpg","id":"5cb03e521e7f5f09009bf1cd","url":"https://old.e-ports.com/files/5cb03e521e7f5f09009bf1cd"}],"link":"https://www.e-ports.com/en-US/news/5cb03e933c19250900730133","title":"Coal Imports Suffer Fluctuation: What Will 2019 Look Like?"}
ns_value_6={"img":[{"name":"ys.jpg","id":"5cad79b41e7f5f09009bf1c7","url":"https://old.e-ports.com/files/5cad79b41e7f5f09009bf1c7"}],"link":"https://www.e-ports.com/en-US/news/5cad79fb3c1925090072f02c","title":"Strong Demand and Sufficient Supply: China Will Import More LNG from US"}
ns_version_#=6
ns_version_1=null
ns_version_2=null
ns_version_3=null
ns_version_4=0
ns_version_5=0
ns_version_6=0
port=8889
```

### 针对 Variable names 的变量处理

以返回值`ns_date_create`为例，需要手动把返回的值，一一写入到Parameters中，如下图。

![1563961112815](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1563961112815.png)

由于返回值中包含空格，被认为是两个字段，所以，在处理时，需要把

```
ns_date_create_#=6
ns_date_create_1=2019-06-13 18:26:08.0
ns_date_create_2=2019-06-13 13:49:26.0
ns_date_create_3=2019-06-12 11:34:59.0
ns_date_create_4=2019-04-28 11:41:51.0
ns_date_create_5=2019-04-12 15:32:34.0
ns_date_create_6=2019-04-10 13:10:50.0
```



```java
int n = bsh.args.length / 2;
String[] strs =new String[n];

for(int i=0;i<bsh.args.length;i+=2){
	n--;
	if(n < 0){
		break;
	}
	//包含多个符号的，用"\\.";因为时间包含毫秒，需要去掉毫秒的部分。
	strs[n] = bsh.args[i]+" "+new String(bsh.args[i+1]).split("\\.")[0];
	
}
props.put("dateCreates",strs);

log.info(">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>> props.get(dateCreates)[0]: " + props.get("dateCreates")[0]);
```



```java
import org.json.*;
//log.info("-------------------------------------------------------------参数验证_findEPNewsBanners is start !!!!");
//log.info("------------------------------------------------------------props.containsKey(dateCreates): "+props.containsKey("dateCreates"));
//log.info("------------------------------------------------------------props.contains(dateCreates): "+props.contains("dateCreates")); 
//log.info("------------------------------------------------------------props.get(dateCreates): "+props.get("dateCreates"));
String tmp=null;
String result = prev.getResponseDataAsString();
JSONObject res = new JSONObject(result);
JSONArray data = res.getJSONArray("data");

if(props.containsKey("dateCreates")){
	String[] dateCreates = props.get("dateCreates");
	for(int i=0; i<data.length(); i++){
		tmp = data.getJSONObject(i).getString("dateCreate");
		for(int j=0; j<dateCreates.length; j++){
			if(tmp.equals(dateCreates[j])){
				Failure = false;
				FailureMessage = "dateCreates 正确! ";
				break;
//				log.info("------------------------------------------------------------dateCreates第"+i+"个不正确! 返回值为："+tmp+", 数据库中为： "+ dateCreates[i]+"。");
			}else{
				Failure = true;
			}
		}
		if(Failure){
			FailureMessage = "dateCreates 不正确! " + tmp + "没有在数据库中找到对应的值";
		}
	}
}else{
	Failure = true;
	FailureMessage = "数据库中不存在数据dateCreates!";
}
```

