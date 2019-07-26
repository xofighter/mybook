# 【Jmeter】Jmeter：断言

2019年04月01日 14:27:59  更多



 版权声明：本文为博主原创文章，欢迎转载，转载须注明出处：By 故三殇 https://blog.csdn.net/qq_39720249/article/details/88743000

**目录**

[一、Assertion（断言）](https://blog.csdn.net/qq_39720249/article/details/88743000#一、Assertion（断言）)

[二、Response Assertion【响应断言】](https://blog.csdn.net/qq_39720249/article/details/88743000#二、Response Assertion【响应断言】)                    [三、JSON Assertion【JSON 断言】](https://blog.csdn.net/qq_39720249/article/details/88743000#三、JSON Assertion【JSON 断言】)

[四、Size Assertion【大小断言】](https://blog.csdn.net/qq_39720249/article/details/88743000#四、Size Assertion【大小断言】)                             [五、JSR223 Assertion](https://blog.csdn.net/qq_39720249/article/details/88743000#五、JSR223 Assertion)

[六、XPath Assertion【XPath 断言】](https://blog.csdn.net/qq_39720249/article/details/88743000#六、XPath Assertion【XPath 断言】)                      [七、Compare Assertion](https://blog.csdn.net/qq_39720249/article/details/88743000#七、Compare Assertion)

[八、Duration Assertion【断言持续时间】](https://blog.csdn.net/qq_39720249/article/details/88743000#八、Duration Assertion【断言持续时间】)               [九、HTML Assertion【HTML 断言】](https://blog.csdn.net/qq_39720249/article/details/88743000#九、HTML Assertion【HTML 断言】)

[十、MD5Hex Assertion【MD5Hex 断言】](https://blog.csdn.net/qq_39720249/article/details/88743000#十、MD5Hex Assertion【MD5Hex 断言】)             [十一、SMIME Assertion【SMIME 断言】](https://blog.csdn.net/qq_39720249/article/details/88743000#十一、SMIME Assertion【SMIME 断言】)

[十二、XML Assertion【XML 断言】](https://blog.csdn.net/qq_39720249/article/details/88743000#十二、XML Assertion【XML 断言】)                        [十三、XML Schema Assertion【XML Schema 断言】](https://blog.csdn.net/qq_39720249/article/details/88743000#十三、XML Schema Assertion【XML Schema 断言】)

[十四、BeanShell Assertion【BeanShell 断言】](https://blog.csdn.net/qq_39720249/article/details/88743000#十四、BeanShell Assertion【BeanShell 断言】)

------

------

# 一、Assertion（断言）

```java
1、断言组件用来对服务器的响应数据做验证，断言就类似 LoadRunner 中的检查点。2、断言对上一个请求返回的信息，做字符串、数据包大小、HTML、XML、图片等做判断，确保返回的信息的准确性。
断言共有 13 个，分别是： 1、Response Assertion【响应断言】2、JSON Assertion【JSON 断言】3、Size Assertion【大小断言】4、JSR223 Assertion5、XPath Assertion【XPath 断言】6、Compare Assertion7、Duration Assertion【断言持续时间】8、HTML Assertion【HTML 断言】9、MD5Hex Assertion【MD5Hex 断言】10、SMIME Assertion【SMIME 断言】11、XML Assertion【XML 断言】12、XML Schema Assertion【XML Schema 断言】13、BeanShell Assertion【BeanShell 断言】
```

![img](https://img-blog.csdnimg.cn/20190322154706828.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5,size_16,color_FFFFFF,t_70)

![img](https://img-blog.csdnimg.cn/20190322154620904.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5,size_16,color_FFFFFF,t_70)

------

------

# 二、Response Assertion【响应断言】

![img](https://img-blog.csdnimg.cn/2019032215560946.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5,size_16,color_FFFFFF,t_70)

```java
响应断言：1、响应断言可以将请求或响应的各个字段与模式字符串进行比较。2、响应断言是常用的断言，其支持正则表达式。  // ************************* 【响应断言】 参数说明 ************************* //1、名称：响应断言名称，可以随意设置，甚至可以为空。  2、注释：可以随意设置，可以为空。  3、Apply to：匹配范围// Apply to：：是应用范围，设定匹配的范围,用于可以生成子取样器的取样器// 例如：带有嵌入资源的 HTTP 取样器、Mail Reader 或者事务控制器生成的取样器。○ Main sample and sub-samples ：可以同时应用到主取样器和子取样器○ Main sample only ：[默认] 只能应用到主取样器○ Sub-samples only ：只能应用到子取样器○ JMeter Variable Name to use （输入框）：应用到指定名称的变量（对指定的变量的值进行提取），输入框填写变量名[单选框] 4、测试字段:// 针对响应数据不同部分进行匹配需要检查的字段○ 响应文本：[默认] 响应服务器返回的文本内容，http 协议排除 header 部分// 从服务器返回的响应，例如，响应体，包括所有的 HTTP 响应头○ 响应代码：匹配响应代码，比如 http 请求中 ‘200’ 代表成功○ 响应信息：匹配响应信息，处理成功返回 ‘成功’ 或者 “ok” 字样○ Response Headers：匹配响应头中的信息，对于非 HTTP 的取样器可能不存在○ Request Headers：匹配请求头中的信息，包括 Set-Cookie 的头（如果有的话）// http 协议排除 header 部分:对于 HTTP 取样器，头是有用的；但对于其他类型的取样器，它可能不存在。○ URL样本：○ Document（text）：从各种类型的文件通过 Apache Tika（详见结果树视图文档部分)提取文本○ Response Data：请求数据□ 忽略状态：用于在进行断言检查前，先将取样器的状态设置为成功// 忽略状态：注意，因为改设置会清除任何之前的断言失败的影响。所以，请确认只在第一个断言设置该选项。[忽略状态：多选框；除此之外选项：单选框] 5、模式匹配规则：指示如何根据模式检查正在测试的文本。○ 包括：[默认] 使用普通文本模式匹配——部分匹配；若文本包含模式字符串（区分大小写），则断言成立○ 匹配：使用普通文本模式匹配——全部匹配；若整个文本与模式字符串相同（区分大小写），则断言成立○ Equals：应用正则表达式——全部匹配；若整个文本与正则表达式模式匹配，则断言成立○ Substring：应用正则表达式——部分匹配；若文本包含正则表达式模式，则断言成立□ 否：反转检查结果□ 或者：如果不想用 AND 连接（所有的模式都必须匹配，断言才算成功）；用 OR 选项可以用于将多个断言模式进行 OR 连接（只要一个模式匹配，断言就是成功的）[否、或者：多选框；除此之外选项：单选框] 6、测试模式：（1）一列出要测试的模式，每个模式分别测试。（2）如果一个模式失败，则就不再继续进行模式检查。（3）在一个断言下设置用多个模式和设置多个仅有一个模式的断言是没有区别的。[输入框] 7、Custom failure message：自定义失败消息定义失败时的信息。[输入框]
```

------

------

# 三、JSON Assertion【JSON 断言】

![img](https://img-blog.csdnimg.cn/20190322155643275.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5,size_16,color_FFFFFF,t_70)

```java
JSON 断言：1、适用于 json 格式的响应断言,JSON 断言允许完成对JSON文档的校验。2、当响应结果是 json 格式时，用 JSON 断言更方便判断。 3、如果如果数据不是 JSON ，则会解析失败。4、JMeter 会用指定语法寻找指定的路径，如果没有找到，则会失败。5、如果验证期望值为 null ，JMeter提供了 null 复选框，校验空数组使用[]。 // ************************* 【JSON 断言】 参数说明 ************************* //1、名称：JSON 断言名称，可以随意设置，甚至可以为空。  2、注释：可以随意设置，可以为空。  3、Assert JSON Path exists：断言 JSON 路径是否存在JSON Path 输入框默认显示 $. ：在 $. 后面加入需要检查的 json 字段 4、Additionally assert value：附加断言值添加验证的值,只有勾选了此复选框，才可以在 Expected Value 中设置期望的值。[多选框] 5、Match as regular expression：与正则表达式匹配（1）匹配正则表达式，在期望的值中填写正则表达式（2）如果不勾选此项，在 Expected Value 中设置了正则表达式，则不进行匹配。（3）如果 Additionally assert value 不勾选，此项未勾选时，此项将无法勾选。（4）如果 Additionally assert value 不勾选，此项之前勾选过，此时也将处于禁用状态。[多选框] 6、Expected Value：期望值（1）勾选 Additionally assert value ，此处填写断言值。（2）同时勾选 Additionally assert value 和 Match as regular expression ，此处填写正则表达式。（3）勾选 Expect null，此项的内容将被禁用掉。[输入框] 7、Expect null：期望值为 null 如果期望的值为 null ，可以勾选此项。（1）可单独勾选，勾选后，Expected Value 将无法输入。（2）如果勾选 Additionally assert value ，再勾选此项，Match as regular expression 将无法勾选。（3）如果同时勾选 Additionally assert value 和 Match as regular expression，再勾选此项后，Match as regular expression 会变为禁用状态。[多选框] 8、Invert assertion(will fail if above conditions met)：反转断言（如果满足上述条件，将失败）如果匹配的值存在，则断言失败；不匹配，则断言成功[多选框]
```

------

------

# 四、Size Assertion【大小断言】

![img](https://img-blog.csdnimg.cn/20190322155716709.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5,size_16,color_FFFFFF,t_70)

```java
大小断言：验证响应数据 Size 大小，它的作用范围有主 Sample 与子 Sample 。 // ************************* 【大小断言】 参数说明 ************************* //1、名称：大小断言名称，可以随意设置，甚至可以为空。  2、注释：可以随意设置，可以为空。  3、Apply to：匹配范围// Apply to：：是应用范围，设定匹配的范围,用于可以生成子取样器的取样器// 例如：带有嵌入资源的 HTTP 取样器、Mail Reader 或者事务控制器生成的取样器。○ Main sample and sub-samples ：可以同时应用到主取样器和子取样器○ Main sample only ：[默认] 只能应用到主取样器○ Sub-samples only ：只能应用到子取样器○ JMeter Variable Name to use （输入框）：应用到指定名称的变量（对指定的变量的值进行提取），输入框填写变量名[单选框] 4、Response Size Field to Test：要测试的响应大小字段响应域   // 选择需要参与断言的响应域范围。○ Full Response: 全部响应信息，包括响应头和响应体○ Response Headers: 响应头信息，比如 http 协议的头信息○ Response Body: 响应主体内容部分，比如 http 协议 HTML 代码中的 Body 部分。 ○ 响应代码：响应代码，比如 200 ，500 ， 404○ 响应信息：响应信息，比如处理成功返回 ‘成功’ 或者 “ok” 字样 5、Size to Assert：尺寸要求   断言时 Size 的阀值设置，可以设置响应的内容是否小于，大于，等于给定字节大小。（1）比较类型：比值符号○ =：等于○ ！=：不等于○ >：大于○ <：小于○ >=：大于等于○ <=：小于等于[单选框]（2）字节大小：输入框填写数字，字节单位是：byte[输入框]
```

------

------

# 五、JSR223 Assertion

![img](https://img-blog.csdnimg.cn/20190322155753158.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5,size_16,color_FFFFFF,t_70)

```java
JSR223 Assertion：1、JSR223 即 Java 规范请求，是指向 JCP(Java Community Process) 提出新增一个标准化技术规范的正式请求。2、JSR223 Assertion 是针对取样器中的 JSR223 sampler 而使用的断言。 // ************************* 【JSR223 Assertion】 参数说明 ************************* //1、名称：JSR223 Assertion 名称，可以随意设置，甚至可以为空。  2、注释：可以随意设置，可以为空。  3、Script language（e.g.beanshell,javascirpt,jexl）：脚本语言（例如：beanshell、javascirpt、jexl）// 脚本语言（可以从下面的 Language 下拉框中选择对应的脚本语言 beanshell、javascirpt、jexl 等）Language：脚本语言[下拉框] 4、parameters to be passed to script（=> String Parameters and String []args）:要传递到脚本的参数（=> 字符串参数和字符串[]参数）// 传递给脚本的参数：可以理解为使用 JSR223 断言脚本时候一起引用的参数[输入框] 5、Script file（overrides script）：脚本文件（重写脚本）重写脚本：可以通过选择脚本文件的状态，可以浏览调用已有的脚本，也可以在下面的 Script 输入框内写入脚本。File Name：文件名，可输入文件路径 【浏览...：浏览调用已有的脚本路径】[输入框] 6、Script compilation caching：脚本编译缓存Cache compiled script if available：缓存编译脚本（如果可用）[多选框：默认勾选] 7、Script （variables:ctx vars props SampleResult(aka prev) AssertionResult sampler log Label Filename Parameters args[] OUT）:脚本（变量：CTX vars props示例结果（aka prev）断言结果采样器日志标签文件名参数 args[] 输出）Script：脚本，下面的输入框表示可以输入变量类型，运用的脚本（取样结果、断言结果、取样日志文件等参数）[输入框]
```

------

------

# 六、XPath Assertion【XPath 断言】

![img](https://img-blog.csdnimg.cn/20190322155825736.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5,size_16,color_FFFFFF,t_70)

```java
XPath 断言：1、WebServices 协议接口测试可以通过响应断言(支持正则表达式)来做验证，除此之外 XPath Assertion 也能够对返回的 XML 格式信息进行断言，而且是专用的。2、XPath 即为 XML 路径语言，它是一种用来确定 XML（标准通用标记语言的子集）文档中某部分位置的语言。3、XPath 基于 XML 的树状结构，提供在数据结构树中找寻节点的能力。5、XPath 断言针对返回信息为 XPath 的数据类型进行断言。 // ************************* 【XPath 断言】 参数说明 ************************* //1、名称：XPath 断言名称，可以随意设置，甚至可以为空。  2、注释：可以随意设置，可以为空。  3、Apply to：匹配范围// Apply to：：是应用范围，设定匹配的范围,用于可以生成子取样器的取样器// 例如：带有嵌入资源的 HTTP 取样器、Mail Reader 或者事务控制器生成的取样器。○ Main sample and sub-samples ：可以同时应用到主取样器和子取样器○ Main sample only ：[默认] 只能应用到主取样器○ Sub-samples only ：只能应用到子取样器○ JMeter Variable Name to use （输入框）：应用到指定名称的变量（对指定的变量的值进行提取），输入框填写变量名[单选框] 4、XML Parsing Options：XML 解析选项□ Use Tidy(tolerant parser)：使用 Tidy（容错语法解析器）□ Quiet：[默认] 不显示□ Report errors：错误报告□ Show warnings：显示错误□ Use Namespaces：使用命名空间□ Validate XML：验证 XML（文件包/数据）□ Ignore Whitespace：忽略空格// 这允许你指定语法分析器可以忽略哪个空格，而哪个空格是重要的。□ Fetch external DTDs：获取外部 DTDs// 一些 XML 元素具有属性，属性包含应用程序使用的信息，// 属性仅在程序对元素进行读、写操作时，提供元素的额外信息，这时候需要在 DTDs 中声明。[多选框]5、XPath 断言：输入框中写入xpath断言，点击 “验证” 按钮验证其正确性。[输入框]6、True if nothing matches：如果没有匹配项，则为真确认都不匹配。[多选框]
```

------

------

# 七、Compare Assertion

![img](https://img-blog.csdnimg.cn/20190322155850684.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5,size_16,color_FFFFFF,t_70)

```java
Compare Assertion：1、Compare Assertion 是比较断言，用来比较两次取样结果，结果支持正则表达式过滤。2、比较断言会消耗较多资源，所以在测试时一般不建议使用，仅仅用来调试。  3、Compare Assertion 要与 Comparison Assertion Visualizer 结合起来用，前者进行设置，后者进行断言结果显示。4、属于调试功能。5、这是一种比较特殊的断言元件，针对需要进行字符串替换时的断言进行使用。 // ************************* 【Compare Assertion】 参数说明 ************************* //1、名称：Compare Assertion 名称，可以随意设置，甚至可以为空。  2、注释：可以随意设置，可以为空。  3、Select Comparison Operators：选择比较运算符● Compare Content：比较内容可以选择比较的内容类型：true/false 或者自定义、编辑● Compare Time：比较时间可以设定比较的时间，单位为：秒，默认为：-1 4、Comparison Fitters：比较修改工具● regular expression substitutions：替换正则表达式● Regex String：要替换的字符串（可从断言结果中选择）● substitutions：替换的字符串（替换结果）
```

------

------

# 八、Duration Assertion【断言持续时间】

![img](https://img-blog.csdnimg.cn/2019032215591422.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5,size_16,color_FFFFFF,t_70)

```java
断言持续时间：1、持续时间断言，验证取样器运行测试消耗掉的时间，可以针对主取样器与子取样器。2、用于判断服务器的响应时间。3、时间单位是：毫秒。 // ************************* 【断言持续时间】 参数说明 ************************* //1、名称：断言持续时间名称，可以随意设置，甚至可以为空。  2、注释：可以随意设置，可以为空。  3、Apply to：匹配范围// Apply to：：是应用范围，设定匹配的范围,用于可以生成子取样器的取样器// 例如：带有嵌入资源的 HTTP 取样器、Mail Reader 或者事务控制器生成的取样器。○ Main sample and sub-samples ：可以同时应用到主取样器和子取样器○ Main sample only ：[默认] 只能应用到主取样器○ Sub-samples only ：只能应用到子取样器○ JMeter Variable Name to use （输入框）：应用到指定名称的变量（对指定的变量的值进行提取），输入框填写变量名[单选框] 4、断言持续时间：持续时间（毫秒）：响应时间设置（单位：毫秒），如果响应时间大于设置的响应时间，则断言失败，否则成功！[输入框]
```

------

------

# 九、HTML Assertion【HTML 断言】

![img](https://img-blog.csdnimg.cn/20190322155938765.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5,size_16,color_FFFFFF,t_70)

```java
HTML 断言：1、HTML 断言：可以对 HTML 代码内容进行断言，当然此元件不仅仅只支持 HTML ，还支持 XHTML 、XML。2、在 HTML 断言中利用 JTidy 来解析 HTML 代码，JTidy 是 HTML Tidy 的 Java 实现版本，提供了 HTML 的语法检查器。 3、JTidy 可以用来清除格式不好和不对的 HTML ，还提供对整个 HTML 的 DOM 分析。4、我们可以将 JTidy 当作一个处理 HTML 文件的 DOM 解析器来使用。5、具体 JTidy 相关知识请参考官网: http://jtidy.sourceforge.net/ 。6、对响应类为XML类型的文件进行断言。7、针对取样器中的 SOAP/XML-RPC Request 而使用的断言。 // ************************* 【HTML 断言】 参数说明 ************************* //1、名称：HTML 断言名称，可以随意设置，甚至可以为空。  2、注释：可以随意设置，可以为空。  3、Tidy Settings：Tidy 配置// Tidy是一个 HTML 语法检查器和打印工具，可以将 HTML 转换为 XML 类型的文件Doctype：文档类型可通过下拉框选择不同文档类型：● omit 省略的● auto 动态的● strict 严格的● loose 宽泛的[下拉框] 4、Format：文件格式可选择不同类型的文件格式来检查返回内容:○ HTML○ XHTML○ XML[单选框] 5、□ Errors only：误差校正（能接受的最大值）[多选框]● Error threshold：误差/错误范围（可选择误差/错误数量的范围，最大值）● Warning threshold：警告范围（可选择误差警告的数量范围，最大值）[输入框]// 如果勾选“Error only”这里忽略 Warning，只对误差作统计检查；// 如果对返回内容的检查结果不超过指定结果，则断言通过，否则失败。 6、Write JTidy report to file：写入 JTidy 报告的文件// JTidy 是 Tidy 的一个 java 移植，可以将它当成一个处理 HTML 文件的 DOM 解析器文件名：写入 JTidy 报告的文件路径  【浏览...】[输入框]
```

------

------

# 十、MD5Hex Assertion【MD5Hex 断言】

![img](https://img-blog.csdnimg.cn/20190322160007256.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5,size_16,color_FFFFFF,t_70)

```java
MD5Hex 断言：1、对返回的 MD5 结果进行断言，使用简单，直接填入 MD5 值。2、MD5是一种消息摘要算法，用以提供消息的完整性保护（具体关于MD5的知识请自行查询）。3、MD5Hex 断言主要针对参数类型为 MD5Hex 加密的参数的断言。 // ************************* 【MD5Hex 断言】 参数说明 ************************* //1、名称：MD5Hex 断言名称，可以随意设置，甚至可以为空。  2、注释：可以随意设置，可以为空。  3、要断言的 MD5Hex MD5Hex：将已被 MD5 加密的参数写入其中，添加取样器等其他元件[输入框]
```

------

------

# 十一、SMIME Assertion【SMIME 断言】

![img](https://img-blog.csdnimg.cn/20190322160032198.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5,size_16,color_FFFFFF,t_70)

```java
SMIME 断言：1、对加密和签名的邮件进行断言，使用此功能需要引入：bcmail-xxx.jar (BouncyCastle SMIME/CMS) 与 bcprov-xxx.jar (BouncyCastle Provider)包。2、SMIME 是一种多用途网际邮件扩充协议，相比于之前的SMAP邮件传输协议，增加了安全性，对邮件主题进行保护。3、SMIME 断言主要针对采用了 SMIME 邮件传输协议的信息进行断言。 // ************************* 【SMIME 断言】 参数说明 ************************* //1、名称：SMIME 断言名称，可以随意设置，甚至可以为空。  2、注释：可以随意设置，可以为空。 3、 signature：签名（可选择对协议的签名验证状态）□ Verify signature：验证签名□ Message not signed：没有签名消息[多选框] 4、Signer certificate：签名证书（因为SMIME协议增加了安全传输，需要证书验证）○ No check：不检查○ Check values：检查[单选框] Signer distinguished name：签名证书者名称（证书注册者的名称）[输入框] Sigmer email address：签名者的邮件地址（注册的邮件地址）[输入框] Issuer distinguished name：发行者名称（由谁发行的证书）[输入框] Serial Number：证书序号[输入框] ○ Certificate file（输入框）：选择证书文件[单选框][输入框] 5、Execute assertion message at position：执行断言消息的位置（在返回消息的具体哪个位置执行断言）[输入框]
```

------

------

# 十二、XML Assertion【XML 断言】

![img](https://img-blog.csdnimg.cn/20190322160059423.png)

```java
XML 断言：1、用来验证响应数据是正确的 XML 格式。2、一般我们不关心返回格式，关心的是其中的值，所以此元件不常用。3、XML(可扩展标记语言) 提供一种描述结构化数据的方法。4、与主要用于控制数据的显示和外观的 HTML 标记不同，XML 标记用于定义数据本身的结构和数据类型。5、XML 断言主要用于：判断返回结果是否符合 XML 的格式出现。 // 即 <> </> 成对出现。 // ************************* 【XML 断言】 参数说明 ************************* //1、名称：XML 断言名称，可以随意设置，甚至可以为空。  2、注释：可以随意设置，可以为空。
```

------

------

# 十三、XML Schema Assertion【XML Schema 断言】

![img](https://img-blog.csdnimg.cn/2019032216012825.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5,size_16,color_FFFFFF,t_70)

```java
XML Schema 断言：1、XML 概要断言：也可以称为 XML 模型断言（或称为：XML 数据类型断言）。2、XML Schema 定义了两种主要的数据类型：● xml document schema 文档架构 ● 文档架构xml-schema xml模式3、XML Schema 断言主要用于：返回结果为 XML 概要断言的2中数据类型的消息。 // ************************* 【XML Schema 断言】 参数说明 ************************* //1、名称：XML Schema 断言名称，可以随意设置，甚至可以为空。  2、注释：可以随意设置，可以为空。 3、XML Schema：XML概要模型File Name：文件名（写入需要断言的文件名称）[输入框]
// ************************* 【XML Schema】 知识说明 ************************* //1、学习 XML Schema 断言之前，大家需要先补下 XML Schema 的知识：（1）可到维基百科查看（需爬墙）： http://zh.wikipedia.org/zh-cn/XML_Schema （2）可到 W3C 宫网查看（无需爬墙）: http://www.w3school.com.cn/schema/schema_intro.asp2、以下 XML Schema 内容来自百度:（1）XML Schema 可扩展标记语言架构是以可扩展标记语言(标准通用标记语言的子集)为基础的（2）XML Schema 用于可替代文档类型定义(外语缩写: DTD);  （3）一份 XML schema 文件描述了可扩展标记语言文档的结构。（4）XML Schema 的作用是定义一份 XML 文档的合法组件群，就像文档类型定义(外语缩写: DTD)的作用一样。（5）一份 XML Schema 定义: ● 可以出现在文档里的元素● 可以出现在文档里的属性● 哪些元素是子元素● 子元素的顺序● 子元素的数量● 一个元素是否能包含文本，或应该是空的● 元素和属性的数据类型● 元素和属性的默认值和固定值
```

------

------

# 十四、BeanShell Assertion【BeanShell 断言】

![img](https://img-blog.csdnimg.cn/20190322160203589.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NzIwMjQ5,size_16,color_FFFFFF,t_70)

```java
BeanShell 断言：1、BeanShell 之前关于定时器的随笔中有介绍过，是一种松散类型的脚本语言（这点和 JS 类似），一种完全符合 java 语法的 java 脚本语言，并且又拥有自己的一些语法和方法。2、在 JMeter 中除了配置元件外，其他类型的元件中都会有 BeanShell 的身影。3、BeanShell 作为脚本语言能够方便地调用 Java 类，在测试执行时方使用户进行简单的逻辑处理。4、BeanShell 断言：针对 sampler 中的 BeanShell sampler 而使用的断言。 // ************************* 【BeanShell 断言】 参数说明 ************************* //1、名称：BeanShell 断言名称，可以随意设置，甚至可以为空。  2、注释：可以随意设置，可以为空。 3、Reset bsh.interpreter before each call：在每次调用 BeanShell 之前重置 bsh.interpreter 类（bsh.interpreter 是 BeanShell 脚本语言的一种类，也可以理解为一种解析器）[多选框] 4、Parameters（String Parameters and String []bsh.args）：String参数（String []bsh.args是主类main函数的形式参数，是一个String 对象数组，可以用来获取命令行用户输入进去的参数）[输入框] 5、Script file（输入框）：脚本文件（也可以点击 “浏览...” 按钮，填入脚本文件路径）[输入框] 6、Script（see below for variables that are defined）：参照下文定义的变量（使脚本文件参照定义的变量来运行）[输入框] 7、注释翻译：● The following variables are defined for the script：为脚本定义了以下变量：● Read/Write:Failure,FailureMessage,SampleResult,vars,props,log.读/写：失败，故障报文，抽样调查，vars，props，日志。● ReadoOnly：Response[Date|Code|Message|Headers],RequestHeaders,SamplerData,ctx只读的：响应[数据|代码|信息|报文头]，请求头，样本数据，变量
// ************* 在断言元件中，BeanShell 也可以访问 JMeter 的诸多属性，具体有 ************ //1、log 对象：可以利用此对象写日志2、SampleResult 对象：从中可以获取响应数据、响应码等信息，只读3、Response 对象：获取响应数据，只读4、Failure ：用来设置断言成功与否，Boolean 类型5、FailureMessage ：用来设置失败信息6、ResponseData 对象：获取响应数据7、ResponseCode ：响应码8、ResponseMessage ：响应信息9、ResponseHeaders ：响应头信息10、RequestHeaders ：请求头信息11、SampleLabel ：取样器 Label 信息12、SamplerData ：发送给服务器的数据13、Ctx  (JMeterContext) ：JMeter 上下文信息，从中可以获取线程数、线程号等信患14、Vars (JMeterVariables) ：获取 JMeter 中定义的变量，或者设置变量15、Props (JMeterProperties) ：获取 JMeter 中的属性，或者设置属性 // ************************* 下面简单介绍下其他几个方法 ************************* //1、getResponseDataAsString: 从 SampleResult 获取响应数据2、getResponseCode: 从 SampleResult 获取响应 Code 3、getResponseMessage: 从 SampleResult 获取响应消息
```

 