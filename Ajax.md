# Ajax

## 1. 概述

Asynchronous Javascript And Xml: 通过JS异步向服务器发送请求并接收响应,响应的数据格式是XML或JSON格式

* 同步访问:当客户端浏览器向服务端发送请求时,服务器在处理的过程中,浏览器只能等待
* 异步访问:当客户端浏览器向服务端发送请求时,服务器在处理的过程中,浏览器可以做其他操作,无需一直等待

Ajax的优点:

* 异步访问
* 局部刷新

使用场合:

1. 搜索建议
2. 表单验证
3. 前后端完全分离(SPA: single page application)

## 2. Ajax核心对象

异步对象-XMLHttpRequest:由JS提供,简称xhr,代替浏览器向服务器端发送异步请求并接收响应

* 主流的异步对象是XMLHttpRequest类型的
* IE7以下不支持XMLHttpRequest类型,需要用ActiveXObject()来创建

判断浏览器是否支持XMLHttpRequest

```js
if (window.XMLHttpRequest) {
    alert("支持XMLHttpRequest");
}else{
    alert("支持ActiveXObject");
}
```


### 2.1 创建XMLHttpRequest

`var xhr = new XMLHttpRequest();`

### 2.2 创建ActiveXObject

`var xhr = new ActiveXObject("Microsoft.XMLHTTP");`

## 3. xhr的成员

### 3.1 方法 open()

* 作用:创建请求
* 语法:`xhr.open(method,url,async)`
  * method:请求方式, 取值`get`或`post`
  * url:请求地址,字符串
  * async:是否采用异步的方式提交请求
    * true:异步
    * false:同步
* e.g. 以异步的提交方式使用get请求方法向`/01-server`的地址发送请求 `xhr.open('get','/01-server',true);`

### 3.2 属性 readyState

* 作用:表示xhr的请求状态.通过不同的状态值表示xhr与服务器的交互情况
  * 0:请求尚未初始化
  * 1:已经与服务器建立链接
  * 2:服务器端已经接收请求
  * 3:请求正在处理中
  * 4:响应已完成

### 3.3 属性 status

* 作用:服务器端的响应状态码
* 常用取值:
  * 200:服务器端正确处理请求并给出响应
  * 404:请求资源不存在
  * 500:服务器端内部错误

### 3.4 事件 onreadystatechange

* 作用:每当xhr的readyState的值发生改变的时候都要自动触发的一个事件--回调函数
* 在回调函数中执行:只有当readyState的值为4并且status的值为200的时候才可以正常的接收响应数据
* e.g. 

```js
xhr.onreadystatechange = functuon () {
    if (xhr.readyState == 4 && xhr.status == 200) {
        // 正常接收响应的内容
        var resTxt = xhr.responseText;
        ...
    }
}
```

### 3.5 属性 responseText

* 作用:响应数据

### 3.6 方法 send()

* 作用:通知xhr向服务器端发生请求
* 语法:`xhr.send(body)`
  * body:请求体
    * get请求时,body的值为null,即 `xhr.send(null)`
    * post请求时,body的值为要提交的数据,即 `xhr.send("请求数据")`

## 4 Ajax发送Post请求

### 4.1 请求数据要放在send()

```py
xhr.open('post',url,asynac)`
xhr.send("参数1=值1&参数2=值2")
```

### 4.2 修改Content-Type的值application/x-www-form-urlencoded

在创建请求之后,发送请求之前
`xhr.setRequestHeader('Content-Type','application/x-www-form-urlencoded');`

在请求头中有一项 Content-Type,对应HTML中的enctype, 可取值:

* application/x-www-form-urlencoded:
* multipart/form-data:
* text/plain:

## 5. JSON

### 5.1 JSO

JSO: JavaScript Object--JS对象

JS对象的表示方式:

1. 使用`{}`表示一个对象
2. 在`{}`中,使用 `key:value` 的格式来表示数据
3. 如果value是字符串,则需要用引号`' "`引起来

示例:表示一个用户信息 (name, age)

```js
var user = {
    name:'Alan',
    age:20
};
```

JSO的值可以使用点语法和键值方式访问,即:

```js
name = user.name;
age = user['age'];
```

### 5.2 JSON

JavaScript Object Notation: 采用JS对象的格式所描述出来的字符串

#### 5.2.1 JSON表现

JSON表示单个对象

1. 使用 `{}` 表示单个对象
2. 在 `{}` 中使用 `key:value` 的格式来表示数据
3. 多对 key:value 使用 `,` 来分割
4. key 必须使用 `""` 引起来
5. value如果是字符串的话,也必须使用 `""` 引起来
6. 最外面用 `''` 引起来表示字符串

e.g. `var str = '{"name":"bran", "age":20}';`

JSON表示一个数组

1. 使用 `[]` 表示一个数组
2. 数组中允许包含若干对象或字符串

e.g.

```js
var arr = '["Alan","Bran","Carl"]';
var ary = '[{"name":"Alan","age":20},{"name":"Bran","age":19}]';
```

#### 5.2.2 JSON转JS对象

在前端利用 `JSON.parse()`, 语法: `jsObj = JSON.parse(jsonStr)`

#### 5.2.3 Python中JSON处理

##### 5.2.3.1 将对象转换成JSON串

* 元组,列表,字典是可以转换成JSON串的
* 元组,列表,字典中必须是:
  1. 字符串,数字
  2. 元组,列表,字典

语法:

```python
import json
jsonStr = json.dumps(元组|列表|字典)
```

## 6. jQuery对Ajax的支持

### 6.1 $obj.load(url, data, callback);

* 作用:加载网页内容到指定对象中
* 参数:
  * url: 请求地址
  * data: 发送到服务器端的参数数据
  * callback: 加载成功时的回调函数
* 说明 $obj: 通过JQ选择器获取到的页面元素

### 6.2 $.get(url, data, callback, type)

* 作用: 通过远程HTTP GET请求载入信息
* 参数:
  * URL: 待载入页面的URL地址
  * data: 待发送 key/value 参数
  * callback: 载入成功时的回调函数
  * type: 返回内容格式, __default, json
* 返回值:

### 6.3 $.post(url, data, callback, type)

* 作用: 通过远程HTTP POST请求载入信息
* 参数:
  * url: 发送请求地址
  * data: 待发送 key/value 参数
  * callback: 发送成功时的回调函数
  * type: 返回内容格式

### 6.4 $.ajax({请求设置对象})

* 作用: 通过HTTP请求加载远程数据
* 请求设置对象中的属性:
  * url: 字符串,表示异步请求的地址
  * type: 字符串,表示请求方式, 'get'或'post'
  * data: 传递到服务器端的参数
    * 字符串: "key=value&key=value"
    * JS对象: {key:value,key:value} 
  * async: 是否使用异步的方式发送请求
    * true: 使用异步
    * false: 使用同步
  * dataType: 字符串,表示响应回来的数据的格式
    * 'html': 返回数据按照HTML解析
    * 'text': 返回的数据不会经过处理,直接传递给回调函数
    * 'xml': 返回的数据不会经过处理,直接传递给回调函数
    * 'json': 将获取到的数据作为JavaScript对象来解析
    * 'script': 返回的数据是js脚本时,按照js脚本运行
    * 'jsonp':跨域时使用
  * success: 请求成功时的回调函数

    ```js
    success:function (data) {
        data表示的是请求成功后的响应数据
    }
    ```

  * error: 请求失败时的回调函数
  * beforeSend: 发送请求之前的回调函数
    * 返回true: 正常发送ajax请求
    * 返回false: 终止本次请求的发送 

## 7. 跨域

同源:多个地址,相同协议,相同域名,相同端口时被视为是同源

HTTP 协议中有一个同源策略,即在HTTP中只有同源地址才能发送ajax的请求,非同源时称为跨域,跨域会被拒绝访问,`<scripte>`和`<img>`除外

同源地址示例:`http://www.tedu.cn/a.html`和`http://www.tedu.cn/server`

非同源地址示例:`http://www.tedu.cn/a.html`和`https://www.tedu.cn/server`,`http://localhost:5000/a.html`和`http://127.0.0.1:5000/server`

### 7.2 jQuery对跨域的支持

jsonp: json with padding

```js
$.ajax({
    url:"请求地址",
    type:"get",
    dataType:"jsonp",
    success: function(data){
        ...// 此时返回来的data就是js对象,可以直接使用
    }
});
```

## 8. 蓝图 - Blueprint

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
from branch import app_branch01 as 别名
app.register_blueprint(别名)
```
