# [Jmeter(7)调试工具---HTTP Mirror Server](https://www.cnblogs.com/dyee/p/5510627.html)



　之前我介绍过Jmeter的一种调试工具[Debug Sampler](http://www.cnblogs.com/puresoul/p/4817832.html)，它可以输出Jmeter的变量、属性甚至是系统属性而不用发送真实的请求到服务器。既然这样，那么HTTP Mirror Server又是做什么用的呢？ 

**一、HTTP Mirror Server的作用：**

　　它可以在本地临时搭建一个HTTP服务器，该服务器把接收到的请求原样返回，这样就可以看到发送出的请求的具体内容，以供调试。 

 **二、示例：**

 1、添加HTTP Mirror Server：

　　右键点击WorkBench-->Add-->Non-Test Elements-->HTTP Mirror Server ,点击【Start】启动

![img](https://images2015.cnblogs.com/blog/77835/201510/77835-20151024210519114-1983211060.png)

 2、发送请求到该服务器:

![img](https://images2015.cnblogs.com/blog/77835/201510/77835-20151024210524474-1976166346.png)

 

3、执行结果:

request:

![img](https://images2015.cnblogs.com/blog/77835/201510/77835-20151024210529817-93752871.png)

Response:

![img](https://images2015.cnblogs.com/blog/77835/201510/77835-20151024210534958-1406889195.png)

 

可以看到：response中的内容与request内容一模一样，我们就可以通过此种方法判断我们发送出去的请求是否确实是我们预期的结果。