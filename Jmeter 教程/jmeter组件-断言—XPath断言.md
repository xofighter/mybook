Xpath断言
XPath是W3C的一个标准。XPath是一种表达式语言，它使用路径表达式来选取 XML 文档中的节点或节点集。XPath断言和XPath提取器配置和使用方法类似，只是XPath提取器是从文档中是提取符合路径表达式条件的节点信息。而XPath断言是判断文档中存在符合路径表达式条件的节点。

![å¨è¿éæå¥å¾çæè¿°](https://img-blog.csdn.net/20181012132354302?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Nhb2hvbmd4aW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

Apply to：
Main sample and sub-samples – 可以同时应用到主取样器和子取样器。例如带有嵌入资源的HTTP取样器、Mail Reader 或者事务控制器生成的取样器。
Main sample only -只能应用到主取样器。
Sub-samples only - 只能应用到子取样器
JMeter Variable Name to use –应用到指定名称的变量（对指定的变量的值进行提取）
对所有符合条件的取样器按顺序进行取样。例如，如果有一个主取样器和三个子取样器，
当设置为Sub-samples only时，则只对3个子取样器进行检查；
当Main sample and sub-samples，则对1个主取样器和3个子取样器进行检查；。
XML Parsing Options （XML解析选项）：
Use Tidy：当需要处理的页面是HTML格式时，必须选中该选项；如果是XML或XHTML格式（例如RSS返回），则取消选中；
Quiet表示只显示需要的HTML页面，Report errors表示显示响应报错，
Show warnings表示显示警告；
Use Namespaces：如果启用该选项，后续的XML解析器将使用命名空间来分辨；
Validate XML：根据页面元素模式进行检查解析；
Ignore Whitespace：忽略空白内容；
Fetch external DTDs：如果选中该项，外部将使用DTD规则来获取页面内容引用名称：下一个请求要引用的参数名称，如填写title，则可用${ var_regexp2}引用它。
Return entire XPath fragment of text content：返回文本内容的整个XPath片段；
Reference Name：存放提取出的值的参数。
XPath Query：用于提取值的XPath表达式。
Default Value：参数的默认值。

PS:XPath是XML/XHTML中常用的选取给定节点和节点集的方法。

--------------------- 
