# 泛型（generic）
## 1. 泛型出现的原因
泛型是JDK1.5以后出现的安全机制（或者说是安全新特性）
是为了避免运行时出现类型不一致的问题ClassCastException而出现的。
泛型出现的好处
将运行时期的问题ClassCastException转到了编译时期；
避免了强制转换的麻烦。
泛型什么时候用？
当操作的引用数据类型不确定的时候，就是用泛型，将要操作的引用数据类型传入即可；其实泛型就是一个用于接收具体引用数据类型的参数范围（注意：泛型不能接收基本数据类型，例如：int，double等）。
在程序中，只要用到了带有<>的类或者接口，就要明确传入的具体引用数据类型。
泛型的擦除
泛型是给编译器使用的技术，用于编译时期，确保了类型的安全。
运行时会将泛型去掉，生成的class文件中是不带泛型的，这个称为泛型的擦除。
为什么擦除？因为5.0以及之前的版本都不支持泛型，擦除是为了兼容运行的（5.0以及之前的）类加载器，避免类加载器还要升级。也就是说，泛型在编译时期帮忙检查了一下类型问题，检查完后在类加载时又擦除了，这避免了类加载器不识别~。
泛型的补偿
在程序运行时，通过获取元素的类型进行转换动作（通过反射机制来获取元素的类型），使用者就不用再强制转换了。
泛型在集合中的应用

```代码
package cn.itcast.p2.bean;

public class Person implements Comparable<Person>{

    private String name;
    private int age;
    
    public Person() {
        super();
        // TODO Auto-generated constructor stub
    }
    public Person(String name, int age) {
        super();
        this.name = name;
        this.age = age;
    }
    public String getName() {
    	return name;
    }
    public void setName(String name) {
    	this.name = name;
    }
    public int getAge() {
    	return age;
    }
    public void setAge(int age) {
    	this.age = age;
    }

    @Override
    public int compareTo(Person p) {
        // TODO Auto-generated method stub
        //        Person p=obj;
        int temp = this.age - p.age;
        return temp == 0?this.name.compareTo(p.name):temp;
    }   
}
```
```代码
package cn.itcast.p3.bean.comparator;

import java.util.Comparator;
import cn.itcast.p2.bean.Person;

public class ComparatorByName implements Comparator<Person> {

    @Override
    public int compare(Person arg0, Person arg1) {
        // TODO Auto-generated method stub
        int temp = arg1.getName().compareTo(arg1.getName());
        //当名字相同时，就按年龄排序（通过返回值的正负来判断）
        return temp == 0?arg0.getAge()-arg1.getAge():temp;
    }
}
```
```代码
package cn.itcast.generic.demo;

import java.util.Iterator;
import java.util.TreeSet;
import cn.itcast.p2.bean.Person;
import cn.itcast.p3.bean.comparator.ComparatorByName;

public class GenericDemo2 {
    public static void main(String[] args) {
        // TODO Auto-generated method stub
        //TreeSet需要排序，所以Person类要实现接口Comparable
        //        TreeSet<Person> ts = new TreeSet<Person>();
        TreeSet<Person> ts = new TreeSet<Person>(new ComparatorByName());
        ts.add(new Person("wanger0",21));
        ts.add(new Person("zhangsan2",24));
        ts.add(new Person("wagnwu3",17));
        ts.add(new Person("lisi1",20));
        Iterator<Person> it = ts.iterator();
        while(it.hasNext()){
        Person p = it.next();
        System.out.println(p.getName()+":"+p.getAge());
        }
    }
}
```

泛型的书写格式
自定义泛型类
泛型除了能在集合中的使用，还能进行自定义，并给我们提供了设计上的便捷~。
之前的做法：为了提高程序的扩展性，对象都设计成Obejct类型，但它在具体使用时必须进行强转；但是这种做法有可能会造成问题——当你传入的对象和你强转的对象类型不一致，程序编译不会出问题，但是运行就会报错：……cannot be cast to……。例如下面的代码：
//定义一个工具类Tool，为了后续使用时具有扩展性，这里的对象类型都设计成了Object类型
public class Tool {
private Object object;
public Object getObject() {
return object;
}
public void setObject(Object object) {
this.object = object;
}
}
//使用工具类Tool，这里具体传入的对象类型与后续输出时强转的对象类型不一致，会有报错信息出现。
public class GenericdefineDemo3 {
public static void main(String[] args) {
// TODO Auto-generated method stub
Tool tool = new Tool();
//这里传入的对象是worker类型
tool.setObject(new Worker());
//这里传入的对象是Student类型
//tool.setObject(new Student());
//这里把Obejct类型强转成Student类型
Student stu = (Student)tool.getObject();
}
}
现在的做法：在jdk1.5之后，使用泛型来接收类中要操作的引用数据类型；
什么时候定义泛型类？
当类中要操作的引用数据类型不确定的时候，就把该类定义为泛型类；使用泛型定义类比之前的Object扩展方法更安全——它会把之前类型不一致的问题在编译时期就暴露出来。
区别：泛型类不需要强转，但是要比之前的Object扩展方法要麻烦一点；例如下面的代码：
// 定义一个泛型类
public class Tool<QQ>{
private QQ qq;
public QQ getQq() {
return qq;
}
public void setQq(QQ qq) {
this.qq = qq;
}
}
//如果你在setQq方法中传入的引用类型不是Student的数据，程序编译时就会报错
public class GenericdefineDemo3 {
public static void main(String[] args) {
// TODO Auto-generated method stub
Tool<Student> tool = new Tool<Student>();
tool.setQq(new Student());
tool.getQq();
}
}
自定义泛型方法
在类中，可以在方法中传入泛型对象，注意，泛型方法不一定是在泛型类中，可以在方法中自定义泛型，也可以引用泛型类中定义的泛型；
当方法是静态时，不能引用类定义的泛型，如果静态方法要使用泛型，只能使用在方法上定义的泛型；
一旦使用了泛型，泛型对象的方法就只能使用Obejct的方法，因为泛型对象不确定，所有其他方法不能使用；例如下面的代码：
public class Tool<QQ>{
private QQ qq;
public QQ getQq() {
return qq;
}
public void setQq(QQ qq) {
this.qq = qq;
}
//将泛型定义在方法上
public<W> void show(W w){
System.out.println("show:"+w);
}
//直接使用泛型类中定义的泛型
public void print(QQ qq){
System.out.println("print:"+qq);
}
//当方法是静态时，方法不能引用类定义的泛型，如果静态方法要使用泛型，只能使用在方法上定义的泛型
public static <WW> void method(WW ww){
System.out.println("method:"+ww);
}
}
//使用泛型类中泛型方法，
public class Genericdemo4 {

public static void main(String[] args) {
// TODO Auto-generated method stub
Tool<String> tool = new Tool<String>();
tool.show(33);
tool.print("hahah");
Tool.method(new Integer(9));
Tool.method("dfgd");
}
}

自定义泛型接口
例如下面的代码：
public class Genericdemo5 {
public static void main(String[] args) {
// TODO Auto-generated method stub
InterImpl in = new InterImpl();
in.show("hafgd");
InterImpl2<Integer> in2 = new InterImpl2<Integer>();
in2.show(5);
}
}

//定义接口
interface Inter<T>{
public void show(T t);
}

//在定义实现接口的类时，不确定泛型的类型
class InterImpl2<Q> implements Inter<Q>{

@Override
public void show(Q t) {
// TODO Auto-generated method stub
System.out.println("show :"+t);
}
}

//在定义实现接口的类时，以确定泛型的类型
class InterImpl implements Inter<String>{
@Override
public void show(String t) {
// TODO Auto-generated method stub
System.out.println("show :"+t);
}
}

泛型的通配符【?】
通配符【?】——表示未知类型
通配符和泛型的区别：当你指定一个泛型T，他就可以代表一个具体引用类型，去定义一个对象；而通配符则不能代表一个具体引用类型，去定义一个对象。
通配符的体现

泛型限定
泛型限定——上限
上限表现为：<? extends E >，它表示接收E类型或E的子类型对象；
泛型限定——下限
下限表现为：<? super E>，它表示接收E类型或E的父类型对象。
泛型限定的体现——上限

泛型限定的体现——下限

向上转型和向下转型
向上转型：Java中的一种调用方式。向上转型是对A（父类）对象的方法的扩充，即A对象可访问B从A中继承来的和B复写A的方法【子类转型成父类】。
向下转型：父类转型为子类，这里需要强制转换；
例如 A是父类，B为A的子类，那么
A a = new B();  //这种方式就是向上转型
B b = (B)a;    //这种方式就是向下转型，编译不会报错，运行时也不会报错
b.methodA();
b.methodB();
A a2= new A();
//B b = (B)a2;  //这种方式的向下转型，编译不会报错，但是在运行时会报错
//b.methodA();
//b.methodB();    
If(a2 instanceof B){ /*使用instanceof方法来避免向下转型的错误，instanceof运算符是用来在运行时指出对象是否是特定类的一个实例。*/
B b2 = (B)a2;
b.methodA();
b.methodB();