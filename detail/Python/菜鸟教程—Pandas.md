https://www.runoob.com/pandas/pandas-tutorial.html

# Pandas 教程

## Pandas 应用

Pandas 的主要数据结构是 Series （一维数据）与 DataFrame（二维数据），这两种数据结构足以处理金融、统计、社会科学、工程等领域里的大多数典型用例。

## 数据结构

**Series** 是一种类似于一维数组的对象，它由一组数据（各种Numpy数据类型）以及一组与之相关的数据标签（即索引）组成。

**DataFrame** 是一个表格型的数据结构，它含有一组有序的列，每列可以是不同的值类型（数值、字符串、布尔型值）。DataFrame 既有行索引也有列索引，它可以被看做由 Series 组成的字典（共同用一个索引）。

# Pandas 安装

pip install pandas

import pandas as pd

# Pandas Series

Pandas Series 类似表格中的一个列（column），类似于一维数组，可以保存任何数据类型。

Series 由索引（index）和列组成，函数如下：

```
pandas.Series( data, index, dtype, name, copy)
```

参数说明：

- **data**：一组数据(numpy的ndarray 类型)。

- **index**：数据索引标签，如果不指定，默认从 0 开始。

- **dtype**：数据类型，默认会自己判断。

- **name**：设置名称。

- **copy**：拷贝数据，默认为 False。

---

  显示：

索引  数据

 数据类型

# Pandas DataFrame

DataFrame 是一个表格型的数据结构，它含有一组有序的列，每列可以是不同的值类型（数值、字符串、布尔型值）。DataFrame 既有行索引也有列索引，它可以被看做由 Series 组成的字典（共同用一个索引）。

![pandas-DataStructure](pic/pandas-DataStructure.png)

DataFrame 构造方法如下：

```
pandas.DataFrame( data, index, columns, dtype, copy)
```

参数说明：

- **data**：一组数据(ndarray、series, map, lists, dict 等类型)。
- **index**：索引值，或者可以称为行标签。
- **columns**：列标签，默认为 RangeIndex (0, 1, 2, …, n) 。
- **dtype**：数据类型。
- **copy**：拷贝数据，默认为 False。

Pandas DataFrame 是一个二维的数组结构，类似二维数组。

index\columns可访问标签值

## 访问数据

```
df = pd.DataFrame(data)
df.loc[1] #访问某行数据
df[["列标签的名称"]] #访问某列数据
```

### iloc

iloc索引器用于按位置进行==基于整数位置==的索引或者选择

~~~python

df.iloc[row selection, column selection]  #某行某列处的数据
test_data_label = test_data.iloc[:, -1].values
~~~

### loc

loc按照==标签==或者索引、布尔值或者条件进行选择数据，这种选择数据的方法较为常用。

~~~python
df.loc[["行标签"]]
df.loc[:,["列标签"]]
~~~



# Pandas CSV

CSV（Comma-Separated Values，逗号分隔值，有时也称为字符分隔值，因为分隔字符也可以不是逗号），其文件以纯文本形式存储表格数据（数字和文本）。

`csv_read = pd.read_csv('*.csv')`

TSV（Tab-Seperated Values），分隔字符为制表符（Tab，‘\t’）

`tsv_read = pd.read_csv('*.csv'.sep='\t')`

Python的csv模块准确的讲应该叫做dsv模块，因为它实际上是支持范式的分隔符分隔值文件（DSV，delimiter-separated values）的。 delimiter参数值默认为半角逗号，即默认将被处理文件视为CSV。 当delimiter='\t'时，被处理文件就是TSV。

# Pandas JSON

# Pandas 数据清洗

