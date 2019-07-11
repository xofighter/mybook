## JMeter 检查点之响应断言(Response Assertion)

 ![此博文包含图片](http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif)	

[![img](http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif)转载*▼*](javascript:;)

| 标签： [jmeter检查点](http://search.sina.com.cn/?c=blog&q=jmeter%BC%EC%B2%E9%B5%E3&by=tag) [响应断言](http://search.sina.com.cn/?c=blog&q=%CF%EC%D3%A6%B6%CF%D1%D4&by=tag) [responseassertion](http://search.sina.com.cn/?c=blog&q=responseassertion&by=tag) [jmeter断言](http://search.sina.com.cn/?c=blog&q=jmeter%B6%CF%D1%D4&by=tag) | 分类： [工具研究](http://blog.sina.com.cn/s/articlelist_5314188213_8_1.html) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

**检查点之响应断言****(Response Assertion)**

 

**by:****授客** **QQ****：****1033553122**

JMeter断言用于对sampler(采样器)进行额外检查，且在相同作用域中，每执行完一个sample就会进行一次断言处理，为了确保断言仅作用于某个sample，把该sample添加为子sample。

 

**测试环境**

apache-jmeter-2.13

 

**1.**   **添加响应断言**

**右键线程组****->****添加****->****断言****->****响应断言**

 

[![JMeter 检查点之响应断言(Response Assertion)](http://s6.sinaimg.cn/mw690/005NDN77zy75M6dLsUt35&690)](http://album.sina.com.cn/pic/005NDN77zy75M6dLsUt35)

 

**2.**   **配置响应断言**

 

[![JMeter 检查点之响应断言(Response Assertion)](http://s13.sinaimg.cn/mw690/005NDN77zy75M6fGslC0c&690)](http://album.sina.com.cn/pic/005NDN77zy75M6fGslC0c)





说明：

**Apply to**：指定断言作用范围。

Main sample and sub-sample：作用于主main sample和子sub-sample

Main sample only：只作用于main sample

Sub-samples only：只作用于sub-sample

JMeter Variable：作用于JMeter变量



 

注意：

1、大多数情况下，可只勾选“main sample only”，因为一般情况下，发起一个请求，实际就只有一个请求。但是在某些情况下，发起一个请求时，会触发多个服务器请求，这时候就有main sample和sub-sample之分，类似ajax请求，另外，如果发起重定向请求，并且勾选“跟随重定向”， 则把重定向后的请求视为main-sample

 

2、如果sub-sample断言失败，但main sample断言成功，那么main sample也被设置为失败状态。如果作用域JMeter变量，且该变量关联main sample，那么如果断言失败，则main sample也被设置为失败(If the JMeter variable option is used, it is assumed to relate to the main sample, and any failure will be applied to the main sample only)。

 

3.如果执行完每个sampler的所有断言，变量JMeterThread.last_sample_ok会被设置为true或false

 

**要测试的响应字段：**

 响应文本(Text Response) - 从服务器返回的响应文本，比如body，包含HTTP头

 Document(text) -通过Apache Tika追踪的各种各种类型文档的文本

 URL样本

 响应代码(Response Code) - 比如 200

 响应消息(Response Message) - 比如 OK

 Response Headers - 响应头，包括Set-Cookie 头，如果有的话

 Ignore Status - 指示JMeter设置sampler status的初始状态为success。sample status是否成功，由已Response status和断言结果决定，当选中Ignore Status时，Response status被强制设置为success，不执行进一步的断言判断。仅第一次断言时使用。

[![JMeter 检查点之响应断言(Response Assertion)](http://s11.sinaimg.cn/mw690/005NDN77zy75M6ZOYBkfa&690)](http://album.sina.com.cn/pic/005NDN77zy75M6ZOYBkfa)

 

**要测试的模式****(Patterns to Test):**

填写需要测试的模式列表（list of patterns）。 每个模式都单独测试，如果某个模式失败了，那将不会往下检查剩余的模式。添加一个断言，多个测试模式(通过重复点击面板的添加按钮来添加多个测试模式)，和多个断言，每个断言一个模式是一样的

 

例：一个断言，多个测试模式

 

[![JMeter 检查点之响应断言(Response Assertion)](http://s7.sinaimg.cn/mw690/005NDN77zy75M6lnJ0W46&690)](http://album.sina.com.cn/pic/005NDN77zy75M6lnJ0W46)

 

**3.**   **查看断言结果**

添加监听器：**右键线程组****->** **添加** **->** **监听器** **->** **断言结果**



 

**4.**   **例子**

|                                                              |
| ------------------------------------------------------------ |
| 通常断言组织形式如下： [![JMeter 检查点之响应断言(Response Assertion)](http://s6.sinaimg.cn/mw690/005NDN77zy75M6mfyapb5&690)](http://album.sina.com.cn/pic/005NDN77zy75M6mfyapb5) 例子如下： |

 [![JMeter 检查点之响应断言(Response Assertion)](http://s11.sinaimg.cn/mw690/005NDN77zy75M6q0Pgu2a&690)](http://album.sina.com.cn/pic/005NDN77zy75M6q0Pgu2a)



 



 