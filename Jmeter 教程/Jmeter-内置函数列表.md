# JMeter基础：内置函数列表

2019年03月05日 22:13:47 [alice_tl](https://me.csdn.net/alice_tl) 阅读数 784



### **Jmeter有两种类型的函数**

- 用户定义的静态值（或变量）
- 内置函数

用户定义的静态值允许用户定义变量时被替换为静态的值测试树编译并提交运行。需要注意的是，变量目前无法嵌套，即${Var${N}}不起作用

__ V（变量）函数（版本2.2后）可用于执行此操作: ${__V(Var${N})}. 这种类型的替换可能没有函数，但更方便，更直观

###  

### 函数和变量可以在哪里使用？

函数和变量可以被写入到任何领域的任何测试部件。

下面的函数测试计划应确定工作:

- intSum
- longSum
- machineName
- BeanShell
- javaScript
- jexl
- random
- time
- property functions
- log functions

测试计划使用的功能有一些限制。JMeter的线程变量没有被完全成立处理功能时，不会设置变量名作为参数传递，将无法正常工作和变量引用，所以split() 和 regex() 变量赋值函数不会工作。 threadNum() 函数将不能工作

###  

### 引用变量及函数

引用的变量中的测试元件是通过包围在变量名 '${' and '}'.

函数中引用同样的方式，但按照惯例，函数的名称以“__”开头，以避免冲突与用户值的名称

有些函数带参数的配置，而这些括号中，逗号分隔。如果函数没有参数，括号可以省略。对于例如：

```html
${__BeanShell(vars.put("name","value"))} 另外，可以定义你的脚本作为一个变量，例如测试计划：
SCRIPT     vars.put("name","value")
```

然后，该脚本可以被引用如下：

```html
${__BeanShell(${SCRIPT})} 
```

###  

### 预定义变量

JMeter 内部定义的一些变量。它们分别是：

- COOKIE_cookiename - 包含cookie的值
- JMeterThread.last_sample_ok - 与否的最后一个样本是确定的 -  true/false。注：这是更新后的后处理和断言已经运行。
- START 变量

###  

### 预先定义的特性

一些内置的的属性定义JMeter。下面列出了这些。为方便起见，启动的属性也被复制到具有相同名称的变量。

- START.MS - JMeter 启动时间（毫秒）
- START.YMD - JMeter 启动时间为 yyyyMMdd
- START.HMS - JMeter 启动时间为 HHmmss
- TESTSTART.MS - 测试开始时间（毫秒）

请注意，开始变量/属性代表 JMeter 的启动时间，而不是测试开始时间。它们主要适用于使用文件名等。

 

表11-1  JMeter内置函数列表

| 函数类型    | 名称                 | 注释                                                |
| :---------- | :------------------- | :-------------------------------------------------- |
| Information | threadNum            | get thread number                                   |
| Information | samplerName          | get the sampler name (label)                        |
| Information | machineIP            | get the local machine IP address                    |
| Information | machineName          | get the local machine name                          |
| Information | time                 | return current time in various formats              |
| Information | log                  | log (or display) a message (and return the value)   |
| Information | logn                 | log (or display) a message (empty return value)     |
| Input       | StringFromFile       | read a line from a file                             |
| Input       | FileToString         | read an entire file                                 |
| Input       | CSVRead              | read from CSV delimited file                        |
| Input       | XPath                | Use an XPath expression to read from a file         |
| Calculation | counter              | generate an incrementing number                     |
| Calculation | intSum               | add int numbers                                     |
| Calculation | longSum              | add long numbers                                    |
| Calculation | Random               | generate a random number                            |
| Calculation | RandomString         | generate a random string                            |
| Calculation | UUID                 | generate a random type 4 UUID                       |
| Scripting   | BeanShell            | run a BeanShell script                              |
| Scripting   | javaScript           | process JavaScript (Mozilla Rhino)                  |
| Scripting   | jexl, jexl2          | evaluate a Commons Jexl expression                  |
| Properties  | property             | read a property                                     |
| Properties  | P                    | read a property (shorthand method)                  |
| Properties  | setProperty          | set a JMeter property                               |
| Variables   | split                | Split a string into variables                       |
| Variables   | V                    | evaluate a variable name                            |
| Variables   | eval                 | evaluate a variable expression                      |
| Variables   | evalVar              | evaluate an expression stored in a variable         |
| String      | regexFunction        | parse previous response using a regular expression  |
| String      | escapeOroRegexpChars | quote meta chars used by ORO regular expression     |
| String      | char                 | generate Unicode char values from a list of numbers |
| String      | unescape             | Process strings containing Java escapes (e.g. & )   |
| String      | unescapeHtml         | Decode HTML-encoded strings                         |
| String      | escapeHtml           | Encode strings using HTML encoding                  |
| String      | TestPlanName         | Return name of current test plan                    |