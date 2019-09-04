# Spider

[TOC]

## 概述

其实就是用Python程序模仿人点击浏览器并访问网站，而且模仿的越逼真越好。

爬虫步骤

- 访问网页，查看数据加载的方式
- 确定要爬取的URL
- 由请求模块向URL地址发出请求,并得到网站的响应
- 从响应内容中提取所需数据，提取数据
- 数据存储

爬虫分类

1、通用网络爬虫(搜索引擎使用,遵守robots协议)

> robots协议 ：网站通过robots协议告诉搜索引擎哪些页面可以抓取,哪些页面不能抓取，
>                通用网络爬虫需要遵守robots协议（君子协议）
> 			  https://www.taobao.com/robots.txt

2、聚焦网络爬虫 ：自己写的爬虫程序



## 爬虫模块

|                      | urllib                                                       | requests                                                 |
| -------------------- | ------------------------------------------------------------ | -------------------------------------------------------- |
| 包装请求头           | request = urllib.request.Request(url, headers={'User-Agent':'Mozilla/5.0'}) | headers={'User-Agent':'Mozilla/5.0'}                     |
| 发送请求获取请求对象 | response = urllib.request.urlopen(request)                   | response = requests.get(url, headers=headers, timeout=3) |
|                      | html = response.read().decode('utf-8')                       | html = response.text                                     |



## 请求模块

### urllib.request

#### urllib.request.urlopen()

发送请求

```python
from urllib import request

# 向网站发起请求，得到响应对象
response = request.urlopen('http://www.baidu.com', timeout=1)
print(response)  # <http.client.HTTPResponse object at 0x7f5ed92db198>
```

参数：

- URL 需要爬取的URL地址
- timeout 设置等待超时时间,指定时间内未得到响应抛出超时异常

__response 对象方法__

```python
bytes = response.read()  # bytes类型
string = response.read().decode('utf-8')  # string类型
url = response.geturl()  # 返回实际数据的URL地址
code = response.getcode()  # 返回HTTP的响应码
```

查看请求头网站 http://httpbin.org/get

#### urllib.request.Request()

构造请求对象，包装请求，重构User-Agent

```python
# 构造请求对象
request = urllib.request.Request(
    url,
    headers={
        'User-Agent':'Mozilla/5.0 xxx'
    }
)

# 发送请求对象
response = urllib.request.urlopen(request)

# 获取响应内容
html = response.read().decode()
```

#### URL地址编码模块 -- urllib.parse

作用：给URL地址中的查询参数进行编码

```python
编码前：https://www.baidu.com/s?wd=美女
编码后：https://www.baidu.com/s?wd=%E7%BE%8E%E5%A5%B3
```

常用方法

```python
from urllib import parse

urlencode({dict})
urlencode({'wd':'皮卡丘'})
编码后: 'wd=%E7%9A%AE%E5%8D%A1%E4%B8%98&pn=20'

quote(string)
quote('皮卡丘')
编码后: '%E7%9A%AE%E5%8D%A1%E4%B8%98'
   
unquote('%E7%9A%AE%E5%8D%A1%E4%B8%98')
解码后: '皮卡丘'
```


案例：[爬取百度贴吧内容](code/tieba.py)

```python
from urllib import request, parse
import time

class BaiduSpider:
    def __init__(self):
        self.headers = {'User-Agent':'Opera/9.80 (Windows NT 6.1; U; en) Presto/2.8.131 Version/11.11'}
        self.baseurl = 'http://tieba.baidu.com/f?{}'

    # 获取页面
    def get_page(self, url):
        # 构造请求头
        req = request.Request(url, headers=self.headers)
        # 发送请求
        resp = request.urlopen(req)
        # 获取响应内容
        html = resp.read().decode('utf-8')
        return html

    # 解析页面
    def parse_page(self):
        pass

    # 保存数据
    def write_page(self, filename, html):
        with open(filename, 'w', encoding='utf-8') as f:
            f.write(html)

    # 主函数
    def main(self):
        name = input("请输入贴吧名:")
        start = int(input("请输入起始页:"))
        stop = int(input("请输入终止页:"))

        for page in range(start, stop+1):
            pn = (page-1) * 50
            # 查询参数
            query_string = {
                'kw':name,
                'pn':str(pn)
            }
            # 编码查询参数
            query_string = parse.urlencode(query_string)
            # 拼接url
            url = self.baseurl.format(query_string)

            html = self.get_page(url)
            filename = '{}-第{}页.html'.format(name, page)
            self.write_page(filename, html)
            print("第%d页爬取成功" % page)
            time.sleep(1)

if __name__ == '__main__':
    spider = BaiduSpider()
    spider.main()
```

案例：[电影天堂，二级页面抓取]()

```python
from urllib import request
import re

class FilmskySpider:
    def __init__(self):
        self.headers = {'User-Agent':'Mozilla/5.0'}

    def get_page(self, url):
        req = request.Request(url, headers=self.headers)
        resp = request.urlopen(req)
        html = resp.read().decode('gb18030', 'ignore')  # 忽略异常
        return html

    def parse_one_page(self, html):
        '''解析一级页面'''
        pattern = re.compile('<table width="100%".*?<a href="(.*?)".*?>(.*?)</a>', re.S)
        film_list = pattern.findall(html)
        for film in film_list:
            name = film[1].strip()
            link = 'https://www.dytt8.net' + film[0].strip()
            download_link = self.parse_two_page(link)
            print(name, download_link)

    def parse_two_page(self, link):
        '''解析二级页面'''
        html = self.get_page(link)
        pattern = re.compile('<td style="WORD-WRAP.*?<a href="(.*?)"', re.S)
        return pattern.findall(html)[0]

    def main(self):
        url = 'https://www.dytt88.net/html/gndy/dyzz/index.html'
        html = self.get_page(url)
        self.parse_one_page(html)

if __name__ == '__main__':
    spider = FilmskySpider()
    spider.main()
```



### requests

安装

```python
pip3 install requests
```

#### request.get()

```python
import requests
# 向网站发起get请求，并获取响应对象
resp = requests.get(url, headers=headers, timeout=3)
```

响应对象response属性：

- encoding 响应字符编码
- text 字符串
- content 字节流
- status_code HTTP响应码
- url 实际数据的url地址
- json() 将json数据转为Python对象

```python
# 修改响应字符编码
resp.coding = 'utf-8'
# 非结构化存储数据
with open('xxx.jpg', 'wb') as f:
    f.write(resp.content)
```

##### 查询参数 params

以字典的键值对作为查询参数

```python
resp = requests.get(url, params=params, headers=headers)
```

- url为基准url地址，不包含任何查询参数
- 该方法会自动对params字典编码，然后和url拼接

```python
# urlencode
baseurl = 'http://tieba.baidu.com/f?{}'
params = {
    'kw':'盗墓笔记',
    'pn':'50'
}
params = parse.urlencode(params) # '%E8...'
url = baseurl.format(params)

# requests params
baseurl = 'http://tieba.baidu.com/f?'
params = {
    'kw':'盗墓笔记',
    'pn':'50'
}
resp = requests.get(baseurl, params=params, headers=headers)
```

##### 代理参数 proxies

代替字节的ip地址取对接网络的ip地址，能隐藏自身真实ip，避免被封

```python
proxies = {
    '协议':'协议://IP:PORT'
}

proxies = {
    'http':'http://IP:PORT',
    'https':'https://IP:PORT'
}
```

使用免费普通代理IP访问测试网站: http://httpbin.org/get

```python
import requests

url = 'http://httpbin.org/get'
headers = { 'User-Agent' : 'Mozilla/5.0' }
proxies = {
    'http' : 'http://58.65.128.234:40344',
    'https': 'https://58.65.128.234:40344'
}

html = requests.get(
    url=url,
    proxies=proxies,
    headers=headers,
).text

print(html)
```

私密代理

```python
proxies = {
    '协议':'协议://用户名:密码@IP:端口号'
}

proxies = {
	'http':'http://用户名:密码@IP:端口号',
    'https':'https://用户名:密码@IP:端口号'
}
```

##### 验证参数 auth

针对需要web客户端用户名和密码认证的网站

```python
auth = (username, password)

auth = ('zhangsan', '123456')
requests.get(url, auth, headers, params=params, proxies=proxies)
```

##### SSL证书认证参数 verify

https类型网站，但是没有经过证书认证机构认证的网站，抛出SSLError错误时使用此参数忽略证书认证

```python
verify = True # 默认，检测证书认证
verify = False # 忽略证书认证
response = requests.get(
    url=url,
    params=params,
    proxies=proxies,
    auth=auth,
    verify=False,
    headers=headers,
    timeout=3
)
```

#### request.post()

对于post类型的网站，需要传入请求参数data

```python
response = requests.post(url, data=data, headers=headers)
# data: post数据 (Form表单数据 - 字典格式)
```

案例：[获取有道翻译结果]()

```python
'''
1、浏览器F12开启网络抓包,Network-All,页面翻译单词后找Form表单数据
2、在页面中多翻译几个单词，观察Form表单数据变化（有数据是加密字符串）
3、刷新有道翻译页面，抓取并分析JS代码（本地JS加密）
4、找到JS加密算法，用Python按同样方式加密生成加密数据
5、将Form表单数据处理为字典，通过requests.post()的data参数发送
'''

import requests
import time
from hashlib import md5
import random

# 获取相关加密算法结果
def get_salt_sign_ts(word):
    # ts
    ts = str(int(time.time() * 1000))
    # salt
    salt = ts +  str(random.randint(0,9))
    # sign
    string = "fanyideskweb" + word + salt + "@6f#X3=cCuncYssPsuRUE"
    # 创建md5加密对象
    s = md5()
    # 进行加密,参数必须为bytes数据类型
    s.update(string.encode())
    # 获取十六进制加密结果
    sign = s.hexdigest()
    return salt,sign,ts

# 引用有道翻译
def attack_yd(word):
    # 获取salt,sign,ts的值
    salt,sign,ts = get_salt_sign_ts(word)
    # F12抓到的Request URL的地址
    url = 'http://fanyi.youdao.com/translate_o?smartresult=dict&smartresult=rule'

    headers = {
        # cookie
        "Cookie": """OUTFOX_SEARCH_USER_ID=-753429634@10.108.160.17; JSESSIONID=aaaULe5n_rFYA4n1tMnUw; OUTFOX_SEARCH_USER_ID_NCOO=1644007711.3741386; ___rl__test__cookies=1561452293484""",
        # 从哪里过来的
        "Referer": "http://fanyi.youdao.com/",
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36",
    }

    data = {
        "i": word,
        "from": "AUTO",
        "to": "AUTO",
        "smartresult": "dict",
        "client": "fanyideskweb",
        "salt": salt,
        "sign": sign,
        "ts": ts,
        "bv": "b396e111b686137a6ec711ea651ad37c",
        "doctype": "json",
        "version": "2.1",
        "keyfrom": "fanyi.web",
        "action": "FY_BY_REALTlME",
    }

    # 发请求
    html = requests.post(
        url,data=data,headers=headers).json()
    print(html)

if __name__ == '__main__':
    word = input('请输入要翻译的单词:')
    attack_yd(word)
```



## 解析模块

- 正则表达式
- xpath
- CSS

### re

```python
# 方法1
r_list = re.findall('正则表达式', html, re.S)
# re.S 是功能标志位，让正则表达式的能匹配'\n'

# 方法2
# 1.创建正则编译对象
pattern = re.compile('正则表达式', re.S)
# 2.匹配得到结果列表
r_list = pattern.findall(html)
```

贪婪匹配和非贪婪匹配

```python
# 贪婪匹配 .*
# 非贪婪匹配 .*?
```

正则表达式分组

```python
1、想要什么内容在正则表达式中加()
2、多个分组,先按整体正则匹配,然后再提取()中数据。结果：[(),(),(),(),()]
```

```python
import re

s = 'A B C D'
p1 = re.compile('\w+\s+\w+')
print(p1.findall(s))
# 分析结果是什么？？？
# ['A B', 'C D']

p2 = re.compile('(\w+)\s+\w+')
print(p2.findall(s))
# 分析结果是什么？？？
# ['A', 'C']

p3 = re.compile('(\w+)\s+(\w+)')
print(p3.findall(s))
# 分析结果是什么？？？
# 第一步整体匹配: ['A B', 'C D']
# 第二步匹配分组: [('A', 'B'), ('C', 'D')]
```

HTML示例

```html
<div class="animal">.*?title="(.*?)".*?content">(.*?)</p>

<div class="animal">
    <p class="name">
		<a title="Tiger"></a>
    </p>
    <p class="content">
		Two tigers two tigers run fast
    </p>
</div>

<div class="animal">
    <p class="name">
		<a title="Rabbit"></a>
    </p>

    <p class="content">
		Small white rabbit white and white
    </p>
</div>
```

匹配演示

```python
<div class='animal'>.*?title="(.*?)"></a>.*?content">(.*?)</p>
```



### xpath

XPath即为XML路径语言，它是一种用来确定XML文档中某部分位置的语言，同样适用于HTML文档的检索


匹配规则


```python
1. // 从所有节点中查找（包括子节点和后代节点）
2. / 从子节点中查找
3. [] : 添加条件
4. @  ：获取属性值
	# 使用场景1（属性值作为条件）
    	[@属性="值"] 
    	//div[@class='movie-item-info']
    # 使用场景2（直接获取属性值）
    	/@属性 
    	//a[@class='ulink']/@href
5. xpath表达式1 | xpath表达式2 | xpath表达式3 匹配多路径
	//book/title | //book/price # 选取 book 元素的所有 title 和 price 元素
6. contains() 匹配属性值中包含某些字符串节点
	# 查找class属性值中包含"book_"的title节点
    //title[contains(@class, 'book_')]
7. text() ：获取节点的文本内容
   # 查找所有书籍的名称
   //title/text()  
```

xpath高级

```python
1、基准xpath表达式: 得到节点对象列表
2、for r in [节点对象列表]:
       username = r.xpath('./xxxxxx')
       # 此处注意遍历后继续xpath一定要以:  . 开头，代表当前节点
```

示例HTML

```html
<ul class="book_list">
    <li>
        <title class="book_001">Harry Potter</title>
        <author>J K. Rowling</author>
        <year>2005</year>
        <price>69.99</price>
    </li>

    <li>
        <title class="book_002">Spider</title>
        <author>Forever</author>
        <year>2019</year>
        <price>49.99</price>
    </li>
</ul>
```

匹配演示

```python
1、查找所有的li节点
   //li
   # /ul/li 一个斜杠表示紧跟着, 两个表示从整个查找
2、查找li节点下的title子节点中,class属性值为'book_001'的节点
   //li/title[@class='book_001']
3、查找li节点下所有title节点的,class属性的值
   //li//title/@class # 要包括所有后代节点，而不仅仅是子节点

# 只要涉及到条件,加 []
# 只要获取属性值,加 @
```

#### lxml 解析库

- 安装

```python
sudo pip3 install lxml
```

- 使用流程

```python
# 导入模块
from lxml import etrre
# 创建解析对象
parse_html = etree.HTML(html)
# 解析对象调用xpath
r_list = parse_html.xpath("xpath表达式")
```

案例：[链家二手房案例]()

```python
import requests
from lxml import etree
import time
import random

class LianjiaSpider(object):
    def __init__(self):
        self.url = 'https://bj.lianjia.com/ershoufang/pg{}/'
        self.headers = {
            'User-Agent': 'Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.1; WOW64; Trident/4.0; SLCC2; .NET CLR 2.0.50727; .NET CLR 3.5.30729; .NET CLR 3.0.30729; Media Center PC 6.0; .NET4.0C; InfoPath.3)'
        }
        # 添加计数变量
        self.page = 1

    def get_page(self, url):
        res = requests.get(url, headers=self.headers)
        res.encoding = 'utf-8'
        html = res.text
        # 直接调用解析函数
        self.parse_page(html)

    # 用xpath提取数据
    def parser_page(self, html):
        parse_html = etree.HTML(html)
        # 基准xpath [(), (), ()]
        li_list = parse_html.xpath('//li[@class="clear LOGCLICKDATA]')
        print(len(li_list))

        for li in li_list:
            name = li.xpath('.//div[@class="houseInfo"]/a/text()')[0].strip()
            total_price = li.xpath('.//div[@class="totalPrice"]/span[1]/text()')[0].strip()
            unit_price = li.xpath('.//div["unitPrice"]/span[1]/text()')[0].strip()
            house_dict = {
                "名称":name,
                "总价":total_price,
                "单价":unit_price
            }
            print(house_dict)

    def main(self):
        for pg in range(1, 3):
            url = self.url.format(str(pg))
            self.get_page(url)
            print("第%d页爬取完成" % self.page)
            self.page += 1
            time.sleep(random.randint(1, 3))

if __name__ == '__main__':
    spider = LianjiaSpider()
    spider.main()
```



## 动态加载数据抓取-Ajax

网页源码中没有具体数据，滚动鼠标滑轮或其他动作时加载数据。

### 抓取步骤

- 打开控制台，找到Network选项卡，页面动作抓取网络数据包
- 控制台常用选项卡
  - Network 抓取网络数据包
    - ALL 抓取所有的网络数据包
    - XHR 抓取异步加载的网络数据包
    - JS 抓取所有JS文件
  - Source 格式化输出并打断点调试JavaScript代码，助于分析爬虫中一些参数
  - Console 交互模式，可对JavaScript中代码进行测试
- 抓取具体网络数据
  - 单击左侧网络数据包地址，进入数据包详情，查看
    - Headers 整个请求头 General、Response Headers、Request Headers、Query String、Form Data
    - Preview 对响应内容预览
    - Response 响应内容
    - Cookies 请求响应中Cookies信息
- 抓取json文件URL地址
- 请求获取json
- 从json中提取数据

案例：[豆瓣电影数据抓取]()

```python
import requests
import json

class DoubanSpider(object):
    def __init__(self):
        self.headers = {
            'User-Agent' : 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36'
        }
        self.url = 'https://movie.douban.com/j/chart/top_list?'

    def get_page(self,params):
        res = requests.get(
            url=self.url,
            params=params,
            headers=self.headers,
            verify=False
        )
        # html: '[{},{},{}]'
        html = res.text
        # html:[{},{}]
        html = json.loads(html)
        self.parse_page(html)

    # 解析+保存
    def parse_page(self,html):
        # html: [{},{},{},{}]
        for film in html:
            # 名称
            name = film['title']
            # 评分
            score = film['score']

            print({'name':name,'score':score})


    def main(self):
        menu = '''\033[31m1.剧情 2.喜剧 3.动作 请选择(1/2/3):\033[0m'''
        c = input(menu)
        ty_dict = {'1':'11','2':'24','3':'5'}
        n = input('请输入电影数量:')
        # 定义查询参数
        params = {
            'type': ty_dict[c],
            'interval_id': '100:90',
            'action': '',
            'start': '0',
            'limit': n
        }
        self.get_page(params)

if __name__ == '__main__':
    spider = DoubanSpider()
    spider.main()
```

### json解析模块

```python
json.loads(json格式字符串)
# 把json格式的字符串转为Python数据类型
html_json = json.laods(res.txt)
# 等价于
html_json = res.json()

json.dump(python, f, ensure_ascii=False)
# 把python数据类型 转为 json格式的字符串
# 一般让你把抓取的数据保存为json文件时使用
# 第1个参数: python类型的数据(字典，列表等)
# 第2个参数: 文件对象
# 第3个参数: ensure_ascii=False # 序列化时编码

json_str = json.dumps(python_obj)
# 将Python数据转为json对象
```



## 数据存储

- csv模块
- MySQL
- MongoDB

### csv

```python
# 1. 导入模块
import csv

# 2. 打开csv文件
with open('test.csv', 'w') as f:
    # 3. 初始化写入对象
    writer = csv.writer(f)
	# 4. 写入数据(参数为列表)
    writer.writerow(['alan', '20'])  # 单行写入
    writer.writerrows([('ben', '21'), ('bran', '19')])  # 多行写入
```

### MongoDB

```python
import pymongo

conn = pymongo.MongoClient('127.0.0.1', 27017)
db = conn['testdb']
myset = db['student']

# 插入数据
# insert_one({:})
myset.insert_one({'name':'zhangsan'})
# insert_many([{:},{:}])
myset.insert_many([{'name':'lisi'}, {'name':'wangwu'}])
```

### MySQL

```python
import pymysql
db = pymysql.connect('localhost', 'root', '123456', 'testdb', charset='utf8')
cursor = db.cursor()
# execute() 方法第二个参数为列表传参补位
cursor.execute('insert into film values(%s, %s)', ['霸王别姬', '1993'])
cursor.executemany(
    'insert into film values(%s, %s)', 
    [
        ['霸王别姬', '1993'],
        ['喜剧之王', '2000']
    ]
)
# 提交事务
db.commit()
# 关闭
cursor.close()
db.close()
```

案例：[爬取猫眼电影](code/maoyan.py)

```python
from urllib import request
import time, csv, re, random
import pymongo
import pymysql

class FilmSpider:
    def __init__(self):
        self.reg = '''
            <div class="movie-item-info">.*?title="(.*?)".*?star">(.*?)</p>.*?releasetime">(.*?)</p>
        '''
        self.headers = {'User-Agent':'Opera/9.80 (Windows NT 6.1; U; en) Presto/2.8.131 Version/11.11'}
        self.url = 'http://maoyan.com/board/4?offset={}'
        self.page = 1

    def get_page(self, url):
        req = request.Request(url, headers=self.headers)
        resp = request.urlopen(req)
        html = resp.read().decode('utf-8')
        self.parse_page(html)

    def parse_page(self, html):
        pattern = re.compile(self.reg, re.S)
        film_list = pattern.findall(html)
        self.write_csv(film_list)
        self.write_mongo(film_list)
        self.write_mysql(film_list)

    def write_csv(self, film_list):
        films = []
        for film in film_list:
            tup = (
                film[0].strip(),
                film[1].strip(),
                film[2].strip()
            )
            films.append(tup)
        with open('films.csv', 'a') as f:
            writer = csv.writer(f)
            writer.writerrows(films)

    def write_mongo(self, film_list):
        conn = pymongo.MongoClient('localhost', 27017)
        db = conn['maoyandb']
        self.myset = db['filmset']
        for film in film_list:
            film_dict = {'电影名称':film[0].strip(), '主演':film[1].strip(), '上映时间':film[2].strip()}
            self.myset.insert_one(film_dict)

    def write_mysql(self, film_list):
        self.db = pymysql.connect('localhost','root','123456','maoyandb', charset='utf8')
        self.cursor = self.db.cursor()
        sql = 'insert into filmset values(%s, %s, %s)'
        data_list = []
        for film in film_list:
            L = [film[0].strip(), film[1].strip(), film[2].strip()]
            data_list.append(L)
        self.cursor.executemany(data_list)
        self.db.commit()


    def main(self):
        for offset in range(0, 91, 10):
            url = self.url.format(str(offset))
            self.get_page(url)
            print("第%d页完成" % self.page)
            self.page += 1
            time.sleep(random.randint(1, 3))

        self.cursor.close()
        self.db.close()
        self.db.close()

if __name__ == '__main__':
    filmspider = FilmSpider()
    filmspider.main()
```

案例：[B站视频爬取]()



## 浏览器插件

- Xpath Helper：轻松获取HTML元素的xPath路径
- JsonView：格式化输出json格式数据
- Proxy SwitchyOmega：Chrome浏览器中的代理管理扩展程序



## 模拟登陆

抓取需要登陆才能访问的页面

### cookie携带信息模拟登录

- 先登陆一次，获取携带登录信息的cookie

- 发送携带cookie的请求

```python
# 登录成功后获取携带登录信息的cookie

# headers携带cookie发送请求
headers = {
   'Cookies' = 'xxxx',
   'Referer'='http://...',
   'User-Agent'='Mozilla/5.0'
}
```

```python
import requests
from lxml import etree

url = 'http://www.renren.com/970294164/profile'
headers = {
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3",
    "Accept-Encoding": "gzip, deflate",
    "Accept-Language": "zh-CN,zh;q=0.9",
    "Connection": "keep-alive",
    "Cookie": "anonymid=jxcu58gtb3su1j; depovince=GW; _r01_=1; JSESSIONID=abcHa63uavQqQcYAbzsUw; ick_login=99eed731-d6e6-435e-86f4-48d79665dc7e; first_login_flag=1; ln_uact=15110225726; ln_hurl=http://head.xiaonei.com/photos/0/0/men_main.gif; jebe_key=7bcea0a4-10fd-4ca5-b293-88b450860dbb%7C3317b1face1adcda7e34f17db4558a85%7C1561529179783%7C1%7C1561529181483; jebe_key=7bcea0a4-10fd-4ca5-b293-88b450860dbb%7C3317b1face1adcda7e34f17db4558a85%7C1561529179783%7C1%7C1561529181490; wp_fold=0; td_cookie=18446744069786964649; jebecookies=0c1c850f-7d48-401f-988a-bf559aff0777|||||; _de=5411E55883CC3142BC1347536B8CB062; p=5853eb6bce75b4d8f6f00d71afb004484; t=2e3ba1766211b39caec43c2b1071bd924; societyguester=2e3ba1766211b39caec43c2b1071bd924; id=970294164; xnsid=6d94fc61; loginfrom=syshome",
    "Host": "www.renren.com",
    "Referer": "http://www.renren.com/SysHome.do",
    "Upgrade-Insecure-Requests": "1",
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36",
}
html = requests.get(url,headers=headers).text
parse_html = etree.HTML(html)
result = parse_html.xpath(
'//*[@id="operate_area"]/div[1]/ul/li[1]/span/text()'
)

print(result)
```



### 利用request模块中session会话保持功能

- 创建session对象
- 让session发送post请求
- 再get需要登录才能访问的页面

```python
# 实例化session对象
session = requests.session()
# 让session对象发送post请求
session.post(url=post_url, data=post_data, headers=headers)
# 再get需要登录才能访问的页面
html = session.get(url, headers=headers).text
```

案例：[利用session会话保持功能爬取人人网信息]()

```python
import requests
from lxml import etree

# 1.先POST（把用户名和密码post到一个地址中）
post_url = 'http://www.renren.com/PLogin.do'
post_data = {
    'email' : '15110225726',
    'password' : 'zhanshen001'
}
headers = {
    'User-Agent' : 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.100 Safari/537.36',
    'Referer':'http://www.renren.com/SysHome.do'
}
# 实例化session对象
session = requests.session()
session.post(
    url=post_url,
    data=post_data,
    headers = headers
)
# 2.再get（访问需要登录后才能访问的页面）
url = 'http://www.renren.com/970294164/profile'
html = session.get(url,headers=headers).text

parse_html = etree.HTML(html)
result = parse_html.xpath(
'//*[@id="operate_area"]/div[1]/ul/li[1]/span/text()'
)
print(result)
```



## selenium+phantomjs/Chrome/Firefox

### selenium

Web自动化测试工具，可运行在浏览器,根据指令操作浏览器。只是工具，必须与第三方浏览器结合使用

```shell
# 安装
# Linux
sudo pip3 install selenium
# Windows
python -m pip install selenium
```

### phantomjs浏览器

无界面浏览器(又称无头浏览器)，在内存中进行页面加载,高效

```shell
# 安装phantomjs、chromedriver、geckodriver
# Windows
1、下载对应版本的phantomjs、chromedriver、geckodriver
2、把chromedriver.exe拷贝到python安装目录的Scripts目录下(添加到系统环境变量)
   查看python安装路径: where python
3、验证
   cmd命令行: chromedriver

# 下载地址
chromedriver : 下载对应版本
http://chromedriver.storage.googleapis.com/index.html

geckodriver
https://github.com/mozilla/geckodriver/releases

# Linux
1、下载后解压
   tar -zxvf geckodriver.tar.gz 
2、拷贝解压后文件到 /usr/bin/ （添加环境变量）
   sudo cp geckodriver /usr/bin/
```

### selenium+phantomjs的使用

```python
# 导入selenium中webdriver接口
from selenium import webdriver

# 1.创建浏览器对象
browser = webdriver.PhantomJS()
# 2.输入网址
browser.get('http://www.baidu.com/')
# 获取截图
browser.save_screenshot('baidu.png')
# 打印响应内容
print(browser.page_source.find('su'))

# 关闭浏览器
browser.quit()
```

```python
from selenium import webdriver
import time

# 创建浏览器对象
browser = webdriver.PhantomJS()
# 打开百度
browser.get('http://www.baidu.com/')
# 找到搜索框,发送文本
text = browser.find_element_by_xpath('//*[@id="kw"]')
text.send_keys('皮卡丘')
time.sleep(3) # 输入关键词后百度会跳到新页面，所以等待跳转完毕再找到按钮点击进行搜索
# 找到 百度一下 按钮,点击
button = browser.find_element_by_xpath('//*[@id="su"]')
button.click()
# 截图
```

### webdriver对象方法

```python
browser = webdriver.Firefox(executable_path='path')# 创建浏览器对象
browser.get(url)# 访问网站
browser.page_source # 查看响应内容
browser.page_source.find('字符串')# 从html源码中搜索指定字符串,没有找到返回：-1
browser.quit() # 关闭浏览器

# 单元素查找，找到第一个符合条件的就返回
browser.find_element_by_id('')
browser.find_element_by_name('')
browser.find_element_by_class_name('')
browser.find_element_by_xpath('')

# 多元素查找，返回节点对象列表
browser.find_elements_by_id('')
browser.find_elements_by_name('')
browser.find_elements_by_class_name('')
browser.find_elements_by_xpath('')

# 节点对象操作
ele.send_keys('') # 搜索框发送内容
ele.click() # 点击按钮
ele.text # 能获取当前节点和后代节点的文本内容
ele.get_attribute('src') # 获取属性值
```

```python
from selenium import webdriver

browser = webdriver.PhantomJS()
browser.get('https://www.qiushibaike.com/text/')

# 单元素查找
# 1.找到第一个符合条件的就返回
# 2.text属性获取当前节点及后代节点的文本内容
one = browser.find_element_by_class_name('content')
# print(one.text)

# 多元素查找
many = browser.find_elements_by_class_name('content')
for one in many:
    print(one.text)
    print('*'*50)
```

案例：[京东商品爬取]()

### chromedriver设置无界面模式

```python
from selenium import webdriver

# 创建功能对象并添加无界面参数
options = webdriver.ChromeOptions()
options.add_argument('--headless')
# 创建浏览器对象
browser = webdriver.Chrome(options=options)
browser.get('http://www.baidu.com/')
browser.save_screenshot('baidu.png')
```



## 多线程爬虫

### 队列 Queue

```python
# 导入模块
from queue import Queue
from multiprocessing import Queue
# 使用
q = Queue()
q.put(url)  # 入队列
q.get()  # 从队列中取数据，当队列为空时，阻塞
q.empty()  # 判断队列是否为空，True/False
q.get(block=True, timeout=1)  # 1秒内没有get到数据，抛出异常
```

### threading

```python
# 导入模块
from threading import Thread

# 使用流程  
t = Thread(target=函数名) # 创建线程对象
t.start() # 创建并启动线程
t.join()  # 阻塞等待回收线程

# 如何创建多线程，如下方法你觉得怎么样？？？？？
# 不怎么样
for i in range(5):
    t = Thread(target=函数名)
    t.start()
    t.join()
# 改进版
t_list = []
# 创建并启动5个线程
for i in range(5):
    t = Thread(target=函数名)
    t_list.append(t)
    t.start()
# 回收线程
for i in t_list:
    i.join()
```

案例：[小米应用商店爬取]()



## scrapy框架

异步处理框架,可配置和可扩展程度非常高,Python中使用最广泛的爬虫框架

### scrapy框架五大组件

- 引擎Engine：整个框架核心
- 调度器Scheduler：维护请求队列
- 下载器Downloader：获取响应对象
- 爬虫文件Spider：数据解析提取
- 项目管道Pipeline：数据入库处理
- 下载器中间件Downloader Middlewares：引擎 -> 下载器，包装请求（随机代理等）
- 蜘蛛中间件Spider Middlewares：引擎 -> 爬虫文件，可修改响应对象属性

### scrapy爬虫工作流程

1. 由引擎向爬虫程序索引第一个要爬取的URL，交个调度器入队列
2. 调度器处理请求后出队列，通过下载器中间件交个下载器去下载
3. 下载器得到响应对象后，通过蜘蛛中间件交个爬虫程序
4. 爬虫程序进行数据提取：
   1. 数据交给管道文件去入库处理
   2. 对于需要继续跟进的URL，再交个调度器入队列，依次循环

### scrapy常用命令

```shell
# 安装Scrapy
pip3 install Scrapy
# 创建爬虫项目
cd 项目文件夹
scrapy startproject 项目名
# 创建爬虫文件
scrapy genspider 爬虫名 域名
# 运行爬虫
scrapy crawl 爬虫名
```

### scrapy项目目录结构

```python
Baidu                   # 项目文件夹
├── Baidu               # 项目目录
│   ├── items.py        # 定义数据结构
│   ├── middlewares.py  # 中间件
│   ├── pipelines.py    # 数据处理
│   ├── settings.py     # 全局配置
│   └── spiders
│       ├── baidu.py    # 爬虫文件
└── scrapy.cfg          # 项目基本配置文件
```

### 创建爬虫项目步骤

```shell
1、新建项目 ：scrapy startproject 项目名
2、cd 项目文件夹
3、新建爬虫文件 ：scrapy genspider 文件名 域名
4、明确目标(items.py)
5、写爬虫程序(文件名.py)
6、管道文件(pipelines.py)
7、全局配置(settings.py)
8、运行爬虫 ：scrapy crawl 爬虫名
```

### pycharm运行爬虫项目

```python
1、创建begin.py(和scrapy.cfg文件同目录)
2、begin.py中内容：
	from scrapy import cmdline
	cmdline.execute('scrapy crawl maoyan'.split())
```

### 全局配置文件settings.py详解

```python
# 1、定义User-Agent
USER_AGENT = 'Mozilla/5.0'
# 2、是否遵循robots协议，一般设置为False
ROBOTSTXT_OBEY = False
# 3、最大并发量，默认为16
CONCURRENT_REQUESTS = 32
# 4、下载延迟时间
DOWNLOAD_DELAY = 1
# 5、请求头，此处也可以添加User-Agent
DEFAULT_REQUEST_HEADERS={}
# 6、项目管道
ITEM_PIPELINES={
	'项目目录名.pipelines.类名':300
}
```

### Scrapy案例

#### 爬取百度首页

目标：打开百度首页，把 '百度一下，你就知道' 抓取下来，从终端输出

- 创建项目Baidu和爬虫文件baidu

```shell
scrapy startproject Baidu
cd Baidu
scrapy genspider baidu www.baidu.com
```

- 编写爬虫文件baidu.py，xpath提取数据

```python
# -*- coding: utf-8 -*-
import scrapy

class BaiduSpider(scrapy.Spider):
    name = 'baidu'
    allowed_domains = ['www.baidu.com']
    start_urls = ['http://www.baidu.com/']

    def parse(self, response):
        result = response.xpath('/html/head/title/text()').extract_first()
        print('*'*50)
        print(result)
        print('*'*50)
```

- 全局配置settings.py

```python
USER_AGENT = 'Mozilla/5.0'
ROBOTSTXT_OBEY = False
DEFAULT_REQUEST_HEADERS = {
	'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
	'Accept-Language': 'en',
}
ITEM_PIPELINES = {
    '项目目录名.pipelines.类名':300,
}
```

- 创建begin.py（和scrapy.cfg同目录）

```python
from scrapy import cmdline

cmdline.execute('scrapy crawl baidu'.split())
```

- 启动爬虫

```python
直接运行 begin.py 文件即可
```

#### 猫眼电影案例

- 创建项目和爬虫文件

```shell
# 创建爬虫项目
scrapy startproject Maoyan
# 创建爬虫文件
cd Maoyan
scrapy genspider maoyan maoyan.com
```

- 定义要爬取的数据结构（items.py）

```python
name = scrapy.Field()
start = scrapy.Field()
time = scrapy.Field()
```

- 编写爬虫文件（maoyan.py）

```python
1、基准xpath,匹配每个电影信息节点对象列表
	dd_list = response.xpath('//dl[@class="board-wrapper"]/dd')
2、for dd in dd_list:
	电影名称 = dd.xpath('./a/@title')
	电影主演 = dd.xpath('.//p[@class="star"]/text()')
	上映时间 = dd.xpath('.//p[@class="releasetime"]/text()')
```

```python
from ..items import MaoyanItem

def parse(self, response):
    # 基准xpath
    dd_list = response.xpath('xxxx')
    for dd in dd_list:
        item = MaoyanItem()
        item['name'] = dd.xpath('xxx').extract_first()
        item['star'] = dd.xpath('xxx').extract_first()
        item['time'] = dd.xpath('xxx').extract_first()
        # 把 item 交给管道文件的 process_item() 处理
        yield item
```



## 反爬总结

- 基于User-Agent反爬

  - 发送携带请求头的请求

  - 多个请求随机切换

    - 定义列表存放大量User-Agent，使用random.choice()每次随机选择

    - 使用fake_useragen每次随机访问生成User-Agent

      ```python
      from fake_useragent import UserAgent
      ua = UserAgent
      user_agent = ua.random
      ```

- 响应内容前端做处理反爬

  - html页面中可以匹配出内容，程序中匹配结果为空
    - 前端对响应内容做了一些结构上的调整，通过查看网页源代码，格式化输出查看结构，更改xpath或正则测试

- 基于IP反爬

  - 控制爬取速度，每爬取页面后随即休眠一定时间，再继续爬取下一页面
  - 频繁更换IP地址，使用代理IP，随即切换代理访问页面

- Ajax动态加载：从url加载网页的源代码后,会在浏览器执行JavaScript程序,这些程序会加载更多内容

  - 找到真实数据URL，再发请求提取数据

- 对查询参数加密

  - 找到JS文件,分析加密算法,用Python实现加密执行JS文件中的代码,返回加密数据
            

## Fiddler抓包工具

- 配置Fiddler

```python
# 添加证书信任
1、Tools - Options - HTTPS
   勾选 Decrypt Https Traffic 后弹出窗口，一路确认
# 设置只抓取浏览器的数据包
2、...from browsers only
# 设置监听端口（默认为8888）
3、Tools - Options - Connections
# 配置完成后重启Fiddler（重要）
4、关闭Fiddler,再打开Fiddler
```

- 配置浏览器代理

```python
1、安装Proxy SwitchyOmega插件
2、浏览器右上角：SwitchyOmega->选项->新建情景模式->AID1901(名字)->创建
   输入 ：HTTP://  127.0.0.1  8888
   点击 ：应用选项
3、点击右上角SwitchyOmega可切换代理
```

- Fiddler常用菜单

```python
1、Inspector ：查看数据包详细内容
   整体分为请求和响应两部分
2、常用菜单
   Headers ：请求头信息
   WebForms: POST请求Form表单数据 ：<body>
             GET请求查询参数: <QueryString>
   Raw
   将整个请求显示为纯文本
```

