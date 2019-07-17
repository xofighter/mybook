Jmeter+Ant集成--build.xml文件解析

1.project标签



| 属性    | 描述                                                         | 是否必须                                                     |
| ------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| name    | 项目名称                                                     | 否                                                           |
| default | 如果没有任何target的话，需要这个默认的target                 | 否，自从蚂蚁1.6.0，每个项目包括一个隐含的目标，包含所有的顶级任务和/或类型。这一目标将永远作为项目的初始化部分执行，即使当蚂蚁运行时有projecthelp选项。 |
| basedir | 用于指定基路径的位置。该属性没有指定时，使用 Ant 的构件文件的父目录作为基准目录 | 否                                                           |
|         |                                                              |                                                              |

build.xml需要遵循一定的格式，这样ant命令才能正确执行，一个build.xml文件是以<project>标签为根节点的；<project>节点中可以指定name属性，表示项目的名称，basedir代表项目的根目录，default表示项目的默认任务名。

比如：default的属性值为run,我们输入ant,那么ant就执行run这一个target；

           default的属性值为run,我们输入ant report,那么ant就执行report这一个target；

总的来说，如果ant指定了要执行的target，那就执行指定的target；未指定就执行default的属性值。

PS：ant后面也可以指定多个target，例如，ant run,report；则ant会先执行run,再执行report



2.<target>标签

| 属性                    | 描述                                                         | 是否必须 |
| ----------------------- | ------------------------------------------------------------ | -------- |
| name                    | Target名                                                     | 是       |
| depends                 | 依赖                                                         | 否       |
| if                      | 某property必须设置了才可以运行这个target或者property扩展属性评估是true | 否       |
| unless                  | 某property必须没有设置才可以运行这个target或者property扩展属性评估是false | 否       |
| description             | Target功能的描述                                             | 否       |
| extensionOf             | 当这个target添加到extension-point的依赖列表中，1.8.0开始     | 否       |
| onMissingExtensionPoint | 如果这个target视图扩展丢失的extension-point该怎么办          | 否       |
|                         |                                                              |          |

1.target标签中的name属性表示任务名，我们可以在命令行下直接使用ant + 任务名来执行某个特定的任务；上述的project标签其实已经把target标签带着一起讲了~

2.一个target可以依赖于其他的target；使用depends来定义target之间的依赖关系。例如：

```xml
<target name="A"/>
<target name="B" depends="A"/>
<target name="C" depends="B"/>
<target name="D" depends="C,B,A"/>
```



假定我们要执行target D,  则会先执行A，然后B，然后C，最后D被执行。一个target只能被执行一次，即时有多个target依赖于它

3.条件设定if/unless

|                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <target name="_message_xalan" if="xalan.present"<echo>Cannot find all xalan and/or serialiser jars</echo> > | 如果xalan.presentt这个属性被设置了，我就执行_message_xalan这个target ,即输出Cannot find all xalan and/or serialiser jars<br/> |
| <target name="_message_xalan" unless="xalan.present"/><echo>Cannot find all xalan and/or serialiser jars</echo> > | 如果xalan.presentt这个属性没有被设置了，我就执行_message_xalan这个target ,即输出Cannot find all xalan and/or serialiser jars |
|                                                              |                                                              |

3.property标签
1.property标签用来定义属性值，一个 property有一个名字和一个值。如：<property name="test1" value="MTIFS1"/>，属性名字为test1，值为字符串“MTIFS1”

2.定义好属性之后可以使用这个属性了，用${}来使用定义的属性。如：testplan ="${testpath}/${test1}.jmx"   等于  testplan ="${testpath}/MTIFS1.jmx"

4.taskdef
taskdef是Ant内置任务，用于将任务定义添加到当前project中, 以便可以在当前project中使用此任务。taskdef是一种将adapter和adaptto属性分别设置为org.apache.tools.ant.TaskAdapter和org.apache.tools.ant.Task的typedef。typedef的特性同样适用于taskdef常用属性有：

1.name:数据类型的名称

2.classname:实现数据类型的类全名

3.file:加载定义的文件名称

4.classpath:查找classname的类路径

5.classpathref:实现数据类型的类的相关引用（如jar包）



5.其它标签
1.echo表示在命令行下输出，如<echo>Report generated at time}</echo>

2.tsamp，定义指定格式日期时间--<tstamp><format property="time" pattern="yyyy-MM-dd-HH-mm-ss"/></tstamp>

3.copy、delete、makedir、Java、jar等等......

 

梳理完之后，其实我们在使用ant+jmeter进行构建的build.xml文件可以写的很简单了，下面的这些代码就可以满足基本需求了

 

build.xml 

```xml
<?xml version="1.0" encoding="utf-8"?>

//定义项目名

<project name="ant-jmeter" default="all">

<property name="testpath" value="${user.dir}"/>

 <property name="jmeter.home" value="${basedir}/.."/>

 <property name="report.title" value="Load Test Results"/>

<property name="test1" value="MTIFS1"/>

//执行第一个target的准备工作

<path id="jmeter.classpath">

 <fileset dir="${basedir}">

 <include name="ant-jmeter*.jar"/>

 </fileset>

 </path>

 <taskdef

 name="jmeter"

 classpathref="jmeter.classpath"

 classname="org.programmerplanet.ant.taskdefs.jmeter.JMeterTask"

 />

 <target name="all" depends="run,report"/>

//第一个target

<target name="run">

 <echo>funcMode = ${funcMode}</echo>

 <tstamp>

 <format property="time" pattern="yyyy-MM-dd-HH-mm-ss"/>

 </tstamp>

 <jmeter

 jmeterhome="${jmeter.home}"

 testplan ="${testpath}/${test1}.jmx"

 resultlog="${testpath}/${test1}/${time}.jtl">

</jmeter>

 </target>

//执行第二个target的准备工作

 

<property name="lib.dir" value="${jmeter.home}/lib"/>

<!-- Use xalan copy from JMeter lib directory to ensure consistent processing with Java 1.4+ -->

 <path id="xslt.classpath">

 <fileset dir="${lib.dir}" includes="xalan*.jar"/>

 <fileset dir="${lib.dir}" includes="serializer*.jar"/>

 </path>

<target name="report" depends="xslt-report">

 <echo>Report generated at ${report.datestamp}</echo>

 </target>

//第二个target

 <target name="xslt-report">

 <tstamp><format property="report.datestamp" pattern="yyyy/MM/dd HH:mm"/></tstamp>

 <xslt

 classpathref="xslt.classpath"

 force="true"

 in="${testpath}/${test1}/${time}.jtl"

 out="${testpath}/${test1}/${time}.html"

 style="${basedir}/styletest.xsl" >

</xslt>

 </target>


</project>
```
