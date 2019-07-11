# BeanShell 入门

2010年12月25日 16:01:00 [sanchaji2](https://me.csdn.net/sanchaji2) 阅读数 1211



BeanShell是一个小型的，免费的，可嵌入式的，具有面向对象脚本语言特性的Java代码解释器。它是用Java语言写的。它能执行标准的Java 语句和表达式，还自带简单的脚本命令和语法。它把编程对象当成一个简单的方法，这很像Perl和JavaScript.
        你 可以在写Java测试或调试时使用BeanShell，也可以用它作为你的应用程序的脚本引挚。简而言之，BeanShell可以动态的解释JAVA语 言。也就是说BeanShell在许多方面对于Java的用处就像Tcl/Tk对于C的用处一样：BeanShell是可嵌入式的---你可以在运行时从 你的应用程序调用BeanShell去动态的执行Java代码或是为你的应用程序提供脚本扩展。相反，你也可以从BeanShell调用你的应用程序及其 对象，它可以让JAVA对象和API动态运行。正因为BeanShell是用JAVA写的，所以它可以和你的应用程序运行在同一个JVM空间内，你也可以 自由的传递实时对象的参照(References)到脚本代码中并且作为结果返回。
**主要特性** 
1.使用了JAVA的反射机制，可在运行时解释JAVA语句和表达式。
2.可以透明的存取所有的JAVA物件和API。
4.有四种运行模式：命令行，控制台，Applet,远程会话服务器(Remote Session Server)。
5.可以在Applet和Application中。
6.这个解释器包只有不到175k(不包括约135k的实用工具)。
7.采用纯JAVA语言。
9.它是完全免费的!
**JAVA方面的特性** 
1.覆盖了所有的Java语句和表达式语法。
2.强效的变量类型与方法。
3.原始值到正确数据类型处理能力。
4.完全Java算法，逻辑，与按位操作。
5.对象构造和多维数组处理。
6.具有break和return的循环及流程控制结构。 7.具有多形/重载特性的方法调用。
8.完全的异常(Exception)处理。
**脚本特性** 
1.动态类型(无类型)的变量。
2.具有参数和返回值的脚本方法。
3.脚本对象。
4.AWT/SWING事件的脚本处理。
5.BeanShell脚本能使用Java Interface的implements属性(要求jdk1.3+)。
6.方便的使用JavaBean的属性，hashtable,和原始数据类型的封装器(Wrapper)。
7.可扩展的工具命令集，像"load/save(bean)"和"eval(String)"。
8.支持namespace/scope修饰：this,super,global。
**用途提示** 
1.与JAVA程序交互---试出对象特性，API和GUI对象。
2.作为应用程序的脚本扩展环境---从你的应用程序使用BeanShell代替JAVA编译器来调用。
3.教育用途---放一个JAVA开发环境在你的java教学网页上真是再好不过了。
4.科学和金融应用中的表达式求值程序---可以使用条件和循环计算复杂的表达式。
5.处理JAVABEAN的设置---用BeanShell的save()命令来写。
6.调试用途---深入JVM玩一玩。
主页：http://www.beanshell.org/



**下载和运行BeanShell**

请到[http://www.beanshell.org](http://www.beanshell.org/) 下载最新的JAR文件。你可以用图形桌面模式和命令行模式起动BeanShell。
如果你只是要玩一玩BeanShell，你可以在BeanShell的jar文件上双击来起动BeanShell的桌面。但不管怎样，如果你要让BeanShell与你的类与应用程序一起工作就必须将BeanShell的jar文件加到classpath中。
你可以将BeanShell的jar文件拖到JAVA_HOME的ext目录也可以直接加到classpath中。

- windows用户请将bsh.jar放在JAVA_HOME/jre/lib/ext文件夹，OSX用户可以放在/Library/Java/Extensions.
  或者增加BeanShell到你的classpath目录，如：
  unix: export CLASSPATH=$CLASSPATH:bsh-xx.jar
  windows:set classpath %classpath%;bsh-xx.jar

然后你就可以运行BeanShell在GUI或命令行模式：

-  java bsh.Console       // run the graphical desktop
  or
       java bsh.Interpreter   // run as text-only on the command line
  or
       java bsh.Interpreter filename [ args ] // run script file

可以在你的应用程序内部来运行,也可以作为debug及servlet的远程服务器模式,或一个Applet内部来运行BeanShell。请参考"BeanShell Modes of Operation"获得更多详情。

**BeanShell GUI**

用GUI模式启动BeanShell后，将打开一个桌面视窗。用鼠标右击在桌面的背景上，你可以打开另一个控制台视窗及其它的工具如一个简单的类游览器。
每一个控制台视窗运行一个独立的BeanShell解释器。这个图形化的控制台支持基本的历史命令，行编辑，剪切和粘贴，甚至类和变量名的自动完成功能。从控制台你能开启一个简单的编辑视窗。在它里面，你可以编写脚本和使用‘eval’选项求表达式的值。

**Java语句和表达式**

BeanShell能理解标准的JAVA语句，表达式，和方法宣告。语句和表达式的内容可以是：变量，宣告，赋值，方法调用，循环，条件等。
在Java 程序中你必须严格的使用它们，但在BeanShell中，你可以用“宽松类型”(loosely typed)的方式来使用它们。也就是说，你可以在写脚本时偷懒，不进行变量类型的宣告(在原始数据类型和对象都可以)。如果你试着用错变量类型， BeanShell将会给出一个错误。
这里有一些例子：

- foo = "Foo";    
  four = (2 + 2)*2/2;
  print( foo + " = " + four );  // print() is a BeanShell command
  // Do a loop
  for (i=0; i<5; i++)
      print(i);   
  // Pop up a frame with a button in it
  button = new JButton( "My Button" );
  frame = new JFrame( "My Frame" );
  frame.getContentPane().add( button, "Center" );
  frame.pack();
  frame.setVisible(true);

**有用的BeanShell命令**

在刚才那个例子中我们用了一个内建在BeanShell中的一个方便的命令print(),来显示变量的值。print()跟ava的 System.out.println()非常的相像，除非它能保证输出总是命令行。print()也可以显示一些对象的类型(如数组)，但比Java的 更详细。另一个相关的命令是show(),用来开启与关闭显示你输入的每一行的结果。
这儿是一些其它的BeanShell的命令：
source(), run() - 将一个bsh脚本读到解释器或运行在另一个解释器。 
frame() - 显示一个Frame或JFrame的GUI组件. 
load(), save() - 载入和保存一个序列化的对象到一个文件. 
cd(), cat(), dir(), pwd(), etc. - 类unix的shell命令。 
exec() - 运行一个本地的程序。
javap() - 打印一个对象的方法和字段，类似于Java的javap命令。
setAccessibility() - 开启无限制的存取private 和protected的组件。
要获得更多的信息请查看BeanShell命令的详细清单。

*提示:* 
BeanShell命令并不是真的"内建"其中的，而是作为脚本方法自动从classpath载入的. 你可以扩展基本命令集并加到classpath中作为自订义的脚本来使用。

**脚本方法**

你可以在bsh中宣告和使用方法，就像在java的类中一样。

- int addTwoNumbers( int a, int b ) {
      return a + b;
  }
  sum = addTwoNumbers( 5, 7 );  // 12

bsh的方法可以有动态的(宽松的)参数和返回类型。

- add( a, b ) {
      return a + b;
  }
  foo = add(1, 2);            // 3
  foo = add("Oh", " baby");   // "Oh baby"

**实现Interface**

注意：如果要BeanShell能实现任意的Interface，必须有jdk1.3及以上支持。
你可以在脚本中用标准的Java内部类的语法来实现Interface.例如：

- ActionListener scriptedListener = new ActionListener() {
      actionPerformed( event ) { ... }
  }

你可以不用实现Interface的所有方法，而只用实现你需要的方法。如果代码中调用了未被实现的方法，将丢出异常。如果你想重载大量的方法的行 为--例如为日志生成一个"哑"适配器--你可以在脚本对象中实现一个特殊的方法:invoke(name,args)。invoke()方法用来处理任 何未被定义的方法的调用:

- ml = new MouseListener() {
      mousePressed( event ) { ... }
      // handle the rest
      invoke( name, args ) { print("Method: "+name+" invoked!");
  }

**脚本对象**

在BeanShell中，和在JavaScript与Perl中一样，脚本对象是用封闭的方法体一构成的。通过在方法未尾返回一个特殊值 "this"，你就可以像使用方法一样调用这个对象了。在这个方法调用时，你可以给与它任何的值。通常对象内部需要包括方法，所以BeanShell的脚 本方法在一定程度上可再包含一些方法以构成脚本对象。例如：

- foo() {
      print("foo");
      x=5;
      bar() {
          print("bar");
      }
      return this;
  }
  myfoo = foo();    // prints "foo"
  print( myfoo.x ); // prints "5"
  myfoo.bar();      // prints "bar"

如果这些代码对你来说很陌生，别急，请用户手册可得到更透彻的解释。

在你的脚本中，BeanShell脚本对象(也就是先前例子中的"this"参照)能自动实现任何JAVA介面类型。当JAVA代码调用相应与之通 讯的脚本方法内的方法。当你试着将脚本对象作为参数传给Java方法时，BeanShell会自动将它造型(cast)为相应的类型。如要传递 BeanShell外部的对象时，你可以在需要时显式的进行造型(cast).请看用户手册中的详细内容。

**从你的应用程序调用BeanShell**

通过建立一个BeanShell解释器，使用eval()或source()命令，你可以在你的应用程序中求文本表达式的值和运行脚本。如果你希望在你的脚本内部使用一个对象，可以用set()方法传递对象的变量参照给BeanShell,并通过get()方法取得结果。

- import bsh.Interpreter;
  Interpreter i = new Interpreter();  // Construct an interpreter
  i.set("foo", 5);                    // Set variables
  i.set("date", new Date() ); 
  Date date = (Date)i.get("date");    // retrieve a variable
  // Eval a statement and get the result
  i.eval("bar = foo*10");             
  System.out.println( i.get("bar") );
  // Source an external script file
  i.source("somefile.bsh");

--------------------

