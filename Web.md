# Web

web分为前端与后端

* 前端：网页，包括HTML、CSS、JS
  * 静态网页：不能与服务器交互的网页
  * 动态网页：能够与服务器交互的网页
* 后端
  * Flask、Django
  * Ajax技术

## 1. 前端

### 1.1 前端/浏览器/客户端)

1. 代替用户发请求（用户代理）
2. 解析数据并呈现给用户

### 1.2 前端开发架构

网页是基于浏览器的应用程序

B/S : Browser/Server 浏览器与服务器交互模式
C/S ：Client/Server  客户端与服务器交互 不能跨平台

### 1.3 浏览器产品

* Chrome  - Google
* IE / Edge - Microsoft
* Safari - Apple
* FireFox - Mozilla
* Opera - Opera

### 1.4 引擎组成

1. 渲染引擎：关系整个页面的渲染
2. JS引擎：对JS代码的处理

### 1.5 服务器架构

* Apache
* Tomcat
* IIS - Internet Information Service
* Nginx

### 1.6 W3C标准

World Wid Web Consortium 万维网联盟

一个页面由三部分组成:

* 结构 -- HTML, 用于描述页面结构
* 表现 -- CSS, 用于控制页面中元素样式
* 行为 -- JavaScript, 用于响应用户操作

## 2. 后端/服务器

1. 存储数据
2. 处理并响应请求

### 2.1 组成

1. 硬件
    * 主机
2. 软件: 能够处理用户请求的程序
    * Apache
    * Tomcat
    * IIS
    * Nginx

### 2.2 作用

* 存储web相关的数据
* 处理请求和响应
* 具备安全性的功能

### 2.3 框架

为了解决一些开放性问题，重复性问题而产生的程序结构

#### 2.3.1 框架模式

1. MVC
    * M：模型层Model，与数据库打交道，将数据库的表抽象成类
    * V：视图层View
    * C：控制层Controller
2. MTV（Flask）
    * M：模型层Model
    * T：模板Template(HTML模板)，主要操作视图
    * V：View视图层，处理请求与响应
3. MVVM
    * M: 模型层Model
    * V: 视图层View
    * VM: 视图模型ViewModel,处理请求与响应

## 3. 协议

通信协议，规范数据在传输过程中以何种形式传递 e.g.
`http(s)` : HyperText Transfer Protocal 超文本传输协议

## 路由与URL

`http://mail.163.com/index.html`

* `http://:`这个是协议，也就是HTTP超文本传输协议，也就是网页在网上传输的协议。
* `mail`：这个是服务器名，代表着是一个邮箱服务器，所以是mail.
* `163.com`: 这个是域名，是用来定位网站的独一无二的名字。
* `mail.163.com`：这个是网站名，由`服务器名+域名`组成。
* `/`：这个是根目录，也就是说，通过网站名找到服务器，然后在服务器存放网页的根目录
* `index.html`：这个是根目录下的默认网页（当然，163的默认网页是不是这个我不知道，只是大部分的默认网页，都是index.html）
* `index/`: 这个是路由
* `http://mail.163.com/index.html`: 这个叫做URL，统一资源定位符，全球性地址，用于定位网上的资源

Uniform Resource Locator, URL 对互联网上得到的资源的位置和访问方法的一种简洁的表示，是互联网上标准资源的地址。互联网上的每个文件都有一个唯一的URL，它包含的信息指出文件的位置以及浏览器应该怎么处理它