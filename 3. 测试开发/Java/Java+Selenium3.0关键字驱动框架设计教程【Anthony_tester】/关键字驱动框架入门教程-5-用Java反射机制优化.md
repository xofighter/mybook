[TOC]

# 优化关键字匹配
我们在前面几章中发现，如果要运行任何操作，需要利用Apache POI去Excel表中获取关键字这列数据，并和代码中相关静态方法去匹配。到目前为止，我们写了6个关键字动作，结果在if-else-if-else中我们需要写好多个判断分支；试想一下，如果按照这个思路，一个项目的自动化，有几百个测试用例，其中一个测试用例有好几个测试场景，那么我们的动作关键字驱动会写几千甚至上万个，难道也需要写上万个if判断吗？这样显然不合理，效率太低，代码可维护太差（不够优雅 ^_^）。

## 使用Java反射类
在Java中利用反射类是可以解决上面提到的问题。**反射**是一种非常有用的方法，可以在运行时处理Java类，因为它可以用来装载Java类、调用它的方法或者在运行时分析类。（在这里，其实我也不了解什么是Java反射机制）这里我们先不管，按照下面代码进行敲一遍，然后，自己再去百度或者谷歌搜索下java反射机制。
让我来给你用其他方式来解释为什么要用到反射，这样你才能更好第理解。在我们当前的关键字驱动框架中，我们需要在if-else代码中去写上各种动作的关键字代码，让程序去循环判断并执行。前面我们提到，为了避免这个情形，我们需要采用Java的反射机制。反射的作用场景就是这样，当你添加了新的关键字方法代码，反射类会在运行时间加载所有的方式或者动作关键字的类。（我额外添加的，不知道这么去解释是否适合：这个可以利用我们POM中的页面工厂类的初始化作用去理解，只有我们初始化了页面，就自动化初始化了页面的元素和所有页面相关方法。）
ActionsKeywords.java代码不变和前面文章保持一致。调用selenium的driverScript.java的代码我们修改如下

```java
package executionEngine;
 
import config.ActionsKeywords;
import utility.ExcelUtils;
import java.lang.reflect.Method;
 
 
/**
 * create by Anthony on 2018/1/30
 */
public class DriverScript {
 
    // 声明一个public static的类对象，所以我们可以在main方法范围之外去使用。
    public static ActionsKeywords actionKeywords;
    public static String sActionKeyword;
    // 下面是返回类型是方法，这里用到反射类
    public static Method method[];
 
 
    // 这里我们初始化'ActionsKeywords'类的一个对象
    public DriverScript() throws NoSuchMethodException, SecurityException, ClassNotFoundException {
        //actionKeywords = new ActionsKeywords()
        // 原文作者是采用上面这个代码，下面的代码是我查找反射资料，是这么获取class对象的
        Class<?> actionKeywords = Class.forName("config.ActionsKeywords");
        method = actionKeywords.getMethods();
    }
 
    public static void main(String[] args) throws Exception {
        // 由于上面初始化反射类写在构造函数，而main方法是第一个被最新，如果不添加
        // 下面这个当前类的初始化代码，就会报method.length空指针异常，我这里暂时这里处理
        // 原文作者不知道为啥没有报错。
        DriverScript ds = new DriverScript();
        String excel_path = "C:\\Users\\Administrator\\Desktop\\SeleniumKeywordDriverFramework\\src\\dataEngine\\dataEngine.xlsx";
        // 加载读取excel文件
        ExcelUtils.setExcelFile(excel_path, "Test Steps");
 
        for (int iRow = 1; iRow <= 6; iRow++) {
            //3表示excel中keyword对应的列的索引，也就是左边数起第4列
            sActionKeyword = ExcelUtils.getCellData(iRow, 3);
            //调用'execute_Actions'方法
            execute_Actions();
        }
 
    }
 
    private static void execute_Actions() throws Exception {
        //循环遍历每一个关键字驱动方法（在actionskeywords.java中）
        // 下面method.length表示方法个数，method变量表示任何一个关键字方法，例如openBrowser()
        for(int i = 0;i < method.length;i++){
            //开始对比代码中关键字方法名称和excel中关键字这列值是否匹配
            if(method[i].getName().equals(sActionKeyword)){
                //一但匹配到相关关键字方法，就会调用对应的关键字静态方法
                method[i].invoke(actionKeywords);
                //一旦任何关键字被执行，利用break语句去跳出for循环。
                break;
            }
        }
    }
 
}
```
上面原作者采用new的方式去得到反射类的对象，我改成了forName，但是在main方法第一行还是需要添加创建一个当前driverScript类的对象，否则由于代码执行顺序，先执行main方法，然后执行构造方法，这样由于actionKeywords没有初始化，所以声明的类对象method得到是null,从而method.length报空指针错误。
在使用了Java反射机制之后，上面的代码要比前面一篇那么多if-else if要简洁，好看多了。这样就不用写很多判断分支语句。下面一篇介绍如何把一些常量放在一个类文件，这样就不用在代码中写死，也就是所谓硬编码问题。