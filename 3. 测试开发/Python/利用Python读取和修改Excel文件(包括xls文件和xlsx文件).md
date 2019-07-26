# 利用Python读取和修改Excel文件（包括xls文件和xlsx文件）——基于xlrd、xlwt和openpyxl模块

2018年08月19日 16:28:31 [TheGkeone](https://me.csdn.net/sinat_28576553) 阅读数 16916



 版权声明：版权声明：本文为博主原创文章，未经博主允许不得转载 https://blog.csdn.net/sinat_28576553/article/details/81275650

 

本文介绍一下使用Python对Excel文件的基本操作，包括使用xlrd模块读取excel文件，使用xlwt模块将数据写入excel文件，使用openpyxl模块读取写入和修改excel文件。

------

# 1、使用xlrd模块对xls文件进行读操作

假设我们的表如下，是一个“农村居民家庭人均纯收入和农村居民家庭人均消费情况”的表格。后缀为.xls。里面包含两个工作表，“各省市”和“测试表”。

提一下，一个Excel文件，就相当于一个“工作簿”(workbook)，一个“工作簿”里面可以包含多个“工作表（sheet）”

![img](https://img-blog.csdn.net/20180819025656924?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI4NTc2NTUz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 

## 1.1 获取工作簿对象

引入模块，获得工作簿对象。

```python
import xlrd  #引入模块 
#打开文件，获取excel文件的workbook（工作簿）对象		workbook=xlrd.open_workbook("DataSource/Economics.xls")  #文件路径  
```

## 1.2 获取工作表对象

我们知道一个工作簿里面可以含有多个工作表，当我们获取“工作簿对象”后，可以接着来获取工作表对象，可以通过“索引”的方式获得，也可以通过“表名”的方式获得。

```python
'''对workbook对象进行操作'''
 
#获取所有sheet的名字
names=workbook.sheet_names()
print(names) #['各省市', '测试表']  输出所有的表名，以列表的形式
 
#通过sheet索引获得sheet对象
worksheet=workbook.sheet_by_index(0)
print(worksheet)  #<xlrd.sheet.Sheet object at 0x000001B98D99CFD0>
 
#通过sheet名获得sheet对象
worksheet=workbook.sheet_by_name("各省市")
print(worksheet) #<xlrd.sheet.Sheet object at 0x000001B98D99CFD0>
 
#由上可知，workbook.sheet_names() 返回一个list对象，可以对这个list对象进行操作
sheet0_name=workbook.sheet_names()[0]  #通过sheet索引获取sheet名称
print(sheet0_name)  #各省市
 
```

## 1.3 获取工作表的基本信息

在获得“表对象”之后，我们可以获取关于工作表的基本信息。包括表名、行数与列数。

```python
'''对sheet对象进行操作'''
name=worksheet.name  #获取表的姓名
print(name) #各省市
 
nrows=worksheet.nrows  #获取该表总行数
print(nrows)  #32
 
ncols=worksheet.ncols  #获取该表总列数
print(ncols) #13
```

## 1.4 按行或列方式获得工作表的数据

有了行数和列数，循环打印出表的全部内容也变得轻而易举。

```python
for i in range(nrows): #循环打印每一行
    print(worksheet.row_values(i))  #以列表形式读出，列表中的每一项是str类型
#['各省市', '工资性收入', '家庭经营纯收入', '财产性收入', ………………]
#['北京市', '5047.4', '1957.1', '678.8', '592.2', '1879.0，…………]
 
col_data=worksheet.col_values(0)  #获取第一列的内容
print(col_data)
 for i in range(nrows): 
    #循环打印每一行    
    print(worksheet.row_values(i))  
    #以列表形式读出，列表中的每一项是str类型
    #['各省市', '工资性收入', '家庭经营纯收入', '财产性收入', ………………]
    #['北京市', '5047.4', '1957.1', '678.8', '592.2', '1879.0，…………] 
    col_data=worksheet.col_values(0)  
    #获取第一列的内容print(col_data) 
```

## 1.5 获取某一个单元格的数据

 我们还可以将查询精确地定位到某一个单元格。

在xlrd模块中，工作表的行和列都是从0开始计数的。

```python
#通过坐标读取表格中的数据
cell_value1=sheet0.cell_value(0,0)
cell_value2=sheet0.cell_value(1,0)
print(cell_value1)  #各省市
print(cell_value2)   #北京市
 
cell_value1=sheet0.cell(0,0).value
print(cell_value1) #各省市
cell_value1=sheet0.row(0)[0].value
print(cell_value1)  #各省市#通过坐标读取表格中的数据cell_value1=sheet0.cell_value(0,0)cell_value2=sheet0.cell_value(1,0)print(cell_value1)  #各省市print(cell_value2)   #北京市 cell_value1=sheet0.cell(0,0).valueprint(cell_value1) #各省市cell_value1=sheet0.row(0)[0].valueprint(cell_value1)  #各省市
```

# 2、使用xlwt模块对xls文件进行写操作

## 2.1 创建工作簿

```python
 # 导入xlwt模块
import xlwt
 
#创建一个Workbook对象，相当于创建了一个Excel文件
book=xlwt.Workbook(encoding="utf-8",style_compression=0)
 
'''
Workbook类初始化时有encoding和style_compression参数
encoding:设置字符编码，一般要这样设置：w = Workbook(encoding='utf-8')，就可以在excel中输出中文了。默认是ascii。
style_compression:表示是否压缩，不常用。
'''
```

## 2.2 创建工作表

创建完工作簿之后，可以在相应的工作簿中，创建工作表。

```python
  
# 创建一个sheet对象，一个sheet对象对应Excel文件中的一张表格。
sheet = book.add_sheet('test01', cell_overwrite_ok=True)
# 其中的test是这张表的名字,cell_overwrite_ok，表示是否可以覆盖单元格，其实是Worksheet实例化的一个参数，默认值是False
```

## 2.3 按单元格的方式向工作表中添加数据

```python
 
# 向表test中添加数据
sheet.write(0, 0, '各省市')  # 其中的'0-行, 0-列'指定表中的单元，'各省市'是向该单元写入的内容
sheet.write(0, 1, '工资性收入')
 
#也可以这样添加数据
txt1 = '北京市'
sheet.write(1,0, txt1)  
txt2 = 5047.4
sheet.write(1, 1, txt2)
```

 最后被文件被保存之后，上文语句形成的“工作表”如下所示:

![img](https://img-blog.csdn.net/20180819031031765?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI4NTc2NTUz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 2.4 按行或列方式向工作表中添加数据

为了验证这个功能，我们在工作簿中，再创建一个工作表，上个工作表叫“test01”，那么这个工作表命名为“test02”，都隶属于同一个工作簿。在下面代码中test02是表名，sheet2才是可供操作的工作表对象。

```python
  
#添加第二个表
sheet2=book.add_sheet("test02",cell_overwrite_ok=True)
 
 
Province=['北京市', '天津市', '河北省', '山西省', '内蒙古自治区', '辽宁省',
          '吉林省', '黑龙江省', '上海市', '江苏省', '浙江省', '安徽省', '福建省',
          '江西省', '山东省', '河南省', '湖北省', '湖南省', '广东省', '广西壮族自治区',
          '海南省', '重庆市', '四川省', '贵州省', '云南省', '西藏自治区', '陕西省', '甘肃省',
          '青海省', '宁夏回族自治区', '新疆维吾尔自治区']
 
Income=['5047.4', '3247.9', '1514.7', '1374.3', '590.7', '1499.5', '605.1', '654.9',
        '6686.0', '3104.8', '3575.1', '1184.1', '1855.5', '1441.3', '1671.5', '1022.7',
        '1199.2', '1449.6', '2906.2', '972.3', '555.7', '1309.9', '1219.5', '715.5', '441.8',
        '568.4', '848.3', '637.4', '653.3', '823.1', '254.1']
 
Project=['各省市', '工资性收入', '家庭经营纯收入', '财产性收入', '转移性收入', '食品', '衣着',
         '居住', '家庭设备及服务', '交通和通讯', '文教、娱乐用品及服务', '医疗保健', '其他商品及服务']
 
#填入第一列
for i in range(0, len(Province)):
    sheet2.write(i+1, 0, Province[i])
 
#填入第二列
for i in range(0,len(Income)):
    sheet2.write(i+1,1,Income[i])
 
#填入第一行
for i in range(0,len(Project)):
    sheet2.write(0,i,Project[i])
```

## 2.5 保存创建的文件

最后保存在特定路径即可。

```python
# 最后，将以上操作保存到指定的Excel文件中
book.save('DataSource\\test1.xls')  
```

执行出来的工作表test02如下所示：

![img](https://img-blog.csdn.net/20180819032336728?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI4NTc2NTUz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 

 

#  3、使用openpyxl模块对xlsx文件进行读操作

 

上面两个模块，xlrd和xlwt都是针对Excel97-2003操作的，也就是以xls结尾的文件。很显然现在基本上都是Excel2007以上的版本，以xlsx为后缀。要对这种类型的Excel文件进行操作要使用openpyxl，该模块既可以进行“读”操作，也可以进行“写”操作，还可以对已经存在的文件做修改。

 

## 3.1 获取工作簿对象

```python
import openpyxl
 
#获取 工作簿对象
workbook=openpyxl.load_workbook("DataSource\Economics.xlsx")
#与xlrd 模块的区别
#wokrbook=xlrd.open_workbook(""DataSource\Economics.xls)import openpyxl #获取 工作簿对象workbook=openpyxl.load_workbook("DataSource\Economics.xlsx")#与xlrd 模块的区别#wokrbook=xlrd.open_workbook(""DataSource\Economics.xls)
```

## 3.2 获取所有工作表名

```python
#获取工作簿 workbook的所有工作表
shenames=workbook.get_sheet_names()
print(shenames)  #['各省市', '测试表']
#在xlrd模块中为 sheetnames=workbook.sheet_names()
 
#使用上述语句会发出警告：DeprecationWarning: Call to deprecated function get_sheet_names (Use wb.sheetnames).
#说明 get_sheet_names已经被弃用 可以改用 wb.sheetnames 方法
shenames=workbook.sheetnames
print(shenames)  #['各省市', '测试表']
```

## 3.3 获取工作表对象

上一小节获取的工作表名，可以被应用在这一节中，用来获取工作表对象。

```python
#获得工作簿的表名后，就可以获得表对象
worksheet=workbook.get_sheet_by_name("各省市")
print(worksheet)  #<Worksheet "各省市">
 
#使用上述语句同样弹出警告：DeprecationWarning: Call to deprecated function get_sheet_by_name (Use wb[sheetname]).
#改写成如下格式
worksheet=workbook["各省市"]
print(worksheet)  #<Worksheet "各省市">
 
#还可以通过如下写法获得表对象
worksheet1=workbook[shenames[1]]
print(worksheet1)  #<Worksheet "测试表">#获得工作簿的表名后，就可以获得表对象worksheet=workbook.get_sheet_by_name("各省市")print(worksheet)  #<Worksheet "各省市"> #使用上述语句同样弹出警告：DeprecationWarning: Call to deprecated function get_sheet_by_name (Use wb[sheetname]).#改写成如下格式worksheet=workbook["各省市"]print(worksheet)  #<Worksheet "各省市"> #还可以通过如下写法获得表对象worksheet1=workbook[shenames[1]]print(worksheet1)  #<Worksheet "测试表">
```

## 3.4 根据索引方式获取工作表对象

上一小节获取工作表对象的方式，实际上是通过“表名”来获取，我们可以通过更方便的方式，即通过“索引”方式获取工作表对象。 

```python
#还可以通过索引方式获取表对象
worksheet=workbook.worksheets[0]
print(worksheet) #<Worksheet "各省市">
 
#也可以用如下方式
#获取当前活跃的worksheet,默认就是第一个worksheet
ws = workbook.active
```

## 3.5 获取工作表的属性

得到工作表对象后，可以获取工作表的相应属性，包括“表名”、“行数”、“列数”

```python
#经过上述操作，我们已经获得了第一个“表”的“表对象“，接下来可以对表对象进行操作
 
name=worksheet.title  #获取表名
print(name)  #各省市
#在xlrd中为worksheet.name
 
#获取该表相应的行数和列数
rows=worksheet.max_row
columns=worksheet.max_column
print(rows,columns)  #32 13
#在xlrd中为 worksheet.nrows  worksheet.ncols
```

## 3.6 按行或列方式获取表中的数据

要想以行方式或者列方式，获取整个工作表的内容，我们需要使用到以下两个生成器：

sheet.rows，这是一个生成器，里面是每一行数据，每一行数据由一个元组类型包裹。

sheet.columns，同上，里面是每一列数据。

```python
for row in worksheet.rows:
    for cell in row:
        print(cell.value,end=" ")
    print()
"""
各省市 工资性收入 家庭经营纯收入 财产性收入 转移性收入 食品 衣着 居住 家庭设备及服务 ……
北京市 5047.4 1957.1 678.8 592.2 1879.0 451.6 859.4 303.5 698.1 844.1 575.8 113.1 ……
天津市 3247.9 2707.4 126.4 146.3 1212.6 265.3 664.4 122.4 441.3 315.6 263.2 56.1 ……
……
"""
 
for col in worksheet.columns:
    for cell in col:
        print(cell.value,end=" ")
    print()
 
'''
各省市 北京市 天津市 河北省 山西省 内蒙古自治区 辽宁省 吉林省 黑龙江省 上海市 江苏省 浙江省 ……
工资性收入 5047.4 3247.9 1514.7 1374.3 590.7 1499.5 605.1 654.9 6686.0 3104.8 3575.1 ……
家庭经营纯收入 1957.1 2707.4 2039.6 1622.9 2406.2 2210.8 2556.7 2521.5 767.7 2271.4  ……
……
'''
```

我们可以通过查看sheet.rows 里面的具体格式，来更好的理解代码

```python
  
for row in worksheet.rows:
    print(row)
'''
(<Cell '各省市'.A1>, <Cell '各省市'.B1>, <Cell '各省市'.C1>, <Cell '各省市'.D1>, <Cell '各省市'.E1>,……
(<Cell '各省市'.A2>, <Cell '各省市'.B2>, <Cell '各省市'.C2>, <Cell '各省市'.D2>, <Cell '各省市'.E2>, ……
……
'''
#可知，需要二次迭代
 
for row in worksheet.rows:
    for cell in row:
        print(cell,end=" ")
    print()
 
'''
<Cell '各省市'.A1> <Cell '各省市'.B1> <Cell '各省市'.C1> <Cell '各省市'.D1>……
<Cell '各省市'.A2> <Cell '各省市'.B2> <Cell '各省市'.C2> <Cell '各省市'.D2> ……
……
'''
#还需要cell.value
for row in worksheet.rows:
    for cell in row:
        print(cell.value,end=" ")
    print()
```

## 3.7 获取特定行或特定列的数据

上述方法可以迭代输出表的所有内容，但是如果要获取特定的行或列的内容呢？我们可以想到的是用“索引”的方式，但是sheet.rows是生成器类型，不能使用索引。所以我们将其转换为list之后再使用索引，例如用list(sheet.rows)[3]来获取第四行的tuple对象。

```python
#输出特定的行
for cell in list(worksheet.rows)[3]:  #获取第四行的数据
    print(cell.value,end=" ")
print()
#河北省 1514.7 2039.6 107.7 139.8 915.5 167.9 531.7 115.8 285.7 265.4 166.3 47.0
 
#输出特定的列
for cell in list(worksheet.columns)[2]:  #获取第三列的数据
    print(cell.value,end=" ")
print()
#家庭经营纯收入 1957.1 2707.4 2039.6 1622.9 2406.2 2210.8 2556.7 2521.5 767.7 2271.4 3084.3……
 
#已经转换成list类型，自然是从0开始计数。
```

## 3.8 获取某一块的数据

有时候我们并不需要一整行或一整列内容，那么可以通过如下方式获取其中一小块的内容。

注意两种方式的区别，在第一种方式中，由于生成器被转换成了列表的形式，所以索引是从0开始计数的。

而第二种方式，行和列都是从1开始计数，这是和xlrd模块中最大的不同，在xlrd中行和列都是从0计数的，openpyxl之所这么做是为了和Excel表统一，因为在Excel表，就是从1开始计数。

```python
for rows in list(worksheet.rows)[0:3]:
    for cell in rows[0:3]:
        print(cell.value,end=" ")
    print()
'''
各省市 工资性收入 家庭经营纯收入 
北京市 5047.4 1957.1 
天津市 3247.9 2707.4 
'''
 
for i in range(1, 4):
    for j in range(1, 4):
        print(worksheet.cell(row=i, column=j).value,end=" ")
    print()
'''
各省市 工资性收入 家庭经营纯收入 
北京市 5047.4 1957.1 
天津市 3247.9 2707.4 
'''
```

## 3.9 获取某一单元格的数据

有两种方式。

```python
#精确读取表格中的某一单元格
content_A1= worksheet['A1'].value
print(content_A1)
 
content_A1=worksheet.cell(row=1,column=1).value
#等同于 content_A1=worksheet.cell(1,1).value
print(content_A1)
#此处的行数和列数都是从1开始计数的，而在xlrd中是由0开始计数的
```

 

 

# 4、使用openpyxl模块对xlsx文件进行写操作

## 4.1 创建工作簿和获取工作表

同样的workbook=openpyxl.Workbook() 中“W”要大写。

```python
import openpyxl
 
# 创建一个Workbook对象，相当于创建了一个Excel文件
workbook=openpyxl.Workbook()
#wb=openpyxl.Workbook(encoding='UTF-8')
 
#获取当前活跃的worksheet,默认就是第一个worksheet
worksheet = workbook.active
worksheet.title="mysheet"
```

## 4.2 创建新的工作表

```python
worksheet2 = workbook.create_sheet()   #默认插在工作簿末尾
#worksheet2 = workbook.create_sheet(0)  #插入在工作簿的第一个位置
worksheet2.title = "New Title"
```

## 4.3 将数据写入工作表

```ruby
#以下是我们要写入的数据
Province=['北京市', '天津市', '河北省', '山西省', '内蒙古自治区', '辽宁省',
          '吉林省', '黑龙江省', '上海市', '江苏省', '浙江省', '安徽省', '福建省',
          '江西省', '山东省', '河南省', '湖北省', '湖南省', '广东省', '广西壮族自治区',
          '海南省', '重庆市', '四川省', '贵州省', '云南省', '西藏自治区', '陕西省', '甘肃省',
          '青海省', '宁夏回族自治区', '新疆维吾尔自治区']
 
Income=['5047.4', '3247.9', '1514.7', '1374.3', '590.7', '1499.5', '605.1', '654.9',
        '6686.0', '3104.8', '3575.1', '1184.1', '1855.5', '1441.3', '1671.5', '1022.7',
        '1199.2', '1449.6', '2906.2', '972.3', '555.7', '1309.9', '1219.5', '715.5', '441.8',
        '568.4', '848.3', '637.4', '653.3', '823.1', '254.1']
 
Project=['各省市', '工资性收入', '家庭经营纯收入', '财产性收入', '转移性收入', '食品', '衣着',
         '居住', '家庭设备及服务', '交通和通讯', '文教、娱乐用品及服务', '医疗保健', '其他商品及服务']
 
#写入第一行数据，行号和列号都从1开始计数
for i in range(len(Project)):
    worksheet.cell(1, i+1,Project[i])
 
#写入第一列数据，因为第一行已经有数据了，i+2
for i in range(len(Province)):
    worksheet.cell(i+2,1,Province[i])
 
#写入第二列数据
for i in range(len(Income)):
    worksheet.cell(i+2,2,Income[i])#以下是我们要写入的数据Province=['北京市', '天津市', '河北省', '山西省', '内蒙古自治区', '辽宁省',          '吉林省', '黑龙江省', '上海市', '江苏省', '浙江省', '安徽省', '福建省',          '江西省', '山东省', '河南省', '湖北省', '湖南省', '广东省', '广西壮族自治区',          '海南省', '重庆市', '四川省', '贵州省', '云南省', '西藏自治区', '陕西省', '甘肃省',          '青海省', '宁夏回族自治区', '新疆维吾尔自治区'] Income=['5047.4', '3247.9', '1514.7', '1374.3', '590.7', '1499.5', '605.1', '654.9',        '6686.0', '3104.8', '3575.1', '1184.1', '1855.5', '1441.3', '1671.5', '1022.7',        '1199.2', '1449.6', '2906.2', '972.3', '555.7', '1309.9', '1219.5', '715.5', '441.8',        '568.4', '848.3', '637.4', '653.3', '823.1', '254.1'] Project=['各省市', '工资性收入', '家庭经营纯收入', '财产性收入', '转移性收入', '食品', '衣着',         '居住', '家庭设备及服务', '交通和通讯', '文教、娱乐用品及服务', '医疗保健', '其他商品及服务'] #写入第一行数据，行号和列号都从1开始计数for i in range(len(Project)):    worksheet.cell(1, i+1,Project[i]) #写入第一列数据，因为第一行已经有数据了，i+2for i in range(len(Province)):    worksheet.cell(i+2,1,Province[i]) #写入第二列数据for i in range(len(Income)):    worksheet.cell(i+2,2,Income[i])
```

## 4.4 保存工作簿

```python
workbook.save(filename='DataSource\\myfile.xlsx')
```

最后运行结果如下所示：

![img](https://img-blog.csdn.net/20180819160434398?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI4NTc2NTUz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 

 

# 5、修改已经存在的工作簿(表)

## 5.1 插入一列数据

将第四节中最后保存的myfile.xlsx作为我们要修改的表格，我们计划在最前面插入一列“编号”，如下所示：

```python
import openpyxl
 
workbook=openpyxl.load_workbook("DataSource\myfile.xlsx")
worksheet=workbook.worksheets[0]
 
#在第一列之前插入一列
worksheet.insert_cols(1)  #
 
for index,row in enumerate(worksheet.rows):
    if index==0:
        row[0].value="编号"  #每一行的一个row[0]就是第一列
    else:
        row[0].value=index
#枚举出来是tuple类型，从0开始计数
 
workbook.save(filename="DataSource\myfile.xlsx")
```

运行结果如下：

![img](https://img-blog.csdn.net/20180819161535496?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI4NTc2NTUz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 5.2 修改特定单元格

```python
worksheet.cell(2,3,'0')
worksheet["B2"]="Peking"
```

运行结果如下：

![img](https://img-blog.csdn.net/20180819161952513?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI4NTc2NTUz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

## 5.3 批量修改数据

批量修改数据就相当于写入，会自动覆盖。在上一节中已经有介绍，不再赘述。

还有sheet.append()方法，可以用来添加行。

```python
taiwan=[32,"台湾省"]
worksheet.append(taiwan)
```

运行结果如下：

![img](https://img-blog.csdn.net/20180819162526547?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzI4NTc2NTUz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

 

 

本文完。行笔匆忙，如有错误，还请指出。



---------------

# python 读写excel xlsx与xls

2018年10月25日 08:54:18 [m0_37981829](https://me.csdn.net/m0_37981829) 阅读数 118



xlwt 对应xls
xlsxwriter 对应xlsx
def letter_to_number(self, letter_var):
letter_var = letter_var.strip().lower()
re = ‘’
for i in letter_var:
index = string.ascii_lowercase.index(i) + 1
if index > 9:
index = chr(65 + (index - 10))

```python
        re += str(index)
        
    return re
        
def read_excel(self, filename, begin_cr=None, target_r=None, target_c=None, as_header=True):
    """read_excel('test.xlsx', 'A1', 6, 'AB')"""
    file_path = os.path.join(ROOT_DIR, filename)
    #file_path = filename
    with xlrd.open_workbook(file_path) as book:
        def cell_ctype(cell, workbook):
            if 0 == cell.ctype:
                return None
            elif 1 == cell.ctype:
                return cell.value
            elif 2 == cell.ctype:
                return int(cell.value) if str(cell.value).endswith('.0') else float(cell.value)
            elif 3 == cell.ctype:
                return datetime(*xlrd.xldate_as_tuple(cell.value,workbook.datemode)).strftime('%Y-%m-%d %H:%M:%S')
            elif 4 == cell.ctype:
                return True if cell.value else False
            
            return cell.value
        
        temp = {}
        for sheet_name in book.sheet_names():
            sheet = book.sheet_by_name(sheet_name)
            row_num = sheet.nrows
            col_num = sheet.ncols
            if col_num < 1 or row_num < 1:
                continue
            
            temp[sheet_name] = []
            temp_header = []
            header_r = 0
            header_c = 1
            if begin_cr and as_header:
                header_r = int(re.findall(r'\d+', begin_cr)[0])
                header_c = int(self.nbase_to_decimal(self.letter_to_number(re.findall(r'\D+', begin_cr)[0]), 26))
                temp_header = [cell_ctype(sheet.cell(header_r-1, i), book) for i in range(header_c-1, col_num)]
            elif begin_cr and not as_header:
                header_r = int(re.findall(r'\d+', begin_cr)[0]) - 1
                header_c = int(self.nbase_to_decimal(self.letter_to_number(re.findall(r'\D+', begin_cr)[0]), 26))
                
            temp_merged = [[(i, j) for i in range(m_cells[0], m_cells[1]) \
                            for j in range(m_cells[2], m_cells[3])] for m_cells in sheet.merged_cells]
            
            for i in range(header_r, row_num):
                if 0 != len(temp_header):
                    temp_row = {}
                    for j in range(header_c-1, col_num):
                        temp_row[temp_header[j-header_c+1]] = cell_ctype(sheet.cell(i, j), book)
                    
                    temp[sheet_name].append(temp_row)
                else:
                    temp_row = [cell_ctype(sheet.cell(i, j), book) for j in range(header_c-1, col_num)]
                    temp[sheet_name].append(temp_row)
              
            for m_cells in temp_merged:
                merded_value = None
                for m_cell in m_cells:
                    m_cell_r = m_cell[0]-1 if 0 != len(temp_header) else m_cell[0]
                    m_cell_c = temp_header[m_cell[1]] if 0 != len(temp_header) else m_cell[1]
                    if 0 == m_cells.index(m_cell):
                        merded_value = temp[sheet_name][m_cell_r][m_cell_c]
                    
                    temp[sheet_name][m_cell_r][m_cell_c] = merded_value
            
            if target_r and not target_c:
                return temp[sheet_name][target_r-1-header_r]
            
            if target_c and not target_r:
                target_c = self.nbase_to_decimal(self.letter_to_number(target_c), 26)
                m_c = temp_header[target_c-header_c] if 0 != len(temp_header) else target_c-header_c
                target_cels = [r[m_c] for r in temp[sheet_name]]
                if 0 != len(temp_header): 
                    target_cels.insert(0, m_c)
                    
                return target_cels
            
            if target_c and target_r:
                target_c = self.nbase_to_decimal(self.letter_to_number(target_c), 26)
                m_c = temp_header[target_c-header_c] if 0 != len(temp_header) else target_c-header_c
                return temp[sheet_name][target_r-1-header_r][m_c]
                
        return temp
    return None

def write_excel(self, filename, data={}, header=None, begin_cr=None, merged_cells=None):
    """filename = 'test.xls' or 'test.xlsx'
       data = {'sheet_name': [[],...]}
       header = {'sheet_name': ['a',...]}
       begin_cr = {'sheet_name': 'A1'}
       merged_cells = {'sheet_name': ['A1:B2',...]} #row 1-2 col A-B merged"""
    file_path = os.path.join(ROOT_DIR, filename)
    #file_path = filename
    xls_m = True if file_path.endswith('.xls') else False
    try:
        def set_xlwt_style(content='pattern: pattern solid, fore_colour gray_ega; font: bold on; alignment: horz center;'):
            return xlwt.easyxf(content)
        
        def set_xlsxwriter_style(workbook, content={'align': 'center','bold': 1,'bg_color':'gray'}):
            return workbook.add_format(content)
        
        book = xlwt.Workbook() if xls_m else xlsxwriter.Workbook(file_path)
        for sheet_name in data:
            sheet = book.add_sheet(sheet_name, cell_overwrite_ok=True) if xls_m else \
                    book.add_worksheet(sheet_name)
                    
            header_r = 0
            header_c = 0
            if begin_cr and begin_cr.has_key(sheet_name):
                header_r = int(re.findall(r'\d+', begin_cr[sheet_name])[0]) -1
                header_c = int(self.nbase_to_decimal(self.letter_to_number(re.findall(r'\D+', begin_cr[sheet_name])[0]), 26)) - 1
                
            begin_r = 0
            if header and header.has_key(sheet_name):
                begin_r += 1
                set_style = set_xlwt_style() if xls_m else set_xlsxwriter_style(book)
                [sheet.write(header_r, j+header_c, header[sheet_name][j], set_style) for j in range(len(header[sheet_name]))]
                
            for i in range(len(data[sheet_name])):
                temp = data[sheet_name][i]
                [sheet.write(i+begin_r+header_r, j+header_c, temp[j]) for j in range(len(temp))]
            
            if merged_cells and merged_cells.has_key(sheet_name):
                m_cells = merged_cells[sheet_name]
                for mcells in m_cells:
                    mcells = mcells.split(':')
                    cell_r1 = int(re.findall(r'\d+', mcells[0])[0]) - 1
                    cell_c1 = int(self.nbase_to_decimal(self.letter_to_number(re.findall(r'\D+', mcells[0])[0]), 26)) - 1
                    cell_r2 = int(re.findall(r'\d+', mcells[1])[0]) - 1
                    cell_c2 = int(self.nbase_to_decimal(self.letter_to_number(re.findall(r'\D+', mcells[1])[0]), 26)) - 1
                    
                    cell_value = data[sheet_name][cell_r1-begin_r-header_r][cell_c1-header_c]
                    set_style = xlwt.Style.default_style if xls_m else None
                    if cell_r1-begin_r-header_r < 0:
                        cell_value = header[sheet_name][cell_c1-header_c]
                        set_style = set_xlwt_style() if xls_m else set_xlsxwriter_style(book)
                        
                    if xls_m:
                        sheet.write_merge(cell_r1, cell_r2, cell_c1, cell_c2, cell_value, set_style)
                    else:
                        sheet.merge_range(mcells[0] + ':' + mcells[1], cell_value, set_style)
               
        if 0 == len(data):
            sheet = book.add_sheet('sheet', cell_overwrite_ok=True)
            
        if xls_m:
            book.save(file_path)
        else:
            book.close()
            
        return True
    except Exception, e:
        pass
    
    return False

```



-----------

## Python通过pandas读写Excel文件，同时支持xls和xlsx



本质上pandas的表对象是一个字典，所以对字典怎么操作就怎么操作pandas。

经测试，pandas可以直接读取xls或者xlsx格式的文件，不需要额外处理，且读取日期格式数据时，也可以自动识别，比较方便。

```python
xlswj=r'C:\Users\测试机\Desktop\测试\测试文件.xlsx'

import pandas as pd                                #载入模块，简化命名为pd

xls_file=pd.ExcelFile(xlswj)                      #使用pandas读取excel文件

print(xls_file.sheet_names)                      #显示出读入excel文件中的表名字,一个列表

table1=xls_file.parse('Sheet1')                #可以用表名

table2=xls_file.parse(0)                          #也可以用表的序号

print(list(table1)[3])                              #输出表头,并可以指定
```

**以上不好用，没找到更多资料，换个方法**

```python
input_file =r'C:\Users\测试机\Desktop\测试\测试文件.xlsx'

data_frame = pd.read_excel(input_file, sheetname='Sheet1')#读取测试文件的Sheet1   另有可选参数,header=None参数是代表没标题行，因为默认载入时，excel表的第一行是标题行。（及pandas表结构中的列的索引名称）

print(data_frame.head(2))#看前2行数据，可以直接用head()，默认是5行

print(data_frame['序号'][1])#访问标题为“序号”的列的第2行数据，字典计数都是从0开始，所以1代表2。

print(data_frame[0][0])#当载入时启用了hearder=None参数时此方法可用，第一个代表列数，第二个代表行数

print(len(data_frame)) #输出行数，方法1

print(data_frame.iloc[:,0].size)#输出行数，方法2

print(data_frame.columns.size)#输出列数，方法1

print(len(data_frame.columns))#输出列集合的长度，即输出列数，方法2

print(data_frame.ix[[2]].values[0][3])#第三行第4列的值，不推荐使用方式，因为ix方式已经不被推荐。

print(data_frame.iloc[[2]].values[0][3])#第三行第4列的值  字典取数，第一个集合中的第四个值

"""

loc、iloc、ix三者差异：

loc——通过行标签索引行数据 

iloc——通过行号索引行数据 

ix——通过行标签或者行号索引行数据（基于loc和iloc 的混合）,但是ix现在已经不建议使用 """

print(data_frame.columns)#输出表的标题栏

\#新插入列开始

col_name = data_frame.columns.tolist()#先读取原有的标题栏，转为一个列表。

col_name.insert(col_name.index('序号')+1,'电话号') # 在“序号” 列后面插入一个新的列，列名称为“电话号”

\#不指定位置直接插入多列

data_frame=pd.concat([data_frame, pd.DataFrame(columns=list(['测试列1','测试列2']))])#插入测试列1，测试列2，位置不确定，依列名自行排序。

data_frame=data_frame.reindex(columns=col_name,fill_value=0)#重新更新索引值（即列标题行），不加参数（fill_value=0）就是NaN，此时，完成新列插入

\#移动列方法开始

col_name = data_frame.columns.tolist()#先读取原有的标题栏，转为一个列表。

\#某列插入到另外一列前面，纯数字的标准写法

item=col_name.pop(1)#将第二列弹出

col_name.insert(0, item)#再将第二列插入到第一列前面

data_frame=data_frame.reindex(columns=col_name)#最后更新下表的索引。完成。

\#某列插入到另外一列前面，用列名的标准写法，此方法最好列名没有重复。

item=col_name.pop(col_name.index('电话号'))#当标题栏没有重复名称时候，可以如下使用。

col_name.insert(col_name.index('时间'), item)#将“电话”列插入到“时间”列前面

data_frame=data_frame.reindex(columns=col_name)#最后更新下表的索引。完成

print(data_frame)#输出整个表对象

print('%s%s' % ('第三行索引值为：',data_frame.iloc[[2]].index.values[0]))#第3行的索引值,用格式化方式合并，可以不管数据类型。

\#删除列

\#也可以直接在原DataFrame上原地删除：

DataFrame= DataFrame.drop('ID', axis=1, inplace=True)#删除名称为ID的列，不加参数, inplace=True，则新生成一个表对象。

\#在末尾插入一行

hs=len(data_frame)

data_frame.loc[hs]=['2-1','2-2','2-3','2-4']

\#在末尾插入一列

ls=len(data_frame)

data_frame[ls]=['2-1']

\#输出部分指定列

print(pd.DataFrame(data_frame_gs,columns = ['地址','序号']))

\#将运算完成的表对象重新输出为excel文件。

data_frame.to_excel(r'C:\Users\测试机\Desktop\测试\测试文件2.xlsx', index=False) 

\#多个dataframe怎样to_excel到不同的sheet里？

writer = pd.ExcelWriter('a.xlsx')#先定义写入的文件名

data_frame1.to_excel(writer, sheet_name='Sheet1')#表1写入sheet1

data_frame2.to_excel(writer, sheet_name='Sheet2')#依次将表2写入sheet2

writer.save()#写完后保存
```



### 可用参考文献

Python使用pandas读取Excel文件数据和预处理小案例

https://blog.csdn.net/oh5W6HinUg43JvRhhB/article/details/78153553

pandas的行列名更改与数据选择

https://blog.csdn.net/zhang_diandian/article/details/79903592

浅析Python中的多条件排序实现

https://m.jb51.net/article/86040.htm