# python解析ini、conf、cfg文件

2015年08月26日 16:10:55 [傻呆](https://me.csdn.net/qq_21398167) 阅读数 12272



版权声明：本文为博主原创文章，未经博主允许不得转载。 https://blog.csdn.net/qq_21398167/article/details/48005003



### 1 使用python自带的ConfigParser模块：

```ini
#test.cfg文件内容：
[sec_a]
a_key1 = 20
a_key2 = 10

[sec_b]
b_key1 = 121
b_key2 = b_value2
b_key3 = $r
b_key4 = 127.0.0.1
```

**读配置文件 ** 


```python
# -* - coding: UTF-8 -* -
import ConfigParser
#生成config对象
conf = ConfigParser.ConfigParser()
#用config对象读取配置文件
conf.read("test.cfg")
#以列表形式返回所有的section
sections = conf.sections()
print 'sections:', sections         #sections: ['sec_b', 'sec_a']
#得到指定section的所有option
options = conf.options("sec_a")
print 'options:', options           #options: ['a_key1', 'a_key2']
#得到指定section的所有键值对
kvs = conf.items("sec_a")
print 'sec_a:', kvs                 #sec_a: [('a_key1', '20'), ('a_key2', '10')]
#指定section，option读取值
str_val = conf.get("sec_a", "a_key1")
int_val = conf.getint("sec_a", "a_key2")

print "value for sec_a's a_key1:", str_val   #value for sec_a's a_key1: 20
print "value for sec_a's a_key2:", int_val   #value for sec_a's a_key2: 10
```

#写配置文件

```python
#更新指定section，option的值
conf.set("sec_b", "b_key3", "new-$r")
#写入指定section增加新option和值
conf.set("sec_b", "b_newkey", "new-value")
#增加新的section
conf.add_section('a_new_section')
conf.set('a_new_section', 'new_key', 'new_value')
#写回配置文件
conf.write(open("test.cfg", "w"))
```

### 2 ConfigParser的一些问题：

1. 不能区分大小写。
2. 重新写入的ini文件不能保留原有 INI文件的注释。
3. 重新写入的ini文件不能保持原有的顺序。
4. 不支持嵌套。
5. 不支持格式校验。

尝试下：configobj模块

```python
#读文件
from configobj import ConfigObj  
    config = ConfigObj(filename)  

#  

value1 = config['keyword1']  
value2 = config['keyword2']  

#  

section1 = config['section1']  
value3 = section1['keyword3']  
value4 = section1['keyword4']  

#  

# you could also write  

value3 = config['section1']['keyword3']  
value4 = config['section1']['keyword4'] 
   
    
#写文件如下：
from configobj import ConfigObj
config = ConfigObj()
config.filename = filename
#
config['keyword1'] = value1
config['keyword2'] = value2
#
config['section1'] = {}
config['section1']['keyword3'] = value3
config['section1']['keyword4'] = value4
#
section2 = {
'keyword5': value5,
'keyword6': value6,
'sub-section': {
'keyword7': value7
}
}
config['section2'] = section2
#
config['section3'] = {}
config['section3']['keyword 8'] = [value8, value9, value10]
config['section3']['keyword 9'] = [value11, value12, value13]
#

## config.write()
```

