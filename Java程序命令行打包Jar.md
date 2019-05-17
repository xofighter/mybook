> 最近要跑爬虫程序，需要打包成jar发在linux服务器中运行。主要是第三方的lib包与配置文件，不进行打包,方便修改。

#### 1.eclipse中src中源码编译后生成的源码在bin文件中，把里面源码单独拿出。

![img](https://images2017.cnblogs.com/blog/893406/201708/893406-20170810224632964-1413165769.png)

#### 2.编写MANIFEST.MF文件

```
Manifest-Version:  1.0
Class-Path: lib/commons-codec-1.9.jar lib/commons-logging-1.2.jar lib/fluent-hc-4.5.3.jar lib/httpclient-4.5.3.jar lib/httpclient-cache-4.5.3.jar lib/httpclient-win-4.5.3.jar lib/httpcore-4.4.6.jar lib/httpmime-4.5.3.jar lib/jna-4.1.0.jar lib/jna-platform-4.1.0.jar
Main-Class:  com.zyw.main.Main
```

这里需要注意MANIFEST.MF文件的格式有：

1. Manifest-Version,classPath,Main-Class冒号后面都有空格

2. Main-Class后一行必须是空行

3. Main-Class后面为主类完整路径名

   #### 如下图：

![img](https://images2017.cnblogs.com/blog/893406/201708/893406-20170810230241245-466709105.png)

#### 3.在程序中读取配置文件示例

```
public void getConf(){
    // 获得当前目录路径
    String rootPath = System.getProperty("user.dir").replace("\\", "/");
    String jdbc=rootPath+"/conf/jdbc.conf";
    Properties env = new Properties();
    FileInputStream fis = null;
    try {
        fis = new FileInputStream(jdbc);
        env.load(fis);
    } catch (Exception ex) {
    } finally {
        try {
            if(fis!=null){
                fis.close();                    
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    System.out.println(env.getProperty("jdbc.usename"));
    System.out.println(env.getProperty("jdbc.password"));
}
```

#### 4.Jar命令打包与运行

```
jar cvfm JarDemo.jar MANIFEST.MF   com/   ##打包命令
java -jar JarDemo.jar                     ##运行命令
```