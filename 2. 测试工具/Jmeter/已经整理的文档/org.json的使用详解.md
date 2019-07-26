# org.json的使用详解



### 文章目录

- [jar下载](https://blog.csdn.net/qy20115549/article/details/84099339#jar_4)

- [JsonObject类中方法的使用](https://blog.csdn.net/qy20115549/article/details/84099339#JsonObject_16)

- - [Bean对象转化为JsonObject对象](https://blog.csdn.net/qy20115549/article/details/84099339#BeanJsonObject_17)
  - [Map集合转化为JsonObject对象](https://blog.csdn.net/qy20115549/article/details/84099339#MapJsonObject_67)
  - [JSONTokener转化为JsonObject对象](https://blog.csdn.net/qy20115549/article/details/84099339#JSONTokenerJsonObject_110)
  - [基于反射机制构造JSONObject 对象](https://blog.csdn.net/qy20115549/article/details/84099339#JSONObject__207)
  - [基于json字符串构造JSONObject 对象](https://blog.csdn.net/qy20115549/article/details/84099339#jsonJSONObject__432)
  - [基于键值对的方式为JSONObject 对象添加内容](https://blog.csdn.net/qy20115549/article/details/84099339#JSONObject__465)

- [JSONArray类](https://blog.csdn.net/qy20115549/article/details/84099339#JSONArray_540)



# jar下载

基于maven工程的pom文件配置：

```java
<!-- https://mvnrepository.com/artifact/org.json/json -->
		<dependency>
			<groupId>org.json</groupId>
			<artifactId>json</artifactId>
			<version>20180130</version>
		</dependency>
123456
```

保存文件便可以下载所需的jar包。

# JsonObject类中方法的使用

## Bean对象转化为JsonObject对象

将Bean对象转化为JsonObject对象，需要使用JsonObject中的JSONObject(Object bean)构造方法，该构造方法源码为：

```java
 /**
     * Construct a JSONObject from an Object using bean getters. It reflects on
     * all of the public methods of the object. For each of the methods with no
     * parameters and a name starting with <code>"get"</code> or
     * <code>"is"</code> followed by an uppercase letter, the method is invoked,
     * and a key and the value returned from the getter method are put into the
     * new JSONObject.
     * <p>
     * The key is formed by removing the <code>"get"</code> or <code>"is"</code>
     * prefix. If the second remaining character is not upper case, then the
     * first character is converted to lower case.
     * <p>
     * For example, if an object has a method named <code>"getName"</code>, and
     * if the result of calling <code>object.getName()</code> is
     * <code>"Larry Fine"</code>, then the JSONObject will contain
     * <code>"name": "Larry Fine"</code>.
     * <p>
     * Methods that return <code>void</code> as well as <code>static</code>
     * methods are ignored.
     * 
     * @param bean
     *            An object that has getter methods that should be used to make
     *            a JSONObject.
     */
    public JSONObject(Object bean) {
        this();
        this.populateMap(bean);
    }
12345678910111213141516171819202122232425262728
```

这里提供了一个方法，可以在主函数中直接调用。

```java
/***
	 * 使用JSONObject(Object bean)构造方法将Bean对象转化成JSONObject
	 */
	public static JSONObject BeanToJson (){
		BookModel book = new BookModel();
		book.setId("07");
		book.setLanguage("Java");
		book.setEdition("third");
		book.setAuthor("Herbert Schildt");
		//使用JSONObject(Object bean)构造方法
		return new JSONObject(book);
	}
123456789101112
```

## Map集合转化为JsonObject对象

将Map集合转化为JsonObject对象，需要使用JsonObject中的JSONObject(Map<?, ?> m)构造方法，该构造方法源码为：

```java
 /**
     * Construct a JSONObject from a Map.
     *
     * @param m
     *            A map object that can be used to initialize the contents of
     *            the JSONObject.
     */
    public JSONObject(Map<?, ?> m) {
        if (m == null) {
            this.map = new HashMap<String, Object>();
        } else {
            this.map = new HashMap<String, Object>(m.size());
        	for (final Entry<?, ?> e : m.entrySet()) {
                final Object value = e.getValue();
                if (value != null) {
                    this.map.put(String.valueOf(e.getKey()), wrap(value));
                }
            }
        }
    }

123456789101112131415161718192021
```

这里提供了一个方法，可以在主函数中直接调用。

```java
/***
	 * 使用JSONObject(Map<?, ?> m)构造方法将Map集合数据转出成JSONObject
	 */
	public static JSONObject MapToJson (){
		Map<String,String> bookmap = new HashMap<String, String>();
		bookmap.put("id", "07");
		bookmap.put("author", "Herbert Schildt");
		bookmap.put("edition", "third");
		bookmap.put("language", "Java");
		//使用JSONObject(Object bean)构造方法
		return new JSONObject(bookmap);
	}
123456789101112
```

## JSONTokener转化为JsonObject对象

将JSONTokener 对象转化为JsonObject对象，需要使用JsonObject中的JSONObject(JSONTokener x)构造方法，该构造方法源码为：

```java
 /**
     * Construct a JSONObject from a JSONTokener.
     *
     * @param x
     *            A JSONTokener object containing the source string.
     * @throws JSONException
     *             If there is a syntax error in the source string or a
     *             duplicated key.
     */
    public JSONObject(JSONTokener x) throws JSONException {
        this();
        char c;
        String key;

        if (x.nextClean() != '{') {
            throw x.syntaxError("A JSONObject text must begin with '{'");
        }
        for (;;) {
            c = x.nextClean();
            switch (c) {
            case 0:
                throw x.syntaxError("A JSONObject text must end with '}'");
            case '}':
                return;
            default:
                x.back();
                key = x.nextValue().toString();
            }

            // The key is followed by ':'.

            c = x.nextClean();
            if (c != ':') {
                throw x.syntaxError("Expected a ':' after a key");
            }
            
            // Use syntaxError(..) to include error location
            
            if (key != null) {
                // Check if key exists
                if (this.opt(key) != null) {
                    // key already exists
                    throw x.syntaxError("Duplicate key \"" + key + "\"");
                }
                // Only add value if non-null
                Object value = x.nextValue();
                if (value!=null) {
                    this.put(key, value);
                }
            }

            // Pairs are separated by ','.

            switch (x.nextClean()) {
            case ';':
            case ',':
                if (x.nextClean() == '}') {
                    return;
                }
                x.back();
                break;
            case '}':
                return;
            default:
                throw x.syntaxError("Expected a ',' or '}'");
            }
        }
    }

123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869
```

而实例化JSONTokener对象的方法有三种，具体可以参考JSONTokener类的源码，这三种三别为：

1. JSONTokener(Reader reader)
2. JSONTokener(InputStream inputStream)
3. JSONTokener(String s):这个案例中使用的是这种
   以下为使用案例。

```java
/***
	 * 使用JSONObject(JSONTokener x)构造方法将Map集合数据转出成JSONObject
	 */
	public static JSONObject JSONTokenerToJson (){
		/***
		 * JSONTokener类中的构造方法有:
		 * 1. JSONTokener(Reader reader)
		 * 2. JSONTokener(InputStream inputStream)
		 * 3. JSONTokener(String s):这个案例中使用的是这种
		 */
		String jsonStr = "{\"id\":\"07\",\"language\": \"C++\",\"edition\": \"second\",\"author\": \"E.Balagurusamy\"}";
		JSONTokener jsonTokener = new JSONTokener(jsonStr);
		return new JSONObject(jsonTokener);
	}
1234567891011121314
```

## 基于反射机制构造JSONObject 对象

使用到的构造方法为JSONObject(Object object, String names[])，从源码中可以发现其使用的是反射机制。

```java
 /**
     * Construct a JSONObject from an Object, using reflection to find the
     * public members. The resulting JSONObject's keys will be the strings from
     * the names array, and the values will be the field values associated with
     * those keys in the object. If a key is not found or not visible, then it
     * will not be copied into the new JSONObject.
     *
     * @param object
     *            An object that has fields that should be used to make a
     *            JSONObject.
     * @param names
     *            An array of strings, the names of the fields to be obtained
     *            from the object.
     */
    public JSONObject(Object object, String names[]) {
        this(names.length);
        Class<?> c = object.getClass();
        for (int i = 0; i < names.length; i += 1) {
            String name = names[i];
            try {
                this.putOpt(name, c.getField(name).get(object));
            } catch (Exception ignore) {
            }
        }
    }
12345678910111213141516171819202122232425
```

**关于java映射中Class类和Field类的学习，读者可参考javaschool的教程学习，地址为：**

```
http://www.51gjie.com/java/777.html
1
```

Field是一个类,位于Java.lang.reflect包下，在Java反射中Field用于获取某个类的属性或该属性的属性值。这里简单讲解一下Field类的使用：

- 获取Field类对象的方法：

(1 ) Class.getDeclaredField(String name)：返回一个 Field 对象，该对象反映此 Class 对象所表示的类或接口的指定已声明字段（包括私有成员）。
(2 )Class.getDeclaredFields()：返回 Field 对象的一个数组，该数组包含此 Class 对象所表示的类或接口所声明的所有字段（包括私有成员）。
(3)Class.getField(String name)：返回一个 Field 对象，它反映此 Class 对象所表示的类或接口的指定公共成员字段。
(4)Class.getFields():返回一个包含某些 Field 对象的数组，该数组包含此 Class 对象所表示的类或接口的所有可访问公共字段。

- 主要方法有：
  getType()： 获取属性声明时类型对象（返回class对象）
  getGenericType() ： 返回属性声的Type类型
  getName() ： 获取属性声明时名字
  getAnnotations() ： 获得这个属性上所有的注释
  getModifiers() ： 获取属性的修饰
  isEnumConstant() ： 判断这个属性是否是枚举类
  isSynthetic() ： 判断这个属性是否是 复合类
  get(Object obj) ： 取得obj对象这个Field上的值
  set(Object obj, Object value) ： 向obj对象的这个Field设置新值value

下面给出具体的使用案例：
**首先我们编写一个类BookModel1 。在该类中，包含4个变量，其中三个是公有变量(id,language,)edition，一个是私有变量(author)。并且每个变量都有其set和get方法。**

```java
package com.model;

public class BookModel1 {
	public String id;
	public String language;
	public String edition;
	private String author;
	public String getId() {
		return id;
	}
	public void setId(String id) {
		this.id = id;
	}
	public String getLanguage() {
		return language;
	}
	public void setLanguage(String language) {
		this.language = language;
	}
	public String getEdition() {
		return edition;
	}
	public void setEdition(String edition) {
		this.edition = edition;
	}
	public String getAuthor() {
		return author;
	}
	public void setAuthor(String author) {
		this.author = author;
	}
}
1234567891011121314151617181920212223242526272829303132
```

**我们以下面的程序演示获取Field类对象的方法**：

```java
package com.orgjson.parse;

import java.lang.reflect.Field;
import com.model.BookModel1;


public class Demo {

	public static void main(String[] args) throws NoSuchFieldException, SecurityException, IllegalArgumentException, IllegalAccessException {
		BookModel1 book = new BookModel1();
		book.setId("07");
		book.setLanguage("Java");
		book.setEdition("third");
		book.setAuthor("Herbert Schildt");
		//获取 Class对象
		Class cls = book.getClass();
		System.out.println(cls);
		/*返回一个 Field对象,反映此 Class对象所表示的类或接口的指定公共成员字段。
		 * 注意这里如果使用getField("author")则会产生报错,原因是author是私有变量
		 * */
		Field field1 = cls.getField("id");
        System.out.println("Public field found: " + field1.toString());
        //共有变量和私有变量皆可行
        Field field2 = cls.getDeclaredField("author"); 
        System.out.println("Public and Private field found: " + field2.toString());
        //getDeclaredFields()
        System.out.println("getDeclaredFields()结果为:");
        Field[] fieldarr1 = cls.getDeclaredFields(); 
        for (int i = 0; i < fieldarr1.length; i++) {
        	System.out.println("Public and Private field found: " + fieldarr1[i].toString());
		}
        //getFields()
        System.out.println("getFields()结果为:");
        Field[] fieldarr2 = cls.getFields(); 
        for (int i = 0; i < fieldarr2.length; i++) {
        	System.out.println("Public field found: " + fieldarr2[i].toString());
		}
        /*
         * 演示主要方法
		 * */
        //获取属性值
       Object object =  field1.get(book);
       System.out.println(object);
       //获取类型对象
       Class<?> class_test = field1.getType();
       System.out.println(class_test);
	}

}

1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950
```

这个程序的运行结果为：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181115153514621.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3F5MjAxMTU1NDk=,size_16,color_FFFFFF,t_70)

通过这个案例，再去看JSONObject(Object object, String names[]) 构造方法的源码时，发现其本质是将Object 的数据取出来put到Map集合中，然后构造JSONObject。

**下面我将以一个程序演示该构造方法的使用**：
首先，编写一个类，这个类中只有共有变量：

```java
package com.model;

public class BookModel2 {
	public String id;
	public String language;
	public String edition;
	public String author;
	public String getId() {
		return id;
	}
	public void setId(String id) {
		this.id = id;
	}
	public String getLanguage() {
		return language;
	}
	public void setLanguage(String language) {
		this.language = language;
	}
	public String getEdition() {
		return edition;
	}
	public void setEdition(String edition) {
		this.edition = edition;
	}
	public String getAuthor() {
		return author;
	}
	public void setAuthor(String author) {
		this.author = author;
	}	
}
1234567891011121314151617181920212223242526272829303132
```

下面为主函数：

```java
package com.orgjson.parse;

import java.util.HashMap;
import java.util.Map;
import org.json.JSONObject;
import org.json.JSONTokener;
import com.model.BookModel;
import com.model.BookModel2;

public class OrgJsonBeanToObject {

	public static void main(String[] args) {
		JSONObject namesToJson = namesToJson();
		System.out.println(namesToJson);
	}
	
	/***
	 * 使用JSONObject(Object object, String names[])构造方法转出成JSONObject
	 */
	public static JSONObject namesToJson (){
		BookModel2 book = new BookModel2();
		book.setId("07");
		book.setLanguage("Java");
		book.setEdition("third");
		book.setAuthor("Herbert Schildt");
		String names[] = {"id","language","edition","author"};
		return new JSONObject(book,names);
	}
}

123456789101112131415161718192021222324252627282930
```

该程序成功得到json对象：

> {“edition”:“third”,“language”:“Java”,“id”:“07”,“author”:“Herbert Schildt”}

## 基于json字符串构造JSONObject 对象

使用的构造方法为JSONObject(String source)，对应的源码为：

```java
 /**
     * Construct a JSONObject from a source JSON text string. This is the most
     * commonly used JSONObject constructor.
     *
     * @param source
     *            A string beginning with <code>{</code>&nbsp;<small>(left
     *            brace)</small> and ending with <code>}</code>
     *            &nbsp;<small>(right brace)</small>.
     * @exception JSONException
     *                If there is a syntax error in the source string or a
     *                duplicated key.
     */
    public JSONObject(String source) throws JSONException {
        this(new JSONTokener(source));
    }
123456789101112131415
```

这种方式在网络爬虫解析数据时非常常用。如下给出了一个简单的案例：
直接编写一个主函数调用这个函数就可以运行了。

```java
	/***
	 * 使用JSONObject(String source)构造方法转出成JSONObject
	 */
	public static JSONObject stringToJson (){
		//json字符串
		String json = "{\"id\":\"07\",\"language\": \"C++\",\"edition\": \"second\",\"author\": \"E.Balagurusamy\"}";
		return new JSONObject(json);
	}
12345678
```

## 基于键值对的方式为JSONObject 对象添加内容

使用到的处理方法是accumulate和append方法，该方法的源码为：

```java
public JSONObject accumulate(String key, Object value) throws JSONException {
        testValidity(value);
        Object object = this.opt(key);
        if (object == null) {
            this.put(key,
                    value instanceof JSONArray ? new JSONArray().put(value)
                            : value);
        } else if (object instanceof JSONArray) {
            ((JSONArray) object).put(value);
        } else {
            this.put(key, new JSONArray().put(object).put(value));
        }
        return this;
    }

    /**
     * Append values to the array under a key. If the key does not exist in the
     * JSONObject, then the key is put in the JSONObject with its value being a
     * JSONArray containing the value parameter. If the key was already
     * associated with a JSONArray, then the value parameter is appended to it.
     *
     * @param key
     *            A key string.
     * @param value
     *            An object to be accumulated under the key.
     * @return this.
     * @throws JSONException
     *             If the key is null or if the current value associated with
     *             the key is not a JSONArray.
     */
    public JSONObject append(String key, Object value) throws JSONException {
        testValidity(value);
        Object object = this.opt(key);
        if (object == null) {
            this.put(key, new JSONArray().put(value));
        } else if (object instanceof JSONArray) {
            this.put(key, ((JSONArray) object).put(value));
        } else {
            throw new JSONException("JSONObject[" + key
                    + "] is not a JSONArray.");
        }
        return this;
    }
12345678910111213141516171819202122232425262728293031323334353637383940414243
```

针对上述源码中的两种方法以下提供了使用案例：

```java
/***
	 * JSONObject accumulate(String key, Object value)方法的使用
	 */
	public static JSONObject accumulateToJson (){
		JSONObject jsonObject = new JSONObject();
		jsonObject.accumulate("id", "07");
		jsonObject.accumulate("language", "C++");
		jsonObject.accumulate("edition", "second");
		jsonObject.accumulate("author", "E.Balagurusamy");
		return jsonObject;
	}
	/***
	 * JSONObject append(String key, Object value)方法的使用
	 */
	public static JSONObject appendToJson (){
		JSONObject jsonObject = new JSONObject();
		jsonObject.append("id", "07");
		jsonObject.append("language", "C++");
		jsonObject.append("edition", "second");
		jsonObject.append("author", "E.Balagurusamy");
		return jsonObject;
	}
12345678910111213141516171819202122
```

# JSONArray类



--------------------

# Java org.json基本用法（JSONObject/JSONArray）

2017年10月30日 14:32:25 [李炳良](https://me.csdn.net/linysuccess) 阅读数 11837



 版权声明：原创文章，转载请注明链接：【李炳良CSDN博客]】 https://blog.csdn.net/linysuccess/article/details/78392737

#### org.json.JSONObject

JSONObject内部封装了一个HashMap<String, Object>，用于存储json对象的属性名和属性值。其中属性名为String类型。

```java
//JSONObject构造1
JSONObject obj = new JSONObject();
System.out.println(obj.toString());//输出空对象: {}
obj.put("key1", 1);
obj.put("key2", 2);
System.out.println(obj.toString());//输出: {"key2":2,"key1":1}

//JSONObject构造2，参数传入json格式的字符串
JSONObject obj2 = new JSONObject(obj.toString());
System.out.println(obj2.toString());

//JSONObject属性遍历
Iterator<String> it = obj2.keySet().iterator();
while (it.hasNext()) {
	String key = it.next();
	System.out.println(key+"="+(int)obj2.get(key));
}

//获取JSONObject对象某个属性的值
String key = "key1";
if(obj.has(key)) {
	System.out.println(obj.get(key));
}
1234567891011121314151617181920212223
```

`TIPS:` **在用get(String key)获取JSONObject对象的属性值的时候，出于健壮性的考虑应通过has(String key)判断一下，否则可能会抛JSONException异常，可能不同版本JSONObject实现会有差异。**

#### org.json.JSONArray

org.json.JSONArray内部实现则是封装了一个ArrayList<Object>

```java
//JSONArray构造1
JSONArray array = new JSONArray();
array.put(obj);//这里是put,而不是add
array.put(obj);
System.out.println(array.toString());

//JSONArray构造2，传入json数组格式的字符串
String jsonArrStr = "[{\"1000\":2,\"100\":111},{\"1000\":2,\"100\":222}]";
array = new JSONArray(jsonArrStr);
System.out.println(array.toString());

//JSONArray遍历
for(int i=0; i<array.length(); i++) {
	JSONObject jsonObj = array.getJSONObject(i);
	System.out.println(jsonObj.toString());
}
```





--------------------

解析JSONObject
JSONObject为每一种数据类型都提供了一个getXXX(key)方法，例如：获取字符串类型的字段值就使用getString()方法，获取数组类型的字段值就使用getJSONArray()方法。

![img](https://img-blog.csdnimg.cn/20181112085854480.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3BlbmdqdW5sZWU=,size_16,color_FFFFFF,t_70)

 范例：

		// 获取基本类型数据
		System.out.println("Female: " + jsonObj.getBoolean("female"));
		System.out.println("Discount: " + jsonObj.getDouble("discount"));
		System.out.println("Age: " + jsonObj.getLong("age"));
		
		// 获取JSONObject类型数据
		JSONObject features = jsonObj.getJSONObject("features");
		String[] names = JSONObject.getNames(features);
		System.out.println("Features: ");
		for (int i = 0; i < names.length; i++) {
			System.out.println("\t"+features.get(names[i]));
		}
	 
		// 获取数组类型数据
		JSONArray hobbies = jsonObj.getJSONArray("hobbies");
		System.out.println("Hobbies: ");
		for (int i = 0; i < hobbies.length(); i++) {
			System.out.println("\t"+hobbies.get(i));
		}


```
结果：

Female: true
Discount: 9.5
Age: 26
Features: 
	70
	175
Hobbies: 
	yoga
	swimming
```



---------------------

# [Java创建和解析Json数据方法——org.json包的使用(转)](https://www.cnblogs.com/geek1116/p/7413619.html)



# org.json包的使用

## 1.简介

  工具包org.json.jar，是一个轻量级的，JAVA下的json构造和解析工具包，它还包含JSON与XML, HTTP headers, Cookies,  CDL的转换。

​        这篇学习笔记，主要介绍常用的一些类如：JSONObject、JSONArray、JSONStringer等的一些用法；介绍了如何将Map、Collection、Java Bean等对象转化为json数据；介绍了如何使用org.json包解析json数据等。

​      

## 2.常用类

![img](http://img.blog.csdn.net/20160110164551978)

​    ![img](https://images2018.cnblogs.com/blog/841228/201807/841228-20180711113006883-594133747.png)

 

## 3.构造json的示例用法

### **3.1 JSONObject.java**

​        官网给出的JSONObject的构造函数如下：

![img](https://images2018.cnblogs.com/blog/841228/201807/841228-20180711113018540-26580608.png)

![img](http://img.blog.csdn.net/20160110180022945)

​        比较常用就是传入String、map或者bean来构造JSON对象，代码例子如下：

首先定义一个java bean类：

**[java]**[view plain](http://blog.csdn.net/Zen99T/article/details/50351637#)

 

1. **package orgjson;**  
2.   
3. /** 
4.  \* 包含getter和setter的java bean类 
5.  \* @author Zen9 
6.  */  
7. **public class Student {**  
8. ​    **private String name;**  
9. ​    **private String sex;**  
10. ​    **private int age;**  
11. ​      
12. ​    **public String getName() {**  
13. ​        **return name;**  
14. ​    }  
15. ​    **public void setName(String name) {**  
16. ​        **this.name = name;**  
17. ​    }  
18. ​    **public String getSex() {**  
19. ​        **return sex;**  
20. ​    }  
21. ​    **public void setSex(String sex) {**  
22. ​        **this.sex = sex;**  
23. ​    }  
24. ​    **public int getAge() {**  
25. ​        **return age;**  
26. ​    }  
27. ​    **public void setAge(int age) {**  
28. ​        **this.age = age;**  
29. ​    }  
30. }  

然后测试构造方法；也可以用put方法来向JSON对象中添加key/value对，当用put方法时候，value值可以是int、double、String、、boolean、collection、Map等等，但不可以为bean类型，代码如下：

**[java]**[view plain](http://blog.csdn.net/Zen99T/article/details/50351637#)

 

1. **package orgjson;**  
2. **import java.util.ArrayList;**  
3. **import java.util.HashMap;**  
4. **import java.util.List;**  
5. **import java.util.Map;**  
6. **import org.json.\*;**  
7. **public class JsonTest {**  
8. ​    **public static void constructorTest() {**  
9. ​          
10. ​        String jsonStr = "{'name':'JTZen9','age':21}";  
11. ​        JSONObject strJson = **new JSONObject(jsonStr); // 传入字符串**  
12. ​        System.out.println("构造参数为String类：" + strJson);  
13.   
14. ​        Map<string object> map = **new HashMap<string object>();**  
15. ​        map.put("age", 21);  
16. ​        map.put("sex", "male");  
17. ​        map.put("name", "JTZen9");  
18. ​        JSONObject mapJson = **new JSONObject(map); // 传入Map类型**  
19. ​        System.out.println("构造参数为Map类：" + mapJson);  
20.   
21. ​        Student student = **new Student();**  
22. ​        student.setAge(21);  
23. ​        student.setName("JTZen9");  
24. ​        student.setSex("male");  
25. ​        JSONObject beanJson = **new JSONObject(student); // 传入Bean类型**  
26. ​        System.out.println("构造参数为Bean类：" + beanJson);  
27. ​    }  
28.   
29. ​    **public static void putMethodTest() {**  
30. ​          
31. ​        JSONObject jsonObject1 = **new JSONObject();**  
32. ​        jsonObject1.put("bookName", "JTZen9");  
33. ​        jsonObject1.put("age", 21);  
34. ​        System.out.println(jsonObject1);  
35. ​          
36. ​        JSONObject jsonObject2 = **new JSONObject();**  
37. ​        List<object> list = **new ArrayList</object><object>();**  
38. ​        **for (int i = 1; i < 3; i++) {**  
39. ​            Map<string object=""> map = **new HashMap<string object="">();**  
40. ​            map.put("title", "java程序设计 第" + i + "版");  
41. ​            map.put("price", i*20);  
42. ​            list.add(map);  
43. ​        }  
44. ​        jsonObject2.put("book", list);  
45. ​        System.out.println(jsonObject2);  
46. ​          
47. ​        Student student = **new Student();**  
48. ​        student.setAge(21);  
49. ​        student.setName("JTZen9");  
50. ​        student.setSex("male");  
51. ​        jsonObject2 = **new JSONObject(student);**  
52. ​        JSONObject jsonObject3 = **new JSONObject();**  
53. ​        jsonObject3.put("people", jsonObject2);   //不可以直接传bean类对象put("people",student)  
54. ​        System.out.println(jsonObject3);  
55. ​    }  
56.   
57. ​    **public static void main(String[] args) throws Exception {**  
58. ​        constructorTest();  
59. ​        System.out.println("---------------------------------------------------------");  
60. ​        putMethodTest();  
61. ​    }  
62. }  

输出结果：

![img](http://img.blog.csdn.net/20160111001827859)

 ![img](https://images2018.cnblogs.com/blog/841228/201807/841228-20180711113038079-1932496412.png)

### **3.2 JSONArray.java**

#### 1.构造函数

​        官网上的JSONObject的构造函数如下：

![img](https://images2018.cnblogs.com/blog/841228/201807/841228-20180711113048163-309856233.png)

![img](http://img.blog.csdn.net/20160111003818281)

​        这里用Collection、String和Object型的array作为参数，给出例子如下：

**[java]**[view plain](http://blog.csdn.net/Zen99T/article/details/50351637#)

 

1. **package orgjson;**  
2. **import java.util.ArrayList;**  
3. **import java.util.HashMap;**  
4. **import java.util.List;**  
5. **import java.util.Map;**  
6. **import org.json.\*;**  
7. **public class JsonArrayTest {**  
8. ​    **public static void constructorTest() {**  
9. ​          
10. ​        String jsonStr = "[{'name':'JTZen9','age':21}]";  
11. ​        JSONArray strJson = **new JSONArray(jsonStr);     // 传入字符串**  
12. ​        System.out.println("构造参数为String类：" + strJson);  
13.   
14. ​        List<Object> list = **new ArrayList<Object>();**  
15. ​        **for (int i = 1; i < 3; i++) {**  
16. ​            Map<string object=""> map = **new HashMap<string object="">();**  
17. ​            map.put("title", "java程序设计 第" + i + "版");  
18. ​            map.put("price", i*20);  
19. ​            list.add(map);  
20. ​        }  
21. ​        JSONArray mapJson = **new JSONArray(list);        // 传入Collection类型**  
22. ​        System.out.println("构造参数为Collection类：" + mapJson);  
23.   
24. ​        **int[] numlist = new int[10];**  
25. ​        **for (int i = 0; i < numlist.length; i++) {**  
26. ​            numlist[i] = i;  
27. ​        }  
28. ​        JSONArray arrayJson = **new JSONArray(numlist);   // 传入Array类型，实例1**  
29. ​        System.out.println(arrayJson);  
30. ​          
31. ​        Student[] student = {**new Student(),new Student()};**  
32. ​        student[0].setAge(21);  
33. ​        student[0].setName("JTZen9");  
34. ​        student[0].setSex("male");  
35. ​        student[1].setAge(21);  
36. ​        student[1].setName("heiheihei");  
37. ​        student[1].setSex("female");  
38. ​        JSONArray beanJson = **new JSONArray(student);    // 传入Array类型，实例2**  
39. ​        System.out.println("构造参数为Array类：" + beanJson);  
40. ​    }  
41. ​      
42. ​    **public static void main(String[] args) {**  
43. ​        constructorTest();  
44. ​    }  
45. }  

​        输出结果如下：

![img](https://images2018.cnblogs.com/blog/841228/201807/841228-20180711113057529-659423114.png)

![img](http://img.blog.csdn.net/20160111011211139)

#### 2.put方法创建

​        put方法中可以直接将int、double、Collection、Map等加进去，也可以添加下标来指定添加的位置：put(int index, java.util.Map value) ，使用put方法构造的JSON数组，如果传入的是数组，都会自动加一对中括号[ ]，那么出来的结果就会有两层的中括号[ ]，代码例子如下： 

**[java]**[view plain](http://blog.csdn.net/Zen99T/article/details/50351637#)

 

1. **package orgjson;**  
2. **import java.util.HashMap;**  
3. **import java.util.Map;**  
4. **import org.json.\*;**  
5. **public class JSONArrayTest {**  
6. ​    **public static void putMethodTest() {**  
7. ​        JSONArray jsonArray1 = **new JSONArray();**  
8. ​        jsonArray1.put("JTZen9");  
9. ​        jsonArray1.put(21);  
10. ​        jsonArray1.put("male");  
11. ​        System.out.println(jsonArray1);  
12.   
13. ​        JSONArray jsonArray2 = **new JSONArray();**  
14. ​        Map<string object=""> map = **new HashMap<string object="">();**  
15. ​        map.put("title", "java程序设计 第2版");  
16. ​        map.put("price", 20);  
17. ​        jsonArray2.put(map);        //传入一个map  
18. ​        System.out.println("传入一个Map：" + jsonArray2);  
19. ​        map.clear();  
20. ​        map.put("title", "java程序设计 第3版");  
21. ​        map.put("price", 30);  
22. ​        jsonArray2.put(map);        //没有下标的直接在结果后面添加  
23. ​        System.out.println("没有指定下标：" + jsonArray2);  
24. ​        map.clear();  
25. ​        map.put("title", "java程序设计 第1版");  
26. ​        map.put("price", 10);  
27. ​        jsonArray2.put(0,map);      //使用下标可以添加到自定义的位置  
28. ​        System.out.println("添加到第一个位置：" + jsonArray2);  
29. ​          
30. ​        Student[] student = { **new Student(), new Student() };**  
31. ​        student[0].setAge(21);  
32. ​        student[0].setName("JTZen9");  
33. ​        student[0].setSex("male");  
34. ​        student[1].setAge(21);  
35. ​        student[1].setName("heiheihei");  
36. ​        student[1].setSex("female");  
37. ​        JSONArray jsonArray3 = **new JSONArray();**   
38. ​        jsonArray3.put(student);  
39. ​        System.out.println("注意输出结果：" + jsonArray3);  
40.   
41. ​    }  
42.   
43. ​    **public static void main(String[] args) {**  
44. ​        putMethodTest();  
45. ​    }  
46. }  

输出的结果如下：

![img](http://img.blog.csdn.net/20160111095350529)

 ![img](https://images2018.cnblogs.com/blog/841228/201807/841228-20180711113105271-848202967.png)

### 3.3 JSONStringer.java & JSONWriter.java

​        ①JSONWriter可以用来构建一个JSON格式的文本，并转换成String，可以写入文件，便于传输和存储。只有一个构造函数：JSONWriter(java.io.Writer w) 
​        ②常用几个函数和说明：

![img](http://img.blog.csdn.net/20160111101357587)

​        ③JSONStringer是JSONWriter的子类；用法跟JSONWriter几乎一样，区别是可以直接new JSONStringer()创建对象，不用传入构造参数。JSONStringer也可以通过object().key().value().key().value().endObject()进行构造;

​        下面是简单的写入代码例子：（可以尝试写入更复杂数据）

**[java]**[view plain](http://blog.csdn.net/Zen99T/article/details/50351637#)

 

1. **package orgjson;**  
2. **import java.io.PrintWriter;**  
3. **import java.util.HashMap;**  
4. **import java.util.Map;**  
5. **import org.json.\*;**  
6. **public class JSONWriterStringerTest {**  
7. ​    **public static void JSONStringerTest() throws Exception {**  
8. ​          
9. ​        PrintWriter writer = **new PrintWriter("test.txt");**  
10. ​        JSONWriter jsonWriter = **new JSONWriter(writer);**  
11. ​        jsonWriter.object().key("name").value("JTZen9").key("age").value(21).key("sex").value("male").endObject();  
12. ​        writer.flush();  
13. ​        writer.close();  
14. ​          
15. ​        Map<string object=""> map1 = **new HashMap<string object="">();**  
16. ​        map1.put("age", 21);  
17. ​        map1.put("sex", "male");  
18. ​        map1.put("name", "jtzen9");  
19. ​        Map<string object=""> map2 = **new HashMap<string object="">();**  
20. ​        map2.put("age", 21);  
21. ​        map2.put("sex", "female");  
22. ​        map2.put("name", "heiheihei");  
23. ​        JSONStringer jsonStringer = **new JSONStringer();**  
24. ​        jsonStringer.array().value(map1).value(map2).endArray();  
25. ​        System.out.println(jsonStringer);  
26. ​    }  
27.   
28. ​    **public static void main(String[] args) throws Exception {**  
29. ​        JSONStringerTest();  
30. ​    }  
31. }  

输出结果，上面为test.txt文件，下面为控制台：

![img](http://img.blog.csdn.net/20160111105414499)

 

### 3.4 JSONTokener.java

​        JSONTokener读取包含json格式数据的文件，然后可以将JSONTokener对象作为参数来构造JSONObject或JSONArray，然后再进行相应的解析。

以上面的test.txt为例，简单代码示例如下：

**[java]**[view plain](http://blog.csdn.net/Zen99T/article/details/50351637#)

 

1. **package orgjson;**  
2. **import java.io.File;**  
3. **import java.io.FileReader;**  
4. **import org.json.\*;**  
5. **public class JSONTokenerTest {**  
6. ​      
7. ​    **public static void readJsonFile() throws Exception{**  
8. ​        JSONTokener jsonTokener = **new JSONTokener(new FileReader(new File("test.txt")));**  
9. ​        JSONObject jsonObject = **new JSONObject(jsonTokener);**  
10. ​        System.out.println(jsonObject);   
11. ​        System.out.println("姓名：" + jsonObject.getString("name"));  
12. ​        System.out.println("年龄：" + jsonObject.getInt("age"));  
13. ​    }  
14.   
15. ​    **public static void main(String[] args) throws Exception {**  
16. ​        readJsonFile();  
17. ​    }  
18. }  

输出结果如下：

![img](http://img.blog.csdn.net/20160111113244500)

 

## 4.解析例子

​        这里给出一个相对复杂的json数据：

json.txt

**[java]**[view plain](http://blog.csdn.net/Zen99T/article/details/50351637#)

 

1. [  
2. ​    {  
3. ​        "institute":{  
4. ​            "name":"Software Institute",  
5. ​            "grade":[  
6. ​                {  
7. ​                    "name":"freshman",  
8. ​                    "class":[  
9. ​                        {  
10. ​                            "no.":1,  
11. ​                            "students":61  
12. ​                        },  
13. ​                        {  
14. ​                            "no.":2,  
15. ​                            "students":62  
16. ​                        },  
17. ​                        {  
18. ​                            "no.":3,  
19. ​                            "students":63  
20. ​                        }  
21. ​                    ]  
22. ​                },  
23. ​                {  
24. ​                    "name":"sophomore",  
25. ​                    "class":[  
26. ​                        {  
27. ​                            "no.":1,  
28. ​                            "students":51  
29. ​                        },  
30. ​                        {  
31. ​                            "no.":2,  
32. ​                            "students":52  
33. ​                        },  
34. ​                        {  
35. ​                            "no.":3,  
36. ​                            "students":53  
37. ​                        }  
38. ​                    ]  
39. ​                },  
40. ​                {  
41. ​                    "name":"junior",  
42. ​                    "class":[  
43. ​                        {  
44. ​                            "no.":1,  
45. ​                            "students":41  
46. ​                        },  
47. ​                        {  
48. ​                            "no.":2,  
49. ​                            "students":42  
50. ​                        },  
51. ​                        {  
52. ​                            "no.":3,  
53. ​                            "students":43  
54. ​                        }  
55. ​                    ]  
56. ​                }  
57. ​            ]  
58. ​        }  
59. ​    }  
60. ]  

然后，如果我要获取grade为sophomore的no.等于3的students数量，那么代码如下：

**[java]**[view plain](http://blog.csdn.net/Zen99T/article/details/50351637#)

 

1. **package orgjson;**  
2. **import java.io.File;**  
3. **import java.io.FileReader;**  
4. **import org.json.JSONArray;**  
5. **import org.json.JSONObject;**  
6. **import org.json.JSONTokener;**  
7. **public class JSONTest {**  
8. ​    **public static void main(String[] args) throws Exception {**  
9. ​        JSONTokener jsonTokener = **new JSONTokener(new FileReader(new File("json.txt")));**  
10. ​        JSONArray jsonArray = **new JSONArray(jsonTokener);//获取整个json文件的内容，因为最外层是数组，所以用JSONArray来构造**  
11. ​        System.out.println(jsonArray);  
12. ​          
13. ​        //这个JSONArray数组只包含一个JSONObject对象，标为jsonObject1  
14. ​        JSONObject jsonObject1 = jsonArray.getJSONObject(0);  
15. ​        System.out.println(jsonObject1);   
16. ​          
17. ​        //jsonObject1只包含一个institute字段，这里获取这个字段内容赋给jsonObject2  
18. ​        JSONObject jsonObject2 = jsonObject1.getJSONObject("institute");  
19. ​        System.out.println(jsonObject2);  
20. ​          
21. ​        //jsonObject2包含name字段和grade字段，grade字段对应的是一个JSONArray数组  
22. ​        String valueOfname = jsonObject2.getString("name");  
23. ​        System.out.println(valueOfname);  
24. ​        JSONArray jsonArray2 = jsonObject2.getJSONArray("grade");  
25. ​        System.out.println(jsonArray2);  
26. ​          
27. ​        //jsonArray2数组包含3个对象，每个对象里面有name字段和class字段，这里获取第二个对象  
28. ​        JSONObject jsonObject3 = jsonArray2.getJSONObject(1);  
29. ​        System.out.println(jsonObject3);  
30. ​          
31. ​        //然后从jsonObject3对象里获取class字段对应的JSONArray数组  
32. ​        JSONArray jsonArray3 = jsonObject3.getJSONArray("class");  
33. ​        System.out.println(jsonArray3);  
34. ​          
35. ​        //下面直接获取no.等于3的students数量，过程都一样  
36. ​        **int num = jsonArray3.getJSONObject(2).getInt("students");**  
37. ​        System.out.println("最后获取的结果为：" + num);  
38. ​    }  
39. }  

输出的结果如下，这里只截取控制台输出的前半部分结果：

![img](http://img.blog.csdn.net/20160111130440099)

 

## 5.结束语

​        之前解析json数据都是一直百度百度，别人怎么用就怎么用，做大作业时有时候会org.json包也导入，Gson包也导入，然后各用一点功能。现在想想其实只用一个org.json包就可以完全解决我的需求，只是之前一直没有总结各种用法，所以这两天看了下官网源码，总结了下学习笔记，还是收获蛮大的。

​        其实看官网api文档，然后自己动手实践各种方法才是最好的学习方式。百度别人的教程，有时候只能得到小部分的说明。

​        org.json包还有很多的api方法并没有用过，所以就不做笔记了。以后用到再继续完善。