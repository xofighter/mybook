**关键字：排查问题技巧、js处理器（参数加密、生成校验码。。。）**

1.首先录制脚本，这里运用fiddler录制APP的登录、退出脚本

2.打开jmeter，测试计划--添加线程组--打开录制的脚本--添加监听器（查看结果树）--保存--运行

查看结果树：运行失败，响应数据显示404。。。。



![img](https:////upload-images.jianshu.io/upload_images/3089283-753cf2cf0b3af7b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/483/format/webp)



> **排查原因小技巧：**jmeter设置代理，fiddler开启抓包 ，比对正常request请求数据与脚本运行的请求数据情况

**添加请求默认设置HTTP  RequestDefaults.**  



![img](https:////upload-images.jianshu.io/upload_images/3089283-b6682f4af46ffed9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/628/format/webp)





![img](https:////upload-images.jianshu.io/upload_images/3089283-47d7bdcad5960eee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/551/format/webp)



结果比对：缺少哈希time



![img](https:////upload-images.jianshu.io/upload_images/3089283-fcde42c19aa83b0d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/757/format/webp)



问题找到了，要如何处理呢？，处于安全性考虑，很多应用会做些相关处理，比如登录密码加密， [jmeter引用js实现RSA加密](https://blog.csdn.net/waynibear/article/details/78084465)。。。。

如何引用js处理器去处理哈希time呢？

**.添加前置处理器 JSR223 PreProcessor.**



![img](https:////upload-images.jianshu.io/upload_images/3089283-a28e19c51b6287ec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/630/format/webp)





![img](https:////upload-images.jianshu.io/upload_images/3089283-030db1ecd2224013.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/896/format/webp)



添加后选择脚本语言js, 然后在Script面板写上我们要执行的js方法(其他js的复杂操作大家可以去找下相关资料). 配合 Log Viewer 可以查看调试执行结果.

​     PS：顺便提下js里面的JMeter变量"vars",vars 是Jmeter提供的一个全局变量对象，用户操作Jmeter中的变量！ vars的本质是Map, 可以使用put("xxx", abc), get("xxx"), 用来设置值和取值. 这在我们下面将要用到的管理器里面很有用.

**.添加请求头管理器 Header Manager.**



![img](https:////upload-images.jianshu.io/upload_images/3089283-79978afec3e90108.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/618/format/webp)





![img](https:////upload-images.jianshu.io/upload_images/3089283-83e15532338fe9eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/943/format/webp)



​    这里的设置会对我们发起的请求自动加上这个值, 达到自定义请求头header设置的效果.在管理器面板添加参数, value对应的是我们需要从js处理后的值, 用${xxx}取vars里面put进去的值.

再次运行一下，OK了。。。

作者：芯W

链接：https://www.jianshu.com/p/9a36bd9f1597

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。