**一、Debug Sampler介绍：**

　　使用Jmeter开发脚本时，难免需要调试，这时可以使用Jmeter的Debug Sampler，它有三个选项：JMeter properties，JMeter variables，System properties:

![img](https://images2015.cnblogs.com/blog/77835/201509/77835-20150930174249340-1970950864.jpg)

1、JMeter properties和System properties：通常都选false，这两个就是JMeter和系统的属性，在Jmeter的bin的jmeter.properties中定义，一般都不会变。

2、JMeter variables：这个是我们自已定义的变量，定义的方式有如下这些：

　　a) 选中测试计划(Test plan)，在右边的面板上添加User Defined Variables

　　b) 选中线程组，右键选择 配置元件( config element)-->User Defined Variables

　　c) 通过后置处理器生成的变量，可参考我的另一篇：[Jmeter关联](http://www.cnblogs.com/puresoul/p/4742587.html)[
](http://www.cnblogs.com/puresoul/p/4742587.html)

　　d)使用csv参数化的变量，参数化可参考我另一篇：[Jmeter参数化](http://www.cnblogs.com/puresoul/p/4742120.html)[
](http://www.cnblogs.com/puresoul/p/4742120.html)

 

**二、综合示例：**为了涵盖上面的四种情况，特意编写如下脚本：

1、在Test plan右侧面板添加变量：name=test，value=111

![img](https://images2015.cnblogs.com/blog/77835/201509/77835-20150930181550855-1741712160.jpg)

2、在sampler one(访问百度首页)下添加一个用户变量：name=hello，value=222

![img](https://images2015.cnblogs.com/blog/77835/201509/77835-20150930181608121-1934072358.jpg)

3、在sampler one 下使用后置处理器（正则表达式处理器），获取百度首页title的信息

![img](https://images2015.cnblogs.com/blog/77835/201509/77835-20150930181620371-1163176037.jpg)

4、参数化，变量名为username，值为：tom

![img](https://images2015.cnblogs.com/blog/77835/201509/77835-20150930181641824-536640955.jpg)

![img](https://images2015.cnblogs.com/blog/77835/201509/77835-20150930182206152-529323432.jpg)

 

5、运行结果：

![img](https://images2015.cnblogs.com/blog/77835/201509/77835-20150930181659230-1118317053.jpg)

  

 **三、总结：**

1、Debug Sampler会把我们自定义的变量输出在response data中，方便我们调试的时候使用

2、在正式执行脚本时需要删除Debug Sample

 

 

PS：Jmeter另一种调试工具：[HTTP Mirror Server](http://www.cnblogs.com/puresoul/p/4907655.html)


