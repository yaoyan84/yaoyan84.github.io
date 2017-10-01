---
layout: post
title:  "python使用openpyxl处理Excel文件"
date:   2017-08-27 23:00:05
categories: Python
tags:  Python  openpyxl Excel
excerpt: python使用openpyxl处理Excel文件。
---

openpyxl模块用来读写Excel文件。openpyxl工作时，在内存中创建Excel工作簿和工作表，然后在工作表中的单元格中进行各种数据编辑和样式编辑操作，或在工作表中绘制图形，最后再保存文件写入到Excel中。

官方文档： [http://openpyxl.readthedocs.io/en/default/](http://openpyxl.readthedocs.io/en/default/ "http://openpyxl.readthedocs.io/en/default/")

# 1. 基本操作
## 1.1. 引入openpyxl库

```python 
import openpyxl
```
 
## 1.2. 打开工作簿（xlsx）文件
 
### 1.2.1. 全新创建工作簿

```python 
wb = openpyxl.Workbook()
```

得到一个wb对象，它就是我们要操作的工作簿文件的对象。

```python 
>>> wb
<openpyxl.workbook.workbook.Workbook object at 0x0000000003543358>
```
 
### 1.2.2. 打开现有xlsx文件：
 
wb = openpyxl.load_workbook("test.xlsx")
 
## 1.3. 保存wb对象到xlsx文件
``` 
wb.save("test.xlsx")
```
>文件名支持路径。

![](/images/posts/2017/08/openpyxl001.png)

以上语句获得的一个空的工作簿，默认含有一个名为“Sheet”的工作表页面。
 
## 1.4.  工作表(sheet)操作
wb对象既是整个xlsx文件在内存中的存在形式，它一般由一个或多个sheet页面组成，我们对xlsx文件的操作，主要就是在sheet页面上操作；我们在Excel软件中，能对sheet页面进行什么操作，基本使用openpyxl也都能完成。

### 1.4.1. 工作表的创建和选定
wb对象创建后，默认含有一个默认的名为 Sheet 的 页面，可以使用active来得到它

``` 
>>> ws1 = wb.active
>>> ws1
<Worksheet "Sheet">
```
 
或使用名称来得到它：  
```
ws1 = wb.get_sheet_by_name('Sheet')  
```
或
```
ws1 = wb["Sheet"]
```
名称有中文要使用unicode
 
也可以使用工作表序号获得该页面： ```ws1 = wb.worksheets[0]```
>注：序号从0开始
 
可以使用 wb.create_sheet() 来创建新的sheet页面。
创建时可以同时关联一个变量。
```
ws2 = wb.create_sheet()
``` 
如：
``` 
import openpyxl
wb = openpyxl.Workbook()
# ws1 = wb.active
ws1 = wb.get_sheet_by_name('Sheet')
ws2 = wb.create_sheet()
wb.create_sheet()
wb.create_sheet()
wb.create_sheet()
ws1['A1'] = "This is a test!"
wb.save("test.xlsx")
``` 
得到文件：

![](/images/posts/2017/08/openpyxl002.png)

对于已有的工作表，我们也可以通过遍历名称、序号等方式拿到相应的对象地址。

```wb.sheetnames``` 是所有工作表sheet页面的名称列表。

### 1.4.2. 工作表命名
工作表sheet可以在创建时候直接指定名字，中文要使用unicode，也可以通过修改对象的title值，来修改名称。

``` 
import openpyxl
wb = openpyxl.Workbook()
# ws1 = wb.active
ws1 = wb.get_sheet_by_name('Sheet')
ws1.title = "Test"
ws2 = wb.create_sheet("ABC")
wb.create_sheet(u"中文表名")
ws1['A1'] = "哇嘎嘎"
wb.save("test.xlsx")
```

![](/images/posts/2017/08/openpyxl003.png)

```
>>> print wb.sheetnames
[u'Test', u'ABC', u'\u4e2d\u6587\u8868\u540d']
``` 

### 1.4.3. 工作表的标签颜色修改

修改工作表的标签的颜色，等价于工作表标签右键菜单中“工作表标签颜色”功能：

![](/images/posts/2017/08/openpyxl004.png)

```
ws1.sheet_properties.tabColor="0000FF"
```

颜色的值为颜色的RRGGBB格式。

``` 
import openpyxl
wb = openpyxl.Workbook()
#ws1 = wb.active
ws1 = wb.get_sheet_by_name('Sheet')
ws1.title = "Test"
ws2 = wb.create_sheet("ABC")
wb.create_sheet(u"中文表名")
ws1.sheet_properties.tabColor="0000FF"
ws2.sheet_properties.tabColor="FF00FF"
ws1['A1'] = "哇嘎嘎"
ws1['B2'] = "嗯~ o(*￣▽￣*)o"
wb.save("test.xlsx")
```

![](/images/posts/2017/08/openpyxl005.png)

### 1.4.4. 删除工作表
 
使用wb.remove_sheet()方法删除工作表
 
wb.remove_sheet(wb.get_sheet_by_name("中文表名"))
wb.remove_sheet(ws2)
 
## 1.5. 单元格操作
 
获得一个工作表对象后，就可以工作表中的单元格进行操作。
 
### 1.5.1. 向单个单元格中写入值
 * 按照Excel单元格定位的传统方法，在指定单元格写入数据，之前例子中已有体现：
   ```
	ws1['A1'] = "哇嘎嘎"
	ws1['B2'] = "嗯~ o(*￣▽￣*)o"
   ```

 * 在指定行、列的单元格中写入数据：
 	```
	ws1.cell(row=3, column=2).value = "AAAAA"
 	```

![](/images/posts/2017/08/openpyxl006.png)

### 1.5.2. 遍历方式向多个单元格中写入值：

有多种的方式可对多个单元格进行选定：
 * 采用iter_cols方法，指定行列范围，对范围内的列进行遍历操作，然后再对列中的单元格操作；
 * 对应有iter_rows方法，与inter_cols区别，是遍历出所有行先；
	```
	for col in ws1.iter_cols(min_row=2, min_col=2, max_row=7, max_col=6):
	    for cell in col:
	```
  * 采用传统的Excel多单元格选择的标记方法，区域选择单元格，遍历与iter_rows相同，返回每行，然后再遍历行中单元格；
	```
	for row in ws2["B2:F7"]:
	    for cell in row:
	``` 

完整示例：

``` 
import openpyxl
wb = openpyxl.Workbook()
ws1 = wb.get_sheet_by_name('Sheet')
ws1.title = "Test"
i = 0
# 遍历方式1：
ws1['A1'] = "遍历方式1"
for col in ws1.iter_cols(min_row=2, min_col=2, max_row=7, max_col=6):
    for cell in col:
        cell.value = i
        i = i + 1
ws2 = wb.create_sheet("Test2")
i = 0
# 遍历方式2：
ws2['A1'] = "遍历方式2"
for row in ws2["B2:F7"]:
    for cell in row:
        cell.value = i
        i = i + 1
wb.save("test.xlsx")
```

![](/images/posts/2017/08/openpyxl007.png)

![](/images/posts/2017/08/openpyxl008.png)

可以看出这两种遍历方式遍历的行列顺序是完全不一样的。

```
import openpyxl
wb = openpyxl.Workbook()
ws1 = wb.get_sheet_by_name('Sheet')
ws1.title = "Test"
i = 0
# 遍历方式1：
ws1['A1'] = "遍历方式1"
print("遍历方式1")
j = 1
for col in ws1.iter_cols(min_row=2, min_col=2, max_row=7, max_col=6):
    print("Cols No." + str(j) + ":  ", end="")
    print(col)
    j = j + 1
    for cell in col:
        cell.value = i
        i = i + 1
ws2 = wb.create_sheet("Test2")
i = 0
# 遍历方式2：
ws2['A1'] = "遍历方式2"
print("遍历方式2")
j = 1
for row in ws2["B2:F7"]:
    print("Row No." + str(j) + ":  ", end="")
    print(row)
    j = j + 1
    for cell in row:
        cell.value = i
        i = i + 1
wb.save("test.xlsx")
```
执行后控制台输出：

```
遍历方式1
Cols No.1:  (<Cell 'Test'.B2>, <Cell 'Test'.B3>, <Cell 'Test'.B4>, <Cell 'Test'.B5>, <Cell 'Test'.B6>, <Cell 'Test'.B7>)
Cols No.2:  (<Cell 'Test'.C2>, <Cell 'Test'.C3>, <Cell 'Test'.C4>, <Cell 'Test'.C5>, <Cell 'Test'.C6>, <Cell 'Test'.C7>)
Cols No.3:  (<Cell 'Test'.D2>, <Cell 'Test'.D3>, <Cell 'Test'.D4>, <Cell 'Test'.D5>, <Cell 'Test'.D6>, <Cell 'Test'.D7>)
Cols No.4:  (<Cell 'Test'.E2>, <Cell 'Test'.E3>, <Cell 'Test'.E4>, <Cell 'Test'.E5>, <Cell 'Test'.E6>, <Cell 'Test'.E7>)
Cols No.5:  (<Cell 'Test'.F2>, <Cell 'Test'.F3>, <Cell 'Test'.F4>, <Cell 'Test'.F5>, <Cell 'Test'.F6>, <Cell 'Test'.F7>)
遍历方式2
Row No.1:  (<Cell 'Test2'.B2>, <Cell 'Test2'.C2>, <Cell 'Test2'.D2>, <Cell 'Test2'.E2>, <Cell 'Test2'.F2>)
Row No.2:  (<Cell 'Test2'.B3>, <Cell 'Test2'.C3>, <Cell 'Test2'.D3>, <Cell 'Test2'.E3>, <Cell 'Test2'.F3>)
Row No.3:  (<Cell 'Test2'.B4>, <Cell 'Test2'.C4>, <Cell 'Test2'.D4>, <Cell 'Test2'.E4>, <Cell 'Test2'.F4>)
Row No.4:  (<Cell 'Test2'.B5>, <Cell 'Test2'.C5>, <Cell 'Test2'.D5>, <Cell 'Test2'.E5>, <Cell 'Test2'.F5>)
Row No.5:  (<Cell 'Test2'.B6>, <Cell 'Test2'.C6>, <Cell 'Test2'.D6>, <Cell 'Test2'.E6>, <Cell 'Test2'.F6>)
Row No.6:  (<Cell 'Test2'.B7>, <Cell 'Test2'.C7>, <Cell 'Test2'.D7>, <Cell 'Test2'.E7>, <Cell 'Test2'.F7>)
```

### 1.5.3. 按行追加数据
 
采用工作表的append()方法，可以向工作表中按行追加数据：

```
import openpyxl
wb = openpyxl.Workbook()
ws1 = wb.get_sheet_by_name('Sheet')
ws1.title = "Test"
DATA = [
    ['第一天', 123, 12, 123, 900, 231, 7],
    ['第二天', 13, 56, 3, 900, 231, 90],
    ['第三天', 216, 38, 37, 543, 55, 376],
    ['第四天', 89, 99, 88, 453, 87, 527]
]
for row in DATA:
    ws1.append(row)
wb.save("test.xlsx")
```

![](/images/posts/2017/08/openpyxl009.png)

### 1.5.4. 加载已有文件，读取数据
已有文件：test2.xlsx
![](/images/posts/2017/08/openpyxl010.png)

```
import openpyxl
wb = openpyxl.load_workbook("test2.xlsx")
ws = wb.get_sheet_by_name('Sheet')
print("数据表最大行数：" + str(ws.max_row))
print("数据表最大列数：" + str(ws.max_column))
DATA = []
# 遍历读取
for row in ws.iter_rows(min_col=1, min_row=1, max_row=ws.max_row, max_col=ws.max_column):
    ROW = []
    for cell in row:
        ROW.append(cell.value)
    DATA.append(ROW)
print("表中读取到的数据：", end="")
print(DATA)
 
# 读取指定位置
B2 = ws['B2'].value
print("B2 = " + B2)
```
执行结果：
```
数据表最大行数：2
数据表最大列数：2
表中读取到的数据：[['测试2', None], [None, 'B列2行']]
B2 = B列2行
``` 
### 1.5.5. 使用公式
 
可以直接在单元格中写入公式：

```
import openpyxl
wb = openpyxl.Workbook()
ws1 = wb.get_sheet_by_name('Sheet')
ws1.title = "Test"
DATA = [
    ['第一天', 123, 12, 123, 900, 231, 7],
    ['第二天', 13, 56, 3, 900, 231, 90],
    ['第三天', 216, 38, 37, 543, 55, 376],
    ['第四天', 89, 99, 88, 453, 87, 527]
]
ws1['A1'] = '这是一个测试用表格'
for row in DATA:
    ws1.append(row)
ws1.append(['合计', '=sum(B2:B5)', '=sum(C2:C5)', '=sum(D2:D5)', '=sum(E2:E5)', '=sum(F2:F5)', '=sum(G2:G5)'])
wb.save("test.xlsx")
```
 
结果：

![](/images/posts/2017/08/openpyxl011.png)


# 2.单元格样式设置
 
单元格样式的控制，依赖openpyxl.style包，其中定义有样式需要的对象，引入样式相关：
```
from openpyxl.styles import PatternFill, Font, Alignment, Border, Side
```
 * PatternFill  填充
 * Font  字体
 * Aignment 对齐
 * Border 边框
 * Side 边线
 
以上五个基本可满足需要
 
基本用法是，将单元格对象的设置的属性赋为新的与默认不同的相应对象。
 
比如设置一个字体对象：

## 2.1. 字体设置
在 **1.5.5.** 的示例中，保存文件前先设置一下标题字体：

``` 
# 样式
font = Font(size=14, bold=True, name='微软雅黑',  color="FF0000")
ws1['A1'].font = font
wb.save("test.xlsx")
```
 
效果：
![](/images/posts/2017/08/openpyxl012.png)

## 2.2. 设置边框
边框Border对象，创建时可指定边框各边的Side对象，根据之前的执行结果，默认都是无边框

``` 
# 样式
font = Font(size=14, bold=True, name='微软雅黑', color="FF0000")
thin = Side(border_style="thin", color="0000FF")
border = Border(left=thin, right=thin, top=thin, bottom=thin)
ws1['A1'].font = font
ws1['A1'].border = border
for row in ws1['A2:G6']:
    for cell in row:
        cell.border = border
wb.save("test.xlsx")
```
 
效果：

![](/images/posts/2017/08/openpyxl013.png)

## 2.3. 对齐设置

``` 
# 对齐
alignment = Alignment(horizontal="center", vertical="center", wrap_text=True)
ws1['A1'].alignment = alignment
``` 
 * wrap_text=True 打开自动换行
 
效果：

![](/images/posts/2017/08/openpyxl014.png)

## 2.4. 合并单元格
 
采用 ```merge_cells``` 方法合并单元格

```
# 对齐
alignment = Alignment(horizontal="center", vertical="center", wrap_text=True)
ws1['A1'].alignment = alignment
# 合并单元格
ws1.merge_cells('A1:G1')
wb.save("test.xlsx")
``` 
效果：

![](/images/posts/2017/08/openpyxl015.png)

## 2.5.填充单元格

```
# 填充
fill = PatternFill(patternType="solid", start_color="33CCFF")
ws1['A1'].fill = fill
for row in ws1.iter_rows(min_row=ws1.max_row, max_col=ws1.max_column):
    for cell in row:
        cell.fill = PatternFill(patternType="solid", start_color="0066FF")
        cell.font = Font(bold=True, color="FFFFFF")
        cell.alignment = Alignment(horizontal="center")
wb.save("test.xlsx")
```
 
效果：

![](/images/posts/2017/08/openpyxl016.png)


----------
以上只是openpyxl的较为基础的用法。还可以直接使用openpyxl在Excel里插入图形，配合一些科学计算和统计的模块使用，以后要用到再深入研究。