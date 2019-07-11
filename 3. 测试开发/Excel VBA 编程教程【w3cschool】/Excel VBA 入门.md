[TOC]

# Excel VBA 入门

本教程所用系统环境：

- Windows 10
- Excel 2013

## 1. 添加开发工具

打开Excel，依然找到“文件”->“选项”->“自定义功能区”，在右侧主选项卡下面的选项中，找到“开发工具”，在前面的小框打勾，确定。返回Excel，即可在菜单栏右侧看到有开发工具：

![img](https://images2015.cnblogs.com/blog/983631/201706/983631-20170613213026353-838138211.png)

![img](https://images2015.cnblogs.com/blog/983631/201706/983631-20170613213048915-1591593381.png)

## 2. 使用VBE

VBE即VBA的编辑环境。通常有两种方式可以进入

- 菜单栏 -> 开发工具 -> Visual Basic
- 快捷键：Alt + F11

## 3. 第一个VBA程序

进入VBE后，在菜单栏依次选择“插入”->“模块”，然后光标会自动定位到代码窗口中，VBA中的代码即在些编写。

VBA常使用“过程”来组织代码（另一种方式是“函数”，后面会介绍）。过程用

```
Sub sub_name() 
    '
    '中间这里是我们要实现各种操作的VBA代码
    '
End Sub
```

表示。其中`Sub`和末尾的`End Sub`是必须的固定形式，sub_name是过程名称，后面的括号`()`是必须的。VBA中允许使用中文作为过程名称，但建议还是尽量使用英文为好。

### 第一个VBA程序

```
Sub Hello_World()
    Debug.Print "Hello, World!"
    MsgBox "Hello, World!"
End Sub
```

这个过程的名字是`Hello_World`。运行VBA代码有三种方式：

- 点击菜单栏下面的工具栏中的绿色向右的小三角![img](https://images2015.cnblogs.com/blog/983631/201706/983631-20170613214829931-1955676325.png)
- 在菜单栏选择“运行”->“运行子过程/用户窗体 F5”
- 快捷键，按上键盘上的F5

应该注意的是，如果运行代码前鼠标光标没有在任一个VBA过程内部，VBE会弹出一个对话框，请求选择将要用于运行的VBA过程：
![img](https://images2015.cnblogs.com/blog/983631/201706/983631-20170613215143212-1447117125.png)

第一行代码`Debug.Print "Hello, World!"`会在位于VBE下方的“立即窗口”中输出双引号中的内容，即`Hello, World`：
![img](https://images2015.cnblogs.com/blog/983631/201706/983631-20170613214544212-1137843987.png)
（如果VBE中没有出现立即窗口，可以在菜单栏“视图”中选择显示）
第二行代码`MsgBox "Hello, World!"`则会以弹窗的形式显示双引号中的`Hello, World!`：
![img](https://images2015.cnblogs.com/blog/983631/201706/983631-20170613214651478-1397602573.png)

## 4. 保存

包含有VBA代码的Excel文件不能再保存为.xls或.xlsx文件，应保存为.xlsm文件
![img](https://images2015.cnblogs.com/blog/983631/201706/983631-20170613215430134-568607441.png)

------
# 一、数据类型

与其它的编程语言一样，VBA也有它自己的数据类型。讲到数据类型，就离不开“变量”与“常量”这两个概念，变量与常量，都是用于保存数据的。顾名思义，“变量”是会变的，即它的值是可以改变的；而常量，则它的值通常是固定不变的。
定义数据类型的优点有2个：

- 便于内存管理
- 便于程序进行计算（数值型）和其它使用（非数值型）
  VBA中的基本数据类型有多种，但常用到的只有其中一半左右。

## 0. 变量的定义

VBA中用

```
Dim 变量名 As 数据类型
```

的格式来定义基本变量。变量名可以是英文字母、数字、下划线的组合，但必须以字母开头。

变量的赋值则直接使用一个等号进行：

```
Dim i as Integer
i = 3
```

## 1. 字符串 String

字符串是用于保存文本数据的，字符串内容应放置于双引号内。
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171105190008904-1971889761.jpg)

## 2. 数字类型

VBA中用于表示数字的数据类型有4种：整型 Integer、长整型 Long、单精度浮点型 Single、双精度浮点型 Double。整型及长整型用于表示整数，单精度与双精度浮点型都用于表示小数。

整型与长整型的区别在于两者所能表示的数值范围不同：

- 整型数据能表示的数据范围：-32768 ~ 32767
- 长整型数据能表示的数据范围：-2147483648 ~ 2147483647

**而单精度浮点数与双精度浮点数除了在数值范围不同之外，两者所能表示的数据精度（即小数点后多少位）也是不同的**

- 单精度浮点型能表示的数据范围

在表示负数时： -3.402823E38 ~ -1.401298E-45
在表示正数时： 1.401298E-45 ~ 3.402823E38

- 双精度浮点型能表示的数据范围

在表示负数时： -1.79769313486231E308 ~ -4.94065645841247E-324
在表示正数时： 4.94065645841247E-324 ~ 1.79769313486231E308

反正一句话，它们可以表示非常大的数据，但要注意的时，单精度浮点型其精度是6，即只能保存**小数点后最多6位**的数据；双精度浮点型其精度是14，即只能保存**小数点后最多14位**的数据。如果超出以上长度，则超出部分会被去掉，并且会自动四舍五入。
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171105190211138-31224103.jpg)

如上图，可以看到，作为单精度浮点数的i1在输出时变成了5.123457。

其实如果试图在给变量赋值时直接写到小数点后15位，VBE是会自动检测到并且直接进行四舍五入只保留14位的。上图i2我本想赋值为5.123456789123456，但写完最后的6之后，它自动消失了并且前面的5变成了6。

因为在数字的表示范围上长整型大于整型，而双精度浮点型大于单精度浮点型，理论上来说，如果把变量定义为表示范围更大的数据类型可以更好地避免掉数据溢出的问题。但在定义变量时，应当遵守的一个原则就是：**够用就好。而不是越大越好**。所以通常如果整型和单精度浮点型就足以表示数据的话，应当使用它们而不是长整型和双精度浮点型。

## 3. 日期型 Date

日期型数据不仅可以表示日期，还可以表示时间。可以表示的日期范围是：100年1月1日 ~ 9999年12月31日；可以表示的时间范围是：0:00:00 ~ 23.59.59。

能被Excel识别到的日期，都可以赋值给日期型变量。因为Excel中有很多很多种日期格式，因此在这里无法一一举例。需要用到的时候，可以多尝试。

## 4. 布尔型 Boolean

布尔型数据用于表示逻辑值：真、假 。其中“真”为True，“假”为False。布尔值数据常用于条件判断语句。
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171105190256091-2101644670.jpg)

应当注意的是，当其它数据类型转换为布尔值时，0会转成False，其它值则变成True。当把布尔值转换成其他数据类型时，False会转换为0，True则是-1。

## 5. 变体型 Variant

变体型数据是一种特殊的数据类型，几乎可以用于保存所有其它数据类型的数据。可以简单地理解为：当不知道变量所要表示的数据是什么类型时，就把它定义为Variant（但这种操作应当**尽量避免**）。
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171105190339857-1289484012.jpg)

## 6. 对象型 Object

对象型是VBA中另一种特殊的数据类型。有点类似于其它高级编程语言中的“对象”，因为它们都有自己的属性与方法，但也仅限于在这两个方面上类似。

对象型数据会在下一章单独讲解

## 7. 枚举型

枚举型数据就我目前而言使用得并不多，几乎没有使用过。但它可以在某些特定场合下比较有用，因此在这里也提一下。

当一个变量只有几种可能的值时，可以定义为枚举类型。枚举就是将变量的值逐一列出，属于该枚举型的变量只能取列举的某一个值。

枚举型数据定义格式如下：

```
Public | Private Enum 变量名
    成员1 [= 常数表达式1]
    成员2 [= 常数表达式2]
    ……
End Enum
```

开头的“Public | Private”表示两者取其一的意思，Public和Private用于定义变量的作用域（即变量的有效范围，具体后面再讲）。用中括号[]括起来的部分是可以省略的，如果省略的话，则默认以0表示第一个成员，1表示第2个成员，以此类推。

最典型的就是用一个枚举型变量来表示星期：

```
Public Enum WorkDays
    星期日
    星期一
    星期二
    星期三
    星期四
    星期五
    星期六
End Enum
```

它的使用如下：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171105190433638-1911458751.jpg)

------

# 二、数组和字典

数组和字典也是VBA的常用到数据类型之一。但是我翻了有四五本VBA教程相关的书，里面都没有介绍到字典，数组到是在介绍数据类型时有介绍，而并没有提到字典。
事实上，字典不是VBA内置的类型，它是Windows脚本语言的。但其实字典在VBA中也是非常重要的，它非常适用于需要进行非重复性数据的操作。我多次需要使用到字典，但很少用到数组。

## 1. 数组

如果有学过其它编程语言，应该对数组不陌生。其实数组就是多个变量的集合，而且它是有顺序的，通过索引来访问一个数组中的各个值。
但应该记住的是：**数组中的各个数据，它们的类型必须是相同的。**

数组也应当要先声明，后使用。VBA中的数组有动态数组和静态数组之分。

### 1.1 静态数组

所谓静态数组，即它的长度是固定不可变的。
声明语法如下：

```
Dim 数组名(a to b) As 数据类型
```

其中a和b均为数字，表示数据的索引起始值。也可以只写一个数字，则此时数组使用默认索引，从**1**开始，数字表示它的长度。
例如：

```
Dim MyArray1(10) As String
Dim MyArray2(10 to 20) As String
```

### 1.2 动态数组

如果在定义时并不知道需要使用的数组的长度是多少，则可以定义它为动态数组：

```
Dim Myarr3() As String
```

可以看到，定义动态数组，只需要在括号里面留空就行了。
待确定了数组的长度之后，可以使用`ReDim`来重新定义数组

### 1.3 数组的赋值与取值

虽然都是通过索引来对数组进行赋值，但与其它主流的编程语言不同的是，VBA中不是使用中括号`[]`而是使用小括号`()`进行的。

```
Dim arr(5) As String

For i = 1 to 5
     '赋值
     arr(i) = i
Next

'取值
Debug.Print arr(1)
```

此外，也可以直接把Excel单元格中的数据赋值给数组。如上面定义的长度为5的一维数组，可以以以下方式把单元格中A1到A5的数据分别载入到数组arr中：

```
arr = Range("A1:A5")
```

### 1.4 多维数组

VBA中也支持多维数组。
如，定义一个四行五列的二维数组如下：

```
Dim Myarr(4, 5) as String
```

或者：

```
Dim Myarr(1 to 4, 5 to 8) As String
```

定义一个三维数组：

```
Dim Myarr(3, 4, 5) As String
```

或者：

```
Dim Myarr(1 to 3, 4 to 6, 7 to 9) As String
```

### 1.5 其它

网上有说到很多对数组的操作，例如求数组的上下界，对数组求和等，我觉得这些都不算是数组的特有操作，因为它对其它数据类型也是可以使用的，所以就不打算再介绍了。有需要用到的，可以再查询

## 2 字典

字典其实就是一些“键－值”对。在很多场合，它都能起到很大的作用，使用起来非常方便，有类似于微型数据库的作用，可用于临时保存一些数据信息。

### 2.1 创建字典

VBA中创建字典有两种方法：

> 1. 引用声明

在VBE菜单中，选择“工具－引用”，在弹出的对话框中选择中边的“浏览”，此时会打开Windows系统的驱动目录(C:\Windows\System32)，然后找到scrrun.dll，确定确定返回就行了。
添加引用后，就可以像定义其它变量一样声明使用字典了。

```
Dim d As New Dictionary
```

> 1. 直接创建

我目前基本上都使用的这种方法。可如下定义：

```
Dim d As Object
Set d = CreateObject("Scripting.Dictionary")
```

### 2.2 字典的方法

字典有6个方法，分别是Add、Exists、Keys、Items、Remove、RemoveAll。使用方法如下：

#### 2.2.1 Add

用于添加内容到字典中。

```
d.Add key, item
```

第一个参数为键，第二个参数为键对应的值

#### 2.2.2 Exists

用于判断指定的关键词是否存在于字典（的键）中。

```
d.Exists(key)
```

如果存在，返回True，否则返回False。通常会在向字典中添加条目的时候使用，即先判断字典中是否已存在这个记录，如果不存在则新增，否则进行其它的操作。

#### 2.2.3 Keys

获取字典所有的键，返回类型是数组。

```
d.Keys()
```

#### 2.2.4 Items

获取字典所有的值，返回类型是数组。

```
d.Items()
```

#### 2.2.5 Remove

从字典中移除一个条目，是通过键来指定的。

```
d.Remove(key)
```

如果指定的键不存在，会发生错误。

#### 2.2.6 RemoveAll

清空字典。

### 2.3 字典的属性

字典有4个属性：Count、Key、Item、ConpareMode

#### 2.3.1 Count

用于统计字典中键－值对的数量。也可以简单理解为统计字典中键的个数。

```
d.Count
```

#### 2.3.2 Key

用于更改字典中已有的键。如：

```
d.Key("apple") = "Orange" 
```

如果指定的键不存在，则会产生错误。

#### 2.3.3 Item

用于写入或读取字典中指定键的值，如果指定的键不存在，则会新增。如：

```
'读取
Debug.Print d.Item("apple")

'写入
d.Item("apple") = 10
```

#### 2.3.4 CompareMode

CompareMode用于设置或者读取字典对象中比较字符串时的比较模式。
这个到目前为止我还没有用过。所以不讨论了。

----------

# 三、流程控制1 - 条件选择

VBA中的流程控制分为两种，其一是条件结构式的，即根据条件判断的结果去选择性执行相应的语句（块）；另一种是循环，即循环地执行语句（块）。本节介绍第一种。

## 1. IF

if 语句其实包含有几种形式：

### ① If...Then...End If

其格式为：

```
If 逻辑表达式 Then
    ' 如果逻辑表达式为真，则执行这里的语句
End if
```

例如：

```
If 8 > 5 Then
    Debug.Print "Yes"
End If
```

输出结果为：Yes

### ② If...Then...Else...End If

其格式为：

```
If 逻辑表达式 Then
    ' 如果逻辑表达式为真，则执行这里的语句
Else
    ' 否则（即逻辑表达式为假），则执行这里的语句
End If
```

例如：

```
If 8 < 5 Then
    Debug.Print "Yes"
Else
    Debug.Print "No"
End If
```

输出结果为：No

### ③ If...Then...ElseIf...Then...End If

与第①种类似，只是它多了一层判断。当然，这种ElseIf是可以多层嵌套的。

其格式为：

```
If 逻辑表达式1 Then
    ' 逻辑表达式1为真，则执行这里的语句
ElseIf 逻辑表达式2 Then
    ' 逻辑表达式2为真，则执行这里的语句
Else
    ' 逻辑表达式1和2都返回了假，则执行这里的语句
End If
```

例如：

```
If 20 > 30 Then
    Debug.Print "Not Right"
ElseIf 20 < 30 Then
    Debug.Print "Right"
End If
```

输出结果为：Right

## 2. Select Case

Select Case语句在功能与If语句非常相似，只是组织形式略有不同。它有2种形式

### ① 不带Else的Select Case

格式为：

```
Select Case 测试表达式
    Case 表达式1
        ' 如果测试表达式等于表达式1，则执行这里的语句
    Case 表达式2
        ' 如果测试表达式等于表达式2，则执行这里的语句
    Case 表达式3
        ' 如果测试表达式等于表达式3，则执行这里的语句
    ' 后面可以有更多的Case
End Select
```

例如常见的判断成绩等级的过程，可以用Select Case进行判断：

```
Sub test()
    Dim score As Integer
    score = 94
    Select Case score
        Case Is >= 90
            Debug.Print "优"
        Case Is >= 80
            Debug.Print "良"
        Case Is >= 60
            Debug.Print "中"
        Case Is < 60
            Debug.Print "差"
    End Select
End Sub
```

此处有2点需要说明的，其一是**上例与前面讲的格式相比，在“Case 表达式” 中间有个Is。这个是用于表达式判断时候用的格式，这样才能够起到类似于If...Else的作用。上例中如果直接用Case 90的格式，则只会当score等于90的时候，才会输出“优”，起不到范围判断的作用。在使用的时候，需要根据实际情况按需选择。第二点是，在如上例中那样进行不同范围内的判断时，顺序很重要，如果把上例中的顺序倒过来，则程序会输出“中”，因为score=94时，Case Is >= 60这条语句成立，后面的就不会再执行了。**

### ② 带Else的Select Case

其实就是类似于If语句中的Else一样，当前面的所有情况都不符合时，就执行Else里面的语句。如上例中最后的`Case Is < 60`就可以改为`Case Else`：

```
Sub test2()
    Dim score As Integer
    score = 53
    Select Case score
        Case Is >= 90
            Debug.Print "优"
        Case Is >= 80
            Debug.Print "良"
        Case Is >= 60
            Debug.Print "中"
        Case Else
            Debug.Print "差"
    End Select
End Sub
```

** Select Case 番外篇 **

如果需要在Case条件中使用闭合区间，即同时出现大于且小于的情况，如上例中，大于60且小于70。则需要使用两个Is语句，中间用逗号隔开：

```VB
Sub test2()
    Dim score As Integer
    score = 53
    Select Case score
        Case Is >= 90
            Debug.Print "优"
        Case Is >= 80
            Debug.Print "良"
 Case Is >= 60, Is <= 70
            Debug.Print "中"
        Case Else
            Debug.Print "差"
    End Select
End Sub
```

--------

# 四、流程控制2 - 循环控制

所谓循环控制，即在循环执行一段代码，用于完成一些重复性任务。

VBA中的循环控制语句主要有3种：for、while、loop。对于大多数人来说，for的使用频率最高，而我个人也觉得for是最为灵活的，在很多场合下都可以使用，相较while和loop，其逻辑也再加清晰，更便于对循环进行控制。

## 1. For循环

for循环有两种形式，一种为明确地知道要循环的次数的，比如从1到10循环执行10次；另一种则用于循环历遍某个集合对象，这个对象有多少个单位就循环执行多少次，可以视为是未知循环次数的。

### ① 循环次数已知的for

其格式如下：

```
for 计数变量=开始值 to 结束值
    ' 用于循环执行的语句
next
```

通常我们会需要对Excel表格中的每一行（或每一列）进行循环历遍，对其中单元格的值进行各种操作。这时候，就可以先求出该工作表中有多少行（或列），然后再逐行（列）进行判断。这个操作单元格的示例会在后面有专门的章节来讲解演示，这里就只讲循环语句本身了。

示例如下：

```
For i = 1 To 10
    Debug.Print i
Next
```

以上代码将会依然输出1到10这10个数字

在这种形式的For里面，还可以在[结束值]后面加上Step，然后跟着写上步长，如果省略，则默认步长为1。像上例，默认是把i从1递增到10，即：1、2、3、4、5、6、7、8、9、10这样依次递增的。如果像下面这样写，把步长设置为2，则变成了：1、3、5、7、9。则循环体（即For里面的代码）只会执行5次。

```
For i = 1 To 10 Setp 2
    Debug.Print i
Next
```

输出：1 3 5 7 9

### ② 循环次数不确定的For

格式如下：

```
For Each 循环变量 In 循环对象
    ' 循环执行的语句
Next 循环变量
```

结合之前讲过的数组来举个例子。假设已有数组myArr并且已赋值，则可以用以下代码历遍它的每个值：

```
For Each a In myArr
    Debug.Print a
Next a
```

这样就不管这个数组包含有多少个元素，都可以逐个历遍。

## 2. Loop循环

Loop循环或许把它称为Do...Loop循环会更加合适。因为它的格式是先Do再Loop的。但我喜欢把它叫Loop循环。

Loop循环也有两种形式。第一种是先判断循环条件再开执行，第二种先执行一次然后再判断循环条件是否成立。

### ① 先判断再执行

格式如下：

```
Do [While | Until] 循环条件
    ' 用于循环执行的语句
Loop
```

这里的`[While | Until]`表示两者随便用一个都可以。`While`就是当条件成立的时候就执行，而`Until`就是直到条件成立时就停止执行。也就是说，While用于指定循环的条件，说明什么时候就执行循环，而Until用于指定停止循环的条件，说明什么时候不再执行循环。这么说可能有些许抽象，举例说明。

**Do While ... Loop**

```
Sub test()
    Dim i As Integer
    i = 5

    Do While i > 0
        Debug.Print i
        i = i - 1
    Loop
End Sub
```

上例的输出结果是：5 4 3 2 1

**Do Until ... Loop**

```
Sub test()
    Dim i As Integer
    i = 5

    Do Until i < 0
        Debug.Print i
        i = i - 1
    Loop
End Sub
```

上例的输出结果是：5 4 3 2 1 0

逻辑还是很简单的，大家可以稍微分析下为什么用While时输出到1就停止了，而用Until时后面会多出个0，而如果需要不让它输出0程序应该怎么改写。搞清楚原因，这个While和Until的区别也就知道了，Loop循环应该也就掌握了。

### ② 先执行再判断

先执行再判断，即将条件判断语句放到Loop的后面，先Do一次，然后再Loop While或者Loop Until。

格式如下：

```
Do
    ' 用于循环执行的语句
Loop [While | Until] 循环条件
```

示例如下：

**Do ... Loop While**

```
Sub test()
    Dim i As Integer
    i = 5

    Do
        Debug.Print i
        i = i - 1
    Loop While i > 0
End Sub
```

输出结果为：5 4 3 2 1

**Do ... Loop Until**

```
Sub test()
    Dim i As Integer
    i = 5

    Do
        Debug.Print i
        i = i - 1
    Loop Until i < 0
End Sub
```

输出结果为：5 4 3 2 1 0

Loop循环总结：可以看到，无论是采用哪种形式的Loop循环，都可以得到相同的结果，实现同样的功能，而具体使用哪一种，纯粹是个人习惯（个人喜欢）。但**无论使用哪一种，都一定一定要记得，必须要有能使循环结束的语句出现在循环中**（例如前面例子中的i=i-1），否则程序会永远不停地执行下去。这时候除了在任务管理器中结束Excel外，就没有什么更好的办法了。

## 3. While循环

While循环有点类似于Loop循环，先判断条件是否成立，再开始（或结束）循环。而我看过的教程里面，有些是没有讲到While循环的。可能是作者觉得没有必要讲了。

While循环的格式如下：

```
While 条件判断语句
    ' 用于循环执行的语句
Wend
```

示例如下：

```
Sub test()
    Dim i As Integer
    i = 5

    While i > 0
        Debug.Print i
        i = i - 1
    Wend
End Sub
```

输出结果为：5 4 3 2 1

同样要记住的是，**必须要有使循环能够结束的条件**，否则程序会进入死循环中。

***由以上示例可以看出，这3种循环语句，是有着某些共同之处的，特别是Loop和While循环。而事实上很多循环事件，用3者都能实现。具体使用哪一种，则或根据实际情况和使用习惯来选择。但相信对于大多数人来说，都会优先考虑使用For循环，因为它的条理更加清晰，流程控制更简易，适用的场景更广，语句也更简练（至少它不需要显式添加使循环结束的语句）***

-------------

# 五、Excel 对象操作

本章是本系列教程的重点。但我觉得应该不是难点。从第零章开始到学完本章，应该可以把VBA用于实战中了。

Excel对象主要有4个：

- 工作薄 Workbook
- 工作表 Worksheet
- 单元格区域 Range
- 单元格 Cell

这里我只讲后面3个，不讲工作薄。原因有2点：

第零章里面讲过，工作薄其实就是一个Excel文件。我不建议直接操作Excel文件。因为文件属性被更改的机率高。比如修改了文件名，或者文件被移动到其它地方去了，这样的话，写死的VBA代码就不管用了。这是主要原因
学完本章，或者后面的章节，应该可以自己寻找到如何操作Excel工作薄的方法
在多数情况下，如果需要操作多个工作薄中的数据，建议把这些工作薄里面的表复制到一个工作薄中进行操作。这样会方便很多。

## 1. 操作工作表

其实对工作表的操作，更多是对其引用。当一个工作薄中有多个工作表而需要用到不同表中的数据时，就需要分别引用不同的工作表。

引用工作表，有两种方式：通过表名引用、通过表顺序引用

### 1.1 按表名引用

顾名思义，表名引用即通过工作表的名字来引用相应的工作表。除了可以直接在Excel中看到工作表的名字外，也可以在VBE中左侧的工程视图里看到当前工作薄中包含有哪些工作表。
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171111144434309-1618131619.jpg)

如上图红框所示，括号里面的即为表名。在VBA中可如下分别引用这3个表：

```
Sub test()
    Dim sht_slea As Worksheet
    Dim sht_result As Worksheet
    Dim sht_para As Worksheet

    Set sht_slea = Worksheets("SLEA")
    Set sht_result = Worksheets("Check_Result")
    Set sht_para = Worksheets("Parameter")
End Sub
```

如上，用`Dim 变量名 As Worksheet`的格式来定义一个工作表对象。用`Set 变量名 ＝ Worksheets("表名")`的格式来把工作表对象赋值给指定的变量。然后就可以用这个变量来引用或操作对应工作表中的对象和数据了。

### 1.2 按表顺序引用

顺序引用，即按工作表出现在工作薄中的顺序从左到右，依次用1、2、3.……来引用。格式和以表名引用一样：

```
Sub test()
    Dim sht_slea As Worksheet
    Dim sht_result As Worksheet
    Dim sht_para As Worksheet

    Set sht_slea = Worksheets(2)
    Set sht_result = Worksheets(1)
    Set sht_para = Worksheets(3)
End Sub
```

这里要提出的是，在VBE工程窗口中看到的自上而下的表顺序并不是在VBA中引用的顺序。这个顺序是以工作表在工作薄中从左到右的顺序为准。因此上例代码是基于如下顺序的：

![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171111144540403-1305190265.jpg)

这也意味着，如果被人为地有意或无意地拖动这些表而改变了它们的顺序，那么以这种方式引用工作表将得不到预想中的结果

## 2. 操作单元格区域

单元格区域，即Range对象。应该是在Excel VBA中用得最多的对象。Range对象是Worksheet对象的一个子集。所以通常通过worksheet_object.Range()的方式来引用。

单元格区域，可以是单个单元格，也可以是多个连续的单元格和多个不连续的单元格。在使用单元格区域对象前，应该先进行变量定义。把变量定义为Range对象即可：

```
Dim rng As Range
```

本节使用下图数据为例进行代码演示：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171111144705622-103206137.jpg)

### 2.1 单个单元格区域的引用

在Excel中，每个单元格都是有其相应的地址的，或者叫做“名字”也可以。最常用到的，就是平时说的A1、B4、D10等。在VBA中，可以通过单元格的地址来引用单个单元格。

```
Sub test()
    Dim sht_slea As Worksheet
    Dim rng As Range

    Set sht_slea = Worksheets("SLEA")
    Set rng = sht_slea.Range("D2")

    Debug.Print rng
End Sub
```

输出：92257598
即D2单元格中的数据。这里可能会引起误会，特说明一下。仅在Range对象引用的是单个单元格时，才可以用`Debug.Print`或者`MsgBox`来输出Range对象中的内容。如果将接下来介绍的引用了多个单元格的Range对象使用`Debug.Print`或者`MsgBox`来输出，将会报错。

### 2.2 多个连续单元格区域的引用

这种引用则类似于用鼠标在工作表中选中特定区域（然后我们可以给这个区域加上边框，或者加上底色等操作），或者是在Excel函数中引用某个区域。如选中A1到D4，或者对D2到D4中的数值进行求和－SUM(D2:D4)。在VBA中也可以这样来引用。

```
Sub test()
    Dim sht_slea As Worksheet
    Dim rng As Range

    Set sht_slea = Worksheets("SLEA")
    Set rng = sht_slea.Range("A1:D4")

    rng.Interior.ColorIndex = 16
End Sub
```

如上代码中，先引用SLEA表，然后把这个表中A1到D4区赋值给rng对象。最后一行把这个区域标上灰底色。结果如下：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171111144843450-1842191464.jpg)

对于这样多行多列的单元格区域，通常只是用于设置其格式，很少会直接对其中每个单元格的数据进行操作的。更多的是对单行或单列中的数据进行操作。比如把上例中D1到D5的数据依次输出：

```
Sub test()
    Dim sht_slea As Worksheet
    Dim rng As Range

    Set sht_slea = Worksheets("SLEA")
    Set rng = sht_slea.Range("D2:D5")

    For Each Item In rng
        Debug.Print Item
    Next Item
End Sub
```

执行结果如下：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171111145047184-1824750525.jpg)

### 2.3 多个不连续单元格区域的引用

这种引用方式应该应用场景不多，我本人目前为止还没有在工作中使用过。

它的引用只需要在Range()函数中的参数里，在双引号中输入多个区域地址中间用逗号隔开即可。如以下代码可将B2到B5，D2到D5区域标上红色。

```
Sub test()
    Dim sht_slea As Worksheet
    Dim rng As Range

    Set sht_slea = Worksheets("SLEA")
    Set rng = sht_slea.Range("D2:D5, B2:B5")

    rng.Interior.ColorIndex = 3
End Sub
```

结果如下：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171111145126669-1418954380.jpg)

## 3. 操作单元格对象

单元格，即Cell。不过在VBA里面，这个Cell得加上个**s**，即`Cells`，然后在连带着的括号里面输入用数字表示的行号和列号，即可引用到单个单元格对象。Cells对象也是Worksheet对象的一个子集。通常通过`worksheet_object.Cells()`的方式来引用。

```
Sub test2()
    Dim sht_slea As Worksheet

    Set sht_slea = Worksheets("SLEA")
    Debug.Print sht_slea.Cells(1, 2)
End Sub
```

输出B1单元格（第1行，第2列）的内容：Subsector

所以Cells()的第1个参数是行号，第2个参数是列号。都用数字表示。在上例中，使用Cells和使用Range好像没什么区别，但是在进行数据处理时，我们经常需要动态地把数据读或写入一个单元格中，这时候，用数字表示位置的Cells对象，再结合For循环，操作起来就很方便了。

如以下代码可以把A1到D5中所有单元格的内容分别输出：

```
Sub test2()
    Dim sht_slea As Worksheet

    Set sht_slea = Worksheets("SLEA")

    For r = 1 To 5
        For c = 1 To 4
            Debug.Print sht_slea.Cells(r, c)
        Next
    Next
End Sub
```

简单来说，Range对象便于把单元格区域作为一个整体来引用或操作，而Cells对象则方便于对每一个单元格分别进行操作。

------

## 番外篇

### 1. 理解Range("B2:B4, D2:D4")和Range("B2:B4", "D2:D4")的区别

先看清楚，上面两种格式

- 一个是把两个区域放在一个双引号里面，用逗号隔开
- 另一个是把两个区域分别放在双引号里面，用逗号隔开

前者是分别引用B2:B4和D2:D4这两个区域，而后者则表示引用的是从B2:B4开始到D2:D4结束为止的这一整个连续的区域。所以后者其实是等价于Range("B2:D4")。

所以虽然使用后者的方式来使用Range也不会报错，但其实通常并不会这么使用

### 2. 结合Cells对象的Range

因为Cells对象接受数字来表示行和列，而在Excel中，如果有两个行列对，就可以表示一个单元格区域了。例如`Range("B2:D4")`也可以用`Range(Cells(2, 2), Cells(4, 4))`来表示。这种方式有时候很有用，如需要根据条件来判断区域的开始和结束位置时，它就派上用场了。

### 3. 父对象的省略

其实前面提到过的Worksheet对象，它是有父对象的。其父对象为Workbook对象，而Workbook对象的父对象是顶级对象Application。Range的父对象是Worksheet对象，Cells对象的父对象也是Worksheet对象。所以在给这些对象赋值时，标准的写法应当要把父对象给写上，如：

```
Sub test3()
    Dim sht_slea As Worksheet
    Dim titl_rng As Range
    Dim data_rng As Range

    Set sht_slea = Application.ThisWorkbook.Worksheets("SLEA")
    Set title_rng = sht_slea.Range("A1:D1")
    Set data_rng = sht_slea.Range(sht_slea.Cells(2, 1), sht_slea.Cells(4, 4))
End Sub
```

但是如果VBA中的代码涉及到的对象都位于一个工作表中，而这个工作表当前是激活状态，则这些父对象是可以省略的。默认就是当前（激活的）工作表。所以当SLEA工作表激活时，上述代码和下面的是等价的：

```
Sub test4()
    Dim sht_slea As Worksheet
    Dim titl_rng As Range
    Dim data_rng As Range

    Set sht_slea = Worksheets("SLEA")
    Set title_rng = sht_slea.Range("A1:D1")
    Set data_rng = sht_slea.Range(Cells(2, 1), Cells(4, 4))
End Sub
```

我个人建议（或者说是我个人习惯）在引用Range对象时，Worksheet对象不要省略

在单独引用Cells对象时，Worksheet对象也不要省略

具体哪里省略哪里不省略，就得看个人习惯和应用场景了。并没有什么固定的规律可循。

--------------

# 六、过程和函数

前面讲过，VBA代码有两种组织形式，一种就是过程（前面的示例中都在使用），另一种就是函数。其实过程和函数有很多相同之处，除了使用的关键字不同之外，还有不同的是：

- 函数有返回值，过程没有
- 函数可以在Excel表格中像一般的Excel函数那样使用，但过程不可以
- 过程可以指定给Excel表格中的按钮或者图片等对象，但是函数不可以
- 函数只能在被调用时执行，比如在过程中调用，或者在另一个函数中调用，或者在Excel表格中调用。但不能直接执行，而过程是可以的

虽然可以把所有代码都放在一个过程，或者一个函数里面，但是这样会使代码难以维护，特别是非常不便于其他人查看修改。而有时一些重复性的代码，也应该把它们独立出去，作为一个子过程或子函数来组织。使用子过程和子函数的目的，就是为了便于组织代码、便于维护。

## 1. 过程 Sub

过程以Sub开头，End Sub结束，过程中需要执行的代码放在中间。格式如下：

```
Sub 过程名()
    ' 需要在过程中执行的代码
End Sub
```

过程名后面的括号是必须的，过程可以带参数，就放置在括号里面，稍后会有说明。

过程示例：

```
Sub test()
    Debug.Print "Hello"
End Sub
```

## 2. 函数 Function

与过程类似，函数的格式如下：

```
Function 函数名(参数1, 参数2, ...) As 数据类型
    ' 需要在函数中执行的代码
    函数名 = 函数执行后的结果
End Function
```

函数名后面的括号是**必须**的，函数通常都带有参数，放置在括号里面。函数也可以不带参数，但这种情况下的函数通常没什么意义。

括号后面的 `As 数据类型` 虽然并不是必须的，但是我强烈建议加上。因为函数通常都需要有返回值，这个数据类型就表示着返回值的数据类型。

函数体最后通常都带有一条返回值语句，把函数执行的结果赋值给函数名，就可以在调用这个函数的地方得到这个函数的执行结果。这个返回结果的数据类型应该与函数名后面As的数据类型一致，否则可能会出错。

函数示例：

```
Function my_sum(n1 as Integer, n2 as Integer) As Integer
    Dim s As Integer
    s = n1 + n2
    my_sum = s
End Function
```

上例中定义了一个名为`my_sum`的函数，它接受两个类型为Integer的参数，并且返回值是Integer类型。在函数内部计算了这两个参数相加的和，并把这个和作为返回值赋给`my_sum`。可以通过调用这个函数，并传递相应的参数，获得它的返回值。

定义函数后，就可以在VBA中的过程，或者其它函数，或者在Excel表格中使用这个函数了。

在Excel表格中使用：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171112183358184-2051381656.jpg)

在VBA过程中使用：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171112183406122-1801961115.jpg)

再次强调，函数是不能直接执行的。上例中，如果试图把鼠标光标放在my_sum函数中，再按F8，或者点击工具栏中的执行按钮，都不会执行这个函数，而是会弹出一个运行宏的选择对话框，而在这个对话框中，是没有任何函数可供选择的，只有过程：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171112183432606-1257517281.jpg)

## 3. 过程和函数的调用

前面说过，过程和函数，都可以是被相互调用的。

### 3.1 过程的调用

格式1：

```
Call 过程名
```

格式2：

```
Call 过程名(参数1, 参数2, ...)
```

如果过程不带参数，那么可以使用第1种格式，过程名后面不需要括号

如果过程带有参数，则要按照参数的顺序依次把参数写到过程名后面的括号中。即使用第2种格式。

### 3.2 函数的调用

函数的调用在本章第2节中有示例。函数的调用通常会在调用时把其赋值给一个变量，以获取函数返回值。

```
Sub test()
    Dim s As Integer
    s = my_sum(5, 5)
    Debug.Print s
End Sub
Function my_sum(n1 As Integer, n2 As Integer) As Integer
    Dim s As Integer
    s = n1 + n2
    my_sum = s
End Function
```

这里应该提出的是，函数应当要有返回值的，因为如果不需要返回值，那就应该直接使用过程而不是函数了。

## 4. 参数的使用

参数和使用对于过程和函数都是一样的。所以本节的内容对过程和函数都适用。

参数可分为可选参数和必选参数两种。必选参数在调用（过程或函数时）必须加上，否则会报错。可选参数则可写可不写。默认是必选参数，可选参数在定义时用`Optional`关键字声明，并且可选参数必须放在参数列表的最后面。

### 4.1 必选参数

必选参数在定义时放置在过程或函数名后面的括号中，格式为：

```
参数名 As 参数类型
```

如：

```
Sub sub_test(s As String)
    Debug.Print s
End Sub
```

在调用时，必须传入参数：

```
Sub test()
    Call sub_test("hello")
End Sub
```

### 4.2 可选参数

格式与必选参数类似，只是在参数名前面用Optional声明参数是可选的。

```
Optional 参数名 As 参数类型
```

如：

```
Sub sub_test(Optional s As String)
    Debug.Print s
End Sub
```

在调用上述过程时，可以不传入参数，此时则不会输出任何东西，也不会报错，因为参数是可选的。如果传入了参数，则会输出这个参数。

可选参数还可以设置默认值，即如果在调用时不显式传入这个参数的话，那么就使用定义时所使用的值。如下过程：

```
Sub sub_test(Optional s As String = "Hello, World")
    Debug.Print s
End Sub
```

**带参数调用**：

```
Sub test()
    Call sub_test("I am a boy")
End Sub
```

输出：I am a boy

**不带参数调用**：

```
Sub test()
    Call sub_test
End Sub
```

输出：Hello, World

### 4.3 同时使用可选参数与必选参数

此时可选参数必须放在最后

```
Sub sub_test(var As Integer, Optional s As String = "Hello, World")
    Debug.Print var
    Debug.Print s
End Sub
```

**只传入必选参数**：

```
Sub test()
    Call sub_test(50)
End Sub
```

输出：

50

Hello, World

**同时传入可选参数与必选参数**：

```
Sub test()
   Call sub_test(50, "Hi, Meinv")
End Sub
```

输出：

50

Hi, Meinv

-------------------

# 七、注释、红按钮及错误处理

系统性的知识前面已经讲完，从本章开始，本系列教程涉及的将会是一些相对凌散的内容。

## 1. 注释

代码注释是一件利人利己的事，为了方便自己在代码需要更新修改时，依然能够快速地看懂自己完的每一行代码到底是什么意思有何用处，在关键点加上代码注释是很有必要的。如果要让别人也能看懂自己的代码，那么注释更是不可或缺的。

VBA中有两种注释的方法，其一是使用单引号，另一种是Rem。在单引号后面或Rem后面的所有内容，在程序运行时都不会被执行，它们就是注释的内容。通常注释的内容会被用其它颜色标识。如下：

![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171112185148716-350561153.jpg)

有点不方便的是，貌似VBA里面并没有提供多行注释的方法，要想把多行代码注释掉，只能在每一行前面都加上单引号或者Rem

在程序写得比较长的时候，就有必要在其中加入注释。注释通常应使用于：

- 逻辑复杂的地方
- 调用自定义的函数或过程
- 每个函数和过程的开头。描述本函数和过程的作用

还有其它一些作者认为应当写上注释的地方。每行都注释肯定是多余的，但不写注释，通常也是不对的。什么时候该写，什么时候不写，只有要自己用得多了之后，才会清楚。

## 2. 宏按钮

前面所讲的所有代码案例，都是在VBE里面执行的（无论是通过单击运行按钮还是快捷键F5），但这样明显是不方便的。一是每次都需要打开VBE甚至要定位到某一个过程中，才可以执行，二是如果不懂VBA操作的人，则完全不知该如何执行了。

在Excel菜单栏－开发工具中，找到“插入”，点击它可发现有“表单控件”和“ActiveX控件”，这里使用的是第一个，表单控件。它的第一个，就是按钮。点击一下这个按钮，则可以在Excel表格中通过鼠标的拖动来放置一个按钮对象。如果使用的是表单控件中的按钮，则在放开鼠标后，会马上弹出一个指定宏的对话框：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171112185258528-345795696.jpg)

当然这时也可以不指定，点取消即可。后面有需要时，通过右键单击这个按钮，选择指定宏也可以达到同样的效果。
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171112185330669-151836080.jpg)

其实理论上来说，VBA中的过程，或者说Excel中的宏，可以指定给Excel中的各种对象实体，比如插入的图片、各种形状，甚至在Excel中的生成的图表，都是可以作为宏执行的载体的。

我通常都通过插入形状来设置按钮，因为形状更加美观，可以设置它的各种格式，和Excel表格配合更为协调，不影响整体版面。用如下方式插入一个自己喜欢的形状：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171112185356653-1018178109.jpg)

在其中加入提示文字并设置好格式后，就可以右键单击它，选择“指定宏”来把相应的VBA过程赋给它：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171112185404606-1751860261.jpg)

*有兴趣的朋友，可以尝试学习使用ActiveX控件。通过鼠标拖放一个ActiveX控件后，右键单击它，查看代码，留意代码上方左右两个对象窗口，点击右边的下拉列表可以发现这个控件可以有很多事件，例如单击、双击、鼠标移入、鼠标移出、被选中时等*
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171112185541825-1981907028.jpg)

## 3. 错误处理

当代码执行出错时，VBA提供了两种处理的方式：

- On Error GoTo
- On Error Resume Next

### 3.1 On Error GoTo

表示如果自本行开始后面的代码执行出错，则跳至指定位置继续执行。如：

```
Sub te4()
    On Error GoTo con
    Debug.Print "a" + 3
    Debug.Print 8 - 5
con:
    Debug.Print "error occur"
End Sub
```

示例中第2行设置了`On Error GoTo con`，即当第3行出错时，就跳到`con`标记处，执行自`con`后的代码。标记后面应加上冒号（:）。第3行中，用双引号包含的a是一个字符串，而3是一个数字，直接将字符串与数字相加是会出错的，所以这里会触发错误，第4行不会被执行到，程序会直接跳转到`con`处，执行后面的Debug.Print语句。程序输出error occur。

注意，如果把第3行和第4行交换位置，则程序会先输出8－5的结果（即3），再输出error occur。

### 3.2 On Error Resume Next

表示如果代码执行出错，则从出错代码的下一行代码继续执行。如：

```
Sub te3()
    On Error Resume Next
    Debug.Print 5 + 3
    Debug.Print "a" + 3
    Debug.Print "error occur"
End Sub
```

代码第3行执行正常，第4行将出错，于是直接执行第5行。所以程序的输出是：

8

error occur

最后要提醒的是，以上提到的两种错误处理的方法应尽量尽量少用。因为有错误应该去解决它，而不是跳过它。

## 作业：

\1. 执行以下代码，观察输出结果，务必结合输出结果理解代码的每一行及它的逻辑。

```
Sub test()
    On Error GoTo con
    Debug.Print 3 + 3
    Debug.Print 5 * 5
    
con:
    Debug.Print "error occur"
    Debug.Print "Do you understand this all?"
End Sub
```

\2. 执行以下代码，观察执行结果，务必结合输出结果以错误提示理解代码的每一行及整个代码的逻辑。

```vb
Sub test1()
    
    On Error GoTo con
    On Error GoTo 0
    Debug.Print 3 + 3
    Debug.Print "c" - 3
    Debug.Print 5 + 5
con:
    Debug.Print "error occur"
    Debug.Print "Do you understand this all?"
End Sub
```

----------------

# 八、单元格边框

本文基于以下文件

<http://pan.baidu.com/s/1nvJtsu9>

（部分）内容预览：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171113223605031-1522299964.jpg)

## 1. 边框样式

```
Sub cell_format()
    Dim sht As Worksheet
    Dim rng As Range
    
    Set sht = Worksheets("Parameter")
    Set rng = sht.Range("B2:C20")
    
    ' 设置边框格式
    ' 这是常规的实线、细线，默认颜色为黑色
    rng.Borders.LineStyle = xlContinuous 
End Sub
```

结果如下：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171113223628812-1801024275.jpg)

这里的xlContinuous代表着一种默认的Excel边框风格，总共有以下几种：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171113223648249-1120697559.jpg)

除了直接用各种风格的名字外，也可以使用它对应的值来代替。有兴趣的杺尝试下不同的边框风格，看看效果。

## 2. 边框颜色

```
rng.Borders.ColorIndex = 3
```

或者

```
rng.Borders.Color = RGB(0, 255, 0)
```

前面一种是直接使用VBA内置的几种颜色值，后一种则可以直接使用RGB颜色，可以实现更高的个性化。VBA内置的颜色及对应的值如下：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171113223732452-705339730.jpg)

所以示例代码中，`ColorIndex = 3`，即表示使用的是红色。结果如下：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171113223757421-1187412726.jpg)

## 3. 边框宽度

（在做这一步之前，我把前面的边框给清掉了）

```
rng.Borders.Weight = xlThick
```

这里的`xlThick`也是一种内置的属性，共有以下几种：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171113223829249-1003978378.jpg)

同样的，也可以使用它们对应的值来代替。

结果如下
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171113223848015-1282454713.jpg)

## 4. 单边框

单边框，也就是只要一个方向有边框。只要在前面例子中的Borders后面加上括号，在括号内指定边就行了，其它设置不变：

```
rng.Borders(xlEdgeBottom).LineStyle = xlContinuous
```

结果如下：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171113223914468-970313849.jpg)

这里代码里面使用的`xlEdgeBottom`表示下边框的意思，但要提醒的是，这个“下边框”指的是整个单元格区域（B2:C20）的底部边框，而不是“这个区域内部所有单元格的底部边框”。如果为要内部所有单元格都加上底线，可以用：

```
rng.Borders(xlInsideHorizontal).LineStyle = xlContinuous
```

其它形式的边框，可以自己去摸索

## 5. 使用with语句

前面1、2、3节，在设置边框的样式、颜色、宽度时，都使用的`rng.Borders.xxx`的形式，其实VBA中支持`with`语句，可以省去一些重复的代码，更简洁高效。如可用以下代码同时设置表格样式、颜色、宽度：

```
    With rng.Borders
        .LineStyle = xlContinuous
        .ColorIndex = 3
        .Weight = xlThick
    End With
```

记得在对对象操作完后，要加上`End With`

--------------------------

# 九、操作工作簿

虽然我前面讲过，在VBA中操作工作薄并不是件明智的事，但有些时候，还是避免不了要这么做。绝大多数情况下，我们要做的是获取到某个工作薄对象，并以此来获得其中的工作表对象，然后再对工作表中的数据进行处理。而后面这一部分（操作工作表）在前面已经讲过了，所以本章只讲如何在VBA中打开、关闭工作薄，以及介绍两个常用的工作薄对象。在VBA中打开工作薄有两种方法：显式打开及隐式打开。

## 1. 显式打开

显式打开即跟我们手动双击打开一个Excel文件一样，可以看到工作薄的内容，可以在里面进行各种操作。代码及示例如下：

```
Sub workbook_operate()

    ' 定义工作薄对象
    Dim wbk As Workbook
    Dim fname As String
    
    fname = "E:/temp/test.xlsx"
    ' 根据工作薄文件路径打开工作薄
    Set wbk = Application.Workbooks.Open(Filename:=fname)
    MsgBox fname & "已打开"
    ' 关闭工作薄
    wbk.Close
End Sub
```

结果如下：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171114184430843-2016810717.jpg)

然后就可以根据这个`workbook`对象（示例中的wbk变量）来获取到其中的工作表：

```
Set parameter_sht = wbk.Worksheets("Parameter")
```

或者取得这个工作薄的一些属性：

```
wbk.Name
```

最后可以使用`Close`方法来关闭这个工作薄：

```
wbk.Close
```

## 2. 隐式打开

使用隐式打开的时候，这个工作薄在前端是看不到的，只有在VBE环境中（的工程窗口）可以看到它。示例代码及结果如下：

```
Sub workbook_operate()

    ' 定义工作薄对象
    Dim wbk As Workbook
    Dim fname As String
    
    fname = "E:/temp/test.xlsx"
    ' 根据工作薄文件路径获取工作薄对象
    Set wbk = GetObject(fname)
    Debug.Print wbk.Name

End Sub
```

与显式打开不同的是，这是使用了`GetObject`方法。结果如下：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171114184548265-531802968.jpg)

可以在VBE的工程窗口（上图左侧）中看到有一个新的工项目被加载了。但此时是并不能看到有一个Excel文件被打开的，这个已经被VBA隐式打开的文件，只能使用VBA进行操作，在使用上与显式打开后得到的workbook对象没什么区别，如获取其中的工作表对象、获取它的名字、关闭它等，都是一样的。这里就不举例了。

## 3. 关闭

无论是显式打开还是隐式打开，如果在新打开的工作薄中有更改，在使用workbook.Close方法时，会有个弹窗提示是否要保存更改，就跟我们平时使用时一样：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171114184621702-1638586858.jpg)

又或者被打开的工作薄里面有些公式，在打开这个工作薄时公式结果变了（其实也等于是有更新了），在关闭时都会出现这个提示。如果可以确定是否需要更改，可以在保存时加入相应的参数`SaveChanges`：

```
wbk.Close SaveChanges:=False
```

`SaveChanges`等于`False`时即为不保存，等于`True`时即为保存

## 4. ThisWorkbook与ActiveWorkbook

`ThisWorkbook`对象是VBA顶级对象`Application`下的一个特殊对象，代表当前（VBA代码所在的）工作薄对象。

`ActiveWorkbook`对象也是VBA顶级对象`Application`下的一个特殊对象，代表当前激活的工作薄对象。

就跟我们在使用QQ聊天时一样，我们不可能同时单独地发信息给两个不同的QQ好友，只能有一个聊天窗口是被激活的。Excel也一样，当前被激活的工作薄就是ActiveWorkbook对象（同样类似地，也有ActiveWorksheet对象）。

有些情况下，`ThisWorkbook`等同于`ActiveWorkbook`，但有些情况下并不是的。如以下代码：

```
Sub workbook_operate()

    ' 定义工作薄对象
    Dim wbk As Workbook
    Dim fname As String
    
    fname = "E:/temp/ActiveMe.xlsx"
    ' 根据工作薄文件路径获取工作薄对象
    Set wbk = Workbooks.Open(fname)
  
    Debug.Print ThisWorkbook.Name
    Debug.Print ActiveWorkbook.Name
End Sub
```

运行结果：
![img](https://images2017.cnblogs.com/blog/983631/201711/983631-20171114184842343-1853989644.jpg)

因为新打开的工作薄会被处于激活状态（就跟手工打开另一个Excel文件一样），所以这时新打开的ActiveMe.xlsx才是ActiveWorkbook，而当前代码所在的工作薄（test.xlsm）则是ThisWorkbook。而如果只有一个工作薄被打开或者是当前代码所在工作薄处于激活状态时，则ThisWorkbook和ActiveWorkbook是同一个对象。

----------------------------