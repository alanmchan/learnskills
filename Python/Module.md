# Module 模块

__定义__：包含一系列代码(数据/函数/类/语句)的文件，通常以**.py**结尾

**作用**：让一些相关的代码，有逻辑的组织在一起，使结构更加清晰



## 导入语法

```python
# 导入
import 模块名
import 模块名 as 模块别名
# 使用
模块名.成员
# 作用 将某个模块整体导入到当前模块
# 本质 使用变量名(模块名)关联指定模块代码

# 导入
from 模块名 import 成员
from 模块名 import 成员 as 别名
# 使用
成员
# 作用 将模块内的指定的成员导入到当前模块作用域中

# 导入
from 模块名 import *
# 使用
成员
# 作用 将模块内的所有成员导入到当前模块作用域中
# 注意
# 1. 小心重名
# 2. 不能导入隐藏成员(以一个下划线开头)
```



## 分类

- **内置模块** **builtins**：解释器可以直接使用

- **标准库模块**：按照python时自带的，经导入后可以使用

- **第三方模块**：需自行下载，再导入使用

- **自定义模块**：经过导入后可以使用



## 模块内置属性

| 模块属性   | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| `__doc__`  | 绑定模块的文档字符串                                         |
| `__name__` | 当前模块名称；如果程序从当前模块运行，则名称被改为 **__main__** |
| `__file__` | 当前模块的文件路径，为字符串：对于内建模块，不绑定路径；对于其他模块，绑定路径名的字符串 |
| `__all__`  | 是一个用来存储可导出属性的字符串列表                         |

```python
# __name__ 
# 在if代码块中定义只能从当前模块开始执行，才调用的代码
if __name__ == '__main__':
    print(__name__)
    import os
    print(os.__name__)
```



### 模块的隐藏属性

模块中以**单下划线**'**_**'开头的属性，在**from xxx import \*** 语句导入时，将不被导入，通常称为隐藏属性。

其他导入语句可以导入



## 常见标准库模块

### math 模块

```python
import math

# 模块属性
math.pi
math.e

# 模块方法
math.ceil(x)  # 向上取整
math.floor(x)  # 向下取整
math.sqrt(x)  # 开方
math.log(x[, base])  # 求对数
math.pow(x, y)  # 求指数
math.sin(x)
math.degree(x)  # 将弧度转化为角度
math.radians(x)  # 将角度转为弧度
```



### time 模块

计算机元年是1970年1月1日0时开始的，此时间为0，之后每一秒+1

UTC时间（Coordinated Universal Time）是从Greenwich时间开始计算的

时间元组是一个9个整型元素组成的元组：

- 4位的年

- 月

- 日

- 时

- 分

- 秒

- 星期几 （0代表星期一，依次类推）

- 元旦开始日 （距离元旦过来多少天，1~366）

- 夏时令修正时间

```python
import time 

time.sleep(x)  # 让程序按给定秒数的浮点数睡眠一段时间
time.time()  # 现在到新纪元过了多少秒，即时间戳
# 秒数 --> 时间元组
time.gmtime([sec])  # 用给定秒数转换为用UTC表达的时间元组，缺省则返回当前时间元组
time.localtime([sec])  # 将秒数转换成本地时间元组
# 时间元组 --> 秒数
time.mktime(tuple)  # 将本地日期时间元组转换为新纪元秒数
# 时间元组 --> 字符串
time.asctime([tuple])  # 将时间元组转换成日期字符串
time.strftime("%y   %m   %d   %H  %M   %S", time.localtime())  # 将时间元组转换成格式化字符串
time.strptime("2019/03/20 17:32",  "%Y/%m/%d %H:%M")  # 将字符串转为时间元组
```



### random 模块

```python
import random

random.random()  # 生成一个[0, 1)间的随机数
random.uniform(a, b)  # 生成一个[a, b)间的随机数
random.randrange([start, ]stop[, step])  # 返回range(start, stop, step)中的随机数
random.choice(seq)  # 从序列中返回任意数
random.shuffle(seq[, random])  # 随机指定序列的顺序，乱序排列，类似于洗牌
random.sample(seq, n)  # 从序列中选择n个随机且不重复的元素
```



### sys 模块

```python
sys.path  # 模块搜索路径，path[0]是当前脚本程序的路径
sys.modules  # 已加载模块的字典，键为模块名，值为模块
sys.version  # 版本信息字符串
sys.version_info  # 版本信息的命名元组
sys.platform  # 操作系统平台名称信息
sys.argv
# 命令行参数列表，argv[0]代表当前脚本程序路径
# sys.argv其实可以看作是一个列表，所以才能用[]提取其中的元素。其第一个元素是程序本身，随后才依次是外部给予的参数
sys.builtin_module_names  # 获得Python内建模块的名称
sys.exit([agrs])  # 为sys模块方法，退出程序，正常退出时sys.exit(0)
```



### os 模块



## 模块的加载过程

### import语句搜索模块的路径顺序

1. 搜索程序运行时的路径，即当前路径
2. **sys.path**提供的路径：sys.path是一个模块路径列表，可以将其他模块路径加入其中后再调用其他模块
3. 搜索内建模块

```python
import sys
sys.path.append('/home/cm')
import my_module
```



### 模块导入和执行

1. 搜索相关路径找模块
2. 判断是否有此模块对应的**.pyc**文件，如果存在.pyc文件，且比**.py**文件新，则直接加载.pyc文件；否则，用.py文件生成.pyc文件后再加载



### 模块加载过程

- 在模块导入时，模块的所有语句都会执行

- 如果一个模块已经导入，则再次导入时不再重复执行语句

```python
# a.py
print(100)
```

```python
# b.py
import a
print(10)
```

```python
# 执行结果
100
10
```



### 模块的重写与加载

```python
# 当模块更新后需要重写加载
import imp

imp.reload  # 已加载过的模块名
```



## 自定义模块

要求

- 模块文件名后缀必须是**.py**

- 模块文件名必须是合法标识符

- 避免名称和内建模块名冲突

优点：

- 有利于多人合作

- 使代码更易于维护

- 提高代码复用率

- 有助于解决变量冲突

