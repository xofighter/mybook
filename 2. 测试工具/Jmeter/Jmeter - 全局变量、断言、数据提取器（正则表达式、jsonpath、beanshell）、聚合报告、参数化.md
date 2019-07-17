## Jmeter - 全局变量、断言、数据提取器（正则表达式、jsonpath、beanshell）、聚合报告、参数化



###  全局变量

### 数据提取器

#### 正则表达式

#### jsonpath

#### beanshell

### 聚合报告
### 参数化

![1561098891881](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1561098891881.png)

![1561099228733](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1561099228733.png)

![1561101163043](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1561101163043.png)

----

### jmeter 跨线程组调用变量

 

由于有些特殊需求需要在线程组之间调用变量，这里就总结一下几种常用方法（只是个人会的）。

在使用时要注意线程组的执行顺序。需要勾选测试计划里的独立运行每个线程组按钮。

一、beanshell自带方法

props.put("变量名","值") 全局变量赋值

props.get("变量名") 获取全局变量

![img](https://images2017.cnblogs.com/blog/1153482/201710/1153482-20171011160622293-1645795660.png)

二、jmeter自带函数__setProperty

${__setProperty(变量名,值} 全局变量赋值

![img](https://images2017.cnblogs.com/blog/1153482/201710/1153482-20171011160653855-682682102.png)

${__property(变量名)} 全局变量提取

![img](https://images2017.cnblogs.com/blog/1153482/201710/1153482-20171011160736293-1372175392.png)

${__P(变量名)} 基本等同于${__property(变量名)}

![img](https://images2017.cnblogs.com/blog/1153482/201710/1153482-20171011160806527-1003389555.png)

三、调用java方法

JMeterUtils.setProperty("变量名", "值") 全局变量赋值

![img](https://images2017.cnblogs.com/blog/1153482/201710/1153482-20171011160838230-838253013.png)

四、使用jmeter插件

利用两个前置跟后置处理器的插件，可以使上下两个线程组传递某一个变量。

通过后置处理器把对应的一个变量值放到FIFO Queue里，然后下个线程组加上前置处理器，再从Queue里把这个变量值重新再赋值给另外一个变量名。

![img](https://images2017.cnblogs.com/blog/1153482/201711/1153482-20171107225451200-335903720.png)

![img](https://images2017.cnblogs.com/blog/1153482/201711/1153482-20171107225529778-747378754.png)

![img](https://images2017.cnblogs.com/blog/1153482/201711/1153482-20171107225545559-247547951.png)

最后，可以通过在工作台添加Property Display元件来查看是否设置成功了全局变量，也可以直接用debugsampler来看，都是可以的。如下图（**但是第四种方法并不设置全局变量，所以是看不到的**）

![img](https://images2017.cnblogs.com/blog/1153482/201710/1153482-20171011160913121-998612650.png)

![img](https://images2017.cnblogs.com/blog/1153482/201710/1153482-20171011160926746-1474087801.png)

![img](https://images2017.cnblogs.com/blog/1153482/201710/1153482-20171011160944621-291440999.png)