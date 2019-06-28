# JMeter系列（六）：BeanShell Sampler与BeanShell断言

2019年06月25日 21:35:07 [仅此而已丶Mu](https://me.csdn.net/mu_wind) 阅读数 15



[![知识共享许可协议](https://csdnimg.cn/release/phoenix/images/creativecommons/80x15.png)](http://creativecommons.org/licenses/by-sa/4.0/) 版权声明：署名，允许他人基于本文进行创作，且必须基于与原先许可协议相同的许可协议分发本文 （[Creative Commons](http://creativecommons.org/licenses/by-sa/4.0/)）



### BeanShell Sampler与BeanShell断言

- - [BeanShell操作变量](https://blog.csdn.net/mu_wind/article/details/93506974#BeanShell_10)

  - [BeanShell引用外部资源](https://blog.csdn.net/mu_wind/article/details/93506974#BeanShell_34)

  - [BeanShell断言](https://blog.csdn.net/mu_wind/article/details/93506974#BeanShell_75)

  - - [校验JSONObject](https://blog.csdn.net/mu_wind/article/details/93506974#JSONObject_84)
    - [校验含JSONArray的JSON](https://blog.csdn.net/mu_wind/article/details/93506974#JSONArrayJSON_134)


BeanShell是一种完全符合Java语法规范的脚本语言（当然，也有一些不一样的地方，比如），但又拥有一些独有的语法和方法。BeanShell应用在JMeter的三个元件中：（位于Sampler中）、（位于前置处理器）和（位于后置处理器）、（位于断言中）。BeanShell Sampler是一个取样器，一般归属于线程组或逻辑控制器，相对独立，而BeanShell PreProcessor和BeanShell PostProcessor归属于sampler（取样器），前者是固定在sampler运行前运行，后者是在sampler运行后运行，二者都依赖于sampler而存在。BeanShell断言同样依赖于sampler，但它的作用是断言sampler是否成功。介绍几个BeanShell常用的独有方法：

| 序号 | 名称                                     | 作用                                                         |
| ---- | ---------------------------------------- | ------------------------------------------------------------ |
| 1    | vars.get(“variableName”)                 | 根据变量名获取内存中的变量值，前提是这个值在脚本前文中已经定义并赋值 |
| 2    | vars.put(“variableName”,“variableValue”) | 将一个值存储到变量中，脚本下文中可以通过${variableName}引用  |
| 3    | prev.getResponseDataAsString()           | 获取sampler（取样器）的响应数据并以String类型接收，用在【后置处理器】的【BeanShell PostProcessor中】 |

## BeanShell操作变量

前面说到了BeanShell的独有方法，vars.get和vars.put了，BeanShell对变量的操作主要就是依赖这两个方法。
首先创建一个脚本，【用户定义的变量】中定义了一个变量，变量名为"paramIn"，值为"in"。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190624195532527.png)
【BeanShell Sampler】中写入下面语句：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190624195638584.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70)
运行脚本，查看结果树，可以看到BeanShell可以改写已有变量（param）的值，也可以新生成一个变量（param2）：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190624195805301.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70)
在BeanShell中直接写代码，方便快捷，在代码量不大的情况下十分便利。如果出于规范化考虑，尤其代码量较大时，为了使BeanShell看起来更清晰，可以按下面的方式写，效果是一样的：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019062510495814.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70)

```javascript
public void test(){
	// vars.get获取paramIn的值
	String param = vars.get("param");
	// 一个简单的字符串拼接
	String str = "Hello," + param;
	// vars.put将值赋予变量
	vars.put("param",str);
	vars.put("param2",str + ",welcome");
}

// 需要主动调用函数，否则函数不会自动起作用
test();
123456789101112
```

## BeanShell引用外部资源

如果JMeter脚本的代码量比较小，那么直接在将代码写在Beanshell中就可以了。如果代码量比较大，在Beanshell里写起来就比较困难，这时候可以考虑引用外部资源，包括引用.java文件、.class文件、.jar文件三种方式。
首先，我们写好这么一个类，类内的md5Encryption方法，是将一个字符串转化为一个经过MD5加密过的新字符串。

```javascript
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;

public class Md5Encryption {
	public static String md5Encryption(String string) {
		try {
			MessageDigest md = MessageDigest.getInstance("MD5");
			md.update(string.getBytes());
			byte[] b = md.digest();
			int i;
			StringBuffer buf = new StringBuffer("");
			for (int offset = 0; offset < b.length; offset++) {
				i = b[offset];
				if (i < 0)
					i += 256;
				if (i < 16)
					buf.append("0");
				buf.append(Integer.toHexString(i));
			}
			return buf.toString();
		} catch (NoSuchAlgorithmException e) {
			e.printStackTrace();
			return null;
		}
	}
}
1234567891011121314151617181920212223242526
```

**引用.java文件**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190625115318720.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70)
**引用.class文件**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190625141555186.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70)
**引用.jar文件**：适用于文件很多的情况，可以将项目打成jar包，**导入JMeter安装目录\lib\etc中，并重启JMeter**。
如何将Java项目打包见文章：还没写完，写完贴上
上面完成后，BeanShell中直接import即可使用：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190625144531311.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70)
运行脚本，查看结果，可以看到原密码"123456"被加密生成了新的字符串：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190625144633827.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70)

## BeanShell断言

接口测试中，所谓断言，是指用一定的判断规则对接口响应数据进行校验，不匹配则判定接口测试失败。在JMeter中，不加断言的话，默认校验接口的响应码。
例如下面的例子，登录失败，但没有添加断言，且接口响应码是200，所以接口被JMeter判定为成功。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190625151107304.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70)
根据接口定义（一般由开发提供的接口文档定义），登录成功后，返回内容的message是“操作成功”，于是我们给接口添加一个【响应断言】
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190625151348691.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70)
再次运行，可以看到接口被判定为失败：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190625151444531.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70)
对于这种校验比较简单的接口，【响应断言】能够满足要求，但遇到结果校验很复杂的接口，【响应断言】就无法胜任了，这时候就要用到【BeanShell断言】了。

### 校验JSONObject

首先使用【BeanShell Sampler】作为mock server返回这样的预期结果：

```javascript
{
	"code" : 0,
	"goodsInfo" : {
		"name" : "computer",
		"price" : 4500,
		"size" : 60
	}
}
12345678
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190625182515424.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70)
假如这个数据是根据id=1（id是接口的一个入参）的条件获取的，那么我们就可以编写sql语句去数据库查询数据了。
数据库中的数据：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190625203936267.png)
JDBC Request请求：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190625182807702.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70)
【BeanShell断言】，**JSON处理用得是阿里的fastjson，jar包自行下载后放入JMeter的安装目录的/lib/etc中并重启JMeter**：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190625211658391.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70)
运行后，查看结果：
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019062521082434.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70)
附上【BeanShell断言】代码

```javascript
import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONObject;
// prev.getResponseDataAsString()方法获取接口响应数据
String goodsDataRes = prev.getResponseDataAsString();
// 将String解析成JSONObject并获取goodsInfo
JSONObject goodsInfoRes = JSON.parseObject(goodsDataRes).getJSONObject("goodsInfo");
// 定义一个新的JSONObject用来存储数据库数据
JSONObject goodsInfoDb = new JSONObject();

try{
	goodsInfoDb.put("name",vars.get("name_1"));
	// 这里注意将数字转为int类型
	goodsInfoDb.put("price",Integer.valueOf(vars.get("price_1")));
	goodsInfoDb.put("size",Integer.valueOf(vars.get("size_1")));

	if(!goodsInfoRes.equals(goodsInfoDb)){
		Failure = true;
		String message = "接口返回数据与数据库数据不一致！\n";
		FailureMessage = message + "数据库内容: " + goodsInfoDb + "\n响应内容: " + goodsInfoRes;
	}
}catch(Exception e){
	Failure = true;
	String message = "数据库数据为空！\n";
	FailureMessage = message + "数据库内容: \n" + goodsInfoDb + "\n" + "响应内容: \n" + goodsInfoRes;
}
12345678910111213141516171819202122232425
```

### 校验含JSONArray的JSON

我们将问题复杂化一些，假如接口返回的是包含JSONArray的数据，如下：

```javascript
{
	"code" : 0,
	"data" : [{
			"name" : "iphone",
			"price" : 6000,
			"size" : 55
		}, {
			"name" : "watch",
			"price" : 500,
			"size" : 35
		},{
			"name" : "computer",
			"price" : 4500,
			"size" : 60
		}
	]
}
1234567891011121314151617
```

上面的数据，对应数据库里status=1的数据：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190625212809523.png)
针对这种需求，sql语句要修改一下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190625213344584.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70)
这个接口结果校验的难点在于，接口数据与数据库数据的顺序不对应，且无法事先确定其顺序，如果直接对比两个JSONObject，由于JSONArray中元素顺序不同，会导致对比返回false，尽管两个JSONObject中的数据除了顺序，其他都相同。
要解决顺序问题，我想到的方案是对数据进行进一步加工，将JSONArray处理成JSONObject格式，这样就消除了顺序的影响（不含JSONArray的JSONObject的对比是不受元素顺序影响的），【BeanShell断言】内代码贴上：

```javascript
import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONObject;
import com.alibaba.fastjson.JSONArray;

String goodsDataRes = prev.getResponseDataAsString();
JSONArray goodsListRes = JSON.parseObject(goodsDataRes).getJSONArray("data");
vars.put("goodsListRes",goodsListRes.toString());

JSONObject goodsInfoDb = new JSONObject();
JSONObject goodsInfoRes = new JSONObject();

try{
	for(int i = 1;i <= Integer.parseInt(vars.get("name_#"));i++){
		JSONObject goods = new JSONObject();
		goods.put("name",vars.get("name_" + i));
		goods.put("price",Integer.parseInt(vars.get("price_" + i)));
		goods.put("size",Integer.parseInt(vars.get("size_" + i)));
		goodsInfoDb.put(vars.get("name_" + i),goods);
	}

	for(int i = 0; i < goodsListRes.size();i++){
		JSONObject goods = goodsListRes.getJSONObject(i);
		goodsInfoRes.put(goods.getString("name"), goods);
	}
	
	if(goodsInfoRes.size() != goodsInfoDb.size()){
		Failure = true;
		String message = "接口返回数据与数据库数据的数量不一致！\n";
		FailureMessage = message + "数据库数据数量: " + goodsInfoDb.size() + "\n响应数据数量: " + goodsInfoRes.size() + "\n数据库内容: " + goodsInfoDb + "\n响应内容: " + goodsInfoRes;
	}else if(!goodsInfoRes.equals(goodsInfoDb)){ 
		Failure = true;
		String message = "接口返回数据与数据库数据的内容不一致！\n";
		FailureMessage = message + "数据库内容: " + goodsInfoDb + "\n响应内容: " + goodsInfoRes;
	}
}catch(Exception e){
	Failure = true;
	String message = "数据处理异常！\n";
	FailureMessage = message + "数据库内容: \n" + goodsInfoDb + "\n" + "响应内容: \n" + goodsInfoRes;
}
123456789101112131415161718192021222324252627282930313233343536373839
```

使用这个断言，我们是测试一下：
1、数据处理异常，可以人为将sql写错，例如：select price,size from test.goods where status=1（缺少name字段）;
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190626110233200.png)
2、接口数据与数据库数据的数量不一致，可以将数据库数据篡改一下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190626110350718.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190626110421504.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70)
3、数据库数据和接口响应数据数量一致，但内容不同
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190626110546474.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190626110605483.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70)
4、数据库数据与接口响应数据一致，断言通过。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2019062611102449.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L211X3dpbmQ=,size_16,color_FFFFFF,t_70)
以上，是BeanShell的相关知识，后续还会更新更多的使用场景。