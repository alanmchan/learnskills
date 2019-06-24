# MongoDB

# 1. 概论

### 1.1 特点

* 非关系型数据库，是属于文档型数据库

* 开源数据库，使用广泛

* 由c++编写的数据库管理系统

* 支持丰富的存储类型和数据操作

* 提供了丰富的编程语言接口

* 方便扩展和部署

### 1.2 安装

* Linux： `sudo apt-get install mongodb`
* Mac OS:` brew install mongodb`
* Windows:` www.mongodb.com`

### 1.3 MongoDB交互界面

交互界面名称： mongodb shell

* 进入： mongo

* 退出： quit() 或 Ctrl C

### 1.4 基础命令：

  ​		mongod：设置MongoDB基本信息
  ​		mongod -h：查看帮助
  ​		mongod --port [port]：设置MongoDB的端口为port
  ​		mongod --dbpath [dir]：将dir设置为数据库存储目录
### 1.5 MongoDB数据库数据结构
数据组织结构：键值对 --> 文档 --> 集合 --> 数据库

e.g.

|ID|Name|Age|
|----|------|------|
|1|Lily|17|
|2|Lucy|18|
```py
{
	'ID':1
	'Name':Lily
	'Age':17
}
{
	'ID':2
	'Name':Lucy
	'Age':18
}
```

### 1.6 基本概念对比
mysql|mongodb|含义
-|-|-
database|database|数据库
table|collection|表/集合
column|field|字段/域
row|document|记录/文档
index| index|索引

