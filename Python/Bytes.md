# bytes 字节串

## 定义

存储以字节为单位的数据

字节串是 __不可变序列__，字节是 __0~255__ 之间的 __整数__

```python
# 字节串的表示方法
# 空字节串
b''
b""
B''
B""

# 非空字节串
b'ABCD'  # 即 b'65 66 67 68' 存的是ABCD对应的ASCII码
B'\x41\x41' # 即b'AA'
b'hello world' # 虽然显示为字母，但存储的是1~255间的整数
```



## 字节串的基础操作

```python
# 字节串的构造函数 bytes()
# 创建空字节串
b = bytes()

# 用可迭代对象初始化一个字节串
bytes(iterable)
bytes([10, 20, 30, 40, 50])  # b'\n\x14\x1e(2<'  用符号表示一个1~255间的整数

# 生成n个值为零的字节串
bytes(n)
bytes(2)  # b'\x00\x00'

# 用字符串的转换编码生成一个字节串
bytes(字符串, encoding='utf-8')
bytes('hell0', 'ascii')  # b'hello'
bytes('中文', 'utf-8')  # b'\xe4\xb8\xad\xe6\x96\x87'
bytes('中文', 'gbk')  # b'\xd6\xd0\xce\xc4'

# 以换行符分隔字节串
b.splitlines()
```

字节串的运算 `+ += * *=, < <= > >= == !=, in / not in, 切片和索引取值` 同字符串一样



## 字节串与字符串

字节串bytes和字符串str的区别

| bytes            | str                       |
| ---------------- | ------------------------- |
| 存储字节   0~255 | 存储Unicode字符   0~65535 |

字节串和字符串间转换

```python
# 编码encode：str---->bytes
b = s.encode（encoding='utf-8'）

# 解码decode：bytes---->str
s = b.decode（decoding='utf-8'）
```



## 字节数组 bytearray

__可变__ 的字节序列

```python
# 字节数组表示方法
# 空字节数组
bytearray(b'')
# 非空字节数组
bytearray(b'hello')

# 字节数组构造函数 bytearray()
# 创建空的字节数组
bytearray()
# 生成n个值为零的字节串
bytearray(n)
bytearray(5)  # bytearray(b'\x00\x00\x00\x00\x00')
# 利用可迭代对象生成自己数组
bytearray(iterable)
# 利用字符串生成字节数组
bytearray(字符串, encoding='utf-8')
```



字节数组方法

| 方法                           | 说明                                           |
| ------------------------------ | ---------------------------------------------- |
| b.clear()                      | 清空字节数组                                   |
| b.append(n)                    | 追加一个字节，n为0~255间的整数                 |
| b.remove(value)                | 删除第一个出现的字节，若没有返回ValueError错误 |
| b.reverse()                    | 字节的顺序进行转换                             |
| b.decode(encoding='utf   -8')  | 解码                                           |
| b.find(sub[,   start[, stop]]) | 查找                                           |