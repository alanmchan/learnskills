# pandas

## 概述

Python Data Analysis Library 或 pandas

是基于numpy的一种工具，该工具是为了解决数据分析而创建的，pandas纳入了大量数据库和一些标准的数据模型，提供了高效地操作大型数据集所需的工具。

numpy能够帮助我们处理数值，但是pandas除了处理数据之外（基于numpy），还能帮助我们除了其他类型的数据

数据分类：

- 结构化数据，可以用二维表结构来逻辑表达实现的数据
- 非结构化数据，图片、音频、视频、办公文档等
- 半结构化数据，介于完全结构化数据和非结构化数据之间，如xml，json



## pandas核心数据结构

### Series

可以理解为一个一维带标签的数组，只是index可以改动，类似于定长的有序字典，有index和value

```python
import pd

# 创建Series
s1 = pd.Series(data=[3, -5, 7, 4])
print(s1)
# 0    3
# 1   -5
# 2    7
# 3    4
# dtype: int64

# 创建Series 并指定索引 注意索引和数据长度相同
s2 = pd.Series(data=[3, -5, 7, 4], index=list('ABCD'))
print(s2)
# A    3
# B   -5
# C    7
# D    4
# dtype: int64

# 借助字典创建Series, key ----> 索引，value ----> 值
d = {'name':'Alan', 'age':20, 'score':100}
s3 = pd.Series(d)
print(s3)
# name     Alan
# age        20
# score     100
# dtype: object

# 字典推导式创建字典
import string
a = {string.ascii_uppercase[i]:i for i in range(10)}
s4 = pd.Series(a)
print(s4)
# A    0
# B    1
# C    2
# D    3
# E    4
# F    5
# G    6
# H    7
# I    8
# J    9
# dtype: int64
```

说明：

- index参数默认从0开始的整数，也是Series的绝对位置，即使index被赋值后，绝对位置也不会被改变
- index可以修改，绝对位置不可更改



#### Series 属性

- index 标签
- values 值
- dtype 数据类型

```python
# Series属性
print(s3.index)
Index(['name', 'age', 'score'], dtype='object')

print(s3.values)
['Alan' 20 100]
# Sereis的vaules属性是ndarray，ndarray的很多方法都可以运用于series类型，比如argmax、clip，但是Series的where方法和ndarray结果不同

print(s3.dtype)
object
```



#### Series 索引 切片

```python
# Series 索引 切片
print(s3['age'])  # 借助标签
20

print(s3[1])  # 借助绝对位置
20

print(s3[:3])  # 切片取多个值
name     Alan
age        20
score     100
dtype: object

print(s3[[0, 2]])  # 列表取多个值
name     Alan
score     100
dtype: object

print(s3[['name', 'age']])  # 列表取多个值
name    Alan
age       20
dtype: object
    
# bool 索引
print(s4[s4 > 4])
F    5
G    6
H    7
I    8
J    9
dtype: int64
```

注意：通过列表和切片取出来的数据仍然是Series对象



### DataFrame

DataFrame可以理解为一个二维数组，index有两个维度，可以更改。可以看成Series容器，DataFrame的单行单列数据为一个Series。

```python
import pandas as pd
import numpy as np

# 创建DataFrame对象
df1 = pd.DataFrame(np.arange(12).reshape(3, 4))
print(df1)
   0  1   2   3
0  0  1   2   3
1  4  5   6   7
2  8  9  10  11

# 指定行索引和列索引
df2 = pd.DataFrame(np.arange(12).reshape(3,4), index=list("abc"), columns=list("WXYZ"))
print(df2)
   W  X   Y   Z
a  0  1   2   3
b  4  5   6   7
c  8  9  10  11
```

DataFrame对象既有行索引，又有列索引

- 行索引，表明不同行，横向索引，称为index，0轴，axis=0
- 列索引，表明不同列，纵向索引，称为columns，1轴，axis=1



#### DataFrame 属性

- index 行索引
- columns 列索引
- values 数据元素
- dtype 数据类型
- shape 数据形状（行列数目）
- size 元素个数
- ndim 维度数

```python
# 创建DataFrame
data = [
    ['Belglum', 'Brussels', 11190846],
    ['Indla', 'New Delhi', 1303171035],
    ['Brazil', 'Brasilia', 207847528]
]

df = pd.DataFrame(data, index=[1, 2, 3], columns=['Country', 'Capital', 'Population'])
print(df)
#    Country    Capital  Population
# 1  Belglum   Brussels    11190846
# 2    Indla  New Delhi  1303171035
# 3   Brazil   Brasilia   207847528
```



## 增删改查

### Series增删改查

```python
s1 = pd.Series(data=[90, 86, 70], index=['Leo', 'Kate', 'John'])

# Series 增删改查
# 通过绝对位置查找指定元素
print(s1[0])

# 通过标签查找指定元素
print(s1['Leo'])

# 通过列表查找指定元素
print(s1[[0, 1]])
print(s1[['Leo', 'Kate']])

# 通过表达式查找指定元素
print(s1[s1>80])
# 通过列表和表达式查找出来的数据还是Series类型
```



### DataFrame增删改查

```python
# 创建时间索引
date = pd.date_range(start='20100101', periods=6)
# 创建DataFrame对象
df = pd.DataFrame(index=date, columns=list('abcd'), data=np.random.randn(6, 4))

# 单列数据
df.a
df['a']

# 多列数据
df[['a', 'b']]

# 利用切片访问行
df[1:2]
df[:3]

# 利用head访问开始的几行，缺省为5
df.head()
# 利用tail访问末尾的几行，缺省为5
df.tail(3)
```

| 方法   | 说明                                     | 语法                                              |
| ------ | ---------------------------------------- | ------------------------------------------------- |
| loc[]  | 针对DataFrame索引名称的切片方法          | `df.loc[行索引名称或条件，列索引名称]`            |
| iloc[] | 针对DataFrame的绝对位置                  | `df.iloc[行索引绝对位置，列索引绝对位置]`         |
| ix[]   | 既可以接收索引名称也可以接收索引绝对位置 | `df.ix[行索引名称或位置或条件，列索引名称或位置]` |

```python
# loc[index1:index2, ['columns']]
print(df.loc['2010-01-01':'2010-01-04', ['a', 'b']])
print(df.loc[:,['a']])
print(df.loc[df.index < '2010-01-04'])
# iloc[index1:index2, ['columns_indexs']]
print(df.iloc[:4, [0, 1]])
# ix[, ] label 和 index 都行
print(df.ix[:4, ['a', 'b']])
```

