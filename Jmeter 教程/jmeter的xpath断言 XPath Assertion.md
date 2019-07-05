# [jmeter的xpath断言 [XPath Assertion\]](https://www.cnblogs.com/xuxiongbing/p/9482930.html)

 

这个断言很简单，就是左边（xpath）等于右边（预期结果）

 

实例：

首先我请求的是我的博客地址https://www.cnblogs.com/xuxiongbing/p/9475772.html，然后要断言两个参数

分别是标题和访问链接正确与否，下图1和2

![img](https://images2018.cnblogs.com/blog/937391/201808/937391-20180815174044246-592109827.png)

![img](https://images2018.cnblogs.com/blog/937391/201808/937391-20180815174120827-193964103.png)

 

 

步骤：

添加xpath断言，我jmeter4.0是自带的，然后左边（xpath）等于右边（预期结果）即可，多个断言用and连接

![img](https://images2018.cnblogs.com/blog/937391/201808/937391-20180815174338249-402645135.png)

 

 最后，当然是成功了

![img](https://images2018.cnblogs.com/blog/937391/201808/937391-20180815174729000-1031355224.png)

 



分类: [jmeter](https://www.cnblogs.com/xuxiongbing/category/1277869.html)