# [使用Python读写csv文件的三种方法](https://www.cnblogs.com/cloud-ken/p/8432999.html)



## Python读写csv文件

### 觉得有用的话,欢迎一起讨论相互学习~[Follow Me](https://github.com/Asurada2015)

### 前言

> 逗号分隔值（Comma-Separated Values，CSV，有时也称为字符分隔值，因为分隔字符也可以不是逗号），其文件以纯文本形式存储表格数据（数字和文本）。纯文本意味着该文件是一个字符序列，不含必须像二进制数字那样被解读的数据。CSV文件由任意数目的记录组成，记录间以某种换行符分隔；每条记录由字段组成，字段间的分隔符是其它字符或字符串，最常见的是逗号或制表符。通常，所有记录都有完全相同的字段序列.

#### 特点

- 读取出的数据一般为字符类型，如果是数字需要人为转换为数字
- 以行为单位读取数据
- 列之间以半角逗号或制表符为分隔，一般为半角逗号
- 一般为每行开头不空格，第一行是属性列，数据列之间以间隔符为间隔无空格，行之间无空行。
- 

行之间无空行十分重要，如果有空行或者数据集中行末有空格，读取数据时一般会出错，引发[list index out of range]错误。PS：已经被这个错误坑过很多次！使用python I/O写入和读取CSV文件使用PythonI/O写入csv文件以下是将"birthweight.dat"低出生体重的dat文件从作者源处下载下来，并且将其处理后保存到csv文件中的代码。

```python
import csv
import os
import numpy as np
import random
import requests

# name of data file

# 数据集名称

birth_weight_file = 'birth_weight.csv'
## download data and create data file if file does not exist in current directory

## 如果当前文件夹下没有birth_weight.csv数据集则下载dat文件并生成csv文件

if not os.path.exists(birth_weight_file):
    birthdata_url = 'https://github.com/nfmcclure/tensorflow_cookbook/raw/master/01_Introduction/07_Working_with_Data_Sources/birthweight_data/birthweight.dat'
    birth_file = requests.get(birthdata_url)
    birth_data = birth_file.text.split('\r\n')
    # split分割函数,以一行作为分割函数，windows中换行符号为'\r\n',每一行后面都有一个'\r\n'符号。
​    birth_header = birth_data[0].split('\t')
    # 每一列的标题，标在第一行，即是birth_data的第一个数据。并使用制表符作为划分。
​    birth_data = [[float(x) for x in y.split('\t') if len(x) >= 1] for y in birth_data[1:] if len(y) >= 1]
​    print(np.array(birth_data).shape)
    # (189, 9)
    # 此为list数据形式不是numpy数组不能使用np,shape函数,但是我们可以使用np.array函数将list对象转化为numpy数组后使用shape属性进行查看。
​    with open(birth_weight_file, "w", newline='') as f:
    # with open(birth_weight_file, "w") as f:
​        writer = csv.writer(f)
​        writer.writerows([birth_header])
​        writer.writerows(birth_data)
​        f.close()常见错误list index out of range其中我们重点需要讲的是 with open(birth_weight_file, "w", newline='') as f: 这个语句。表示写入csv文件,如果不加上参数 newline='' 表示以空格作为换行符，而是用 with open(birth_weight_file, "w") as f: 语句。则生成的表格中会出现空行。不仅仅是用python I/O进行csv数据的读写时，利用其余方法读写csv数据，或者从网上下载好csv数据集后都需要查看其每行后有没有空格，或者有没有多余的空行。避免不必要的错误~影响数据分析时的判断。使用PythonI/O读取csv文件使用python I/O方法进行读取时即是新建一个List 列表然后按照先行后列的顺序(类似C语言中的二维数组)将数据存进空的List对象中，如果需要将其转化为numpy 数组也可以使用np.array(List name)进行对象之间的转化。birth_data = []
with open(birth_weight_file) as csvfile:
​    csv_reader = csv.reader(csvfile)  # 使用csv.reader读取csvfile中的文件
​    birth_header = next(csv_reader)  # 读取第一行每一列的标题
​    for row in csv_reader:  # 将csv 文件中的数据保存到birth_data中
​        birth_data.append(row)

birth_data = [[float(x) for x in row] for row in birth_data]  # 将数据从string形式转换为float形式

birth_data = np.array(birth_data)  # 将list数组转化成array数组便于查看数据结构
birth_header = np.array(birth_header)
print(birth_data.shape)  # 利用.shape查看结构。
print(birth_header.shape)
#

# (189, 9)

# (9,)

使用Pandas读取CSV文件import pandas as pd

csv_data = pd.read_csv('birth_weight.csv')  # 读取训练数据
print(csv_data.shape)  # (189, 9)
N = 5
csv_batch_data = csv_data.tail(N)  # 取后5条数据
print(csv_batch_data.shape)  # (5, 9)
train_batch_data = csv_batch_data[list(range(3, 6))]  # 取这20条数据的3到5列值(索引从0开始)
print(train_batch_data)

# RACE  SMOKE  PTL

# 184   0.0    0.0  0.0

# 185   0.0    0.0  1.0

# 186   0.0    1.0  0.0

# 187   0.0    0.0  0.0

# 188   0.0    0.0  1.0使用Tensorflow读取CSV文件本人在平时一般都是使用Tensorflow处理各类数据，所以对于使用Tensorflow读取数据在此不过多的进行解释。下面贴上一段代码，如果有不懂的地方可以看我原来的博客内容。tf_train_shuffle_batch函数解析Tensorflow简单CNN实现利用TFRecords存储与读取带标签的图片'''使用Tensorflow读取csv数据'''

filename = 'birth_weight.csv'
file_queue = tf.train.string_input_producer([filename])  # 设置文件名队列，这样做能够批量读取文件夹中的文件
reader = tf.TextLineReader(skip_header_lines=1)  # 使用tensorflow文本行阅读器，并且设置忽略第一行
key, value = reader.read(file_queue)
defaults = [[0.], [0.], [0.], [0.], [0.], [0.], [0.], [0.], [0.]]  # 设置列属性的数据格式
LOW, AGE, LWT, RACE, SMOKE, PTL, HT, UI, BWT = tf.decode_csv(value, defaults)

# 将读取的数据编码为我们设置的默认格式

vertor_example = tf.stack([AGE, LWT, RACE, SMOKE, PTL, HT, UI])  # 读取得到的中间7列属性为训练特征
vertor_label = tf.stack([BWT])  # 读取得到的BWT值表示训练标签

# 用于给取出的数据添加上batch_size维度，以批处理的方式读出数据。可以设置批处理数据大小，是否重复读取数据，容量大小，队列末尾大小，读取线程等属性。

example_batch, label_batch = tf.train.shuffle_batch([vertor_example, vertor_label], batch_size=10, capacity=100, min_after_dequeue=10)

# 初始化Session

with tf.Session() as sess:
    coord = tf.train.Coordinator()  # 线程管理器
    threads = tf.train.start_queue_runners(coord=coord)
    print(sess.run(tf.shape(example_batch)))  # [10  7]
    print(sess.run(tf.shape(label_batch)))  # [10  1]
    print(sess.run(example_batch)[3])  # [ 19.  91.   0.   1.   1.   0.   1.]
    coord.request_stop()
    coord.join(threads)

'''
对于使用所有Tensorflow的I/O操作来说开启和关闭线程管理器都是必要的操作
with tf.Session() as sess:
    coord = tf.train.Coordinator()  # 线程管理器
    threads = tf.train.start_queue_runners(coord=coord)
    #  Your code here~
​    coord.request_stop()
​    coord.join(threads)
'''还有其他使用python读取文件的各种方法，这里介绍三种，不定期进行补充。
```



----------

# [python操作csv文件](https://www.cnblogs.com/fighter007/p/9351688.html)



1.首先我们定义一个函数用于读取csv文件。

![img](https://images2018.cnblogs.com/blog/882871/201807/882871-20180722215206296-351609693.png)

2.将下面的内容写入到csv头部信息，比如  headers = ['编号','课程','讲师'] 。
![img](https://images2018.cnblogs.com/blog/882871/201807/882871-20180722215334028-415014547.png)

3.定义一个变量用来写入csv内容。

![img](https://images2018.cnblogs.com/blog/882871/201807/882871-20180722215424184-647541641.png)

4.我们下面使用with open as 方法进行csv文件的写入.。

![img](https://images2018.cnblogs.com/blog/882871/201807/882871-20180722215503676-2091723264.png)

```
代码解析：
1. csv.reader(f)读取的是f对象，也就是读取出来的内容
2. .writerow(headers)方法是写入头部信息
3. .writerows(rows)写入所有行内容
```

5.主函数入口。

![img](https://images2018.cnblogs.com/blog/882871/201807/882871-20180722215822831-231542181.png)

6.输出结果。

![img](https://images2018.cnblogs.com/blog/882871/201807/882871-20180722215905095-1420438058.png)

7.定义一个函数csv_reader()用来读取csv文件。

![img](https://images2018.cnblogs.com/blog/882871/201807/882871-20180722220053677-339721262.png)

8.使用with open as 方法读取csv文件。

![img](https://images2018.cnblogs.com/blog/882871/201807/882871-20180722220215501-1842303017.png)

9.运行if__name__ == '__main__'主函数。

![img](https://images2018.cnblogs.com/blog/882871/201807/882871-20180722220243786-96962193.png)

10.输出读取的csv结果。

![img](https://images2018.cnblogs.com/blog/882871/201807/882871-20180722220259929-1374380565.png)



