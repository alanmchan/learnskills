# Django

## 1. 概述

---

由Python编写的开源的web框架

查看已安装的版本

```py
import django
print(django.__version__)
```

安装 `sudo pip3 install django[==版本]`

卸载 `pip uninstall django`

## 2. Django框架的使用

---

### 2.1 创建项目指令

`django-admin startproject 项目名称`

e.g.

```bash
django-admin startproject mywebsite1
```

运行

```bash
python3 manage.py runserver
python3 manage.py runserver 5000 # 指定网络设备入口端口
python3 manage.py runserver 192.168.1.111:5000 # 指定网络设备IP和端口
```

python3 manage.py runserver
python3 manage.py migrate
python3 manage.py createsuperuser
python3 manage.py startapp

### 2.2 Django项目结构

```bash
tree mywebsite/
mywebsite/
├── manage.py
└── mywebsite
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py

1 directory, 5 files
```

目录解析

* manager.py 项目管理的子程序,在开发阶段管理整个项目的开发运行的调试,包含如下子命令:
  * python3 manager.py runserver 启动服务
  * python3 manager.py startapp 创建应用
  * python3 manager.py migrate 数据库迁移
* mywebsite1 项目包文件夹,默认与项目名称一致
  * __init__.py 项目初始化文件,服务启动时自动运行
  * wsgi.py WEB网关接口的配置文件,仅部署项目时使用
  * urls.py 项目的基础路由配置文件,所有动态路径必须先走该文件进行匹配
  * settings.py Django配置文件,此文件中的一些配置全局变了将为Django框架的运行传递一些参数.此配置文件在启动时会自动调用.在此文件中也可以定义一些自定义的变了用于作用于全局作用域来传递数据

settings.py中的常量

常量|含义
-|-
`BASE_DIR`|用于绑定当前项目的绝对路径
`DEBUG`|用于配置Django项目的启用模式:True表示开发环境中使用,False表示当前项目运行在生产环境中
`ALLOWED_HOST`|设置允许访问到本项目的网络地址列表:如果为空列表,表示只有`localhost`和`127.0.0.1`能访问本项目;`['*']`表示任何网络地址都能够访问到本项目
`INSTALLED_APPS`|指定当前项目中安装的应用列表
`MIDDLEWARE`|用于注册中间组件
`TEMPLATES`|用于指定模板的配置信息
`DATABASES`|用于指定数据库的配置信息
`LANGUAGE_CODE`|用于指定语言配置:`'en-us'`英语;`'zh-Hans'`中文
`TIME_ZONE`|用于指定当前服务端时区,中国时区`'Asia/Shanghai'`
`ROOT_URLCONF`|用于配置根级url配置, e.g. `'mywebsite1.urls'`

### 2.3 Django的框架模式

MVC Model-View-Controller

* M 模型层, 主要用于对数据库层的封装
* V 视图层, 用于向用户展示结果
* C 控制层, 用于处理用户请求\获取数据\返回结果

MTV Model-Template-View

* M 模型层, 负责与数据库交互
* T 模板层, 负责呈现内容到浏览器
* V 视图层, 负责数据接收请求\获取数据\返回结果

## 3. 视图函数

---

## 4. Django中的应用APP

---

## 5. 模板

---

模板是指html页面,模板层提供对设计者友好的语法用于渲染向用户呈现的信息

### 5.1 模板的配置

默认模板文件夹为 `templates`

在settings.py中有一个TEMPLATES变量

* `BACKED` 指定模板的引擎
* `DIRS` 指定模板保存路径
* `APP_DIRS` 指定是否要在应用中搜索模板
* `OPTIONS` 有关模板的选项

修改settings.py文件,设置TEMPLATES的DIRS值为`'DIRS':[os.path.join(BASE_DIR, 'templates')]`

在实际项目中, 每个应用中都有自己的模板目录, 在模板目录下创建属于自己的应用名目录, 将应用的模板放入到属于自己的应用名目录

```bash
Project
├── app1
|   └── templates
|       └── app1
|           └── index.html
└── app2
    └── templates
        └── app2
            └── index.html
```

### 5.2 模板的加载方式

* 通过loader获取模板,再通过HttpResponse进行响应

```py
from django.template import loader
t = loader.get_template('模板名称') # 通过loader加载模板
html = t.render() # 将模板渲染成字符串
return HttpResponse(html) # 响应
```

* 通过render()直接加载并响应

```py
from django.shortcuts import render
return render(request,'模板名称')
```

### 5.3 Django模板语言 The Django Template Language

#### 5.3.1 变量

* 在模板中使用语法: `{{ 变量名 }}`
* 在视图函数中必须封装在字典中才允许传到模板上 `dic = {'变量1':'值1', '变量2':'值2'}`
  * `html = t.render(dic)`
  * `return render(request, 'index.html', dic)`

#### 5.3.2 标签

将服务端的功能嵌入到模板中

语法:

```html
{% 标签 %}
...
{% 结束标签 %}
```

常用标签:

1. `for` 标签

```html
{% for i in lst %}
    <p>{{ i }}</p>
{% endfor %}
```

for循环内置变量forloop

* forloop.counter 得到当前循环遍历次数,从1开始
* forloop.counter0 得到当前循环遍历下标,从0开始
* forloop.first 第一次循环
* forloop.last 最后一次循环

2. `for-empty` 标签

```html
{% for i in lst %}
    ...
{% empty %}
    ...
{% endfor %}
```

当lst为空时就执行empty子句内容

3. `if` 标签

```dj
{% if 条件 %}
    ...
{% elif 条件 %}
    ...
{% else %}
    ...
{% endif %}
```

4. 注释

`{# 注释内容 #}`

#### 5.3.3 过滤器

过滤器作用:在变量输出前对变量的值进行筛选

语法: `{{ 变量|过滤器:参数值 }}`

常用过滤器

过滤器|含义|示例
-|-|-
`add`|将变量的值加上某个指定的值|`{{ 1|add:2 }}`
`cut`|移除字符串中指定内容|`{{ 'hello world'|cut:' ' }}`
`date`|指定时间显示格式|`{{ time|date:'Y/m/d g:i:s' }} {# time 为后端传过来的日期时间 #}`
`first/last`|输出字符串/列表/元组的第一个/最后一个元素
`floatformat`|指定浮点数保留几位小数,默认 保留一位,遵循四舍五入
`length`|获取字符串/列表/元组的长度
`length_is`|判断字符串/列表/元组的长度是否为指定值,返回布尔值
`upper/lower`|将英文字母变为大/小写
`default`|设置默认值,常用于没有传变量或变量为空的情况
`default_if_none`|如果变量的值为None,就显示指定的值
`truncatechars`|如果字符串超过过滤器指定的长度,会进行切割,并且会拼接`...`作为省略号

#### 5.4 静态文件

静态文件:不能与服务器端动态交互的文件都是静态文件,如:图片,css,js,vedio,medio,html(部分)

#### 5.4.1 静态文件配置

在settings.py中进行配置

* `STATIC_URL = '/static/'` 配置静态文件访问路径,即通过哪个URL地址找到静态文件
* `STATICFILES_DIRS = (os.path.join(BASE_DIR, 'static'),)` 配置静态文件的存储路径,静态文件在服务器端的保存位置

#### 5.4.2 静态文件的访问

1. 使用静态文件的访问路径进行访问 e.g. `<img src="/static/images/pic.jpg">`
2. 使用 `{% static %}` 标签访问静态文件, 其中 `{% static %}` 就是静态文件访问路径
    * 在所有静态资源文件上方加载static, 即 `{% static %}`
    * 使用静态资源, 语法 `<img src="{% static 'images/pic.jpg' %}">`

### 5.5 模板的继承

模板的继承可以使父模板的内容重用,子模板直接继承父模板的全部内容,并可以覆盖父模板中相应的块

语法:

1. 在父模板中利用 `{% block %}` 标签标识出哪些是在子模板中可以被修改

```dj
{% block block_name %}
...
{% endblock block_name %}
```

2. 子模板中利用 `{% extends %}` 标签继承父模板, 利用 `{% block %}` 标签进行重写

```dj
{% extends '父模板名' %} {# 一般卸载模板文件第一行 #}
...
{% block '与父模板同名的要重写的块名' %}
子模板中重写覆盖父模板中的内容
{% endblock block_name %}
python3 manage.py runserver
```

重写覆盖规则:

* 不重写,将按照父模板的效果显示
* 重写,则按照重写内容显示
* 模板继承时,服务器端的动态内容无法继承

### 5.6 url反向解析

在模板文件中,在url标签中,可以利用路由的别名,反向寻找路由和视图函数

语法:

1. 在url()中利用name关键字为路由设置别名, `url(regex, views, name='别名')`
2. 在模板中利用 `{% url %}` 标签和别名实现url反向解析, `{% url '别名' %}` 或 `{% url '别名' '参数1' '参数2' %}`

e.g.

`url(r'^child/$', views.child_views, name='child')`

```dj
{# URL正常跳转 #}
<a href="/cart/child/">跳到child.html</a>
{# URL反向解析 #}
<a href="{% url 'child' %}">跳到child.html</a>
```

## 6. 数据库模型 ORM框架

---

ORM: Object Relationship Mapping 对象关系映射

三大特征:

1. 类到表的映射
2. 数据类型的映射
3. 关系映射

### 6.1 创建和配置数据库

#### 6.1.1 创建数据库

`create database webDB default charset=utf8 collate utf8_general_ci`

#### 6.1.2 在 `settings.py` 中修改 `DATABASES` 参数值

```py
DATABASES = {
    'default':{
        'ENGINE':'django.db.backends.sqlite3',
        'NAME':os.path.join(BASE_DIR, 'db.sqlite3')
    }
}
```

```py
DATABASES = {
    'default':{
        'ENGINE':'django.db.backends.mysql',  # 指定数据库引擎
        'NAME':'webDB',  # 指定要连接的数据库
        'USER':'root',  # 指定要登录到数据库的用户名
        'PASSWORD':'123456',  # 指定登录数据库的密码
        'HOST':'localhost',  # 指定要连接的主机
        'PORT':'3306',  # 指定要连接的主机上的端口号
    }
}
```

#### 6.1.3 添加mysql支持

* 安装pymsql `pip3 install pymysql`
* 修改项目中`__init__.py`, 加入如下内容来提供pymysql引擎支持

```py
import pymysql
pymysql.install_as_MySQLdb()
```

### 6.2 数据库的迁移

1. `python3 manage.py makemigrations` 将每个应用下的 `models.py` 生成中间文件, 并保存到 migrations 文件夹中
2. `python3 manage.py migrate` 目录中的中间文件同步回数据库

### 6.3 模型类的编写

#### 6.3.1 语法

```py
class CLASSNAME(models.Model):
    NAME = models.FIELD_TYPE(FIELD_OPTIONS)
```

参数|类中含义|数据库表中含义
-|-|-
`CLASSNAME`|实体类名|默认表名: `应用名_类名`
`name`|属性名|映射到数据库中即字段名
`FIELD_TYPE`|属性类型|字段类型
`FIELD_OPTION`|属性选项|字段约束

注意： 没有给表设置主键 django会自动添加一个id作为主键

#### 6.3.2 `FIELD_TYPE`

字段类型|编程语言中|数据库中|数据库类型|注意
-|-|-|-|-
`CharField()`|-|-|`varchar`|必须要指定`max_length`参数值
`IntegerField()`|整数|整数|`int`
`BooleanField()`|用True或False表示值|使用1或0来表示值|`tinyint`
`DateField()`|-|-|`date`|表示日期
`DateTimeField()`|-|-|`datetime`|表示日期时间
`FloatField()`|-|-|`float`
`ImageField()`|编程语言使用字符串|数据库中使用字符串|`varchar`|在数据库中保存图片的路径
`EmailField()`|编程语言使用字符串|数据库中使用字符串|`varchar`
`TextField()`|编程语言使用字符串|数据库中使用字符串|`text`|表示大量数据
`DecimalField()`|-|-|`decimal`|表示小数
`URLField()`|编程语言使用字符串|数据库中使用字符串|`varchar`

`stu_img = models.ImageField(upload_to='static/images')`

`upload_to` 指定图片上传的路径，在后台上传时会自动保存到指定路径下使用static注意要先配置`settings.py`

#### 6.3.3 `FIELD_OPTIONS`

选项/约束|说明
-|-
`primary_key`|设置为True,表示该列为主键,如果没有指定主键,会自动创建主键
`null`|设置为True,表示该列允许值为空, 默认为False
`default`|设置所在列的默认值
`db_index`|设置为True,表示为该列增加索引
`unique`|设置为True,表示该列值唯一
`db_column`|指定列的名称,如果不指定,则采用属性名作为列名

e.g. `name = models.CharField(max_lenght=30, unique=True, null=False, db_index=True)`

#### 6.3.4 将数据库导出成models

通过指令将数据库导出: `python3 manage.py inspectdb > xxx.py` 自己指定名称

在每个类中都有一个类 class Meta,如下:

```py
class Meta:
    managed = False # 是否修改过
    db_table = '表名' # 可以自己创建实例类的时候在类中创建该类,并指定表名称
```

### 6.4 数据的CRUD

Create - 增加, Read - 读取, Update - 更新, Delete - 删除

#### 6.4.1 增加数据

1. 用类创建对象,再用save()保存

```py
obj = Author(name='alan', age=20)
obj.save()
```

2. 用字典创建对象,再用save()保存

```py
dic = {'name':'bran', age=19}
obj = Author(**dic)
obj.save()
```

3. 利用 `类.objects.create(属性=值)` 创建对象, 返回一个创建好的对象, 无需调用save()保存

`obj = Author.objects.create(name='carl', age=21)`

#### 6.4.2 查询数据

通过 `类.objects.方法` 调用接口进行查询

查询函数|示例|作用|返回结果
-|-|-|-
`all()`| `Author.objects.all()`|查询所有数据|返回QuerySet对象 `<QuerySet [<Author: 1.cm>, <Author: 2.alan>, <Author: 3.carl>]>`
`get(条件)`|`Author.objects.get(id=1)`|查询符合条件的对像,只能返回一个,多个报错,没有数据也报错|返回查询到的对象 `<Author: 1.cm>`
`filter(条件1,条件2)`|`Author.objects.filter(name='carl')``Author.objects.filter(id=1, name='alan')`|查询符合条件的对象,可以返回多个.多个条件用逗号分开,相当于数据库中的`and`|返回结果为QuerySet对象
`order_by()`|`Author.objects.order_by('-age')`|按照指定条件对结果进行排序.默认为升序, 字段前面加`-`则为降序|返回结果为QuerySet对象 `<QuerySet [<Author: 2.alan>, <Author: 3.carl>, <Author: 1.cm>]>`
`values()`| `Author.objects.values('id','name')`|查询部分列的数据,会将查询到的数据封装到字典中,再封装到列表中返回|返回QuerySet对象, `<QuerySet [{'id': 2, 'name': 'alan'}, {'id': 3, 'name': 'carl'}, {'id': 1, 'name': 'cm'}]>`
`values_list()`|`Author.objects.values_list('id','name')`|查询指定返回列,会将查询的数据封装到元组中,再封装到列表中返回|返回QuerySet对象, `<QuerySet [(2, 'alan'), (3, 'carl'), (1, 'cm')]>`
`exclude(条件)`|`Author.objects.exclude(id=2)`|不等条件筛选,查找不满足条件的数据|返回查询结果集
`aggregate()`|`Author.objects.aggregate(Avg('age'), Count('name'), age__Sum=Sum('age'))`|不分组聚合查询|返回字典
`annotate()`|`Author.objects.values('isActive').annotate(Count('name'))`|分组聚合查询|返回查询结果集对象QuerySet

##### 6.4.2.1 查询谓词

在非等值查询中需要借助查询谓词(Field Lookup), 所谓查询谓词是指每一个查询谓词是一个独立的查询功能.通常与filter()函数结合使用,即`XXX.objects.filter(属性__查询谓词=值)`

查询谓词|作用|示例|SQL
-|-|-|-
`__exact`|等值匹配|`Author.objects.filter(id__exact=1)`|`select * from author where id = 1`
`__gt`|大于指定值|`Author.objects.filter(age__gt=20)`|`select * from author where age > 50`
`__gte`|大于等于
`__lt`|小于
`__lte`|小于等于
`__contains`|包含指定值|`Author.objects.filter(name__contains='a')`|`select * from author where name like '%a%'`
`__in`|查找数据是否在指定范围内|`Author.objects.filter(name__in=['alan','carl'])`|`select * from author where name in ('alan', 'carl')`
`__range`|查找数据是否在指定的区间范围内|`Author.objects.filter(age__range=(20,25))`

##### 6.4.2.2 聚合查询

需要导入聚合函数 `from django.db.from django.db.models import Sum,Avg,Count,Max,Min`

* 不分组聚合查询

`aggregate()`

* 语法: `类名.objects.aggregate(名=聚合函数('列'))`
* 返回: 字典
* 说明: 名对应字典的键,聚合函数的结果对应字典的值.名可以不写,默认为`字段__聚合函数名`,e.g. `age__avg`

e.g.

```py
re = Author.objects.aggregate(Avg('age'), Count('name'), age__Sum=Sum('age'))
print(re) # {'name__count': 4, 'age__avg': 20.75, 'age__Sum': 83}
```

* 分组聚合查询

`annotate()`

* 语法: `类名.objects.values('分组列名1','分组列名2').annotate(名=聚合函数('列')).filter(条件)`
* 返回: 查询结果集对象QuerySet

e.g.

```py
re = Author.objects.values('isActive').annotate(Count('name'))
print(re) # <QuerySet [{'name__count': 3, 'isActive': True}, {'name__count': 1, 'isActive': False}]>
re = Author.objects.values('isActive').annotate(Count('name')).filter(age__gt=20)
print(re) # <QuerySet [{'isActive': True, 'name__count': 1}, {'isActive': False, 'name__count': 1}]>
```

#### 6.4.3 修改

##### 6.4.3.1 修改单个实体

1. 利用get()得到要修改的单个实体对象
2. 通过 `对象.属性 = 值` 的方式修改数据
3. 通过 `对象.save()` 保存数据

e.g.

```py
# 1. 查 通过get()得到要修改的单个实体对象
author = Author.objects.get(id=4)
# 2. 改 通过 对象.属性 的方式修改
author.name = 'Eric'
# 3. 存 通过 对象.save() 保存数据
author.save()
```

##### 6.4.3.2 利用QuerySet的update()实现批量修改

`类.objects.all().update(属性=值)`

e.g. `Author.objects.all().update(isActive=True)`

#### 6.4.4 删除

利用 delete() 方法删除单个对象或批量删除

```py
# 删除单个对象
Author.objects.get(id=4).delete()

# 删除查询结果集
Author.objects.filter(isActive=False).delete()
```

### 6.5 F查询 和 Q查询

Django提供的两种特殊的查询方法,使用之前要先导入 `from django.db.models import F, Q`

#### 6.5.1 F-查询

* 作用: 在执行的过程中获取指定列的值
* 语法: `F('列名')`
* 示例: 更新Author中所有作者的年龄age+10 `Author.objects.all().update(age=F('age')+10)`

不使用`F查询`修改数据 VS 使用`F查询`修改数据

```py
def query_f(request):
    # 不使用 F查询 修改数据
    author = Author.objects.get(id=7)
    print(author.age) # 35
    author.age += 2 # 利用Python计算保存到内存中
    author.save() # 将修改保存到数据库中
    print(author.age) # 37

    # 使用 F查询 修改数据
    author.age = F('age') + 2 # 当django遇到F()实例，覆盖原本的Python运算符 创建一个SQL表达式
    print(author.age) # <CombinedExpression: F(age) + Value(2)> 打印结果为 F(age) + Value(2)
    author.save() # 保存数据 在使用F()保存值后 再次使用实例不会拿到新的值需要重新载入实例
    print(author.age) # F(age) + Value(2)
    author = Author.objects.get(id=7)
    print(author.age) # 39
    return HttpResponse("F-Query OK")
```

总结:

* 如果不使用 F查询, 则是在内存中修改, 然后再保存到数据库中
* 如果使用 F查询, 则是直接到数据库中修改,修改后要重新载入数据
* 在多并发编程中建议使用 F查询

#### 6.5.2 Q 查询

Q-查询能实现数据库中的 or 操作

* 语法: `Q(条件1)|Q(条件2)`
* 示例: `Author.objects.filter(Q(id=1)|Q(age__lt=20))`

### 6.6 原生数据库方法

#### 6.6.1 查询

利用raw(sql语句)可以实现原生数据库查询

* 语法: `raw(sql语句)`
* 示例: `Author.objects.raw('select * from index_author')`
* 返回: 原生查询结果集,`<RawQuerySet: select * from index_author>`,也可以遍历

#### 6.6.2 增删改

1. 导入数据库连接对象 `from django.db import connection`
2. 创建游标
3. 利用游标执行sql语句

```py
from django.db imort connection
with connection.cursor() as cursor:
    cursor.execute('update index_author set isActive=0 where id=7')
```

## 7. 关系映射

---

### 7.1 一对多

#### 7.1.1 语法

在多的实体类中增加一个属性: `属性 = models.ForeignKey(需要关联的类, null=True, default='', on_delete=models.CASCADE)`

* 对数据库表的影响:
  * 多的表中添加一个外键, 外键名为 `属性_id`
* 对实体类的影响:
  * 一隐式增加一个属性, `属性_set` 关联多个实例对象的关联管理器RelatedManager, 可以在调用`all()`等查询函数之后遍历
  * 多增加一个属性, `属性` 关联一个实例对象
  * 多隐式增加一个属性, `属性_id` 关联一个实例对象的id

说明:

* 如果表中已有数据, 再增加外键, 则会要求设置默认值, 即 `default=默认值`, 如果不想设置默认值, 可以指定允许为空, 即 `null=True`
* `on_delete=models.CASCADE` 设置当一删除时, 多也跟着删除

#### 7.1.2 利用循环访问 `一.多` 遍历出所有多的实例 

```py
def otm_views(request):
    books = Book.objects.all()
    publishers = Publisher.objects.all()
    for book in books:
        print(book.publisher_id, book.publisher.name)
    for publisher in publishers:
        for book in publisher.book_set.all():  # book_set, 关联管理器对象
            print(book.title)
    return render(request, 'otm.html', locals())
```

```html
<h1> book --> publisher </h1>
{% for book in books %}
    <h3>{{ book.title }}:{{ book.publisher.name }}-{{ book.publisher_id }}</h3>
{% endfor %}

<h1> publisher --> books </h1>
{% for publisher in publishers %}
    <h3>{{ publisher.name }}:
        {% for book in publisher.book_set.all %}
            << {{ book.title }} >>
        {% endfor %}
    </h3>
{% endfor %}
```

### 7.2 一对一

#### 7.2.1 语法

在需要关联的两个类中的任意一个类中添加: `属性 = models.OneToOneField(需要关联的类)`

* 对数据库表的影响:
  * 该表中添加一个外键并设置唯一约束, 外键名为 `属性_id`
* 对实体类的影响:
  * 该实体类增加一个属性,`属性`, 关联另一个类的对象
  * 该实体类隐式增加一个属性,`属性_id`, 关联另一个类的对象的id
  * 关联的实体类隐式增加一个该类名的小写的属性,`类名小写`, 关联这个类的对象

#### 7.2.2 添加数据并建立关联

1. 表中的外键关联对应的对象id: `对象.属性_id = 另一个类的对象.id`
2. 属性关联对象: `对象.属性 = 另一个类的对象` 

```py
# 添加数据并关联另一个表
wife1 = Wife()
wife1.name = 'Daisy'
wife1.age = 28
# 通过wife表中的author_id属性找到对应的Author对象
wife1.author_id = 5
wife1.save()

wife2 = Wife()
wife2.name = 'Kate'
wife2.age = 30
# 通过属相关联对象
author2 = Author.objects.get(name='carl')
wife2.author = author2
wife2.save()

Wife.objects.create(name='Jennifer', age=32, author_id=8)
```

#### 7.2.3 一对一查询

```py
wifes = Wife.objects.all()
authors = Author.objects.all()
# for author in authors:
#     print(author.wife_id) # 无此属性 报错
for wife in wifes:
    print(wife.author_id, wife.author.id)
return render(request, 'oto.html', locals())
```

```html
<h1>反向查询</h1>
{% for author in authors %}
    <h3>{{ author.name }}:{{ author.wife.name }}</h3>
{% endfor %}
<h1>正向查询</h1>
{% for wife in wifes %}
    <h3>{{ wife.name }}:{{ wife.author.name }}-{{ wife.author_id }}</h3>
{% endfor %}
```

### 7.3 多对多

#### 7.3.1 语法

在关联的两个类中的任意一个类中，增加 `属性=models.ManyToManyField(另一个类)`

* 对数据库的影响
  * 创建第三张表（类）, 并创建两个外键表示引用自两张表, 第三张表的名称 `应用_该类名_另一个类名`
* 对实体类的影响
  * 该实体类增加一个属性, `属性`, 关联另一个类的对象们, 可以调用查询函数后再遍历
  * 另一个类中隐式增加一个该类名的小写的属性加set, `类名小写_set`, 关联另一个类的对象们, 可以调用查询函数后再遍历

```py
class Book(models.Model):
    # 多对多关联
    author = models.ManyToManyField(Author)
```

#### 7.3.2 查询

```py
def mtm_views(request):
    # 正向查询
    books = Book.objects.get(id=1)
    print(books.author.all())
    print(books.author.filter(age__gt=30))
    # 反向查询
    author = Author.objects.get(id=5)
    print(author.book_set.all())
    return HttpResponse("多对多查询成功")
```

## 8. request 请求对象

---

### 8.1 request中的成员

成员|说明
-|-
`request.scheme`|请求协议
`request.body`|请求主体(POST,PUT)
`request.path`|请求的具体资源路径
`request.get_full_path`|请求的完整路径
`request.get_host()`|请求的主机
`request.method`|请求方式
`request.GET`|get请求方式中封装的数据
`request.POST`|post请求方式中封装的数据
`request.COOKIES`|请求中的cookies的相关数据
`request.META`|请求中的元数据(消息头)
`request.META['HTTP_REFERER']`|请求源地址

### 8.2 获取请求提交的数据

#### 8.2.1 GET

##### 8.2.1.1 语法

* request.GET['参数名']
* request.GET.get('参数名','默认值')
* request.GET.getlist('参数名')

##### 8.2.1.2 能够产生get请求方式的场合

1. 地址栏的请求

    * `<a href="地址?参数=值&参数=值">`
    * `location.href='地址?参数=值&参数=值'`

2. 表单中的method为get

```html
<form method='get'>
    姓名:<input type="text" name="uname">
</form>
```

注意: 带参数的路由

```py
url(r'^01-xxx/(\d{4})/$',request_views),

def request_views(request):
    request.GET['']
```

#### 8.2.2 POST

##### 8.2.2.1 语法

* request.POST['参数名']
* request.POST.get('参数名','')
* request.POST.getlist('参数名')

##### 8.2.2.2 CSRF验证

Cross-Site Request Forgey
跨    站点 请求    伪装

解决方案:

1. 取消 csrf 验证(不推荐)
    删除 `settings.py` 中 `MIDDLEWARE` 中的 `CsrfViewsMiddleWare` 的中间件
2. 开放验证
    在视图处理函数增加 : `@csrf_protect`

    ```py
    @csrf_protect
    def post_views(request):
        pass
    ```

3. 通过验证
    需要在表单中增加一个标签 `{% csrf_token %}`

## 9. forms 模块

---

### 9.1 作用

Django中的forms模块能将模板中的表单与class结合,利用class生成表单.一个类对应一个表单,一个属性对应一个控件

### 9.2 使用forms模块的步骤

1. 在应用中创建 `forms.py`
2. 导入Django提供的forms模块 `from django import forms`
3. 创建一个类, 一个类生成一个表单
4. 在类中创建属性, 一个属性对应表单中一个控件

```py
from django import forms

class ClassName(forms.Form):
    属性 = forms.类型(参数)
```

### 9.3 forms中的类型与对应的表单控件

forms中的类型|表单控件
-|-
`forms.BooleanField()`|`type='checkbox'`
`forms.CharField()`|`type='text'`
`forms.ChoiceField()`|`<select></select>`
`forms.DateField()`|`type='date'`
`forms.DecimalField()`|`type='number'`
`forms.EmailField()`|`type='email'`

### 9.4 参数选项

#### 9.4.1 label

指定控件前的文本

#### 9.4.2 widget

指定小部件

##### 9.4.2.1 小部件的使用

1. 继承自`forms.Form`

* 基本版

语法:

```py
属性 = forms.CharField() #无预选值使用text,password,email,url,textarea,checkbox
属性 = forms.ChoiceField() #有预选值使用checkbox,radio,select
属性 = forms.CharField(label='xxx', widget=forms.小部件类型)
```

示例:

```py
upwd = forms.CharField(label='用户密码', widget=forms.PasswordInput)
message = forms.CharField(label='评论内容', widget=forms.Textarea)
```

* 高级版

特征: 在指定控件类型的基础之上还能指定控件的一些html属性值

语法:

```py
属性 = forms.CharField(
    label='xxx',
    widget=forms.小部件类型(
        attrs={
            'html属性名':'值',
            'html属性名':'值',
        }
    )
)
```

2. 继承自`forms.ModelForm`

```py
class ClassName(forms.ModelForm):
    class Meta:
        model = xxxx
        fields = "__all__" / ["",""]
        labels = {
            "xxx":"xxx"
        }
        #指定小部件
        widgets = {
            '属性名1':forms.小部件类型,
            '属性名2':forms.小部件类型(
                attrs={
                    "html属性名":"值"
                }
            )
        }
```

示例:

```py
widgets = {
    'upwd':forms.PasswordInput(
        attrs={
            'placeholder': "请输入密码",
            'class':'form-control'  # 添加CSS样式
        }
    ),
    'uphone':forms.TextInput(
        attrs={
            'placeholder': "请输入账号/邮箱/手机号"
        }
    )
}
```

##### 9.4.2.2 常用小部件

小部件类型|说明
-|-
`TextInput`|`type='text'`
`PasswordInput`|`type='password'`
`NumberInput`|`type='number'`
`EmailInput`|`type='email'`
`URLInput`|`type='url'`
`HiddenInput`|`type='hidden'`
`Textarea`|`<textarea></textarea>`

小部件选择框|说明
-|-
`CheckboxInput`|`type='checkbox'`
`CheckboxSelectMultiple`|`type='checkbox'`
`Radioselect`|`type='radio'`
`Select`|`<select></select>`

#### 9.4.3 initial

控件初始值,主要针对文本框类型, `value=""`

### 9.5 在模板中解析form对象

#### 9.5.1 解析步骤

1. 导入forms模块中的类后, 在视图函数中创建form对象, `form = 类名()`
2. 将form对象发送到模板进行解析, `return render(request, 'form.html', locals())`

```py
from .forms import RegiserFrom

def register(request):
    if request.method == 'GET':
        form = RegisterForm()
        return render(request, 'register.html', locals())
```

#### 9.5.2 模板中form对象的解析

##### 9.5.2.1 手动解析

```html
<form action="">
    {% for field in form %}
        <p>
            <span>{{ field.label }}</span>{# 表示的是label参数值 #}
            {{ field }}{# field:表示的是form对象中的每个属性控件 #}
        </p>
    {% endfor %}
    <button>提交</button>
</form>
```

##### 9.5.2.2 自动解析

1. `{{ form.as_p }}` 将form中每个属性/控件都使用`<p>`标签包裹起来显示
2. `{{ form.as_ul }}` 将form中每个属性/控件都使用`<li>`标签包裹起来显示, 但是必须手动提供`<ol>`或`<li>`标签
3. `{{ form.as_table }}` 将form中每个属性/控件都使用`<tr>`标签包裹起来再显示, 但是必须手动提供`<table>`标签

```html
{{ form.as_p }}

<ul>
    {{ form.as_ul }}
</ul>

<table>
    {{ form.as_table }}
</table>
```

### 9.6 通过forms模块获取表单数据

1. 通过`forms.Form`接收数据, 即在视图函数中 `form = RegisterForm(request.POST)`
2. 必须通过验证才能取值, `form.is_vaild()` 返回True则通过验证可以取值, 返回False则没通过验证暂时不能取值
3. 通过 `form.cleaned_data` 接收数据, 接收到的数据是字典形式的

### 9.7 forms模块高级设置

允许将Models实体类和Forms模块结合到一起使用

1. 在`forms.py`中创建类继承自`forms.ModelForm`
2. 创建内部类Meta, 关联Model类
    * model: 指定要关联的实体类
    * fields: 指定要从Model中取哪些字段生成控件
        * `__all__` 取全部的属性生成空间
        * `['uname', 'upwd']` 将允许生成空间的属性名放到列表中
    * labels: 指定每个属性对应的label值, 取值为字典 `labels={'属性名':'labels值'}`
    * widgets: 设置小部件属性, 见小部件部分

## 10 Cookies & Session

---

### 10.1 cookies

cookies 保存在客户端的存储空间, cookies 在浏览器上是以键值对的形式进行存储的, 键和值都是以ASCII字符串的形存储(不能是中文字符串)

#### 10.1.1 响应对象

在Django中要借助响应对象操作cookies, 响应对象有：

1. HttpResponse() `resp = HttpResponse()`
2. render() `resp = render(request,'xxx.html',locals())`
3. redirect() `resp = redirect('/')`

#### 10.1.2 添加和修改cookies

`响应对象.set_cookie(key,value,expires)`

* key:cookie的名字
* value:cookie的值
* expires:保存时长,以s为单位的数字(s不写)

#### 10.1.3 获取cookies

`request.COOKIES`封装了当前站点下所有的cookie - 字典

#### 10.1.4 删除cookies

`响应对象.delete_cookie(key)`

## 10.2 session

## 11. admin 后台数据库关联

---

Django提供了比较完善的后台数据库关联系统,便于在开发和测试中使用

后台数据库登录地址 `127.0.0.1:8000/admin`

### 11.1 创建后台管理账号并进行设置

`python3 manage.py createsuperuser`

* username: cm
* password: `1234.com`
* email: `chmingx@foxmail.com`

### 11.2 后台关联数据库表的配置

#### 11.2.1 在app的admin.py中注册要关联的模型类

```py
from django.contrib import admin
from .models import Author, Book, Publisher

# 注册模型
admin.site.register(Author)
admin.site.register(Book)
admin.site.register(Publisher)
```

#### 11.2.2 在modles模型类中重写`__str__(self)`修改显示内容

```py
class Author(models.Model):
    ...
    def __str__(self):
        return "%d, %s" % (self.id, self.name)
```

#### 11.2.3 添加模型管理器列表所有选定项

```py
# 在app的admin.py文件中声明管理器类
class AuthorManager(admin.ModelAdmin):
    list_display = ['id','name','age','email','isActive']

# 注册模型类并与管理器类进行关联
admin.site.register(Author,AuthorManager)
```

#### 11.2.4 模型类中的内嵌类 Meta

通过内嵌类Meta设定模型类的属性和展示形式

```py
class Author(models.Model):
    ...
    class Meta:
        db_table = 'author' # 指定映射到数据库中的表名
        verbose_name = 'author' # 定义实体类在后台界面中的名称(单数)
        verbose_name_plural = 'author' # 定义实体类在后台界面中的名称(复数)
```

除此之外,还可以通过字段的`verbose_name`选项修改后台界面的名称

```py
title = models.CharField(max_length=32, null=False, unique=True, db_index=True, verbose_name='标题')
# title = models.CharField('标题', max_length=32, null=False, unique=True, db_index=True) # 字段中第一个参数字符串默认为verbose_name
```

#### 11.2.5 高级管理

```py
class AuthorManager(admin.ModelAdmin):
    # 1. 定义在列表页展示的字段
    list_display = ['id','name','age','email','isActive']
    # 2. 定义在列表页上能够链接到详情页的字段
    # 注:该列表中的值必须出现在list_display中
    list_display_links = ['id','name']
    # 3. 定义在列表页可编辑的字段
    # 注: 因为在超链接不能编辑,所以字段不能同时出现在list_display_link和list_editable中
    list_editable = ['age']
    # 4. 在列表页右侧天骄过滤器
    list_filter = ['isActive']
    # 5. 在列表页添加搜索字段
    search_fields = ['name', 'email']
    # 6. 定义在详情页中显示的字段及顺序
    # fields = ['name','email','age']
    # 7. 对详情页进行分组
    # fields 和 fieldsets 有冲突,只能使用一个
    fieldsets = [
        ('基本选项',{ # 分组名
            'fields':('name','age'), # 分组中显示的字段
            'classes':('collapes') # 分组样式:可折叠
        }),
        ('可选选项',{
            'fields':('email','isActive'),
            'classes':('collapes')
        })
    ]
    # 8. 日期分层器
    date_hierarchy = 'pub_date'
```
