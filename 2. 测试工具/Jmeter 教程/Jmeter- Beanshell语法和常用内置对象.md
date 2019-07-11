# Jmeter- Beanshell语法和常用内置对象

2018年06月30日 16:26:39 [AnndyTuo](https://me.csdn.net/hujyhfwfh2) 阅读数 7993



# 1：Beanshell的基本语法

1.1 Beanshell的For循环与集合
String a = "aaa";
int b = 111;

List list  = new ArrayList();
//list集合只能保存String类型
list.add(a);
list.add(b.toString());
//普通for循环的写法
for(int i = 0; i<list.size();i++){
log.info(">>>>>>>>>>>");
log.info(list.get(i));
}
//增强for循环的写法
for(object : list){
log.info("==========");
log.info(object);
}
1.2：Beanshell的数组的写法
String[] str1 = new String[]{"aa","bb","cc"};//new一个String[]一维数组

String[][] str2 = new String[][]{{"AA","BB"},{"CC","DD"},{"EE","FF"}};
//遍历str1这个一维数组
for(int i = 0; i< str1.length ; i++){
log.info(str1[i]);
}
//遍历第二个二维数组
for (String[] strings : str2) {

for(int i = 0; i< strings.length ; i++){

log.info(strings[i]);
}
}
1.3：Beanshell定义一个函数 并调用
//定义一个方法
public void addlist(List list){

list.add("aaa");
list.add("bbb");
list.add("ccc");	
}

List list = new ArrayList();
//调用方法
addlist(list);
//遍历list集合
for(int i = 0; i < list.size(); i++){
log.info(list.get(i));
}





# 2：Beanshell常用的内置对象

## 2.1：bsh.args对象获取数组参数



![img](https://img-blog.csdn.net/2018062922463648?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1anloZndmaDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 2.2：vars内置对象的使用

A：示例定义一个用户自定义变量

![img](https://img-blog.csdn.net/20180630125612244?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1anloZndmaDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

B:vars.get(String key)获取String变量 

   vars.put(String key,Value)放置一个Map到vars 

![img](https://img-blog.csdn.net/20180630125339439?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1anloZndmaDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 2.3：vars内置对象put和get Object对象

如果需要获取或者放置非String类型数据 需要用putObject（）和getObject（）

```
//Beanshell vars.putObject() vars.getObject()的使用
List list = new ArrayList();

list.add("aaa");
list.add("bbb");
//putObject()可以在vars对象中放置Object类型对象
vars.putObject("list",list);
//===========================
//获取Object list对象
Object Value = vars.getObject("list");
//log.info只能输出String类型变量

## log.info(Value.toString());
```

---------------------






![img](https://img-blog.csdn.net/201806301341377?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1anloZndmaDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#  3：Beanshell的常用内置对象-cxt

3.1：cxt内置对象getPreviousResult()方法 调用请求/响应数据

cxt内置对象来自于J[MeterContext](http://jmeter.apache.org/api/org/apache/jmeter/threads/JMeterContext.html) 查看Jmeter API发现有下列get方法可以获取请求/响应数据

```
//SampleResult需要import对象
import org.apache.jmeter.samplers.SampleResult;

SampleResult result = ctx.getPreviousResult();
//getRequestHeaders()方法返回String字符串
String RequestHeaders = result.getRequestHeaders();
//getResponseHeaders() 返回响应headers
String ResponseHeaders = result.getResponseHeaders() 
//getResponseCode() 返回响应状态码字符串
String responseCode = result.getResponseCode();
//getURL() 返回请求URL对象 
URL url = result.getURL();
log.info(RequestHeaders);
log.info(RequestHeaders);

```





# 4：prev内置对象

prev是Beanshell后置处理器的内置对象 相当于ctx.getPreviousResult();

