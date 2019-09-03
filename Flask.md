# Flask

## 1. 概述

Flask是一个基于Python并且依赖于Jinja2模板引擎和WerkZeug WSGI服务的框架

* WSGI：Web Server Gateway Interface，Web服务网关接口，提供处理网络请求相关功能

Flask是MTV框架模式



__安装Flask__

* `pip3 install flask`



## 2. 创建flask应用

```python
# run.py
from flask import Flask

# 创建Flask的程序实例
app = Flask(__name__)

if __name__ == '__main__':
    app.run(
        debug=True,  # 以调试模式启动app, 产品上线时改为False
        host='0.0.0.0',
        # 设置IP，默认为'127.0.0.1', 只能本地访问，改为'0.0.0.0'后局域网中其他计算机可以连接访问
        port='5000'  # 设置端口，flask默认为5000
    )
```



## 3. Flask的路由

路由是为了匹配用户的请求地址，匹配成功则会自动执行视图函数，视图函数中必须有返回值，返回字符串显示到相应的页面中。

### 3.1 定义路由及视图函数

```python
@app.route('/地址')  # 路由
def funcName():
    ...
    return ""  # 响应到页面中内容, 必须是字符串, 浏览器会解析得到的字符串
```

```python
@app.route('/') # '/'表示根路径
def index():
	# 匹配到路径后执行的视图函数
    return "Home"
```

### 3.2. 带参数的路由

```python
@app.route('/login/<name>/<age>')
# name age 就是路由中的参数，用<>括起来
# 路由中的参数统一为字符串类型
def login(name, age):  # 视图函数定义中也要有相应变量名
    return "<h1>%s, %s</h1>" % (name, age)
```

注意：__路径中的参数变量永远是字符串类型__，如果想要转为其他类型，需要用到类型转换器

### 3.3 类型转换器

code|含义|示例
-|-|-
`缺省`|字符串，不能包含'/'|`@app.route('/login/<age>')`
`int:`|转换整数|`@app.route('/login/<int:age>')`
`float:`|转换小数|`@app.route('/login/<float:age>')`
`path:`|字符串，允许包含'/'|

### 3.4 多个URL执行同一个视图函数

```python
@app.route('/')
@app.route('/index')
@app.route('/home')
def index():
    return "首页"
```



## 4. 模板

模板是一种特殊的HTML文件，Python+HTML网页结构，允许在模板文件中使用变量，定义流程控制。使用模板可以使视图函数专注处理业务逻辑，将页面渲染交由模板控制

### 4.1 使用流程

* 导入 __`render_template`__
* 在视图函数中使用 __`render_template('模板文件')`__ ，生成字符串交由浏览器解析
* 项目中创建 __`"templates"`__ 文件夹，存放模板文件，即HTML文件

```python
from flask import render_template

@app.route('/info')
def index():
    # 返回模板文件，可以传递变量
    return render_template('index.html', name='Alan', age=20)
```



### 4.2 将变量传递到模板中

- 从视图函数中获取相关的变量，传递到模板文件中，__`render_template('模板文件', key1=value1, key2=value2)`__ 函数中可以传递若干键值对，其中的`key`就是模板文件中使用的变量
- 使用`locals()`返回字典，包含所有局部变量

```python
# 传递几个变量
@app.route('/show')
def show():
    return render_template('show.html', name='Alan', age=20)

# 传递许多变量
@app.route('/show')
def show():
    # 使用locals()返回字典，包含所有局部变量
    return render_template('show.html', params=locals())
```

```python
params = locals()
{
    'age': 20,
    'dic': {'age': 22, 'name': 'Aray'},
    'lst': ['draw', 'dance'],
    'name': 'Bran',
    'tup': ('Sansa', 24)
}
```



### 4.3 模板中使用后端传递过来的变量

语法： __`{{ 变量名 }}`__

```jinja2
<h1>
    {{ name }}
</h1>
<span>年龄是:{{ age }}</span>
<h2>
    // 列表 元组 字典都可以使用 [index/key] 和 点语法访问
    {{ params.age }}
    {{ params['name'] }}
    {{ params.dict.name }}
    {{ params.list[0] }}
    {{ params.list.0 }}
    // 对象属性和方法使用点语法访问
    {{ params.cat.name }}
    {{ params.cat.play() }}
</h2>
```



### 4.4 过滤器

允许模板中的变量在输出前修改成其他的值，修改显示。语法 __`{{ 变量|过滤器1|过滤器2 }}`__

* `upper` 转大写字母
* `lower` 转小写字母
* `title` 首字母大写
* `first` 获取列表中第一个
* `last` 获取列表中最后一个
* `length` 获取列表长度
* `defualt()` 如果变量未赋值，可采用默认值
* `trim` 去掉字符串两端空格

```jinja2
<span>{{ params.s1|upper }}</span>
<span>{{ params.s2|title }}</span>
<span>{{ params.s3|trim }}</span>
<span>{{ params.list1|first }}</span>
<span>{{ params.list1|length }}</span>
<span>{{ params.name|default("alan") }}</span>
```



### 4.5 控制代码块

在模板文件中书写 `条件语句` 和 `循环语句`，使用 __`{% %}`__

#### 4.5.1 if 语句

```jinja2
{% if 条件 %}
    条件成立时执行，允许书写静态标签，也可以书写变量
{% endif %}
```

```jinja2
{% if 条件 %}
    条件成立时执行，允许书写静态标签，也可以书写变量
{% else %}
    条件不成立时执行，允许书写静态标签，也可以书写变量
{% endif %}
```

```jinja2
{% if 条件 %}
    ...
{% elif 条件2 %}
    ...
{% else %}
    ...
{% endif %}
```

#### 4.5.2 for 语句

```jinja2
{% for 变量 in 可迭代元素 %}
    ...
{% endfor %}
```

for语句内部属性，表示循环相关信息

* loop.index 表示当前循环的次数，默认从1开始计
* loop.index0 表示当前循环的次数，从0开始计
* loop.first 表示是否为第一次循环，值为true表示第一次循环
* loop.last 表示是否为最后一个循环

```jinja2
{% for user in params.info %}
    <h3
        		{# 注释 #}
                {% if loop.first %}
                    class="c1"
                {% elif loop.last %}
                    class="c2"
                {% else %}
                    class="c3"
                {% endif %}

    >姓名：{{ user.name }},年龄：{{ user.age }}</h3>
{% endfor %}
```



### 4.6 反向解析路由

在模板中利用 __`url_for("视图函数名")`__ 实现路由的反向解析，即根据指定的视图函数返回对应的路由地址

```jinja2
{# 根据视图函数解析对应的URL #}
<a href="{{ url_for('index') }}"></a> {# /index #}
<a href="/index"></a>

{# 反向解析带参数的路由，函数中接收多个参数 #}
<a href="{{ url_for('login', name='cm', age=20) }}">login</a>
<a href="/login/cm/20">login</a>

{# 反向路由解析可以用来生成静态文件路径 #}
<link rel="stylesheet" href="{{ url_for('static', filename='css/show.css') }}">
<link rel="stylesheet" href="/static/css/show.css">
```



### 4.7 静态文件

* 不能与服务器交互的文件都是静态文件 (css、js、images、audio)
* 所有静态文件都要存储在一个名为 __`"static"`__ 的文件夹下，Flask程序会自动查找
* 静态文件的访问：
  - 使用 __`"/static/子路径"`__ 访问
  - 反向解析带参数的路由 __`url_for()`__

```jinja2
{# 反向解析路由，自动生成静态文件路径 #}
<link href="{{ url_for('static', filename='css/base.css') }}">
<!-- 等价于 -->
<link href="/static/css/base.css">{# 直接使用路径 #}
```



### 4.8 模板的继承

与类的继承相似。如果两个页面中大部分内容与结构都一致，可以采用模板继承

- __父模板指定可以被子模板重写的内容__

```jinja2
{% block 块名 %}
    <h1>父模板</h1>
{% endblock %}
```

- __子模板继承父模板__

```jinja2
{% extends '父模板名称.html' %}
```

- __子模板可以 "重写" 父模板中指定的内容__

```jinja2
{% block 块名 %}
	{# 重写父模板内容 #}
    <h1>子模板</h1>
{% endblock %}
```

- __子模板可以 "扩展" 父模板中指定的内容__

```jinja2
{% block 块名 %}
	{#
        扩展父模板内容
        1. 先用 super() 调用父模板内容
        2. 再书写扩展部分代码
    #}
    {{ super() }}
    <h1>这是子模板扩展内容</h1>
{% endblock %}
```

注意：__Python中的语法在HTML中应用时要加 `{{  }}`，否则会当成标签的文本内容__



### 4.9 修改模板文件夹和静态文件夹名称

```python
app = Flask(__name__, templates_folder='t', static_folder='s')  # 一般不推荐
```

* 工程目录中的文件夹名称与参数设置名称保持一致
* 再次书写静态文件访问路径时，需要注意文件夹名称的变更



## 5. 网络请求

利用网络通信协议实现前后端数据交互，常用的网络通信协议：HTTP/HTTPS，规定数据传输格式

### 5.1 请求

客户端向服务端发送的消息

#### 5.1.1 请求组成

* 请求行
  * 请求方式 `GET/POST`
  * 资源路径 `/mp3/...`
  * 协议 `HTTP1.1`
* 请求头：使用字典方式存储相关信息
  * `content-type`：请求文件类型
* 空行
* 请求体
  * GET请求如果携带数据，以参数形式直接拼在URL后面，`(?key1=value1&key2=value2)`，没有请求体
  * 只有POST方式才有请求体

### 5.2 响应

服务端接收请求并处理后，返回给客户端的消息（数据）

#### 5.2.1 响应组成

* 响应行
  * 协议 `HTTP1.1`
  * 响应状态码 `200/404`
  * 原因短句 `OK/Not Found`
* 响应头：描述响应回来的数据，以键值对存储
* 空行
* 响应体：保存相应数据

#### 5.2.2. 响应状态码

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



## 6. Flask中的请求与响应

### 6.1 请求对象request

在 __request__ 对象中封装了所有跟当前请求相关的信息

#### 6.1.1 使用步骤

* `from flask import request`
* 在视图函数中获取`request`对象的内部信息

#### 6.1.2 request对象常用属性

属性|说明|示例
-|-|-
`scheme`|获取此次请求使用的协议|
`method`|获取请求方式，默认为GET|
`args`|获取`GET`方式提交的数据|
`form`|获取`POST`方式提交的数据|
`cookies`|获取浏览器cookies中保存的数据，使用键值对保存相关消息|
`files`|获取上传的文件|
`path`|获取请求的资源路径 (不带参数)|`/request`
`full_path`|获取请求的资源路径 (带参数)|`/request?uname=zs&upwd=123456`
`url`|获取完整的请求地址|`http://127.0.0.1:5000/request?uname=zs&upwd=123456`
`headers`|获取请求消息头，使用键值对保存相关消息|

```python
from flask import reqeust

@app.route('/request')
def index():
    # 查看请求对象内部属性
    print(dir(request))
    #从请求消息头中获取源地址（不一定有值）
    if "Referer" in request.headers:
        print(request.headers['Referer'])
    return render_template('reqeust.html', params=request)
```

```jinja2
<h1>scheme:{{ params.scheme }}</h1>
<h1>method:{{ params.method }}</h1>
<h1>args:{{ params.args }}</h1>
<h1>cookies:{{ params.cookies }}</h1>
<h1>url:{{ params.url }}</h1>
```



### 6.2 获取请求中的数据

#### 6.2.1 获取GET中的数据

* `request.args['key']`
* `request.args.get('key', 默认值)`
* `request.args.getlist('key')` 适用于一个key对应多个值的情况，例如复选框

注意：get请求如果未携带数据，在视图函数中直接读取`request.args['']`数据，报400错误

```python
# 获取get请求提交的数据
@app.route('/get')
def get():
    if request.method == 'GET':
        # print(request.args.get['name']) # 第一次因为没有数据报错
        print(request.args.get('pwd', '123456'))  # get('key','default')
        print(request.args.getlist('hobby'))  # ['sing', 'sport']
    return render_template('get.html')
```

```jinja2
<form action="/get" method="get" enctype="application/x-www-form-urlencoded">
    {# 
    	get请求提交数据
    	action: 数据提交的地址
    	method: 数据提交的方式
    	enctype: 数据编码方式
    #}
    <p>
        用户名:
        <input type="text" name="name">
    </p>
    <p>
        密码:
        <input type="password" name="pwd">
    </p>
    <p>
        兴趣:
        <input type="checkbox" name="hobby" value="sing">sing
        <input type="checkbox" name="hobby" value="dance">dance
        <input type="checkbox" name="hobby" value="sport">sport
    </p>
    <input type="submit">
</form>
```

get请求提交的数据直接添加在路由后面

```python
/get?name=cm&pwd=123&hobby=sing&hobby=sport
```



#### 6.2.2 获取POST请求数据

需要在路由中指定：__`methods=['GET', 'POST']`__

* `request.form['key']`
* `request.form.get('key')`
* `request.form.getlist('key')`

注意：post方式即使未携带数据，直接获取字典中的值，返回为空

```python
# 获取post请求提交的数据
# methods设置当前地址允许的请求方式，默认get
@app.route('/post', methods=['GET', 'POST'])
def post():
    # request.method获取此次请求的方式
    if request.method == 'GET':
        return render_template('post.html')
    else:
        # 获取post请求中的数据
        print(request.form.get('name'))
        print(request.form['pwd'])
        print(request.form.getlist('hobby'))  # ['dance']
        return "post ok"
```

```jinja2
<form action="/post" method="post" enctype="multipart/form-data">
    {# post请求提交数据 #}
    <p>
        用户名:
        <input type="text" name="name">
    </p>
    <p>
        密码:
        <input type="password" name="pwd">
    </p>
    <p>
        兴趣:
        <input type="checkbox" name="hobby" value="sing">sing
        <input type="checkbox" name="hobby" value="dance">dance
        <input type="checkbox" name="hobby" value="sport">sport
    </p>
    <input type="submit">
</form>
```



### 6.3 页面重定向

* `from tkinter import redirect`
* 使用函数`redirect('重定向地址')`
* 视图函数中返回 `return redirect('重定向地址')`

```python
# 登录页提交数据后重定向回源地址, 没有源地址回首页
@app.route('/signin', methods=['GET', 'POST'])
def singin():
    if request.method == 'GET':
        if 'Referer' in request.headers:
            print(request.headers['Referer'])
            referer = request.headers['Referer']
        else:
            referer = ''
        # 将源地址交个模板，防止丢失
        return render_template('signin.html', referer=referer)
    else:
        name = request.form.get('name')
        print(name)
        referer = request.form.get('referer', '')
        print(referer)
        if not referer:  # 如果为空, 则重定向回首页
            res = redirect('/')
        else:  # 否则重定向回源地址
            res = redirect(referer)
        return res
```

```jinja2
<form action="/signin" method="post" enctype="multipart/form-data">
    {# 保存传递过来的源地址, 并设置隐藏 #}
    <input type="hidden" name="referer" value="{{ referer }}">
    <p>
        用户名:
        <input type="text" name="name">
    </p>
    <input type="submit">
</form>
```



### 6.4 页面源

当前的请求是从哪一个源地址发起的，保存在消息头中 (`"Referer":""`)

```python
# 从请求头中获取源地址 (不一定存在)
if "Referer" in request.headers:
    print(request.headers['Referer'])
```



### 6.5 文件上传

1. 前端：使用表单控件 `type="file"` 向服务器发送文件，因为这些是二进制数据，必须设置表单的提交方式和编码类型 `<form action="" method="post" enctype="multipart/form-data">`
2. 服务器端: 使用 `request.files` 获取上传的文件，返回字典

  ```python
  f = request.files['name']  #通过键获取指定的文件
  # 拼接文件名和路径并保存文件, 一般利用时间拼接文件名
  f.save(filename)
  ```

e.g.

```python
@app.route('/file', methods=['GET', 'POST'])
def file_views():
    if request.method == 'GET':
        return render_template('file.html')
    else:
        if 'img' in request.files:#防止用户没有上传图片
            # 1. 接收前端传递的图片
            file = request.files['img']
            # 2. 拼接年月日时分秒微秒作为文件名
            # 因为时间戳一般唯一, 可以防止文件名重复覆盖之前文件
            ftime = datetime.datetime.now().strftime("%Y%m%d%H%M%S%f")
            # 3. 获取上传文件的扩展名
            # file.filename 可以得到上传的文件的文件名
            ext = file.filename.split('.')[-1]
            filename = ftime + '.' + ext
            # 4. 准备上传路径
            # 此处使用相对路径, 不建议, 一般使用绝对路径
            upload_path = "static/upload/" + filename
            # 5. 保存文件
            file.save(upload_path)
            return "上传文件成功"
            
            
            # 准备上传路径,建议利用绝对路径
            print(__file__)
            basedir = os.path.dirname(__file__)
            print('basedir:', basedir)
            # 拼上传的完整路径
            upload_path = os.path.join(basedir, "static/images/", filename)# 该方法在字符串间加'/'拼成完整路径
            file.save(upload_path)
            return "上传文件成功"

            
```

```jinja2
<form action="/file" method="post" enctype="multipart/form-data">
    <p>
        头像
        <input type="file" name="img">
    </p>
    <input type="submit">
</form>
```



### 6.6 综合练习

![flask exercise](image/flask-文件上传练习.png)

```python
@app.route('/file-pro', methods=['GET', 'POST'])
def file_pro():
    if request.method == 'GET':
        return render_template('file-pro.html')
    else:
        title = request.form['title']
        type = request.form['type']
        content = request.form['content']
        print("标题:%s,类型:%s,内容:%s" % (title,type,content))
        # 判断文件上传
        if 'img' in request.files:
            file = request.files['img']
            # 利用函数式编程改写文件上传代码
            filename = generate_filename(file.filename)
            up_path = generate_upload_path(__file__, "static/upload", filename)
            print('__file__:', __file__) 
            # C:\Users\UI\Desktop\flaskdemo01\app.py
            # __file__ 绑定模块文件的绝对路径
            file.save(up_path)
        return "获取数据成功"

def generate_filename(filename):
    """
    通过原始文件名生成一个由时间戳来组成的新文件名
    :param filename: 原始文件名
    :return: 生成新的文件名
    """
    ftime = datetime.datetime.now().strftime("%Y%m%d%H%M%S%f")
    ext = filename.split('.')[-1]
    filename = ftime + '.' + ext
    return filename

def generate_upload_path(file, dirname, filename):
    """
    生成文件上传路径
    :param file: 获取当前文件的根路径的文件
    :param dirname: 保存文件的具体目录
    :param filename: 保存文件名称
    :return:
    """
    # 获取传入路径的文件夹部分
    base_dir = os.path.dirname(file)
    print(base_dir)  # C:\Users\UI\Desktop\flaskdemo01
    # 拼接上传的完整路径
    upload_path = os.path.join(base_dir, dirname, filename)
    return upload_path
```

```jinja2
<form action="/file-pro" method="post" enctype="multipart/form-data">
    <p>
        标题
        <input type="text" name="title">
    </p>
    <p>
        类型
        <input type="radio" name="type" value="1" checked>普通博客
        <input type="radio" name="type" value="2">公开博客
        <input type="radio" name="type" value="3">其他博客
    </p>
    <p>
        内容
        <textarea name="content" cols="100" rows="10"></textarea>
    </p>
    <p>
        图片
        <input type="file" name="img">
    </p>
    <p>
        <button>发表</button>
    </p>
</form>
```

以上所有代码见 [flaskdemo01](code/flaskdemo01)



## 7. 模型 Models

模型-根据数据库表结构而创建出来的class。一张表一个类，一个字段一个属性

### 7.1 模型框架 - ORM

ORM - Object Relational Mapping，对象关系映射

三大特征：

* 数据表到编程类的映射
* 数据类型的映射
* 关系映射：将数据库中表与表间的关系，对应到编程语言中类与类之间的关系

ORM的优点

* 封装操作提升效率
* 省略庞大的数据访问层

### 7.2 Flask中的ORM框架

1. 安装SQLAlchemy：`pip3 install sqlalchemy`
2. Flask 中需要使用flask-sqlalchemy支持包：`pip3 install flask-sqlalchemy`
3. 创建数据库：`create database flaskDB default charset utf8 collate utf8_general_ci;`
4. Flask中配置数据库: `app.config['SQLALCHEMY_DATABASE_URI'] = "mysql://用户名:密码@数据库服务器地址:端口号/数据库名称"`
5. 创建数据库对象
6. 创建管理对象
7. 创建迁移对象

```python
# 配置数据库
# 方法1
import pymysql
pymysql.install_as_MySQLdb()
#连接到MySQL中flaskDB数据库
app.config['SQLALCHEMY_DATABASE_URI'] = "mysql://root:123456@127.0.0.1:3306/flaskDB"

# 方法2
app.config['SQLALCHEMY_DATABASE_URI'] = "mysql+pymysql://root:123456@127.0.0.1:3306/flaskDB"

# 指定不要信号追踪
app.config['SQLALCHEMY_TRACK_MODIGICATIONS'] = False
# 指定程序的启动模式为调试模式
app.config['DEBUG'] = True

# 创建SQLAlchemy的实例
db = SQLAlchemy(app)

# 创建Manager对象并指定要管理的app
manager = Manager(app)
# 创建Migrate对象, 并指定关联的app和db
migrate = Migrate(app, db)
# 为manager增加数据库迁移指令
# 为manager增加一个子命令 -db(自定义), 具体操作由MigrateCommand提供
manager.add_command('db', MigrateCommand)
```



### 7.3 定义模型类

通过编写模型类的方式，让程序自动生成数据库表。模型类也称为实体类

```python
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

__db.TYPE 列类型：__

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

__OPTIONS 列选项：__

选项名|说明
-|-
`autoincrement`|如果取值为True，则自增长。如果列类型为整型且为主键，默认就是自增长
`primary_key`|如果取值为True，表示该列为主键
`unique`|如果取值为True，表示该列取值唯一
`index`|如果取值为True，表示该列加索引
`nullable`|如果取值为True，表示该列可为空
`default`|指定该列默认值

e.g.

```python
# 创建 Student 实体类，表名 student
# 1.id，主键，自增
# 2.sname，姓名，长度为30且不为空字符串
# 3.sage，年龄，整数
# 4.isActive ，启用状态，bool类型,默认为True
class Student(db.Model):
    id = db.column(db.Integer, primary_key=True)
    sname = db.Column(db.String(30), nullable=False)
    sage = db.Column(db.Integer, nullable=False)
    isActive = db.Column(db.Boolean, default=True)

# 创建Teacher类，表名 teacher
# 1.id ， 主键自增
# 2.tname ， 同 sname 一样
# 3.tage ，年龄 ，整数
class Teacher(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    tname = db.Column(db.String(30), nullable=False)
    tage = db.Column(db.Integer, nullable=False)

# 创建Course类，表名 course
# 1.id ， 主键自增
# 2. cname 课程名称，长度30字符串
class Course(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    cname = db.Column(db.String(30), nullable=False)

# 创建Wife类，表名 wife
# 1.id ， 主键自增
# 2. wname 妻子名称，长度30字符串
class Wife(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    wname = db.Column(db.String(30))
```

此时可以创建数据库表

```python
# 如果没有管理对象和迁移对象，则直接使用数据库对象方法操作数据库
db = SQLAlchemy(app)
#先删除所有表结构
db.drop_all()

#将所有的实体类生成对应的表结构
#前提:表不存在的情况下才能生成
db.create_all()
```

更好的方法是使用管理和迁移对象



### 7.4 数据库的迁移

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

```python
# 创建Manager对象并指定要管理的app
manager = Manager(app)
# 创建Migrate对象, 并指定关联的app和db
migrate = Migrate(app, db)
# 为manager增加数据库迁移指令
# 为manager增加一个子命令 -db(自定义), 具体操作由MigrateCommand提供
manager.add_command('db', MigrateCommand)

if __name__ == '__main__':
    # 启动服务的操作交给manager
    manager.run()
```

数据库 __管理，迁移__ 指令

指令|作用|特点
-|-|-
init|执行项目和数据库的初始化操作|一个项目只执行一次即可
migrate|将编辑好的实体类生成一个中间文件并保存|只要检测到实体类有更改，就会生成中间文件
upgrade|将中间文件映射回数据库

```bash
python3 run.py db init
python3 run.py db migrate
python3 run.py db upgrade
```



### 7.5 CRUD 增删改查

#### 7.5.1. 增加 - C

* 创建实体类对象，并为对象的属性赋值

```python
user = Users()
user.username = 'Alan'
user.age = 20
userisActive = True
user.birthday = "2000-01-02"
```

* 将实体对象保存回数据库

```python
de.session.add(user) # 增加数据，将数据保存回数据库
db.session.commit() # 提交事务, 手动提交
```

* 可以在app设置自动提交

```python
#指定执行完增删改之后的自动提交
app.config['SQLALCHEMY_COMMIT_ON_TEARDOWN'] = True
```



#### 7.5.2. 查询 - R

##### 7.5.2.1 基于 `db.session.query()` 进行查询

* 参数：要查询的列，如果查询多个列的话使用 , 隔开，如果查询所有列，使用实体类名。

示例语句|含义
-|-
`db.session.query(Users.id, Users.username)`|查询实体类中的id和username
`db.session.query(Users)`|查询Users实体类中所有列
`db.session.query(Users, Wife)`|查询Users以及Wifes类的所有列

* 返回值：返回一个Query对象，类型为BaseQuery

```python
@app.route('/query')
def query_views():
    # 1. 查询Users实体中的id, username两列的值
    query = db.session.query(Users.id, Users.username)
    print(query)
    # SELECT users.id AS users_id, users.username AS users_username FROM users
    print("type(query):", type(query))  # <class 'flask_sqlalchemy.BaseQuery'>
	
	# 2. 查询Users实体中所有数据
    users = db.session.query(Users).all()
    for u in users:
        print("id:%d, 用户名:%s, 年龄:%d, 邮箱:%s" % (u.id, u.username, u.age, u.email))
    # id: 1, 用户名: Alan, 年龄: 20, 邮箱: alan @ xmail.com
    # id: 2, 用户名: Ben, 年龄: 21, 邮箱: ben @ xmail.com
    
    return "Query OK"
```



##### a. 查询执行函数

* 作用：在query的基础上得到最终的查询结果
* 语法：`db.session.query(xxx).查询执行函数()`

函数|作用|语法
-|-|-
`all()`|以列表的方式返回所有数据|db.session.query(Users).all()
`first()`|以实体对象的方式返回第一条数据，没有查询到数据则返回None|db.session.query(Users).first()
`first_or_404()`|效果同上，没查询到结果则响应404|
`count()`|查询结果的数量|db.session.query(Users).count()



##### b. 查询过滤器函数

作用：为查询追加条件
语法：`db.session.query().过滤器函数().执行函数()`

函数|作用|语法
-|-|-
`filter()`|可实现各种各样的查询条件|`db.session.query(Users).filter(条件1, 条件2)` `db.session.query(Users).filter(条件1)filter(条件2)`
`filter_by()`|只做等值条件判断|`db.session.query(Users).filter_by(id==2).all()`
`limit()`|获取限定行数|`db.session.query(Users).limit(2).all()`获取前2条数据
`offset()`|指定结果的偏移量|`db.session.query(Users).offset(3).all()`跳过前3条

```python
@app.route('/filter')
def filter_views():
    # 4.查询isActive为True或者年龄大于30岁的Users
    # or : 使用　or_ 函数
    from sqlalchemy import or_
    users = db.session.query(Users).filter(
        or_(
            Users.isActive==True,
            Users.age>=30
        )
    ).all()
    print(users)
    return "Query OK"
```

```python
# 分页查询
@app.route('/page')
def page_views():
    # 分页查询
    pageSize = 2  # 每页显示的数量
    currentPage = int(request.args.get('currentPage', 1))  # 当前页
    # 查询currentPage页的数据
    # 跳过(currentPage-1) * pageSize条数据, 再获取当前pageSize条数据
    ost = (currentPage-1) * pageSize
    users = db.session.query(Users).offset(ost).limit(pageSize).all()

    # 通过pageSize和总记录计算尾页页码
    totalCount = db.session.query(Users).count()
    lastPage = math.ceil(totalCount / pageSize)

    # 计算上一页页码
    # 如果currentPage>1， 上一页就是currentPage-1, 否则就是1
    prevPage = 1
    if currentPage > 1:
        prevPage = currentPage - 1
    # 计算下一页页码
    # 如果currentPage<lastPage，那么下一页就是currentPage+1，否则下一页就是尾页
    nextPage = lastPage
    if currentPage < lastPage:
        nextPage = currentPage + 1

    return render_template('page.html', params=locals())
```



##### c. 模糊查询

| 函数                | 说明           | 语法                                                         |
| ------------------- | -------------- | ------------------------------------------------------------ |
| like                | 包含某个关键词 | `db.session.query(Users).filter(Users.email.like('%' + kw + '%'))` |
| in_                 | 在xxx里面      | `db.session.query(Users).filter(Users.age.in_([30, 17, 45]))` |
| between ... and ... | 在...和...之间 | `db.session.query(Users).filter(Users.age.between(30, 45))`  |

```python
@app.route('/filter')
def filter_views():
    # 6.查询email中包含an的Users的信息
    # sql:select * from users where email like '%an%'
    # 模糊查询like,需要使用实体类属性提供的like()完成查询
    kw = 'an'  # 模拟查询关键词
    users = db.session.query(Users).filter(
        # Users.email.like('%%%s%%' % kw)
        Users.email.like('%' + kw + '%')
    ).all()
    print(users)
    # 7.查询年龄是30岁,或17岁或45岁的Users的信息
    # 模糊查询in 需要使用实体类属性提供in_函数完成
    users = db.session.query(Users).filter(
        Users.age.in_([30, 17, 45])
    ).all()
    print(users)
    # 7.查询年龄在30-45之间的Users的信息
    # 模糊查询between..and..需要使用实体类属性提供的between(值1,值2)完成查询
    users = db.session.query(Users).filter(
        Users.age.between(30, 45)
    ).all()
    print(users)
    return "Query OK"
```



##### d. order_by() 排序

```python
db.session.query(xxx).order_by("排序规则")
```

```python
# select * from users order by age desc
db.session.query(User).order_by("age")
db.session.query(User).order_by(Users.age.desc())
# select * from users order by age desc,id asc
db.session.query(User).order_by(Users.age.desc(), Users.id)
db.session.query(User).filter(Users.age > 18).order_by("age desc").all()
```



##### e. 聚合查询

* 基本的聚合查询

```python
from sqlalchemy import func # func 中提供了所有的聚合函数
db.session.query(func.聚合函数(实体类.属性), func.聚合函数(实体类.属性)).all()
```

聚合函数

函数|作用
-|-
sum()|总和
count()|数量
max()|最大值
min()|最小值
avg()|平均值

```python
from sqlalchemy import func
result = db.session.query(
    func.avg(Users.age),
    func.sum(Users.age),
	func.max(Users.age),
	func.min(Users.age),
	func.count(Users.id)
).all()
```



* 分组的聚合查询，利用 `group_by()` 分组后筛选用`having()`

```python
db.session.query(查询列, 聚合列)
    .filter(条件) # 分组前数据筛选 - where
    .group_by(分组列) # 分组 - group by
    .having(条件) # 分组后筛选 - having
```

e.g.

```sql
select isActive, count(*) from users where age > 18 group by isActive having count(id) > 2;
```

```python
# 查询users表中年龄大于18岁的，按照isActive分组，组内人数大于2人的组名和人数查询出来
db.session.query(Users.isActive, func.count(Users.id))
    .filter(Users.age > 18)
    .group_by('isActive')
    .having(func.count(Users.id) >= 2 ).all()

# 查询users表中年龄大于"赵金多"的users们的信息
result = db.session.query(Users).filter(
    Users.age > db.session.query(Users.age).filter(Users.username == '赵金多')
).all()
```



##### 7.5.2.2 基于 `实体类` 进行查询

```python
实体类.query.查询过滤器函数().查询执行函数()
```

```python
Users.query.all()
Users.query.filter(Users.age>=20).all()
Users.query.filter(Users.age==20).all()
Users.query.filter_by(Users.age=20).all()
```



#### 7.5.3 修改 - U

1. 查：查询要修改的对象实体
2. 改：通过 `对象.属性 = 值` 修改数据
3. 存：`db.session.add(对象)`

```python
@app.route('/update')
def update_views():
    # 1. 查
    user = Users.query.filter_by(username='Duke').first()
    # 2. 改
    user.age = 24
    # 3. 存
    db.session.add(user)
    return "Updata OK"
```



#### 7.5.4 删除 - D

1. 查：查询出要删除的实体对象
2. 删：`db.session.delete(对象)`

```python
@app.route('/delete')
def delete_views():
    # 1. 查
    user = Users.query.filter_by(username='Eric').first()
    # 2. 删
    db.session.delete(user)
    return "Delete OK"
```



### 7.6 关系映射

#### 7.6.1 一对多

* A表中的一条数据可以管理B表中的多条数据，B表中的一条数据只能关联到A表中的一条数据
* 利用主外键的关系来实现一对多：`一`表中有主键，`多`表中增加外键，表示对`一`表的引用

在SQLAlchemy中的实现

##### 7.6.1.1 原则：

* 在 `多` 的实体类中增加对 `一` 实体类的引用
* 在 `一` 实体类中增加 `关联属性` 以及 `反向引用关系属性`

##### 7.6.1.2 实现：

1. 在 `多` 实体类中，增加一个属性/列，要引用到 `一` 表/类的主键
    `属性名/外键列名 = db.Column(db.TPYE, db.ForeignKey('主键表名.主键列'))`
2. 在 `一` 的实体类中增加 `关联属性` 和 `反向引用关系属性`。目的：为了创建类（对象）与类（对象）之间的关联关系

##### 7.6.1.3 名词解释

* `关联属性`：在`一`的实体类中，要增加`一`个 `<<属性>>` 来获得`多`的实体对象们
* `反向引用关系属性`：在`一`的实体类中的操作最终加到`多`的实体类中，即在`多`的实体类中，要增加`一`个`<<属性>>`来获取到对应的`一`的实体对象

##### 7.6.1.4 语法：

* 在一的实体类中增加: `属性名 = db.relationship('多的实体类名', backref='定义方向引用关系属性名', lazy='dynamic')`

示例：

```python
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

##### 7.6.1.5 关联数据查询

通过一的对象，找关联的多的对象们。通过关联属性来表示对应的类型数据的查询对象

示例：

```python
course = Course.query.filter_by(id=1).first()
course.teacher.all()
```

通过多的对象，找关联的一的对象。通过反向引用关系属性

```python
tea = Teacher.query.filter_by(tname = 'QTX')
cour = tea.course
```

#### 7.6.2 一对一

A表中的一条数据只能关联到B表中一条数据上，B表中的数据也只能关联到A表上的条数据上

##### 7.6.2.1 实现

在关联的两张表中的任意一张表中：

* 增加外键，并引用另一张表的主键
* 并且要增加唯一约束

##### 7.6.2.2 在ORM中实现

* 在任意一个实体类增加外键以及唯一约束

`外键列名 = db.Column(db.TYPE, db.ForeignKey('主表.主键'), unique=True)`

* 关联属性和反向引用关系属性

在关联的两个类中的另一个类中增加 `属性名 = db.relationship("关联的实体类的名称", backref="发现引用关系属性名", uselist=False)`

`uselist`: 设置为False，表示关联属性是一个标量，而并非一个列表

#### 7.6.3 多对多

A表中的一条数据只能关联到B表中多条数据上，B表中的数据也只能关联到A表上的多条数据上

##### 7.6.3.1 多对多数据库中实现

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

##### 7.6.3.2 在ORM中实现多对多

* 创建第三张表（类），并创建两个外键表示引用自两张表
* 关联属性和反向引用关系属性
  * 在关联的两个类中的任意一个类中增加：

  ```python
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

  ```python
  from  flask import make_response
  resp = make_response("Hello World")
  resp = make_response(render_template('index.html'))
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

```python
from flask import Blueprint
app_branch01 = Blueprint('蓝图别名', __name__)

@app_branch01.route('/')
def index():
    pass
```

2. 在主分支模块中

```python
from branch01 import app_branch01 as 别名
app.register_blueprint(别名) # 注册蓝图
```

## 12. Flask 项目结构

![Flask项目结构](image/flask_project_structure.png)
