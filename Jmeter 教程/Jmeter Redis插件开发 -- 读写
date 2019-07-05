#  Jmeter Redis插件开发 -- 读写数据

2018年01月31日 11:33:10 [ben大神点C](https://me.csdn.net/PeriodTang) 阅读数 2546



# 背景

最近一段时间在接触性能压测，遇到一个棘手的问题。性能需求在30KQPS，要求进行单接口压测，接口之间依赖不可避免（下一个接口发压数据需要使用上一接口的返回），还不能通过做数据的方式准备。只能将上一接口返回的数据，保存起来，用于下一接口的参数。

在一开始的时候，犯了一个很二的错误，将数据写入到Jmeter的日志中，再进行提取（发压端文件IO影响性能不是一点点），然后将受影响的性能指标作为测试结果（可进行两次测试，第一次，不写日志，性能指标作为测试结果，第二次，写日志，采集的数据作为下一接口的配置元）。

当然了，这样的工作，为什么要每次都做两遍呢，当然可以考虑开发一个插件来做了。首先考虑的就是写内存记录下来。

主要考虑两个方向：

① Jmeter内存

② Redis

其中，Jmeter内存保存，仅在本次测试有效，还需要在测试结束时，将内存中数据，保存到文件。考虑用redis，网上搜索了一把，[jmeter-plugins-redis](https://jmeter-plugins.org/wiki/RedisDataSet/)能满足读取redis数据的需求（不重复造轮子，就用它了），只要自己完成一个写入数据到redis的插件就可以了。

 

# 准备工作

###  

### 下载Redis插件

官方提供的网址：https://jmeter-plugins.org/wiki/RedisDataSet/

下载下来之后，继续下载依赖的jedis版本，官方依赖的是jedis 2.2.1

然鹅，在jmeter 3.0版本下，竟然用不起来，抛出了一票的异常。度娘了一把（请原谅，公司翻墙要自备梯子），是jedis的版本过低，jedis 2.4.1 以上解决了。正好，项目组其他插件依赖2.8.1，索性就用这个版本。

 

###  

### 源码修改

用Jedis 2.8.1后，又抛异常，真是沮丧啊。怎么办？思来想去，改源码，就不信这个邪！

github上一搜索，还真就有。二话不说，下载源码：https://github.com/undera/jmeter-plugins

这位老哥undera，还是很厚道的，插件源码都来了。

 

打开源码，进行编译，提示下面3个函数已经没有了：

![img](https://img-blog.csdn.net/20180131105822013?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUGVyaW9kVGFuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

那就看下怎么修改吧，setMaxActive -> setMaxTotal，setMaxWait -> setMaxWaitMillis，最后一个实在没有找到，先注释掉。

编译，生成jar包，放进去跑一把，界面出来了。读取数据，就算搞定了，下面去开发写数据。

![img](https://img-blog.csdn.net/20180131110407866?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUGVyaW9kVGFuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

 

# 二次开发

由于前面已经下载了源码，而且jmeter插件网站上，提供了源码，怎么配合Redis Data Set的get mode，插入数据。

那就先做简单的，证明这种方式可以用。考虑用后置处理器来解决。

###  

### 简单应用

1、添加数据操作类：RedisDataWriter

2、添加界面显示：RedisDataWriterGUI

想法很简单，先用起来，能在Jmeter上，找到这个后置处理器，能将数据插入到redis里面。连接信息之类的，都先给写死。

跑完第一把，发现数据竟然能写到redis，很满足了。

###  

### 添加界面

有了上面的经验，那就可以慢慢的往上添加界面，优化连接。

RedisDataWriterGUI.java

```java
import com.bilibili.redis.RedisDataWriter;
import org.apache.jmeter.processor.gui.AbstractPostProcessorGui;
import org.apache.jmeter.testelement.TestElement;
 
import java.awt.*;
 
import java.util.List;
 
import javax.swing.BorderFactory;
import javax.swing.Box;
import javax.swing.JComponent;
import javax.swing.JPanel;
 
import org.apache.jmeter.util.JMeterUtils;
import org.apache.jorphan.gui.JLabeledField;
import org.apache.jorphan.gui.JLabeledTextField;
import org.apache.jorphan.gui.JLabeledChoice;
 
public class RedisDataWriterGUI extends AbstractPostProcessorGui{
 
    private JLabeledTextField redisKeyField;
    private JLabeledTextField variableNamesField;
    private JLabeledChoice    addModeField;
 
    private JLabeledTextField redisServerHostField;
    private JLabeledTextField redisServerPortField;
    private JLabeledTextField redisServerTimeoutField;
    private JLabeledTextField redisServerPasswdField;
    private JLabeledTextField redisServerDbField;
 
    private JLabeledTextField redisPoolMinIdleField;
    private JLabeledTextField redisPoolMaxIdleField;
    private JLabeledTextField redisPoolMaxActiveField;
    private JLabeledTextField redisPoolMaxWaitField;
 
    public RedisDataWriterGUI() {
        super();
        init();
    }
 
    @Override
    public void configure(TestElement el) {
        super.configure(el);
        if (el instanceof RedisDataWriter){
            RedisDataWriter re = (RedisDataWriter) el;
            redisKeyField.setText(re.getRedisKey());
            variableNamesField.setText(re.getVariableNames());
            addModeField.setText(re.getRedisAddMode());
            redisServerHostField.setText(re.getRedisHost());
            redisServerPortField.setText(re.getRedisPort());
            redisServerTimeoutField.setText(re.getRedisTimeout());
            redisServerPasswdField.setText(re.getRedisPasswd());
            redisServerDbField.setText(re.getRedisDatabase());
            redisPoolMinIdleField.setText(Integer.toString(re.getRedisMinIdle()));
            redisPoolMaxIdleField.setText(Integer.toString(re.getRedisMaxIdle()));
            redisPoolMaxActiveField.setText(Integer.toString(re.getRedisMaxActive()));
            redisPoolMaxWaitField.setText(Integer.toString(re.getRedisMaxWait()));
 
            redisKeyField.setLabel("Redis key: ");
            variableNamesField.setLabel("Redis value (可以是变量列表, 逗号分隔, 例如：${token},${host}): ");
            addModeField.setLabel("Redis写入模式: ");
            redisServerHostField.setLabel("Redis host: ");
            redisServerPortField.setLabel("Redis port: ");
            redisServerTimeoutField.setLabel("连接超时设置(ms): ");
            redisServerPasswdField.setLabel("连接密码: ");
            redisServerDbField.setLabel("数据库: ");
            redisPoolMinIdleField.setLabel("minIdle: ");
            redisPoolMaxIdleField.setLabel("maxIdle: ");
            redisPoolMaxActiveField.setLabel("maxActive: ");
            redisPoolMaxWaitField.setLabel("maxWait(s):");
        }
    }
 
    /**
     * Implements JMeterGUIComponent.clearGui
     */
    @Override
    public void clearGui() {
        super.clearGui();
 
        redisKeyField.setText(""); //$NON-NLS-1$
        variableNamesField.setText(""); //$NON-NLS-1$
        addModeField.setText("");
        redisServerHostField.setText(""); //$NON-NLS-1$
        redisServerPortField.setText(""); //$NON-NLS-1$
        redisServerTimeoutField.setText("");
        redisServerPasswdField.setText("");
        redisServerDbField.setText("");
        redisPoolMinIdleField.setText("");
        redisPoolMaxIdleField.setText("");
        redisPoolMaxActiveField.setText("");
        redisPoolMaxWaitField.setText("");
    }
 
    @Override
    public TestElement createTestElement() {
        // TODO Auto-generated method stub
        RedisDataWriter extractor = new RedisDataWriter();
        modifyTestElement(extractor);
        return extractor;
    }
 
    @Override
    public String getLabelResource() {
        // TODO Auto-generated method stub
        return this.getClass().getName();
    }
 
    @Override
    public String getStaticLabel() {//设置显示名称
        // TODO Auto-generated method stub
        return "Redis数据录入器";
    }
 
    @Override
    public void modifyTestElement(TestElement extractor) {
        // TODO Auto-generated method stub
        super.configureTestElement(extractor);
        if (extractor instanceof RedisDataWriter) {
            RedisDataWriter r = (RedisDataWriter) extractor;
            r.setRediskey(redisKeyField.getText());
            r.setVariableNames(variableNamesField.getText());
            r.setRedisAddMode(addModeField.getText());
            r.setRedisHost(redisServerHostField.getText());
            r.setRedisPort(redisServerPortField.getText());
            r.setRedisTimeout(redisServerTimeoutField.getText());
            r.setRedisPasswd(redisServerPasswdField.getText());
            r.setRedisDatabase(redisServerDbField.getText());
            r.setRedisMinIdle(Integer.parseInt(redisPoolMinIdleField.getText()));
            r.setRedisMaxIdle(Integer.parseInt(redisPoolMaxIdleField.getText()));
            r.setRedisMaxActive(Integer.parseInt(redisPoolMaxActiveField.getText()));
            r.setRedisMaxWait(Integer.parseInt(redisPoolMaxWaitField.getText()));
        }
 
    }
 
    private void init() {
        setLayout(new BorderLayout());
        setBorder(makeBorder());
        Box box = Box.createVerticalBox();
        box.add(makeTitlePanel());
        box.add(makeRedisDataPanel());
        add(box, BorderLayout.NORTH);
        box.add(makeRedisConnectionPanel());
        box.add(makeRedisPoolPanel());
    }
 
    private JPanel makeRedisDataPanel() {
        redisKeyField = new JLabeledTextField(JMeterUtils.getResString("rediskey_field")); //$NON-NLS-1$
        variableNamesField = new JLabeledTextField(JMeterUtils.getResString("variable_names_field")); //$NON-NLS-1$
        addModeField = new JLabeledChoice(JMeterUtils.getResString("add_mode_field"), false);
        addModeField.addValue("LST_RPUSH");
        addModeField.addValue("SET_ADD");
 
        JPanel panel = new JPanel(new GridBagLayout());
        panel.setBorder(BorderFactory.createTitledBorder("Redis数据配置")); //$NON-NLS-1$
        GridBagConstraints gbc = new GridBagConstraints();
        initConstraints(gbc);
        addField(panel, redisKeyField, gbc);
        resetContraints(gbc);
        addField(panel, variableNamesField, gbc);
        resetContraints(gbc);
        addField(panel, addModeField, gbc);
 
        return panel;
    }
 
    private JPanel makeRedisConnectionPanel() {
        redisServerHostField = new JLabeledTextField(JMeterUtils.getResString("redis_server_host_field")); //$NON-NLS-1$
        redisServerPortField = new JLabeledTextField(JMeterUtils.getResString("redis_server_port_field")); //$NON-NLS-1$
        redisServerTimeoutField = new JLabeledTextField(JMeterUtils.getResString("redis_server_timeout_field"));
        redisServerPasswdField = new JLabeledTextField(JMeterUtils.getResString("redis_server_passwd_field"));
        redisServerDbField = new JLabeledTextField(JMeterUtils.getResString("redis_server_database_field"));
 
        JPanel panel = new JPanel(new GridBagLayout());
        panel.setBorder(BorderFactory.createTitledBorder("Redis连接配置")); //$NON-NLS-1$
        GridBagConstraints gbc = new GridBagConstraints();
        initConstraints(gbc);
        addField(panel, redisServerHostField, gbc);
        resetContraints(gbc);
        addField(panel, redisServerPortField, gbc);
        resetContraints(gbc);
        addField(panel, redisServerTimeoutField, gbc);
        resetContraints(gbc);
        addField(panel, redisServerPasswdField, gbc);
        resetContraints(gbc);
        addField(panel, redisServerDbField, gbc);
 
        return panel;
    }
 
    private JPanel makeRedisPoolPanel() {
        redisPoolMinIdleField = new JLabeledTextField(JMeterUtils.getResString("redis_pool_minidle"));
        redisPoolMaxIdleField = new JLabeledTextField(JMeterUtils.getResString("redis_pool_maxidle"));
        redisPoolMaxActiveField = new JLabeledTextField(JMeterUtils.getResString("redis_pool_maxactive"));
        redisPoolMaxWaitField = new JLabeledTextField(JMeterUtils.getResString("redis_pool_maxwait"));
 
        JPanel panel = new JPanel(new GridBagLayout());
        panel.setBorder(BorderFactory.createTitledBorder("Redis连接池配置")); //$NON-NLS-1$
        GridBagConstraints gbc = new GridBagConstraints();
        initConstraints(gbc);
        addField(panel, redisPoolMinIdleField, gbc);
        resetContraints(gbc);
        addField(panel, redisPoolMaxIdleField, gbc);
        resetContraints(gbc);
        addField(panel, redisPoolMaxActiveField, gbc);
        resetContraints(gbc);
        addField(panel, redisPoolMaxWaitField, gbc);
 
        return panel;
    }
 
    private void addField(JPanel panel, JLabeledField field, GridBagConstraints gbc) {
        List<JComponent> item = field.getComponentList();
        panel.add(item.get(0), gbc.clone());
        gbc.gridx++;
        gbc.weightx = 1;
        gbc.fill=GridBagConstraints.HORIZONTAL;
        panel.add(item.get(1), gbc.clone());
    }
 
    // Next line
    private void resetContraints(GridBagConstraints gbc) {
        gbc.gridx = 0;
        gbc.gridy++;
        gbc.weightx = 0;
        gbc.fill=GridBagConstraints.NONE;
    }
 
    private void initConstraints(GridBagConstraints gbc) {
        gbc.anchor = GridBagConstraints.NORTHWEST;
        gbc.fill = GridBagConstraints.NONE;
        gbc.gridheight = 1;
        gbc.gridwidth = 1;
        gbc.gridx = 0;
        gbc.gridy = 0;
        gbc.weightx = 0;
        gbc.weighty = 0;
    }
}
```

RedisDataWriter.java

```java
/*
 * Created on 2018/01/26
 * @author: ben大神点C
 */
 
package com.bilibili.redis;
 
import java.io.Serializable;
 
import org.apache.jmeter.processor.PostProcessor;
import org.apache.jmeter.samplers.SampleResult;
import org.apache.jmeter.testelement.AbstractScopedTestElement;
import org.apache.jmeter.testelement.TestStateListener;
import org.apache.jmeter.threads.JMeterContext;
import org.apache.jorphan.logging.LoggingManager;
import org.apache.jorphan.util.JOrphanUtils;
import org.apache.log.Logger;
 
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;
import redis.clients.jedis.Protocol;
 
 
public class RedisDataWriter extends AbstractScopedTestElement implements PostProcessor, Serializable, TestStateListener {
    private static final Logger log = LoggingManager.getLoggerForClass();
 
    private static final String REDISKEY = "RedisData.redisKey"; // $NON-NLS-1$
    private static final String VARIABLE_NAMES = "RedisData.variableNames";
    private static final String REDISADDMODE = "RedisData.addMode";
    private static final String REDISHOST = "RedisServer.host";
    private static final String REDISPORT = "RedisServer.port";
    private static final String REDISTIMEOUT = "RedisServer.timeout";
    private static final String REDISPASSWD  = "RedisServer.passwd";
    private static final String REDISDATABSE = "RedisServer.database";
    private static final String REDISMINIDLE = "RedisPool.minIdle";
    private static final String REDISMAXIDLE = "RedisPool.maxIdle";
    private static final String REDISMAXACTIVE = "RedisPool.maxActive";
    private static final String REDISMAXWAIT = "RedisPool.maxWait";
 
    private transient JedisPool pool;
 
    public enum AddMode {
        LST_RPUSH((byte)0),
        SET_ADD((byte)1);
        private byte value;
        private AddMode(byte value) {
            this.value = value;
        }
 
        public byte getValue() {
            return value;
        }
    }
 
    @Override
    public void testStarted(String distributedHost) {
        JedisPoolConfig config = new JedisPoolConfig();
        config.setMaxTotal(getRedisMaxActive());
        config.setMaxIdle(getRedisMaxIdle());
        config.setMinIdle(getRedisMinIdle());
        config.setMaxWaitMillis(getRedisMaxWait()*1000);
 
        String host = getRedisHost();
 
        int port = Protocol.DEFAULT_PORT;
        if(!JOrphanUtils.isBlank(getRedisPort())) {
            port = Integer.parseInt(getRedisPort());
        }
        int timeout = Protocol.DEFAULT_TIMEOUT;
        if(!JOrphanUtils.isBlank(getRedisTimeout())) {
            timeout = Integer.parseInt(getRedisTimeout());
        }
        int database = Protocol.DEFAULT_DATABASE;
        if(!JOrphanUtils.isBlank(getRedisDatabase())) {
            database = Integer.parseInt(getRedisDatabase());
        }
        String password = null;
        if(!JOrphanUtils.isBlank(getRedisPasswd())) {
            password = getRedisPasswd();
        }
        this.pool = new JedisPool(config, host, port, timeout, password, database);
        //System.out.println("testStarted:" + this.pool + "this:" + this);
    }
 
    @Override
    public void testEnded() {
        testEnded("");
    }
 
    @Override
    public void testEnded(String host) {
        pool.destroy();
    }
 
    @Override
    public void testStarted() {
        testStarted("");
    }
 
    @Override
    public Object clone() {
        RedisDataWriter clonedElement = (RedisDataWriter)super.clone();
        clonedElement.pool = this.pool;
        return clonedElement;
    }
 
 
    @Override
    public void process() {
        // TODO Auto-generated method stub
        JMeterContext context = getThreadContext();
        SampleResult previousResult = context.getPreviousResult();
        if (previousResult == null) {
            return;
        }
        log.debug("RedisDataWriter processing result");
 
        try {
            String redisKey = getRedisKey();
            String redisValue = getVariableNames();
 
            Jedis connection = this.pool.getResource();
            //System.out.println("testStarted:" + this.pool + "this:" + this);
 
            try {
                Enum<AddMode> mode = getAddMode();
                if(mode.equals(AddMode.LST_RPUSH)) {
                    connection.rpush(redisKey, redisValue);
                } else {
                    connection.sadd(redisKey, redisValue);
                }
            } finally {
                if (connection != null) {
                    this.pool.returnResource(connection);
                }
            }
 
        } catch (Exception e) {
            e.printStackTrace();
        }
 
    }
 
    public void setRediskey(String redisKey) {
        setProperty(REDISKEY, redisKey);
    }
 
    public String getRedisKey() {
        return getPropertyAsString(REDISKEY);
    }
 
    public void setVariableNames(String variableNames) {
        setProperty(VARIABLE_NAMES, variableNames);
    }
 
    public String getVariableNames() {
        return getPropertyAsString(VARIABLE_NAMES);
    }
 
    public void setRedisHost(String host) {
        setProperty(REDISHOST, host);
    }
 
    public String getRedisHost() {
        return getPropertyAsString(REDISHOST);
    }
 
    public void setRedisPort(String port) {
        setProperty(REDISPORT, port);
    }
 
    public String getRedisPort() {
        String port = getPropertyAsString(REDISPORT);
        if(JOrphanUtils.isBlank(port)) {
            port = Integer.toString(Protocol.DEFAULT_PORT);
        }
        return port;
    }
 
    public void setRedisTimeout(String timeout) {
        setProperty(REDISTIMEOUT, timeout);
    }
 
    public String getRedisTimeout() {
        String timeout = getPropertyAsString(REDISTIMEOUT);
        if (JOrphanUtils.isBlank(timeout)) {
            timeout = Integer.toString(Protocol.DEFAULT_TIMEOUT);
        }
        return timeout;
    }
 
    public void setRedisPasswd(String password) {
        setProperty(REDISPASSWD, password);
    }
 
    public String getRedisPasswd() {
        return getPropertyAsString(REDISPASSWD, null);
    }
 
    public void setRedisDatabase(String db) {
        setProperty(REDISDATABSE, db);
    }
 
    public String getRedisDatabase() {
        String database = getPropertyAsString(REDISDATABSE);
        if (JOrphanUtils.isBlank(database)) {
            database = Integer.toString(Protocol.DEFAULT_DATABASE);
        }
        return database;
    }
 
    public void setRedisAddMode(String mode) {
        for(Enum<AddMode> e : AddMode.values()) {
            final String propName = e.toString();
            //System.out.println("propName:" + propName + ", mode:" + mode + ", name:" + e.name());
            if (mode.equals(propName)) {
                final int tmpMode = e.ordinal();
                if (log.isDebugEnabled()) {
                    log.debug("Converted " + "addMode=" + mode + " to mode=" + tmpMode);
                }
                super.setProperty(REDISADDMODE, e.toString());
                return;
            }
        }
 
        super.setProperty(REDISADDMODE, AddMode.LST_RPUSH.ordinal());
    }
 
    public String getRedisAddMode() {
        return getPropertyAsString(REDISADDMODE, AddMode.LST_RPUSH.toString());
    }
 
    public Enum<AddMode> getAddMode() {
        String mode = getRedisAddMode();
        for(Enum<AddMode> e : AddMode.values()) {
            final String propName = e.toString();
            if (mode.equals(propName)) {
                return e;
            }
        }
        return AddMode.LST_RPUSH;
    }
 
    public void setRedisMinIdle(int minIdle) {
        setProperty(REDISMINIDLE, minIdle);
    }
 
    public int getRedisMinIdle() {
        return getPropertyAsInt(REDISMINIDLE, 0);
    }
 
    public void setRedisMaxIdle(int maxIdle) {
        setProperty(REDISMAXIDLE, maxIdle);
    }
 
    public int getRedisMaxIdle() {
        return getPropertyAsInt(REDISMAXIDLE, 10);
    }
 
    public void setRedisMaxActive(int maxActive) {
        setProperty(REDISMAXACTIVE, maxActive);
    }
 
    public int getRedisMaxActive() {
        return getPropertyAsInt(REDISMAXACTIVE, 500);
    }
 
    public void setRedisMaxWait(int maxWait) {
        setProperty(REDISMAXWAIT, maxWait);
    }
 
    public int getRedisMaxWait() {
        return getPropertyAsInt(REDISMAXWAIT, 30000);
    }
 
}
```

 

看下界面，基本上，需要设置的地方，都可以用了。

① 位置：Sampler -> 后置处理器：

![img](https://img-blog.csdn.net/20180131112205886?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUGVyaW9kVGFuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

② Rdis数据录入器：

![img](https://img-blog.csdn.net/20180131112022701?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUGVyaW9kVGFuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

 

### 优化界面

 

看了下代码，感觉还是有改进的可能性。起码界面这块，可以使用BenInfoSupport。

这里把代码连接贴一下：

https://github.com/eyotang/jmeter-plugins

写起来很简单，界面出起来也很快，还支撑多语言。最主要的是，和RedisDataSet放到同一个jar包里面。

![img](https://img-blog.csdn.net/20180131113002457?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvUGVyaW9kVGFuZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

 

#  

# 测试性能

既然是性能测试使用，那插件本身也需要有一个性能指标吧。

这里使用本地的一个java程序产生的内容，使用**Redis数据录入器**写入单节点的redis中。

峰值：

QPS：62565.8 (6万)，Redis的QPS已达到极限

CPU：33.2%,     redis: 96%~97%,      java：770%~780%

数据：18522114（1852万）（内存：890M）java：1241M

 

 

# 插件下载

 

为了让大家能直接下载到该版本的插件，这里将jedis-2.81和jmeter-plugins-redis都放进去了。

 

下载地址：https://download.csdn.net/download/periodtang/11014735

请不要举报，不然资源又要被删除了。

![img](http://glganltcs.space/metric/?mid=&wid=51824&sid=&tid=5851&rid=BEFORE_OPTOUT_REQ&t=1517368926069)