# Statement

## 行

- 物理行：程序员编写代码的行
- 逻辑行：Python解释器需要执行的指令

```python
print('hello');print('world')
# 一个物理行，两个逻辑行

# 隐式换行
# 所有括号中的内容都可以换行
x = 1 + ( 2 + 3
        + 4)

# 显示换行
# 通过折行符 \ 换行
x = 1 + 2 + 3 \
	+ 4
```



## del 语句

用于删除变量，同时解除变量与对象的绑定关系。如果可能则释放对象

```python
del 变量名1 变量名2
```

__对象引用计数__：当对象绑定变量时，计数增加1，当变量解除绑定时，计数减少1。如果引用计数为0， 则对象自动释放



## 选择语句 if - else

```python
if 真值表达式1:
    满足条件1执行语句块
elif 真值表达式2:
    满足条件执行语句块
elif 真值表达式3:
    满足条件执行语句块
...
else:
    以上条件都不满足执行的语句块
```

说明：

- elif 子句可以有0个或多个
- else 子句最多有一个，可以没有，且只能放在最后
- if真值表达式：

```python
if 变量:  # 相当于 if bool(变量)
    pass
```

```python
# 判断闰年
if (year % 4 == 0 and year % 100 != 0) or (year % 400 == 0):
    print("是闰年")
```



### 条件表达式

可以选择性地为变量进行赋值

```python
变量 = 满足条件的结果 if 条件 else 不满足条件的结果
result = n if n >=0 else -n  # 求绝对值
```



## pass 语句

用来填充语法空白

```python
if xxx:
    pass
else:
    yyy
```



## 循环语句

### while语句

```python
while 真值表达式:
    满足条件执行的循环体
else:
    条件不满足执行的语句
```

说明：

- 先判断条件，如果满足则执行循环体，如果不满足则执行一次else子句
- else子句最多只有一个，可以没有；如果用break语句退出循环，不会执行else子句

```python
# 死循环
while True:
    ...
```



### for语句

用来遍历可迭代对象的元素。可迭代对象是指能够用来依次获取数据元素的对象

```python
for 变量列表 in 可迭代对象:
    循环体
else:
    循环体结束后执行语句
```

说明：

- else子句可以省略
- break退出循环不执行else子句



### range() 函数

生成一系列整数可迭代对象

```python 
range(start, stop, end)
# 不包含stop
# step默认为1
# start默认为0

range(4)  # 0, 1, 2, 3
range(3, 6)  # 3, 4, 5
range(2, 10, 2)  # 2, 4, 6 ,8
range(9, 0, -2)  # 9, 7, 5, 3, 1
range(4, 0, 1)  # 空
```



## 跳转语句

### break语句

```python
if xxx:
    break  # 跳出循环体，后面的代码不再执行，包括else子句的代码
```

### continue语句

```python
if xxx:
    continue  # 跳出本次循环，不执行后面的语句，继续执行下一次循环
```

