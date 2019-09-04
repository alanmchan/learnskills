# mysql

## PyMySQL

```python
# 导入模块
import pymysql

# 连接数据库
conn = pymysql.connect(host, port, user, passwd, dbname, charset)
"""
	host    -- 服务器地址 127.0.0.1
    port    -- 服务器端口
    user    -- 用户名
    passwd  -- 密码
    dbame   -- 数据库名称
    charset -- 连接数据库使用的编码格式
"""
    
# connection对象支持的方法
"""
    cursor()   -- 获取游标
    commit()   -- 提交事务
    rollback() -- 回滚事务
    close()    -- 关闭连接
"""


# 创建游标
cursor = conn.cursor()
# cursor对象的方法
"""
    execute(sql)    -- 执行SQL语句
    fetchone()      -- 取得结果集的下一笔数据
    fetchmany(size) -- 取得结果集的下几笔数据
    fetchall()      -- 获取结果集的所有数据
    close()         -- 关闭游标
    rowcount()      -- 返回数据笔数或影响行数，只读属性 
"""

# 使用游标的对象提供的方法，执行SQL语句
sql = 'select * from orders'
cursor.execute(sql)
result = cursor.fetchall()

# 提交事务 
conn.commit()

# 关闭游标
cursor.close()

# 关闭数据库
conn.close()
```

## ORM

对象关系映射



## SQLAlchemy

![SQLAlchemy原理图](https://img-blog.csdnimg.cn/201909011000302.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1NTQzMTE1,size_16,color_FFFFFF,t_70)
### Types
- Integer
- Float
- Boolean
- Date
- Datetime
- String
- ForeignKey

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy import Column, Integer, String, Datetime, Boolean, Enum


engine = create_engine('mysql://root:@localhost:3306/class?charset=utf8')
Base = declarative_base()

Session = sessionmaker(bind=engine)


class Student(Base):
	"""学生信息类"""
	__tablename__ = 'student'
	id = Column(Integer, primary_key=True)
	name = Column(String(16), nullable=False)
	age = Column(Interger, nullable=False)
	date_of_birth = Column(Datetime)
	gender = Column(Enum(0, 1))
	is_valid = Column(Boolean)

class OrmTest(object):
	def __init__(self):
		self.sessin = Session()
		
	def add_one(self):
		'''添加数据'''
		stu = Student(
			name='zhangsan',
			age=20,
			gender=1
		)
		self.session.add(stu)
		# self.session.add_all() 添加多条数据
		self.session.commit()
		return stu
	
	def get_one(self):
		'''获取一条数据'''
		return self.session.query(Student).get(1)
	
	def get_more(self):
		'''获取多条数据'''
		return self.session.query(Student).filter_by(is_valid=1)
	
	def update(self):
		'''修改数据'''
		obj = self.session.query(Student).get(id=2)
		obj.name = 'lisi'
		self.session.add(obj)
		self.session.commit()
		return obj
	
	def delete(self):
		'''删除数据'''
		obj = self.session.query(Student).get(3)
		self.session.delete(obj)
		self.session.commit()
```