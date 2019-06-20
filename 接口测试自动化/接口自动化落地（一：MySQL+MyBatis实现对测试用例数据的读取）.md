# 接口自动化落地（一：MySQL+MyBatis实现对测试用例数据的读取）

2018年06月08日 13:06:06

 

简单随风

 

阅读数 2375

更多

所属专栏： [点点点工程师](https://blog.csdn.net/column/details/34084.html)



本篇文章讲解TestNG+MySQL+MyBatis+ExtentReports实现对测试用例数据的读取，下面放出所有配置文件的目录方便下文理解。 
![img](https://img-blog.csdn.net/20180607174640654?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x0MzI2MDMwNDM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



- - - - [1.配置mysql数据库](https://blog.csdn.net/lt326030434/article/details/80621268#1配置mysql数据库)
      - [2.配置pom文件](https://blog.csdn.net/lt326030434/article/details/80621268#2配置pom文件)
      - [3.配置application.properties文件](https://blog.csdn.net/lt326030434/article/details/80621268#3配置applicationproperties文件)
      - [4.配置databaseConfig.xml文件](https://blog.csdn.net/lt326030434/article/details/80621268#4配置databaseconfigxml文件)
      - [5.配置testng.xml文件](https://blog.csdn.net/lt326030434/article/details/80621268#5配置testngxml文件)
      - [6.配置SQLMapper.xml文件](https://blog.csdn.net/lt326030434/article/details/80621268#6配置sqlmapperxml文件)
      - [7.配置model文件](https://blog.csdn.net/lt326030434/article/details/80621268#7配置model文件)
      - [8.配置config文件](https://blog.csdn.net/lt326030434/article/details/80621268#8配置config文件)
      - [9.配置utils文件](https://blog.csdn.net/lt326030434/article/details/80621268#9配置utils文件)
      - [10.配置cases文件](https://blog.csdn.net/lt326030434/article/details/80621268#10配置cases文件)



#### 1.配置mysql数据库

![这里写图片描述](https://img-blog.csdn.net/20180608104902767?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x0MzI2MDMwNDM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) 
在这里，拿logincase表举例，后面也拿login做讲解。 
![这里写图片描述](https://img-blog.csdn.net/20180608105421193?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x0MzI2MDMwNDM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### 2.配置pom文件

首先在pom文件中，配置httpclient、mybatis、mysql、extentreports、testng的各种依赖。

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.course.code</groupId>
    <artifactId>userInfoTest</artifactId>
    <version>1.0-SNAPSHOT</version>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>6</source>
                    <target>6</target>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <dependencies>
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient</artifactId>
            <version>4.1.2</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20170516</version>
        </dependency>
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.4</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>6.0.5</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.16.16</version>
        </dependency>
        <dependency>
            <groupId>com.relevantcodes</groupId>
            <artifactId>extentreports</artifactId>
            <version>2.41.1</version>
        </dependency>
        <dependency>
            <groupId>com.vimalselvam</groupId>
            <artifactId>testng-extentsreport</artifactId>
            <version>1.3.1</version>
        </dependency>
        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>6.11</version>
        </dependency>
        <dependency>
            <groupId>com.aventstack</groupId>
            <artifactId>extentreports</artifactId>
            <version>3.0.6</version>
        </dependency>
    </dependencies>123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869
```

#### 3.配置application.properties文件

resource目录下配置application.properties文件，用以存储测试地址。

```
test.url=http://localhost:9529

#登录接口
login.uri=/v1/login

#更新用户信息
updateUserInfo.uri=/v1/updateUserInfo

#获取用户列表
getUserList.uri=/v1/getUserList

#获取用户信息
getUserInfo.uri=/v1/getUserInfo

#添加用户接口
addUser.uri=/v1/addUser12345678910111213141516
```

#### 4.配置databaseConfig.xml文件

resource目录下配置databaseConfig.xml文件，用以连接数据库。

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <!-- 注册对象的空间命名 -->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <!-- 加载数据驱动 -->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <!-- 数据库连接地址 -->
                <property name="url" value="jdbc:mysql://localhost:3306/myBatisTest"/>
                <!-- 数据库用户、密码 -->
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <!-- 注册映射文件:java对象与数据库之间的xml文件路径 -->
    <mappers>
        <mapper resource="mapper/SQLMapper.xml"/>
    </mappers>
</configuration>12345678910111213141516171819202122232425
```

#### 5.配置testng.xml文件

resource目录下配置testng.xml文件，用以执行所有testng的测试套件。

```
<?xml version="1.0" encoding="UTF-8" ?>
<suite name="用户管理系统测试套件">
    <test name="用户管理系统测试用例">
        <classes>
            <class name="com.course.cases.loginTest">
                <methods>
                    <include name="loginTrue"/>
                    <include name="loginFalse"/>
                </methods>
            </class>

            <class name = "com.course.cases.AddUserTest">
                <methods>
                    <include name="addUser"/>
                </methods>
            </class>

            <class name = "com.course.cases.GetUserInfoTest">
                <methods>
                    <include name="getUserInfo"/>
                </methods>
            </class>

            <class name = "com.course.cases.GetUserListTest">
                <methods>
                    <include name="getUserList"/>
                </methods>
            </class>

            <class name = "com.course.cases.UpdateUserInfoTest">
                <methods>
                    <include name="updateUserInfo"/>
                    <include name="deleteUser"/>
                </methods>
            </class>
        </classes>
    </test>
    <listeners>
        <listener class-name="com.course.config.ExtentTestNGIReporterListener"></listener>
    </listeners>
</suite>1234567891011121314151617181920212223242526272829303132333435363738394041
```

#### 6.配置SQLMapper.xml文件

resources/mapper目录下，配置SQLMapper.xml文件，用户存储所有case的sql语句。

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.course.model">
    <!--获取登录接口case-->
    <select id="loginCase" parameterType="Integer" resultType="com.course.model.LoginCase">
        select * from loginCase
        where id=#{id};
    </select>

    <!--添加用户接口case-->
    <select id="addUserCase" parameterType="Integer" resultType="com.course.model.AddUserCase">
        select * from addUserCase where id=#{id};
    </select>

    <!--获取用户信息case-->
    <select id="getUserInfoCase" parameterType="Integer" resultType="com.course.model.GetUserInfoCase">
        select * from getUserInfoCase where id=#{id};
    </select>

    <!--获取用户列表case-->
    <select id="getUserListCase" parameterType="Integer" resultType="com.course.model.GetUserListCase">
        select * from getUserListCase where id = #{id};
    </select>

    <!--更新/删除用户case-->
    <select id="updateUserInfoCase" parameterType="Integer" resultType="com.course.model.UpdateUserInfoCase">
        select * from updateUserInfoCase where id = #{id};
    </select>
</mapper>12345678910111213141516171819202122232425262728293031
```

#### 7.配置model文件

com.course.model目录下，配置各个接口的数据配置文件，和一个InterfaceName枚举。 
![这里写图片描述](https://img-blog.csdn.net/20180608103917676?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x0MzI2MDMwNDM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) 
在这里，同样拿LoginCase里的数据举例，其他case的格式一样。在这里参照mysql里配置的字段一一进行声明。

```
@Data
public class LoginCase {
    private int id;
    private String userName;
    private String password;
    private String expected;
}1234567
```

InterfaceName配置如下

```
public enum InterfaceName {
    GETUSERLIST,LOGIN,UPDATEUSERINFO,GETUSERINFO,ADDUSER
}123
```

#### 8.配置config文件

com.course.config目录下，新建ExtentTestNGIReporterListener文件和TestConfig文件。 
![这里写图片描述](https://img-blog.csdn.net/20180608112231397?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x0MzI2MDMwNDM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) 
ExtentTestNGIReporterListener里是ExtentReport的测试报告配置文件，固定代码可以直接拷过去使用。

```
public class ExtentTestNGIReporterListener implements IReporter {
    //生成的路径以及文件名
    private static final String OUTPUT_FOLDER = "test-output/";
    private static final String FILE_NAME = "index.html";

    private ExtentReports extent;

    @Override
    public void generateReport(List<XmlSuite> xmlSuites, List<ISuite> suites, String outputDirectory) {
        init();
        boolean createSuiteNode = false;
        if(suites.size()>1){
            createSuiteNode=true;
        }
        for (ISuite suite : suites) {
            Map<String, ISuiteResult> result = suite.getResults();
            //如果suite里面没有任何用例，直接跳过，不在报告里生成
            if(result.size()==0){
                continue;
            }
            //统计suite下的成功、失败、跳过的总用例数
            int suiteFailSize=0;
            int suitePassSize=0;
            int suiteSkipSize=0;
            ExtentTest suiteTest=null;
            //存在多个suite的情况下，在报告中将同一个一个suite的测试结果归为一类，创建一级节点。
            if(createSuiteNode){
                suiteTest = extent.createTest(suite.getName()).assignCategory(suite.getName());
            }
            boolean createSuiteResultNode = false;
            if(result.size()>1){
                createSuiteResultNode=true;
            }
            for (ISuiteResult r : result.values()) {
                ExtentTest resultNode;
                ITestContext context = r.getTestContext();
                if(createSuiteResultNode){
                    //没有创建suite的情况下，将在SuiteResult的创建为一级节点，否则创建为suite的一个子节点。
                    if( null == suiteTest){
                        resultNode = extent.createTest(r.getTestContext().getName());
                    }else{
                        resultNode = suiteTest.createNode(r.getTestContext().getName());
                    }
                }else{
                    resultNode = suiteTest;
                }
                if(resultNode != null){
                    resultNode.getModel().setName(suite.getName()+" : "+r.getTestContext().getName());
                    if(resultNode.getModel().hasCategory()){
                        resultNode.assignCategory(r.getTestContext().getName());
                    }else{
                        resultNode.assignCategory(suite.getName(),r.getTestContext().getName());
                    }
                    resultNode.getModel().setStartTime(r.getTestContext().getStartDate());
                    resultNode.getModel().setEndTime(r.getTestContext().getEndDate());
                    //统计SuiteResult下的数据
                    int passSize = r.getTestContext().getPassedTests().size();
                    int failSize = r.getTestContext().getFailedTests().size();
                    int skipSize = r.getTestContext().getSkippedTests().size();
                    suitePassSize += passSize;
                    suiteFailSize += failSize;
                    suiteSkipSize += skipSize;
                    if(failSize>0){
                        resultNode.getModel().setStatus(Status.FAIL);
                    }
                    resultNode.getModel().setDescription(String.format("Pass: %s ; Fail: %s ; Skip: %s ;",passSize,failSize,skipSize));
                }
                buildTestNodes(resultNode,context.getFailedTests(), Status.FAIL);
                buildTestNodes(resultNode,context.getSkippedTests(), Status.SKIP);
                buildTestNodes(resultNode,context.getPassedTests(), Status.PASS);
            }
            if(suiteTest!= null){
                suiteTest.getModel().setDescription(String.format("Pass: %s ; Fail: %s ; Skip: %s ;",suitePassSize,suiteFailSize,suiteSkipSize));
                if(suiteFailSize>0){
                    suiteTest.getModel().setStatus(Status.FAIL);
                }
            }

        }
//        for (String s : Reporter.getOutput()) {
//            extent.setTestRunnerOutput(s);
//        }

        extent.flush();
    }

    private void init() {
        //文件夹不存在的话进行创建
        File reportDir= new File(OUTPUT_FOLDER);
        if(!reportDir.exists()&& !reportDir .isDirectory()){
            reportDir.mkdir();
        }
        ExtentHtmlReporter htmlReporter = new ExtentHtmlReporter(OUTPUT_FOLDER + FILE_NAME);
        // 设置静态文件的DNS
        //怎么样解决cdn.rawgit.com访问不了的情况
        htmlReporter.config().setResourceCDN(ResourceCDN.EXTENTREPORTS);

        htmlReporter.config().setDocumentTitle("api自动化测试报告");
        htmlReporter.config().setReportName("api自动化测试报告");
        htmlReporter.config().setChartVisibilityOnOpen(true);
        htmlReporter.config().setTestViewChartLocation(ChartLocation.TOP);
        htmlReporter.config().setTheme(Theme.STANDARD);
        htmlReporter.config().setCSS(".node.level-1  ul{ display:none;} .node.level-1.active ul{display:block;}");
        extent = new ExtentReports();
        extent.attachReporter(htmlReporter);
        extent.setReportUsesManualConfiguration(true);
    }

    private void buildTestNodes(ExtentTest extenttest, IResultMap tests, Status status) {
        //存在父节点时，获取父节点的标签
        String[] categories=new String[0];
        if(extenttest != null ){
            List<TestAttribute> categoryList = extenttest.getModel().getCategoryContext().getAll();
            categories = new String[categoryList.size()];
            for(int index=0;index<categoryList.size();index++){
                categories[index] = categoryList.get(index).getName();
            }
        }

        ExtentTest test;

        if (tests.size() > 0) {
            //调整用例排序，按时间排序
            Set<ITestResult> treeSet = new TreeSet<ITestResult>(new Comparator<ITestResult>() {
                @Override
                public int compare(ITestResult o1, ITestResult o2) {
                    return o1.getStartMillis()<o2.getStartMillis()?-1:1;
                }
            });
            treeSet.addAll(tests.getAllResults());
            for (ITestResult result : treeSet) {
                Object[] parameters = result.getParameters();
                String name="";
                //如果有参数，则使用参数的toString组合代替报告中的name
                for(Object param:parameters){
                    name+=param.toString();
                }
                if(name.length()>0){
                    if(name.length()>50){
                        name= name.substring(0,49)+"...";
                    }
                }else{
                    name = result.getMethod().getMethodName();
                }
                if(extenttest==null){
                    test = extent.createTest(name);
                }else{
                    //作为子节点进行创建时，设置同父节点的标签一致，便于报告检索。
                    test = extenttest.createNode(name).assignCategory(categories);
                }
                //test.getModel().setDescription(description.toString());
                //test = extent.createTest(result.getMethod().getMethodName());
                for (String group : result.getMethod().getGroups())
                    test.assignCategory(group);

                List<String> outputList = Reporter.getOutput(result);
                for(String output:outputList){
                    //将用例的log输出报告中
                    test.debug(output);
                }
                if (result.getThrowable() != null) {
                    test.log(status, result.getThrowable());
                }
                else {
                    test.log(status, "Test " + status.toString().toLowerCase() + "ed");
                }

                test.getModel().setStartTime(getTime(result.getStartMillis()));
                test.getModel().setEndTime(getTime(result.getEndMillis()));
            }
        }
    }

    private Date getTime(long millis) {
        Calendar calendar = Calendar.getInstance();
        calendar.setTimeInMillis(millis);
        return calendar.getTime();
    }
}123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475767778798081828384858687888990919293949596979899100101102103104105106107108109110111112113114115116117118119120121122123124125126127128129130131132133134135136137138139140141142143144145146147148149150151152153154155156157158159160161162163164165166167168169170171172173174175176177178179
```

TestConfig内声明各个case的url和之后要用的一些变量。

```
public class TestConfig {
    public static String loginUrl;
    public static String updateUserInfoUrl;
    public static String getUserListUrl;
    public static String getUserInfoUrl;
    public static String addUserUrl;

    public static DefaultHttpClient defaultHttpClient;
    public static CookieStore store;
}12345678910
```

#### 9.配置utils文件

com.course.utils目录下配置ConfigFile文件和DatabaseUtil文件。 
![这里写图片描述](https://img-blog.csdn.net/20180608113840237?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x0MzI2MDMwNDM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) 
ConfigFile内对各个case的url进行赋值

```
public class ConfigFile {
    public static ResourceBundle bundle = ResourceBundle.getBundle("application",Locale.CHINA);
    public static String getUrl(InterfaceName name){
        String address = bundle.getString("test.url");
        String uri = "";
        //最终测试地址
        String testUrl;

        if (name == InterfaceName.GETUSERLIST){
            uri = bundle.getString("getUserList.uri");
        }
        if (name == InterfaceName.LOGIN){
            uri = bundle.getString("login.uri");
        }
        if (name == InterfaceName.UPDATEUSERINFO){
            uri = bundle.getString("updateUserInfo.uri");
        }
        if (name == InterfaceName.GETUSERINFO){
            uri = bundle.getString("getUserInfo.uri");
        }
        if (name == InterfaceName.ADDUSER){
            uri = bundle.getString("addUser.uri");
        }
        testUrl = address + uri;

        return testUrl;
    }

}1234567891011121314151617181920212223242526272829
```

DatabaseUtil内配置一个getSqlsession方法，作用是执行配置文件中的sql语句，用以之后每个case可以直接调用而不用重复的写这一部分代码。

```
public class DatabaseUtil {
    public static SqlSession getSqlsession() throws IOException {

        //获取配置资源文件
        Reader reader = Resources.getResourceAsReader("databaseConfig.xml");

        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader);
        //sqlSession就是能够执行配置文件中的sql语句
        SqlSession sqlSession=factory.openSession();
        return sqlSession;
    }
}123456789101112
```

#### 10.配置cases文件

com.course.cases目录下配置所有case类 
![这里写图片描述](https://img-blog.csdn.net/20180608115429514?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x0MzI2MDMwNDM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70) 
在这里也只拿loginTest进行举例

```
public class loginTest {

    @BeforeTest(groups="loginTrue",description = "测试准备工作，获取httpClient对象")
    public void beforeTest(){
        TestConfig.getUserInfoUrl = ConfigFile.getUrl(InterfaceName.GETUSERINFO);
        TestConfig.getUserListUrl = ConfigFile.getUrl(InterfaceName.GETUSERLIST);
        TestConfig.addUserUrl = ConfigFile.getUrl(InterfaceName.ADDUSER);
        TestConfig.loginUrl = ConfigFile.getUrl(InterfaceName.LOGIN);
        TestConfig.updateUserInfoUrl = ConfigFile.getUrl(InterfaceName.UPDATEUSERINFO);

        TestConfig.defaultHttpClient = new DefaultHttpClient();
    }

    @Test(groups = "loginTrue",description = "用户登录接口测试")
    public void loginTrue() throws IOException {
        SqlSession session = DatabaseUtil.getSqlsession();
        LoginCase loginCase = session.selectOne("loginCase",1);
        System.out.println(loginCase.toString());
        System.out.println(TestConfig.loginUrl);
    }


    @Test(description = "用户登录失败接口测试")
    public void loginFalse() throws IOException {
        SqlSession session = DatabaseUtil.getSqlsession();
        LoginCase loginCase = session.selectOne("loginCase",2);
        System.out.println(loginCase.toString());
        System.out.println(TestConfig.loginUrl);
    }
}123456789101112131415161718192021222324252627282930
```

------

到这里，一套完整的从mysql读取测试用例数据到存储在java工程内的配置就写完了 
我们可以执行一下testng.xml文件，可以看到所有case都执行成功，并且在控制台上打印出了所有数据信息以及每个case的url。 
![这里写图片描述](https://img-blog.csdn.net/20180608130240320?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x0MzI2MDMwNDM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
最后查看一下test-output目录下的index.xml文件，用浏览器打开可以看到ExtentReport的结果也生成成功。 
![这里写图片描述](https://img-blog.csdn.net/201806081304395?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x0MzI2MDMwNDM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

# 接口自动化落地（二：HttpClient+testNG实现对接口的测试及校验）

在接口自动化落地（一：MySQL+MyBatis实现对测试用例数据的读取）中已经实现了基础配置和测试用例数据准备，本篇文章将以登录举例进行测试执行。

这是之前login接口的代码

```java
@Test(groups = "loginTrue",description = "用户登录接口测试")
public void loginTrue() throws IOException {
    SqlSession session = DatabaseUtil.getSqlsession();
    LoginCase loginCase = session.selectOne("loginCase",1);
    System.out.println(loginCase.toString());
    System.out.println(TestConfig.loginUrl);
}
```

接下来只需要进行两步：1.发送请求 2.验证返回结果

```java
//第一步发送请求
String result = getResult(loginCase);
//第二步验证返回结果
Assert.assertEquals(loginCase.getExpected(),result);
```

getResult方法被我抽离出来了放在下面，原因是无论我是登录成功，或者登录失败，等会执行相同的操作，只是测试数据和校验部分会不一样。 
这是getResult方法的具体代码:

    private String getResult(LoginCase loginCase) throws IOException {
        HttpPost post = new HttpPost(TestConfig.loginUrl);
        //设置body部分
        JSONObject param = new JSONObject();
        param.put("userName",loginCase.getUserName());
        param.put("password",loginCase.getPassword());
        //设置header部分信息
        post.setHeader("content-type","application/json");
    
        StringEntity entity = new StringEntity(param.toString(),"utf-8");
        post.setEntity(entity);
    
        //测试执行
        String result;
        HttpResponse response  = TestConfig.defaultHttpClient.execute(post);
        result = EntityUtils.toString(response.getEntity(),"utf-8");
        System.out.println(result);
    
        TestConfig.store = TestConfig.defaultHttpClient.getCookieStore();
    
        return result;
    }

获取的结果会返回到result中，然后与mysql中存的预期值loginCase.getExpected()进行对比，如结果一样则测试通过。 

![è¿éåå¾çæè¿°](https://img-blog.csdn.net/20180615160624910?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x0MzI2MDMwNDM0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)