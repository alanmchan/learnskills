# Flask

## 1. 概述

Flask是一个基于Python并且依赖于Jinja2模板引擎和WerkZeug WSGI服务的框架

* WSGI：Web Server Gateway Interface，Web服务网关接口，提供处理网络请求相关功能

## 2. 安装Flask

* `pip3 install flask`

## 3. Flask的路由

路由是为了匹配用户的请求地址，匹配成功则会自动执行视图函数，视图函数中必须有返回值，返回字符串显示到相应的页面中。

## 4. Flask的使用

### 4.1 定义路由及视图函数

语法

```py
@app.route('/地址')
def funcName():
    ...
    return ""
```

e.g.

```py
@app.route('/') # '/'表示根路径
def index():
    return "Home"
```

#### 4.2. 带参数的路由

变量：__`<变量名>`__

```py
@app.route('/login/<name>/<age>')
def login(name, age):
    return "<h1>%s, %s</h1>" % (name, age)
```

注意：__路径中的参数变量永远是字符串类型__

#### 4.3 类型转换器

code|含义
-|-
`缺省`|字符串，不能包含'/'
`int:`|转换整数
`float:`|转换小数
`path:`|字符串，允许包含'/'

e.g. `@app.route('/<int:num1>/<float:num2>')`

#### 4.4 多个URL执行同一个视图函数

```py
@app.route('/')
@app.route('/index')
@app.route('/home')
def index():
    return "首页"
```

## 5. 模板

模板是一种特殊的HTML文件，Python+HTML网页结构，允许在模板文件中使用变量，定义流程控制。使用模板可以使视图函数专注处理业务逻辑，将页面渲染交由模板控制

### 5.1 使用流程

* 导入 __`render_template`__
* 在视图函数中使用 __`render_template('模板文件')`__ ，生成字符串交由浏览器解析
* 项目中创建 __`"templates"`__ 文件夹，存放模板文件

### 5.2 变量代码块

* 模板中使用变量，语法 __`{{变量名}}`__
* 从视图函数中获取相关的变量，传递到模板文件中，__`render_template('模板文件', key1=value1, key2=value2)`__ 函数中可以传递若干键值对，其中的`key`就是模板文件中使用的变量
* 使用`locals()`返回字典，包含所有局部变量

  ```py
  params = locals()
  {
      'age': 20,
      'dic': {'age': 22, 'name': 'Aray'},
      'lst': ['draw', 'dance'],
      'name': 'Bran',
      'tup': ('Sansa', 24)
  }
  ```

### 5.3 过滤器

允许模板中的变量在输出前修改成其他的值，修改显示。语法 __`{{ 变量|过滤器1|过滤器2}}`__

* `upper` 转大写字母
* `lower` 转小写字母
* `title` 首字母大写
* `first` 获取列表中第一个
* `last` 获取列表中最后一个
* `length` 获取列表长度
* `defualt()` 如果变量未赋值，可采用默认值
* `trim` 去掉字符串两端空格

### 5.4 控制代码块

在模板文件中书写 `条件语句` 和 `循环语句`，使用 __`{% %}`__

#### 5.4.1 if 语句

```html
{% if 条件 %}
    条件成立时执行，允许书写静态标签，也可以书写变量
{% endif %}
```

```html
{% if 条件 %}
    条件成立时执行，允许书写静态标签，也可以书写变量
{% else %}
    条件不成立时执行，允许书写静态标签，也可以书写变量
{% endif %}
```

```html
{% if 条件 %}
    ...
{% elif 条件2 %}
    ...
{% else %}
    ...
{% endif %}
```

#### 5.4.3 for 语句

```html
{% for 变量 in 可迭代元素 %}
    ...
{% endfor %}
```

常用属性

* loop.index
* loop.index0
* loop.first
* loop.last

### 5.5 静态文件

* 不能与服务器交互的文件都是静态文件 (css、js、images、audio)
* 所有静态文件都要存储在一个名为 __`"static"`__ 的文件夹下，Flask程序会自动查找
* 静态文件的访问：
  * 使用 __`"/static/子路径"`__ 访问
  * 反向解析带参数的路由 __`url_for()`__
    * 根据视图函数解析路由地址 `url_for('login')` 得到`"/login"`
    * `url_for('login', uname='cm', upwd='123')` 得到`"/login/cm/123"`

e.g. 自动生成静态文件路径

```html
<link href="/static/css/base.css">
<!-- 等价于 -->
<link href="{{ url_for('static', filename='css/base.css') }}">
```

### 5.6 模板的继承

与类的继承相似。如果两个页面中大部分内容与结构都一致，可以采用模板继承

#### 5.6.1 父模板指定可以被子模板重写的内容

```html
{% block 块名 %}
    <h1>父模板</h1>
{% endblock %}
```

#### 5.6.2 子模板继承父模板

```html
{% extends '父模板名称' %}
```

#### 5.6.3 子模板可以 "重写" 父模板中指定的内容

```html
{% block 块名 %}
    <h1>子模板</h1>
{% endblock %}
```

#### 5.6.4 子模板可以 "扩展" 父模板中指定的内容

```html
{% block 块名 %}
    {{ super() }}
    <h1>子模板</h1>
{% endblock %}
```

注意：__Python中的语法在HTML中应用时要加 `{{  }}`，否则会当成标签的文本内容__

### 5.7 修改模板文件夹和静态文件夹名称

`app = Flask(__name__, templates_folder='t', static_folder='s')`

* 工程目录中的文件夹名称与参数设置名称保持一致
* 再次书写静态文件访问路径时，需要注意文件夹名称的变更

## 6. 网络请求

利用网络通信协议实现前后端数据交互，常用的网络通信协议：HTTP/HTTPS，规定数据传输格式

### 6.1 请求

客户端向服务端发送的消息

#### 6.1.1 请求组成

* 请求行
  * 请求方式 `GET/POST`
  * 资源路径 `/mp3/...`
  * 协议 `HTTP1.1`
* 请求头：使用字典方式存储相关信息
  * `content-type`：请求文件类型
* 空行
* 请求体
  * GET请求如果携带数据，以参数形式直接拼在URL后面，`(?key1=value1&key2=value2)`，没有请求体
  * 只有POST方式才有

### 6.2 响应

服务端接收请求并处理后，返回给客户端的消息（数据）

#### 6.2.1 响应组成

* 响应行
  * 协议 `HTTP1.1`
  * 响应状态码 `200/404`
  * 原因短句 `OK/Not Found`
* 响应头：描述响应回来的数据，以键值对存储
* 空行
* 响应体：保存相应数据

#### 6.2.2. 响应状态码

* 1xx
* 2xx
  * 200 正确接收到请求并已做出处理
* 3xx
  * 301 永久重定向
  * 302 临时重定向 e.g. 登录 --> 注册
  * 304 Not Modified 请求资源未发生改动，重定向至缓存中请求
* 4xx：客户端错误
  * 400 Bad Request 错误请求
  * 403 禁止访问 e.g. 后台管理
  * 404 请求的资源不存在
  * 405 请求方式不被允许
* 5xx：服务端错误
  * 500 服务器内部错误
  * 502 网关错误

## 7. Flask中的请求与响应

### 7.1 请求对象request

在request对象中封装了所有跟当前请求相关的信息

#### 7.1.1 使用步骤

* `from flask import request`
* 在视图函数中获取`request`对象的内部信息

#### 7.1.2 request对象常用属性

属性|说明|示例
-|-|-
`scheme`|获取此次请求使用的协议
`method`|获取请求方式
`args`|获取`GET`方式提交的数据
`form`|获取`POST`方式提交的数据
`cookies`|获取浏览器cookies中保存的数据
`files`|获取上传的文件
`path`|获取请求的资源路径 (不带参数)|`/request`
`full_path`|获取请求的资源路径 (带参数)|`/request?uname=zs&upwd=123456`
`url`|获取完整的请求地址|`http://127.0.0.1:5000/request?uname=zs&upwd=123456`
`headers`|获取请求消息头，使用键值对保存相关消息

### 7.2 获取请求中的数据

#### 7.2.1 获取GET中的数据

* `request.args['key']`
* `request.args.get('key', 默认值)`
* `request.args.getlist('key')` 适用于一个key对应多个值的情况，例如复选框

注意：get如果未携带数据，在视图函数中直接读取`request.args['']`数据，报400错误

#### 7.2.2 获取POST请求数据

需要在路由中指定：__`methods=['GET', 'POST']`__

* `request.form['key']`
* `request.form.get('key')`
* `request.form.getlist('key')`

注意：post方式即使未携带数据，直接获取字典中的值，返回为空

### 7.3 页面重定向

* `from tkinter import redirect`
* 使用函数`redirect('重定向地址')`
* 视图函数中返回 `return redirect('重定向地址')`

### 7.4 页面源

当前的请求是从哪一个源地址发起的，保存在消息头中 (`"Referer":""`)

从请求头中获取源地址 (不一定存在)

```py
if "Referer" in request.headers:
    print(request.headers['Referer'])
```

### 7.5 文件上传

1. 前端：使用表单控件 `type="file"` 向服务器发送文件，因为这些是二进制数据，必须设置表单的提交方式和编码类型 `<form action="" method="post" enctype="multipart/form-data">`
2. 服务器端: 使用 `request.files` 获取上传的文件，返回字典

  ```py
  f = request.files['name']#通过键获取指定的文件
  # 拼接文件名和路径并保存文件, 一般利用时间拼接文件名
  f.save(filename)
  ```

e.g.

```py
@app.route('/01-file', methods=['GET', 'POST'])
def file_view():
    if request.method == 'GET':
        return render_template('01-file.html')
    else:
        if 'uimg' in request.files:#防止用户没有上传图片
            # 接收前端传递的图片
            file = request.files['uimg']
            # 取当前时间作为名称
            ftime = datetime.datetime.now().strftime("%Y%m%d%H%M%S%f")
            # 获取上传文件的扩展名
            ext = file.filename.split('.')[-1]
            filename = ftime + '.' + ext
            # 准备上传路径,建议利用绝对路径
            print(__file__)
            basedir = os.path.dirname(__file__)
            print('basedir:', basedir)
            # 拼上传的完整路径
            upload_path = os.path.join(basedir, "static/images/", filename)# 该方法在字符串间加'/'拼成完整路径
            file.save(upload_path)
            return "上传文件成功"

            # 保存相对路径
            # import datetime
            # filename = datetime.datetime.now() + '.' + filename.split('.')[-1]
            # f.save('static/images/' + filename)
            # return "上传文件成功"
```

## 8. 模型 Models

模型-根据数据库表结构而创建出来的class。一张表一个类，一个字段一个属性

### 8.1 模型框架 - ORM

ORM - Object Relational Mapping，对象关系映射

三大特征：

* 数据表到编程类的映射
* 数据类型的映射
* 关系映射：将数据库中表与表间的关系，对应到编程语言中类与类之间的关系

ORM的优点

* 封装操作提升效率
* 省略庞大的数据访问层

### 8.2 Flask中的ORM框架

#### 8.2.1 SQLAlchemy

1. 安装SQLAlchemy：`pip3 install sqlalchemy`
2. Flask 中需要使用flask-sqlalchemy支持包：`pip3 install flask-sqlalchemy`
3. 创建数据库：`create database flaskDB default charset utf8 collate utf8_general_ci;`
4. Flask中配置数据库: `app.config['SQLALCHEMY_DATABASE_URI'] = "mysql://用户名:密码@数据库服务器地址:端口号/数据库名称"`

  ```py
  app.config['SQLALCHEMY_DATABASE_URI'] = "mysql://root:123456@127.0.0.1:3306/flaskDB"
  ```

数据库工具：

1. Navicat for mysql
2. PowerDesigner

### 8.3 定义模型类

作用：通过编写模型类的方式，让程序自动生成数据库表。模型类也称为实体类

语法：

```py
class MODELNAME(db.Model):
    __tablename__ = "TABLENAME" # 缺省，modelname
    COLUMN_NAME = db.Column(db.TYPE, OPTIONS)
```

说明：

* MODELNAME：定义模型类名称，参考表名
* TABLENAME：指定要映射到的表名，如果不存在，则创建表
* COLUMN_NAME：属性名，映射到数据表中就是列名
* TYPE：映射到列的数据类型
* OPTIONS：列选项

db.TYPE 列类型:

类型名|数据库|Python|说明
-|-|-|-
Integer|int|int
Float|float|float|浮点数
Numeric|numeric|Decimal|定点数
String|varchar|str
Text|text|str
Boolean|tinyint|bool
Date|date|datetime.date
DateTime|datetime|datetime.datetime

OPTIONS 列选项:

选项名|说明
-|-
`autoincrement`|如果取值为True，则自增长。如果列类型为整型且为主键，默认自增长
`primary_key`|如果取值为True，表示该列为主键
`unique`|如果取值为True，表示该列取值唯一
`index`|如果取值为True，表示该列加索引
`nullable`|如果取值为True，表示该列可为空
`default`|指定该列默认值

### 8.4 数据库的迁移

数据库迁移：将实体类的改动再映射回数据库。

依赖于两个第3方库：

* `flask-script`:
  * 安装：`pip3 install flask-script`
  * 包：flask_script
  * 类：Manager
  * 作用：对项目进行管理，启动项目，为项目增加指令
* `flask-migrate`
  * 安装：`pip3 install flask-migrate`
  * 包：flask_migrate
  * 类：Migrate -- 协调app和db之间的关系
  * 类：MigrateCommand -- 在终端中提供实体类迁移的指令

`python3 run.py db 指令`

指令|作用|特点
-|-|-
init|执行项目和数据库的初始化操作|一个项目只执行一次即可
migrate|将编辑好的实体类生成一个中间文件并保存|只要检测到实体类有更改，就会生成中间文件
upgrade|将中间文件映射回数据库

e.g.

```bash
python3 run.py db init
python3 run.py db migrate
python3 run.py db upgrade
```

### 8.5 CRUD 增删改查

#### 8.5.1. 增加 - C

* 创建实体类对象，并为对象的属性赋值

```py
user = Users()
user.username = 'Bran'
user.age = 30
userisActive = True
user.birthday = "2000-01-02"
```

* 将实体对象保存回数据库

```py
de.session.add(user) # 增加数据
db.session.commit() # 提交事务, 手动提交
```

* 可以在app设置自动提交 `app.config['SQLALCHEMY_COMMIT_ON_TEARDOWN'] = True`

#### 8.5.2. 查询 - R

##### 8.5.2.1 基于 `db.session.query()` 进行查询

* 参数：要查询的列，如果查询多个列的话使用 , 隔开，如果查询所有列，使用实体类名。

示例语句|含义
-|-
`db.session.query(Users.id, Users.username)`|查询实体类中的id和username
`db.session.query(Users)`|查询Users实体类中所有列
`db.session.query(Users, Pets)`|查询Users以及Pets类的所有列

* 返回值：返回一个Query对象，类型为BaseQuery

##### a. 查询执行函数

* 作用：在query的基础上得到最终的查询结果
* 语法：`db.session.query(xxx).查询执行函数()`

函数|作用
-|-
`all()`|以列表的方式返回所有数据
`first()`|以实体对象的方式返回第一条数据，没有查询到数据则返回None
`first_or_404()`|效果同上，没查询到结果则响应404
`count()`|查询结果的数量

##### b. 查询过滤器函数

作用：为查询追加条件
语法：`db.session.query().过滤器函数().执行函数()`

函数|作用|语法
-|-|-
`filter()`|可实现各种各样的查询条件
`filter_by()`|只做等值条件判断|`db.session.query(Users).filter_by(id=2).all()`
`limit()`|获取限定行数|`db.session.query(Users).limit(2).all()`获取前2条数据
`offset()`|指定结果的偏移量|`db.session.query(Users).offset(3).all()`跳过前3条

##### c. order_by() 排序

`db.session.query(xxx).order_by("排序规则")`

e.g.

```py
db.session.query(User).order_by("age")
db.session.query(User).order_by(Users.age.desc())
db.session.query(User).order_by(Users.age.desc(), Users.id)
db.session.query(User).filter(Users.age > 18).order_by("age desc").all()
```

##### d. 聚合查询

* 基本的聚合查询

```py
from sqlalchemy import func # func 中提供了所有的聚合函数
db.session.query(func.聚合函数(实体类.属性), func.聚合函数(实体类.属性)).all()
```

聚合函数

函数|作用
-|-
sum()|
count()|
max()|
min()|
avg()|

* 分组的聚合查询，利用 `group_by()`

```py
db.session.query(查询列, 聚合列)
    .filter(条件) # 分组前数据筛选 - where
    .group_by(分组列) # 分组 - group by
    .having(条件) # 分组后筛选 - having
```

e.g.

`sql: select isActive, count(*) from users where age > 18 group by isActive having count(id) > 2;`

```py
db.session.query(Users.isActive, func.count(Users.id))
    .filter(Users.age > 18).group_by('isActive')
    .having(func.count(Users.id) >= 2 )
```

##### 8.5.2.2 基于 `实体类` 进行查询

语法：`实体类.query.查询过滤器函数().查询执行函数()`

e.g.
`Users.query.all()`
`Users.query.filter(Users.age>=20).all()`

#### 8.5.3 修改 - U

1. 查：查询要修改的对象实体
2. 改：通过 `对象.属性 = 值` 修改数据
3. 存：`db.session.add(对象)`

#### 8.5.4 删除 - D

1. 查：查询出要删除的实体对象
2. 删：`db.session.delete(对象)`

### 8.6 关系映射

#### 8.6.1 一对多

* A表中的一条数据可以管理B表中的多条数据，B表中的一条数据只能关联到A表中的一条数据
* 利用主外键的关系来实现一对多：`一`表中有主键，`多`表中增加外键，表示对`一`表的引用

在SQLAlchemy中的实现

##### 8.6.1.1 原则：

* 在 `多` 的实体类中增加对 `一` 实体类的引用
* 在 `一` 实体类中增加 `关联属性` 以及 `反向引用关系属性`

##### 8.6.1.2 实现：

1. 在 `多` 实体类中，增加一个属性/列，要引用到 `一` 表/类的主键
  `属性名/外键列名 = db.Column(db.TPYE, db.ForeignKey('主键表名.主键列'))`
2. 在 `一` 的实体类中增加 `关联属性` 和 `反向引用关系属性`。目的：为了创建类（对象）与类（对象）之间的关联关系

##### 8.6.1.3 名词解释

* `关联属性`：在`一`的实体类中，要增加`一`个 `<<属性>>` 来获得`多`的实体对象们
* `反向引用关系属性`：在`一`的实体类中的操作最终加到`多`的实体类中，即在`多`的实体类中，要增加`一`个`<<属性>>`来获取到对应的`一`的实体对象

##### 8.6.1.4 语法：

* 在一的实体类中增加: `属性名 = db.relationship('多的实体类名', backref='定义方向引用关系属性名', lazy='dynamic')`

示例：

```py
# "多"的实体类
class Teacher(db.Model):
    id = db.Column(db.Integer,primary_key=True)
    tname = db.Column(db.String(30),nullable=False)
    tage = db.Column(db.Integer,nullable=False)
    #增加一个外键列-course_id,引用自Course类(course表)的主键id
    course_id = db.Column(
            db.Integer,
            db.ForeignKey('course.id')
    )

# "一"的实体类
class Course(db.Model):
    id = db.Column(db.Integer,primary_key=True)
    cname = db.Column(db.String(30),nullable=False)
    #增加对Teacher的关联属性和反向引用关系属性
    teachers = db.relationship(
            'Teacher',
            backref="course",
            lazy="dynamic"
    )
```

lazy:指定如何加装相关的记录们

取值：

* select 首次访问属性时就加载相关数据
* immediate 只要用到关联属性就马上加载一次数据
* subquery 功能同上
* noload 永不加载数据
* dynamic 不加载记录，但提供加载记录的查询

以上操作执行完毕后,对数据库和程序的影响:

* 对数据库的影响
    在teacher表中增加一个列 - `course_id(外键)`,表示的就是对course的主键id的一个引用
* 对程序的影响
  * 在Teacher类中，会增加一个属性 - `course_id`, 表示的是该Teacher对象关联的课程的id值
  * 在Course类中，会增加一个属性 - `teachers`, 表示的是该Course对象所关联对应的所有的Teacher们
  * 在Teacher类中，会增加一个属性 - `course`, 表示的是该Teacher对象所关联对应的一门Course的对象

##### 8.6.1.5 关联数据查询

通过一的对象，找关联的多的对象们。通过关联属性来表示对应的类型数据的查询对象

示例：

```py
course = Course.query.filter_by(id=1).first()
course.teacher.all()
```

通过多的对象，找关联的一的对象。通过反向引用关系属性

```py
tea = Teacher.query.filter_by(tname = 'QTX')
cour = tea.course
```

#### 8.6.2 一对一

A表中的一条数据只能关联到B表中一条数据上，B表中的数据也只能关联到A表上的条数据上

##### 8.6.2.1 实现

在关联的两张表中的任意一张表中：

* 增加外键，并引用另一张表的主键
* 并且要增加唯一约束

##### 8.6.2.2 在ORM中实现

* 在任意一个实体类增加外键以及唯一约束

`外键列名 = db.Column(db.TYPE, db.ForeignKey('主表.主键'), unique=True)`

* 关联属性和反向引用关系属性

在关联的两个类中的另一个类中增加 `属性名 = db.relationship("关联的实体类的名称", backref="发现引用关系属性名", uselist=False)`

`uselist`: 设置为False，表示关联属性是一个标量，而并非一个列表

#### 8.6.3 多对多

A表中的一条数据只能关联到B表中多条数据上，B表中的数据也只能关联到A表上的多条数据上

##### 8.6.3.1 多对多数据库中实现

依靠第3张关联表的方式来实现

```sql
select cname, sname
from student as s
inner join student_course as sc
on s.id = sc.student_id
inner join course as c
on sc.course_id = c.id

select sname, cname
from student as s, student_course as sc, course as c
where s.id = sc.student_id and c.id = sc.course_id
```

##### 8.6.3.2 在ORM中实现多对多

* 创建第三张表（类），并创建两个外键表示引用自两张表
* 关联属性和反向引用关系属性
  * 在关联的两个类中的任意一个类中增加：

  ```py
  属性名 = db.relationship("另一个类名",
                        secondary="第3张关联表表名",
                        lazy='dynamic',
                        backref=db.backref(
                            '反向引用关系属性名',
                            lazy='dynamic')
                        )
  ```

## 9. Cookies

cookies：一种数据存储的手段。将一段文本保存在浏览器上的一种手段，并且可以长时间保存
cookies特点：长时间保存，明文，可以通过浏览器修改，有浏览器之分，有网站之分

flask中使用cookies如下：

### 9.1 保存数据到cookies中

通过响应对象将数据保存进cookies中

#### 9.1.1 响应对象的构建

* 重定向就是响应对象 `resp = redirect("/xxx")`
* 通过 `make_response()` 将字符串构建成响应对象

  ```py
  from  flask import make_response
  resp = make_response(""或render_template())
  ```

#### 9.1.2 保存cookies的语法

`响应对象.set_cookie(key, value, max_age)`

* key：保存到cookie中的数据名称
* value: 保存到cookie中的数据的值
* max_age： 最大的存活时长，以秒为单位

### 9.2 获取cookies的值

通过 `request.cookies` 获取所有的cookies的值，request.cookies 的类型是字典

### 9.3 删除cookie的值

`响应对象.delete_cookie('key')`

## 10. Session

session：会话，当浏览器打开时跟一个服务器交互的过程就是一次会话
session目的：为了保存会话中所涉及到的一些信息

session在flask中的实现如下

### 10.1 配置 SECRET_KEY

`app.config['SECRET_KEY'] = "123456"`

### 10.2 使用session

`from flask import session`

#### 10.2.1 向session中保存数据

`session['key'] = value`

#### 10.2.2 从session中获取数据

`value = session['key']`
`value = session.get('key')`

#### 10.2.3 删除session中的数据

`del session['key']`

### 10.3 session PK cookies

cookies|session
:-|-:
保存在客户端|保存在服务器
明文、可修改，安全性低|安全性高
长久保存|临时性存储

## 11. 蓝图 Bluprint

在一个大的Flask实例app下面创建蓝图,分别管理不同路由和视图函数

1. 在分支模块中

```py
from flask import Blueprint
app_branch01 = Blueprint('蓝图别名', __name__)

@app_branch01.route('/')
def index():
    pass
```

2. 在主分支模块中

```py
from branch01 import app_branch01 as 别名
app.register_blueprint(别名) # 注册蓝图
```

## 12. Flask 项目结构

![Flask项目结构](image/flask_project_structure.png)
