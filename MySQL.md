# MySQL

查看和启动关闭数据库

```sh
netstat -an | grep 3306
/etc/init.d/mysql status
/etc/init.d/mysql start
/etc/init.d/mysql stop
```

登录MySQL

```sh
mysql -hlocalhost -P3306 -uroot -p123456
mysql -uroot -p
```

## MySQL 基础命令

SQL语言按照功能可以分为4类：

- 数据查询语言DQL
  - 查询数据，不会修改数据
- 数据定义语句DDL
  - 定义库/表/索引
- 数据操作语言DML
  - 对数据进行增、删、改
- 数据控制语言DCL
  - 授权/吊销权限，事务管理

### 库管理

```mysql
-- 查看库
show databases;
-- 创建库
语法：
create database 库名 [字符集];
create database eshop default charset=utf8;

-- 切换库
语法：
use 库名;
use eshop;

-- 查看当前所在库
select database();

-- 查看当前库中的表格
show tables;

-- 查看某个库的建库语句
语法：
show create database 库名;
show create database eshop;

-- 删除库
语法：drop database 库名;
drop database eshop;
```

### 表管理

```mysql
-- 创建表
-- 语法：
create table 表名(
	字段1 类型(长度) 约束,
	字段2 类型(长度) 约束,
	...
);
create table orders(
    order_id varchar(32), -- 订单编号
    cust_id varchar(32),  -- 客户编号
    order_date datetime,  -- 下单时间，日期时间类型
    status int,           -- 订单状态
    products_num int,     -- 商品数量
    amt decimal(16, 2)    -- 订单总金额
        	                  -- 最长16位，小数2位
);

-- 查看表结构
-- 语法：
desc 表名;
desc orders;

-- 查看建表语句
-- 语法：
show create table 表名;
show create table orders;

-- 删除表
-- 语法：
drop table 表名;
drop table orders;
```

### 表记录管理

```mysql
-- 插入数据
-- 向表中插入一笔数据，所有字段都插入值
-- 语法：
insert into 表名 values(值列表);
insert into orders values('201801010001', 'C0001', now(), 1, 1, 100.00);

-- 向表中插入指定字段数
-- 语法：
insert into 表名(字段名列表) values(值列表);
insert into orders(order_id, cust_id) values('201801010002', 'C0002');

-- 向列表中插入多笔数据
-- 语法：
insert into 表名(字段名列表) values(值列表1), (值列表2), ... ,(值列表n);
insert into orders values
	('201801010003', 'C0003', now(), 1, 2, 200.00),
	('201801010004', 'C0004', now(), 1, 3, 480.00);
```

### 查询语句

```mysql
-- 从表中查询所有数据
-- 语法：
select * from 表名 [where 查询条件];
select * from orders;

-- 查询指定字段
-- 语法：
select 字段1, 字段2 from 表名 [where 查询条件];
select order_id, order_date from orders;

-- 查询指定字段，给每个字段一个别名
select order_id "订单编号", order_date"下单时间" from orders; -- 起别名用双引号

-- 带条件查询
select * from orders where order_id = '201801010001';

-- 满足多个条件
-- 两个条件同时满足
select * from orders where order_id = '201801010001' and status = 1;
-- 两个条件满足一个即可
select * from orders where order_id = '201801010001' or status = 1; 
```

###　数据类型

```mysql
-- 数值
int
decimal

-- 字符
char
varchar
text

-- 枚举
create table enum_test (
	name varchar(32),
	sex enum('boy', 'girl'),
	course set('music', 'dance', 'paint') -- 只能用字符串类型
);
insert into enum_test values ('Kate', 'girl', 'music,dance'); -- 枚举插入值时，逗号后不能有空格

-- 日期时间类型
select now(), sysdate(); -- 取当前时间
select curdate(), curtime(); -- 取当前日期和时间
select year(now()), month(now()),day(now()); -- 取当前年月日
select date(now()), time(now()); -- 取当前日期和时间
```

###  修改数据

```mysql
update 表名
	set 字段1 = 值1，
		字段2 = 值2，
		...
	where 条件表达式;
```

```mysql
update orders
	set status = 2
	where order_id = '201801010001';
```

### 删除记录

```mysql
delete from 表名 where 条件;
```

```mysql
delete from orders where order_id = '201801010002';
```

### 条件查询

```mysql
-- 带比较操作符： >, <, >=, <=, <>/!=, ==/=
select * from orders where amt > 200;
select * from orders where status != 2; -- 不等于
select * from orders where status <>2; -- 不等于

-- 逻辑运算符：and，or
select * from orders where (cust_id = 'C0003' or cust_id = 'C0002') and status =1;

-- 范围比较:
between ... and ... 在...和...之间，包含端点
in / not in 在/不在某个集合内
select * from orders where amt between 200 and 300;
select * from orders where cust_id in ('C0003', 'C0004');

-- 模糊查询
where 字段名称 like "通配字符串"
-- 通配符匹配规则：
	-- 	下划线_：匹配单个字符
	-- 	百分号%：匹配任意个字符
create table customer(
	cust_id varchar(32),
	cust_name varchar(32),
	tel_no varchar(32)
) default charset=utf8;
insert into customer values 
('C0001', 'Jerry', '13511223344'),
('C0002', 'Dekie', '13844445555'),
('C0003', 'Dokas', '15822223333');
select * from customer where cust_name like "D%";
select * from customer where tel_no like '%4455%';

-- 空、非空判断
判断空：is null
判断非空：is not null
update customer set tel_no = null where cust_id = 'C0003'; -- 将某个值改为空null
select * from customer where tel_no is null;
```

### 聚合函数

什么是聚合：不是直接查询表中的数据，而是对数据进行总结，返回结果

| 函数  | 作用     |
| ----- | -------- |
| max   | 求最大值 |
| min   | 求最小值 |
| avg   | 求平均值 |
| sum   | 求和     |
| count | 统计笔数 |

```mysql
select max(amt) "最大金额",
	min(amt) "最小金额",
	avg(amt) "平局金额",
	sum(amt) "订单总金额"
from orders;
select count(*) from orders;
select count(*) from customer where tel_no like '135%';
select count(*) from customer;
select count(tel_no) from customer; -- 如果有null，则结果与上一条语句不同
```

### 查询子句

#### order by: 对查询结果按照某个字段进行排序

```mysql
order by 字段 [asc/desc] -- 默认为升序
select order_id, amt from orders order by amt asc; 
```
#### limit: 限定显示查询结果的记录数

```mysql
limit n -- 限定显示前n笔数据
limit m, n -- 从第m笔开始，总共显示n笔, 不包含第m笔
select * from orders limit 2;
select * from customer limit 2,1;
select * from orders order by amt desc limit 2;
```

#### 利用limit实现分页查询
一批数据不全部显示，而是分页显示，每页显示固定笔数

分页方式：

- 显示时分页: 查询出所有满足条件的数据，分页显示 ---- 性能不好
- 查询时分页: 只查询当前页要显示的数据

查询时分页原理：利用limit函数，带两个参数，控制从哪个一笔数据开始显示、共显示多少笔。
计算方式：只需要知道当前第n页，每页多少笔，
就能得出limit子句的两个参数：(n-1)*m, m从而实现分页查询

```mysql
insert into customer values 
('C0001', 'Jerry', '13511223344'),
('C0002', 'Dekie', '13844445555'),
('C0003', 'Dokas', '15822223333'),
('C0004', 'Jerry', '13511223344'),
('C0005', 'Dekie', '13844445555'),
('C0006', 'Dokas', '15822223333'),
('C0007', 'Jerry', '13511223344'),
('C0008', 'Dekie', '13844445555'),
('C0009', 'Dokas', '15822223333'),
('C0010', 'Jerry', '13511223344'),
('C0011', 'Dekie', '13844445555'),
('C0012', 'Dokas', '15822223333'),
('C0013', 'Jerry', '13511223344'),
('C0014', 'Dekie', '13844445555'),
('C0015', 'Dokas', '15822223333');
-- 第一页
select * from customer limit 0,4;
-- 第二页
select * from customer limit 4,4;
-- 第三页
select * from customer limit 8,4;
-- 第四页
select * from customer limit 12,4;

-- 去除重复数据后显示
select distinct (要去重的字段) from 表名;
select distinct(cust_name) from customer;
```

#### group by: 分组

对查询结果进行分组，通常和聚合函数配合使用. 先分组，再处理

```mysql
group by 分组字段名;
select cust_name, count(*) from customer group by cust_name;
select status,max(amt) from orders group by status;
```

#### having: 过滤

对分组的结果进行过滤，需要和group by配合使用

```mysql
group by 分组字段名 having 过滤条件;
select status, sum(amt) from orders group by status having sum(amt) > 500;
```


说明：group by分组聚合的结果只能用having，不能用where。where只能用于表中已有的字段作为条件

SQL语句执行顺序

- 第一步：首先执行from子句，从表中找到源数据
- 第二步：执行where子句，选出所有满足条件的数据
- 第三步：group by子句进行分组
- 第四步：聚合操作
- 第五步：having子句对聚合结果进行过滤
- 第六步：order by子句对结果进行排序
- 第七步：limit限制显示笔数



表结构调整 ALTER

```mysql
-- 添加字段
-- 添加到表的最后一个字段
alter table 表名 add 字段名 类型(长度);
alter table student add age int;

-- 添加到表的第一个字段
alter table 表名 add 字段名 类型(长度) first;
alter table student add id int first;

-- 添加到表的某个字段后面
alter table 表名 add 字段名 类型(长度) after 字段名;
alter table student add tel_no varchar(32) after stu_name;
```

```mysql
-- 修改字段
-- 修改字段类型
alter table 表名 modify 字段名 类型(宽度);
alter table student modify stu_name varchar(64);

-- 修改字段名称
alter table 表名 change 旧字段名 新字段名 类型(长度);
alter table student change age stu_age int;

-- 删除字段
alter table 表名 drop 字段名;
alter table student drop id;
```



高级查询

- 子查询: 一个查询语句中嵌套另一个查询

- 联合查询
  - 内连接 inner join
  - 外连接 outer join
    - 左连接 left join
    - 右连接 right join

```mysql
-- 查询金额超过平均金额的订单
select * from orders where amt > (select avg(amt) from orders);
-- 查询以D开头的客户的订单
select * from orders where cust_id in 
(select distinct cust_id from customer where cust_name like 'D%');
-- 查询没有下过订单的客户
select * from customer where cust_id not in (select distinct cust_id from orders);
```

说明：

- 括号中的部分称为子查询
- 子查询可以返回一个值，也可以返回多个值，根据外层查询要求来决定
- 先执行子查询，将子查询的结果，作为外层查询的条件，在执行外层查询子查询只执行一次



联合查询

也称连接查询，将多个表中的数据进行连接，得到一个结果集. 当从一个表无法满足查询结果的时候，需要使用联合查询
前提：联合的表之间，一定要有逻辑上的关联性

```mysql
-- 示例：
select orders.order_id, orders.amt, customer.cust_name, customer.tel_no
from orders, customer
where orders.cust_id = customer.cust_id;

-- 起别名
select a.order_id, a.amt, b.cust_name, b.tel_no
from orders a, customer b
where a.cust_id = b.cust_id;
```

连接查询：分为内连接和外连接

- 内连接inner join：没有关联的数据不显示
- 外连接outer join：没有关联的数据也要显示到结果集,  外连接分为左连接和右连接
  - 左连接：以左表为基准，右表的数据进行关联，左表的数据全部显示，右表的字段如果没有关联到，则显示null
  - 右连接：以右表为基准，左表的数据进行关联，右表的数据全部显示，左表的字段如果没有关联到，则显示null

```mysql
-- 方式1：利用where进行条件关联
select a.order_id, a.amt, b.cust_name, b.tel_no
from orders a, customer b
where a.cust_id = b.cust_id;

-- 方式2：利用inner join关键字
select a.order_id, a.amt, b.cust_name, b.tel_no 
from orders a inner join customer b on a.cust_id = b.cust_id;
```

```mysql
-- left join
select a.order_id, a.amt, b.cust_name, b.tel_no 
from orders a left join customer b on a.cust_id = b.cust_id;
-- right join
select a.order_id, a.amt, b.cust_name, b.tel_no 
from orders a right join customer b on a.cust_id = b.cust_id;
```

联合查询理论基础: 笛卡尔积
笛卡尔积：两个集合的乘积，产生一个新集合。表示两个集合所有可能的组合情况
笛卡尔积和关系：在笛卡尔积中去掉没有意义或不存在的组合，就是关系(规范的二维表)



## 约束constraint
什么是约束：数据必须遵循的规则
目的：保证数据完整性、一致性、有效性
作用：可以限制无效的数据今日数据库中，从数据库层面上提供"安检"

| 分类           | 约束     | 意义                                                    |
| -------------- | -------- | ------------------------------------------------------- |
| not null       | 非空约束 | 字段值不能为空                                          |
| unique         | 唯一约束 | 字段值必须唯一                                          |
| primary key    | 主键约束 | 主键在表中唯一标识、区分一个实体，非空、唯一            |
| default        | 默认约束 | 未填写的情况下，自动填写默认值                          |
| auto_increment | 自动增长 | 字段值自动增加, 要求:该字段必须是pk，或设置了unique约束 |
| foreign key    | 外键约束 |                                                         |

```mysql
-- 非空约束
字段名 类型(长度) not null

-- 唯一约束：
字段名 类型(长度) unique

-- 主键约束：
字段名 类型(长度) primary key

-- 默认值约束default constraint：
字段名 类型(长度) default 值

-- 自动增长(auto_increment)：
字段名 类型(长度) auto_increment
```

```mysql
create table t1(
	stu_no varchar(32) primary key,
	stu_name varchar(32) not null,
	id_card_no varchar(32) unique
);
-- 正常
insert into t1 values ('001', 'Jerry', '123456201010101234');
-- 空值报错
insert into t1 values ('002', null, '123456201010101235');
-- 重复报错
insert into t1 values ('003', 'Jack', '123456201010101234');
-- 主键空值、重复报错
insert into t1(stu_no, stu_name) values (null, 'Tom');
insert into t1(stu_no, stu_name) values ('001', 'Tom');

create table t2 (
	id int primary key auto_increment,
	name varchar(32),
	status int default 0
);
insert into t2 values (null, 'Jerry', 1);
insert into t2 values (null, 'Tom', 2);
insert into t2(id, name) values (null, 'Hennry'); 
```

外键约束(foreign key)：

- 什么是外键：一种约束，建立外键的前提：某个字段在当前表不是PK，但在另一个表是PK
- 作用：当一个字段被设置成外键时，另一个表中通过该外键关联的数据必须存在，这个特性被称为"参照(引用)的完整性"，这样保证参照的实体一定存在

字段设置为外键约束后，产生的影响：

- 插入一个在外表中不存在的实体时，报错
- 删除外表中已经被参照的实体时，报错

```mysql
-- 所有字段创建完毕后，
constraint 外键名称 foreign key (当前表字段名) references 参照表 (参照字段);

create table course (
course_id varchar(32) primary key,
name varchar(32)
);
create table teacher(
id int auto_increment primary key,
name varchar(32),
course_id varchar(32),
constraint fk_course foreign key (course_id) references course(course_id)
);
insert into course values ('0001', 'Python Base');
insert into teacher values (1, 'Jerry', '0001');
```

外键使用条件

- 表的存储引擎必须为InnoDB
- 外键字段在外表中必须是主键
- 当前表、外表中字段类型必须一致



```mysql
-- 通过修改字段方式添加约束
create table t6 (
	id int, 
	name varchar(32),
	status int,
	course_id varchar(4),
	tel_no varchar(32)
);

alter table t6 add primary key(id);
alter table t6 modify id int auto_increment;
alter table t6 modify status int default 0;
alter table t6 modify tel_no varchar(32) unique;
alter table t6 add constraint fk_course_id foreign key (course_id) references course (course_id);
```

删除约束

```mysql
-- 删除唯一性约束
ALTER TABLE t_user DROP INDEX user_id;
-- 删除主键约束
ALTER TABLE t_user DROP PRIMARY KEY;
```

表的复制和重命名

```mysql
-- 复制
-- 将源表完全复制为新表
create table orders_new select * from orders;
-- 将源表部分复制到新表
create table orders_new select * from orders where amt <= 200;
-- 只复制表结构，不复制数据
create table orders_new select * from orders where 1=0;
-- Attention：该方式不会将键的属性复制到新表中

-- 重命名
alter table 表名 rename to 新表名;
alter table orders rename to orders_bak;
```



## 索引

- 提高查询效率的一种技术
- 原理：根据某一列(字段)进行分段、排序，通过避免全表扫描提高查询效率
- 分类：
  - 普通索引：MySQL基本类型，字段值可以重复
  - 唯一索引：字段的值不能重复(可以为空), 主键是特殊唯一索引
- 单列索引：一个索引只包含一个字段
  - 组合索引：一个索引包含多个字段
  - 聚集索引：clusetr index，索引的键值顺序和数据顺序是一致的
- 非聚集索引：索引的键值顺序和数据顺序是不一致的

```mysql
-- 创建索引
index | unique | primary key
-- 示例：
create table index_test(
	id int primary key,
	cert_no varchar(32),
	name varchar(32),
	unique(cert_no), index(name)
);
-- 等同于
create table index_test(
	id int primary key,
	cert_no varchar(32) unique, 
	name varchar(32),
	index(name)
);
-- 查看索引：
show index from index_test;

-- 删除索引：
drop index 索引名称 on 表名称;
-- 示例：
drop index cert_no on index_test;
drop index name on index_test;

-- 修改表的方式添加索引
create 索引类型 索引名称 on 表 (字段);
-- 示例：
create unique index idx_cert_no on index_test(cert_no);
create index name on index_test(name);
```

索引优点：

- 提高查询效率
  - 唯一索引能够保证数据的唯一性
  - 可能提高分组、排序的效率
- 缺点：
  - 降低增删改的效率(调整索引结构的开销)，对表中的数据进行增删改操作，需调整索引结构
  - 需要增加额外的存储空间

索引使用注意事项
总体原则：

- 在合适的字段上，建立合适的索引
- 索引不能太多，过多的索引会降低增删改效率
- 索引适用情况：
  - 在经常进行查询、排序、分组的字段上建立索引
  - 数值分布比较均匀、连续的字段，适合建立索引
  - 查询操作较多的表，适合建立索引
- 不适合建立索引：
  - 数据较少的表不适合建立索引
  - 增删改操作较多的表，不适合建立较多的索引
  - 某个字段取值范围很少，不适合建立索引
  - 某个字段很少用作查询、排序、分组，不适合建立索引
  - 二进制字段不适合建立索引

索引失效: 表中有索引，但查询没使用. 会导致放弃使用索引的情况：

- 没有使用索引字段作为条件
- 条件判断中使用了<>符号
- 条件判断中使用null值判断
- 模糊查询%前置
- 对字段运算



## 事务

什么是事务：指数据库执行的一系列增删改操作要么全部执行，要么全都不执行。

作用：保证数据的一致性、完整性

事务具有四个特点：(重要)

- A，原子性Atomicity：一个事务是不可分割的整体，要么全都执行，要么全都不执行
- C，一致性Consistency：事务执行完成后，数据库从一个一致性状态变成下一个一致性状态
- I，隔离性Isolation：不同的事务不会相互影响
- D，持久性Durability：事务一旦提交后，对数据的修改会被持久保存到磁盘中

数据库事务只对 __数据操作语言DML语言(对数据进行增、删、改)__ 起作用

```mysql
create table acct(
    acct_no varchar(32),
    acct_name varchar(32),
    balance decimal(16,2)
);

insert into acct values
    ('0001','Jerry', 1000),
    ('0002','Tom', 2000);

start transaction; -- 启动事务
update acct set balance = balance - 100 where acct_no = '0001'; -- 模拟扣款人
update acct set balance = balance + 100 where acct_no = '0002'; -- 模拟收款人
commit; -- 事务提交
rollback; -- 事务回滚
```

如何操作事务:

- 启动事务
  - 显示启动：start transaction
  - 隐式启动：执行insert, update, delete操作
- 提交事务：commit;
- 回滚：rollback;

在终端中会自动执行 commit/rollback，而在编写的程序中不会自动执行，需要手动添加语句



使用事务的情况及先决条件：

- 先决条件：InnoDB存储引擎支持事务
- 使用事务的情况： 
  - 一个操作涉及一组SQL语句
  - 需要这一组操作完成后，保证数据的一致性、完整性

事务对效率的影响：

- 数据库事务会降低数据库的性能。为了保证数据的一致性、隔离性，事务需要对事务进行加锁。
- 如果有其他事务需要操作这部分数据，必须等待上一个事务结束(提交、回滚)



##　数据导入和导出

- 导出：将数据库中的数据导出到文件中
- 导入：将文件的数据导入到数据库表中

```mysql
-- 查看导出导入路径：
show variables like 'secure_file%';
-- 结果：/var/lib/mysql-files/
-- 只能导出到该目录或从该目录导入

-- 导出：
-- 语法：
select 查询语句 
into outfile '文件路径加名称' 
fields terminated by '字段分隔符' 
lines terminated by '行分隔符'
-- 示例：
select * from orders
into outfile '/var/lib/mysql-files/orders.csv'
fields terminated by ','
lines terminated by '\n';
-- 查看导出的文件：
sudo cat /var/lib/mysql-files/orders.csv

-- 导入：
-- 语法
load data infile '备份文件路径'
into table 表名
fields terminated by ','
lines terminated by '\n';
-- 示例：
-- 导入前先删除表内容delete from orders;
load data infile '/var/lib/mysql-files/orders.csv'
into table orders
fields terminated by ','
lines terminated by '\n'; 
-- 导入后查询确认
```

## 备份和还原



## 权限管理

什么是权限：用户可以执行哪些操作
权限分类：

- 用户类：创建、删除、修改用户的权限，给其他用户授权的权限
- 库/表操作：创建/删除库，创建/删除表
- 数据操作：增、删、改、查

权限表：

- user：存储了用户、密码、以及用户所拥有的权限
- db：记录了库的授权信息
- table_priv：记录表的授权信息
- column_priv：记录对字段的授权信息

权限操作：

```mysql
-- 授予权限
grant 权限列表 on 库名.表名 to '用户名'@'客户端地址' 
    [identified by '密码']
    [with grant option]; -- 是否可以给其他用户授权

-- 说明：
-- 权限列表：表示授予哪些权限
all privileges -- 所有权限
select -- 只授予查询权限
select, insert, update, ... -- 分别制定权限
-- 库名.表名：
*.* -- 所有库下的所有表
bank.* -- bank库下的所有表
bank.acct -- bank库下的acct表
-- 客户端地址：
% -- 表示所有客户端
localhost -- 表示本机
192.168.0.5 -- 表示从指定地址登录
identified by '密码' -- 设置用户登录密码
with grant option -- 表示是否有给其他用户授权的权限
```

```mysql
 -- 创建Tom用户，并授权
grant select on eshop.* to 'Tom'@'%' identified by '123456';
-- 查看权限：
select * from mysql.db where user = 'Tom'\G;
-- 刷新权限，使之生效
flush privileges;
-- 利用新用户操作数据
select * from mysql.user where user = 'Tom'\G;
```

```mysql
-- 吊销权限 
revoke 权限列表 on 库名.表名 from '用户名'@'客户端地址';
revoke delete on eshop.* from 'Jerry'@'localhost';
flush privileges;

-- 查看权限：
show grants; -- 查看自己的权限
show grants for 'Tom'@'%'; -- 查看别人权限：

-- 修改权限：就是修改mysql.user表的记录
update user set host = '%' where user = 'Tom';
```



## 锁

概念：对某个范围的控制(操作)权限列表

目的：解决两个或多个工作单元并发操作数据引发的问题

分类:

- 锁类型
  - 读锁(共享锁):select操作加锁，可进行数据读取，但不能写入
  - 写锁(排他锁):insert/update/delete 时加锁，加锁后数据不能读、写 
- 锁粒度(锁定范围)
    - 行级锁(细粒度)：锁定一行，并发效率高，资源消耗多
    - 表级锁(粗粒度)：锁定一个表，并发效率低，资源消耗少



## 存储引擎

什么是存储引擎：表的物理实现方式，由于物理实现不一样，决定不同存储引擎类型的技术特性不一样， 例如：存储机制、索引机制、锁定方式

```mysql
-- 查看MySQL支持的存储引擎：
show engines;
-- 查看表的存储引擎：
show create table 表名;
-- 修改表的存储引擎：
alter table 表名称 engien = 引擎名称;
```

常用存储引擎及特点：

- InnoDB：MySQL默认存储引擎
  - 特点：支持事务、支持行级锁、支持外键约束、共享表空间
  - 文件构成：
    - *.frm:表结构、索引
    - .ibd:表的数据
  - 使用InnoDB的情况：
      - 更新(增删改)操作密集的表
      - 要求支持数据库事务、外键
      - 自动灾备和恢复
      - 要求支持自动增长的字段
  
- MyISAM：老板MySQL的存储引擎
  - 特点：支持表一级的锁定，不支持事务、外键、行锁定，独占表空间；该类存储引擎容易损坏，灾备、恢复不佳
  - 文件构成：
    - *.frm:表结构
    - *.MYD:数据
    - .MYI:表索引 
  - 适用场合：
          - 查询请求较多
          - 数据一致性要求较低
          - 没有外界约束要求

- Memory：
  - 特点：表结构存于磁盘，数据存在内存中。服务器重启或断电后数据丢失
  - 文件构成：
    - *.frm:表结构
  - 适用场合：
          - 数据量小，数据需要快速访问
          -  数据丢失不会造成损失

```mysql
-- 使用Memory表：
create table engine_memory(id int primary key, name varchar(32)) engine = Memory;
insert into engine_memory values (1, 'Ben');
-- 重启服务，再查询，数据消失
/etc/init.d/mysql restart
```

```mysql
-- 说明：通过下面指令可以查看数据存储的目录
show global variables like '%datadit%';
-- 如果权限不够，使用sudo -i 进入root用户，进入上面的目录查看
cd /var/lib/mysql 
ls orders.* -- 查看orders表的存储文件
```



## E-R模型

E-R模型是"实体-关系方法"的简称，是一种典型的数据库概念设计方法
优点：能够很好地抽象出现实世界的基本内容，并以图形化的表现方法为概念设计人员与用户提供对数据库的直观表达
特点：面向问题的概念模型，即用简单的图形方式，描述现实世界中的数据
基本概念：

- 实体：现实世界中任何可以被认知、区分的事物，矩形
- 属性：实体具有的特征，椭圆，主键加下划线
- 关系：实体间的联系，关系
  - 1 --> 1
  - 1 --> N
  - N -- > N



## 数据库工具

1. Navicat for mysql
2. PowerDesigner