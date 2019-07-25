# Pandas

## 概述

Python Data Analysis Library 或 pandas

是基于Numpy的一种工具，该工具是为了解决数据分析而创建的，Pandas纳入了大量数据库和一些标准的数据模型，提供了高效地操作大型数据集所需的工具。

数据分类：

- 结构化数据，可以用二维表结构来逻辑表达实现的数据
- 非结构化数据，图片、音频、视频、办公文档等
- 半结构化数据，介于完全结构化数据和非结构化数据之间，如xml，json



## Pandas核心数据结构

### Series

可以理解为一个一维数组，只是index可以改动，类似于定长的有序字典，有index和value

```python
import pd

# 创建Series
s1 = pd.Series(data=[3, -5, 7, 4], index=list('ABCD'))
print(s1)
# A    3
# B   -5
# C    7
# D    4
# dtype: int64
s2 = pd.Series(data=[3, -5, 7, 4])
print(s2)
# 0    3
# 1   -5
# 2    7
# 3    4
# dtype: int64
```

说明：

- index参数默认从0开始的整数，也是Series的绝对位置，即使index被赋值后，绝对位置也不会被改变
- index可以修改，绝对位置不可更改

### DataFrame

是一种类似于表格的数据类型，相关参数：

- index 行索引
- columns 列索引
- values 数据元素
- dtype 数据类型
- shape 数据形状（行列数目）
- size 元素个数
- ndim 维度数

DataFrame可以理解为一个二维数组，index有两个维度，可以更改

DataFrame的单列数据为一个Series

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

