在日常接口测试或功能测试过程中，需要把获取到的响应结果，或提取到的某个参数，传递到下一个请求，或者是传递给另外一个线程的请求，这时候就涉及到全局变量的设置，及不同线程如何传参。

#### 函数__setProperty：



![img](https:////upload-images.jianshu.io/upload_images/13144638-528cad5dd23bab25.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/647/format/webp)



${__setProperty(变量名,值} 全局变量赋值

在使用过程中，需要把线程1某个请求返回来的结果，通过正则表达式提取后，设置为全局变量

1、添加--后置处理器 -- 正则表达式

2、使用正则表达式获取第一个线程组得到的响应信息 例如：result



![img](https:////upload-images.jianshu.io/upload_images/13144638-4d0dc7e7f8c25143.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/820/format/webp)



3、添加--后置处理器 -- BeanShell PostProcessor

设置BeanShell PostProcessor ,内容如下：

> ${__setProperty(newresult,${result},)}



![img](https:////upload-images.jianshu.io/upload_images/13144638-62b25aaa4a4f0acb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/518/format/webp)



4、在线程组2中使用_p函数来调用jmeter属性，获取属性值，从而实现跨线程组，参数值传递。

${__P(变量名)} 基本等同于${__property(变量名)}



![img](https:////upload-images.jianshu.io/upload_images/13144638-67b3accfcb35f358.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/504/format/webp)



因此通过__setProperty基本上可以完成全局变量的设置，及后续参数的调用。

5、这种引用关系存在执行的先后顺序，需要在测试计划中设置执行完一个之后，再执行另一个，否则会由于不同线程之间执行时间差，导致参数无法获取到。



![img](https:////upload-images.jianshu.io/upload_images/13144638-7a7a013e257da207.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/675/format/webp)

