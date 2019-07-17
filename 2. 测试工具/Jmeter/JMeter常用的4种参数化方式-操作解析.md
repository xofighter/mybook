##  [JM_05]JMeter常用的4种参数化方式-操作解析



**目录结构**

```
一、JMeter参数化简介
    1.JMeter参数化的概念
    2.JMeter参数化方式之使用场景对比
二、JMeter参数化的4种主要方式-操作演练
    1.User Parameters（用户参数）
    2.CSV Data Set Config（CSV数据配置）
        Configure the CSV Data Source配置项&功能
    3.User Defined Variables（用户自定义变量）
    4.Function Helper中的函数
```

------



### 一、JMeter参数化简介

#### 1.JMeter参数化的概念

当使用JMeter进行测试时，测试数据的准备是一项重要的工作。若要求每次迭代的数据不一样时，则需进行参数化，然后从参数化的文件中来读取测试数据。

> **参数化**：是自动化测试脚本的一种常用技巧，可将脚本中的某些输入使用参数来代替，如登录时利用GET/POST请求方式传递参数的场景，在脚本运行时指定参数的取值范围和规则。
> 脚本在运行时，根据需要选取不同的参数值作为输入，该方式称为**数据驱动测试**（Data Driven Test），而参数的取值范围被称为**数据池**（Data Pool）。

#### 2.JMeter参数化方式之使用场景对比

|      | 参数化方式              | 使用场景                                                     |
| :--- | :---------------------- | :----------------------------------------------------------- |
| 1    | User Parameters         | 适用于参数取值范围很小的时候使用                             |
| 2    | CSV Data Set Config     | 适用于参数取值范围较大的时候使用，该方法具有更大的灵活性     |
| 3    | User Defined Variables  | 一般用于Test Plan中不需要随请求迭代的参数设置，如：Host、Port Number |
| 4    | Function Helper中的函数 | 可作为其他参数化方式的补充项，如：随机数生成的函数${__Random(,,)} |



### 二、JMeter参数化的4种主要方式-操作演练

#### 1.User Parameters（用户参数）

操作路径：HTTP取样器-->Add-->Pre Processors-->User Parameters

**1）添加User Parameters功能模块**



![img](https://upload-images.jianshu.io/upload_images/4866277-39f9ebf394617682.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/672/format/webp)

**2）设置参数项、参数值**



![img](https://upload-images.jianshu.io/upload_images/4866277-a72e7d08d0054baa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/927/format/webp)

**3）配置HTTP取样器界面的请求参数**



![img](https://upload-images.jianshu.io/upload_images/4866277-e114f302f39755a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/972/format/webp)

**4）配置Thread Group**
设置线程数=2，即2个虚拟用户数，对应User Parameters中设置的2个用户



![img](https://upload-images.jianshu.io/upload_images/4866277-6819521fda413871.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/546/format/webp)

**5）添加View Results Tree监控执行过程**
执行Run完成之后的效果：



![img](https://upload-images.jianshu.io/upload_images/4866277-d3d0eb2d8b6971bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/611/format/webp)



#### 2.CSV Data Set Config（CSV数据配置）

操作路径：HTTP取样器-->Add-->Config Element-->CSV Data Set Config

**1）添加CSV Data Set Config模块**



![img](https://upload-images.jianshu.io/upload_images/4866277-a6283217226a0704.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/671/format/webp)

**2）预先准备好要参数化的所有参数值**



![img](https://upload-images.jianshu.io/upload_images/4866277-689583f8658bd6e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/214/format/webp)

users.txt

**3）配置CSV Data来源**



![img](https://upload-images.jianshu.io/upload_images/4866277-28f4906b5d7fbcb4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/764/format/webp)

**Configure the CSV Data Source配置项&功能**：

| 配置项              | 取值or选择项                                                 |
| :------------------ | :----------------------------------------------------------- |
| Filename            | 参数化文件的读取位置，即保存参数化数据的文件目录。可为绝对路径，也可为相对路径。在分布式测试中，还是利用相对路径比较方便，因为有的机器可能安装路径不一样，同时可避免脚本迁移时需要修改路径 |
| File Encoding       | 编码格式，选择utf-8                                          |
| Variable Names      | 变量名称。这里定义的变量名称，后面就可以直接用来引用了。（多个变量名称以逗号隔开，例如username,passwd。参数化文件中同样有对应的两列数据。） |
| Ignore first line   | 忽略第一行数据（类似LR中第一行数据是变量名称，如果你的配置文件中为了记忆第一行也是变量名，可以选择是忽略该行数据） |
| Delimiter           | Variable Names中的参数分隔符，默认为英文逗号                 |
| Allow quoted data?  | 是否允许引用数据，默认false。选项为“true”时对全角字符的处理可能会出现乱码 |
| Recycle on EOF?     | 是否循环读取参数文件内容；因为CSV Data Set Config一次读入一行，分割后存入若干变量中交给一个线程，如果线程数超过文本的记录行数，那么可以选择从头再次读入 |
| Stop thread on EOF? | 当Recycle on EOF为False时（读取文件到结尾），停止进程，当Recycle on EOF为True时，此项无意义 |
| Sharing mode        | 共享模式，即参数文件的作用域：All Threads;Current Thread Group;Current Thread |

**4）设置HTTP取样器的请求参数**



![img](https://upload-images.jianshu.io/upload_images/4866277-139d57b71a15f9da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/749/format/webp)

**5）设置Thread Group的线程执行的总次数**



![img](https://upload-images.jianshu.io/upload_images/4866277-8c1edd1b37437fb1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/640/format/webp)

**6）执行Run，查看效果**



![img](https://upload-images.jianshu.io/upload_images/4866277-f4c1cdc39b8d9449.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/607/format/webp)



#### 3.User Defined Variables（用户自定义变量）

操作路径：Thread Group-->Add-->Config Element-->User Defined Variables

**1）添加User Defined Variables模块**



![img](https://upload-images.jianshu.io/upload_images/4866277-d3e817e28522106a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/637/format/webp)

**2）配置User Defined Variables界面中的参数及其值**



![img](https://upload-images.jianshu.io/upload_images/4866277-501d595656ad82a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/748/format/webp)

**3）配置HTTP取样器中的请求参数**
此时可以联合其他参数化方式（如：User Parameters）一起检测效果



![img](https://upload-images.jianshu.io/upload_images/4866277-37a9b7b5cc4de190.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/582/format/webp)

**4）执行Run，观察效果**



![img](https://upload-images.jianshu.io/upload_images/4866277-8022af9a4f5db2a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700/format/webp)





#### 4.Function Helper中的函数

操作路径：Options-->Function Helper Dialog

**1）选择参数化所需的函数，配置函数**



![img](https://upload-images.jianshu.io/upload_images/4866277-ab5a5f013f0a1c6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/706/format/webp)

**2）配置HTTP取样器的请求参数**



![img](https://upload-images.jianshu.io/upload_images/4866277-7930404fcc637ef7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/829/format/webp)

**3）配置Thread Group**



![img](https://upload-images.jianshu.io/upload_images/4866277-7069d52acde1d5a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/716/format/webp)

**4）执行Run，查看效果**


  ![img](https://upload-images.jianshu.io/upload_images/4866277-5833190157af7029.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/485/format/webp)





---------

## Jmeter进阶--参数化

前言

​       参数化是自动化测试脚本的一种常用技巧。简单来说，参数化的一般用法就是将脚本中的**某些输入**使用参数来代替，比如登录传参、post、delete、put等请求传参，在脚本运行时指定参数的取值范围和规则；

​       这样，脚本在运行时就可以根据需要选取不同的参数值作为输入。这种方式通常被称为数据驱动测试（Data Driven Test），参数的取值范围被称为数据池（Data Pool）。

jmeter的test plan中，支持如下**4种参数化方式**：

**CSV Data Set Config：CSV数据控件（常用）**

**函数助手：_CSVRead**

**User Defined Variables：用户定义的变量**

**User Variables：用户参数**



本文列举post传参请求的参数化，系统：mac，jmeter版本：3.3

首先新建一个测试脚本，可以自己手动编写（或者通过工具（badboy）录制），推荐手动编写

界面如下：





![img](https:////upload-images.jianshu.io/upload_images/8032759-59b14d5153782722.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/706/format/webp)



这里可以对参数id、name进行参数化，将用户名密码写入txt文档，**保存为.dat格式，编码类型选择UTF-8**；

因为配置元件——CSV Data Set Config对参数化的格式要求比较严格，用户名密码一一对应，之间用**半角英文逗号隔开**





![img](https:////upload-images.jianshu.io/upload_images/8032759-4af27acedb36b7f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/261/format/webp)



然后将保存的.dat文件放入计算机中，这里我放入路径为：/Users/xxxxxx/apache-jmeter-3.3/test/data

下面具体介绍参数化常用的的四种方法：

**一、配置元件——CSV Data Set Config**

点击线程组→配置元件→ CSV Data Set Config：



![img](https:////upload-images.jianshu.io/upload_images/8032759-c6fd02c32fdf5cb1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/911/format/webp)



**说明：**

**Filename：**.dat文件名，保存参数化数据的文件目录，可选择相对或者绝对路径（建议填写相对路径，避免脚本迁移时需要修改路径）;

**File encoding:**UTF-8，.dat文件的编码格式，在保存时保存编码格式为UTF-8即可;

**Variable Names**(comma-delimited)：对对应参数文件每列的变量名，类似excel文件的文件头，起到标示作用，同时也是后续引用的标识符，建议采用有意义的英文标示;

(如：有几列参数，在这里面就写几个参数名称，每个名称中间用分隔符分割，这里的 user,pwd，可以被利用变量名来引用：user,user,{pwd};

**Delimitet：**参数文件分隔符，用来在“Variable Names”中分隔参数，与参数文件中的分隔符保持一致即可;

**Allow quote data：**是否允许引用数据，默认false，选项选为“true”的时候对全角字符的处理出现乱码 ;

**Recycle on EOF？：**是否循环读取参数文件内容；因为CSV Data Set Config一次读入一行，分割后存入若干变量中交给一个线程，如果线程数超过文本的记录行数，那么可以选择从头再次读入;

△ Ture：为true时，当已读取完参数文件内的测试用例数据，还需继续获取用例数据时，此时会循环读取参数文件数据（即：读取文件到结尾时，再重头读取文件）;

**△**False：为false时，若已至文件末尾，则不再继续读取测试数据；通常在“线程组线程数* 线程组循环次数＞参数文件行数”时，选用false（即：读取文件到结尾时，停止读取文件）;

**Stop thread on EOF?：**当Recycle on EOF为False时（读取文件到结尾），停止进程，当Recycle on EOF为True时，此项无意义;

**△**若为ture，则在读取到参数文件行末尾时，终止参数文件读取线程；

**△**若为false，此时线程继续读取，但会请求错误，因此时读取的数据为EOF;

**Sharing mode:**共享模式，即参数文件的作用域，有以下几种方式：

**△**All threads:当前测试计划中的所有线程中的所有的线程都有效，默认;

**△**Current thread group:当前线程组中的线程有效;

**△**Current thread:当前线程有效;

完成之后，将刚才生成的参数写入参数对应的值里面：





![img](https:////upload-images.jianshu.io/upload_images/8032759-e317297a38bbeb50.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/887/format/webp)



设置线程组循环次数：



![img](https:////upload-images.jianshu.io/upload_images/8032759-e0a93bf8878a381c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/887/format/webp)



运行，可以看到每次运行依次往下取值：



![img](https:////upload-images.jianshu.io/upload_images/8032759-62e26b88763eff39.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/823/format/webp)



**二、函数助手：_CSVRead（参数化功能较弱）**

点击jmeter的界面，功能栏选项→ 函数助手对话框→ _CSVRead





![img](https:////upload-images.jianshu.io/upload_images/8032759-6345643df4a129ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/591/format/webp)





![img](https:////upload-images.jianshu.io/upload_images/8032759-7caae3a27e686db0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/653/format/webp)



**CSV file to get values from | \*alias**：CSV文件取值路径，这里写入参数化文档存放路径

**CSV文件列号| next|\*alias**：文件起始列号：CSV文件列号是从0开始的，第一列为0，第二列为1，以此类推。。。

**函数字符串**：即生成的参数化后的参数，可以直接在登陆请求中的参数中引用，第一列为用户名，函数字段号为0，第二列为密码，函数字段号为1，以此类推进行修改使用即可



![img](https:////upload-images.jianshu.io/upload_images/8032759-35a0714bf216e6ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



设置线程数，注意：这里如果仍按方法一里设置循环次数，执行时始终只取第一个数据，所以要设置线程数





![img](https:////upload-images.jianshu.io/upload_images/8032759-ea9d2d8232293161.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)



执行脚本，察看结果树，可以看到请求的参数都是参数化后的数据







![img](https:////upload-images.jianshu.io/upload_images/8032759-f2c357681b6e6025.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/678/format/webp)





**三、配置元件——User Defined Variables**

点击线程组添加配置元件→ User Defined Variables（用户定义的变量）：



![img](https:////upload-images.jianshu.io/upload_images/8032759-942577a6480cf1ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/882/format/webp)



如上图所示，在该参数组中已经定义了两个参数，通过界面下方的添加、删除按钮可以向参数列表增加和删除参数，Up和Down可以上下移动参数的位置；

值可以直接输入，也可通过函数__CSVRead从文件中读取，**还可以通过前缀加随机数和方法获取**。

比如用户名为user_0到user_100的用户，那么用户名可设置名user_${__Random(0,100,)}



![img](https:////upload-images.jianshu.io/upload_images/8032759-297b83c243bb015f?imageMogr2/auto-orient/strip%7CimageView2/2/w/591/format/webp)



**但是这种每次执行的多个线程所替换的参数一样，因为先获取到随机参数，再执行多个HTTP请求。**

**PS：**User Defined Variables中定义的参数值在test plan执行过程中不能发生取值的改变，因此一般仅将test plan中不需要随迭代发生改变的参数（只取一次的参数）

​    设置在此处；例如：被测应用的host和port值。



**四、前置处理器——User Variables**

点击线程组添加前置处理器——User Variables（用户参数）：



![img](https:////upload-images.jianshu.io/upload_images/8032759-3d9502e3124e508b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/873/format/webp)



如上图所示，在该参数组中已经设置了两个参数，username和password分别有2组不同的取值，通过页面下方的四个按钮，可以增加删除参数的可能取值。

PS：User Variables中设置的参数可以在test plan执行过程中发生变化。



以上就是jmeter参数化的四种方式，其中：

1、函数助手_CSVRead的参数化功能相比CSV Data Set Config较弱；

2、CSV Data Set Config适用于参数取值范围较大的时候使用，该方法具有更大的灵活性；

3、User Defined Variables一般用于test plan中不需要随请求迭代的参数设置；

4、User Variables适用于参数取值范围很小的时候使用；



**PS：**相比于loadrunner来说，jmeter参数化有以下不同：

1.jmeter参数文件第一行没有列名称

2.参数文件的编码，尽量保存为UTF-8（编码问题在使用CSV Data Set Config参数化时要求的比较严格）

3.Jmeter的参数化没有LoadRunner做的出色，它是依赖于线程设置的（只有CSV Data Set Config参数化方法才有）

----------

# [JMeter学习（四）参数化](https://www.cnblogs.com/yangxia-test/p/3966154.html)



JMeter也有像LR中的参数化，本篇就来介绍下JMeter的参数化如何去实现。

 

**参数化**：录制脚本中有登录操作，需要输入用户名和密码，假如系统不允许相同的用户名和密码同时登录，或者想更好的模拟多个用户来登录系统。

这个时候就需要对用户名和密码进行参数化，使每个虚拟用户都使用不同的用户名和密码进行访问。

 

**一、准备脚本，测试数据**

1、录制一个脚本（可以用badboy工具录制），在jmeter中打开，找到有用户名和密码的页面。如下：

![img](https://images0.cnblogs.com/blog/442008/201409/111105490122721.jpg)

2、我们需要“参数化”的数据，用记事本写了五个用户名和密码，保存为.dat格式的文件，编码问题在使用CSV Data Set Config参数化时要求的比较严格，**记事本另存为修改编码UTF-8.**  注意用户名和密码是一一对应的，中间用户逗号（，）隔开。

![img](https://images0.cnblogs.com/blog/442008/201409/111107235743665.jpg)

我将这个文件放在了我的（ C:\JmeterWorkSpace\t.dat  ）路径下。

 

**二、参数化**

　　这里介绍两种参数化的方式：函数助手，CSV Data Set Config。

 

**1、借助函数助手的方式**

a、点击菜单栏“选项”---->函数助手对话框，看下图:  CSV文件列号是从0开始的，第一列0、第二列1、第三列2、依次类推。。

**![img](https://images0.cnblogs.com/blog/442008/201409/111149020903722.jpg)**

 

 

b、复制生成的参数化函数，打开登陆请求页面，在右则的参数化中找到我们要参数化的字段，这里对用户名和密码做参数化，第一列是用户名，列号为0；第二列是密码，列号为1；修改函数中对应的参数化字段列号就可以啦。

![img](https://images0.cnblogs.com/blog/442008/201409/111345098715325.jpg)

好了，现在我们的参数化设置完成，在脚本的时候，会调用我们C:\JmeterWorkSpace盘下面的t.dat文件，第一列是用户，第二列是密码。

 

**2、借助jmeter中的配置元件（CSV Data Set Config）**

 a、选中线程组，点击右键，添加－配置元件－CSV Data Set Config

![img](https://images0.cnblogs.com/blog/442008/201409/111354070903569.jpg)

说明：

Filename --- 参数项文件
File Encoding --- 文件的编码，设置为UTF-8
Vaiable Names --- 文件中各列所表示的参数项；各参数项之间利用逗号分隔；参数项的名称应该与HTTP Request中的参数项一致。
Delimiter --- 如文件中使用的是逗号分隔，则填写逗号；如使用的是TAB，则填写\t；(如果此文本文件为CSV格式的，默认用英文逗号分隔)

Recycle on EOF? --- True=当读取文件到结尾时，再重头读取文件
                    False=当读取文件到结尾时，停止读取文件
Stop thread on EOF? --- 当Recycle on EOF为False时，当读取文件到结尾时，停止进程，当Recycle on EOF为True时，此项无意义

 

备注说明：这里我用通俗的语言大概讲一下Recycle on EOF与Stop thread on EOF结果的关联

Recycle on EOF ：到了文件尾处，是否循环读取参数，选项：true和false

Stop thread on EOF：到了文件尾处，是否停止线程，选项：true和false

当Recycle on EOF 选择true时，Stop thread on EOF选择true和false无任何意义，通俗的讲，在前面控制了不停的循环读取，后面再来让stop或run没有任何意义

当Recycle on EOF 选择flase时，Stop thread on EOF选择true，线程4个，参数3个，那么只会请求3次

当Recycle on EOF 选择flase时，Stop thread on EOF选择flase，线程4个，参数3个，那么会请求4次，但第4次没有参数可取，不让循环，所以第4次请求错误

 

事例下载：[demo](http://files.cnblogs.com/yangxia-test/csvdataconfig.zip)

 

b、使用刚才定义好的变量

![img](https://images0.cnblogs.com/blog/442008/201409/111356232625166.jpg)

至此，两种参数化的方法就介绍完了。

需要说明一下：函数助手方法要比CSV控件方法参数化功能要弱，推荐使用CSV控件方法。

 

 

再看看与loadrunner参数化不一样的：

1、 jmeter参数文件的第一行没有列名称

2、 这里要注意的是参数文件的编码，可以使用记事本另存为就可以修改该编码（编码问题在使用CSV Data Set Config参数化时要求的比较严格）

3、 Jmeter的参数化设置没有LoadRunner做的出色，它是依赖于线程设置的（只有CSV Data Set Config参数化方法才有）