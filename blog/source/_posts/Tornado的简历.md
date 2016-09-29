---
title: Tornado的简历
date: 2016-05-04 00:32:28
tags: Tornado源码分析
categories: Tornado系列
toc: true
comments: true
---

Python家族有祖孙满堂，其中tornado无疑是其最有出息的子孙之一。接下来，我们从tornado的一份档案开始介绍轻量、易扩展的web界大佬。
<!-- more -->

# Tornado基本信息

## 简历
- 姓名：tornado
- 别名：Facebook 的 Web 服务器及其常用工具的开源版本
- 民族：反正不是中国
- 性别：不确认
- 民族：反正不是汉族
- 职业：Python界轻量级的web服务框架的幕后大老板、独立董事
- 社会关系：
    - 父亲：Python
    - 母亲：Python
    - 儿子：千千万万Python用户
- 生卒：21世纪-永远
- 个人技能： 简单、轻松的为Python的web服务开发者提供帮助，能解决[C10K](http://blog.csdn.net/jysg9/article/details/7901321)问题。tornado和现在主流的web服务器框架相比的区别：它是非阻塞式服务器，速度很快。因为tornado使用了非阻塞的方式和背靠epoll这颗大树，所以tornado每秒可以处理数以千计的链接。
- 出生背景：开发这个web服务器框架的主要目的是为了处理FriendFeed的实时功能----在FriendFeed的应用里每个活动的用户都会保持一个服务器连接。这就意味着tornado是为了解决同时又多个连接出生的。（FriendFeed主要是方便用户跟踪好友在Facebook和Twitter等多个社交网站上的活动。）
- 个人特色：采用asynchronous IO的网络模型，异步式IO一种很高效的网络模型；支持WSGI；tornado是异步http框架，天然具备长轮询的优势。
- 缺点：tornado是单线程的。

## tornado的安装
*忽略，地球人都知道*

## tornado版hello world - 简单的Web服务
首先认识一下tornado是如何工作的，这里看看业界语言最经典的test，hello world问题：
```python
import tornado.httpserver
import tornado.ioloop
import tornado.options
import tornado.web

from tornado.options import define, options
define("port", default=8000, help="run on the given port", type=int)

class IndexHandler(tornado.web.RequestHandler):
    def get(self):
        greeting = self.get_argument('greeting', 'Hello')
        self.write(greeting + ', tornado world!')

if __name__ == "__main__":
    tornado.options.parse_command_line()
    app = tornado.web.Application(handlers=[(r"/", IndexHandler)])
    http_server = tornado.httpserver.HTTPServer(app)
    http_server.listen(options.port)
    tornado.ioloop.IOLoop.instance().start()
```
- 启动服务：python hello.py --port=8000
- 访问服务：
```shell
$ curl http://localhost:8000/
Hello, tornado world!
```
神奇的hello world就这样出生了，虽然谈不上眉清目秀，但是简单，不用怀胎十月，痛苦分娩。下面我们看看hello.py的剖腹产的视频，看看这哥们的出生过程,以及身体器官的构成。

### tornado版hello.py的剖腹产的视频
我们一步步分解开看看，这个小伙子是怎么成人的：
```python
import tornado.httpserver
import tornado.ioloop
import tornado.options
import tornado.web
```
- 导入tornado的必备模块，虽然tornado有很多有用的模块，但是这四个是最重要的模块，这就像婴儿的四肢和大脑，为人类必备。

```python
from tornado.options import define, options
define("port", default=8000, help="run on the given port", type=int)
```
- 这是tornado提供的一个很有用的模块(tornado.options)，是从命令行中读取设置。我们这里读取的是监听的http请求的端口，我们看看define的工作方法：
  - 如果命令行出现define设置的同名参数，则这个参数成为全局的options的一个属性
  - 如果用户在命令行运行了--help，则打印出define指定的help的信息
  - 如果用户没有指定相应的数据值，则使用default
  - type参数是指定port属性的类型，若命令行设置的参数类型校验不通过，则抛出一个异常。
```python
class IndexHandler(tornado.web.RequestHandler):
def get(self):
    greeting = self.get_argument('greeting', 'Hello')
    self.write(greeting + ', friendly user!')
```
- 这个函数是前端请求背后的男人，即tornado前端请求的处理类，这个男人一个月的薪资决定了前端的请求是逛天猫的奢侈品旗舰店呢，还是淘宝小摊。
- 当处理一个请求时，Tornado将这个类实例化，并调用与HTTP请求方法所对应的方法，此处我们定义了一个get方法，也就是说此函数对tornado的HTTP的get请求作出响应。
- tornado的RequestHandler类有一系列的非常有用的函数，也是tornado中非常重要的一个类。这里用到的**get_argument**函数，我们从字面意思就知道这个函数是取得字符串中greeting参数的值，缺省为Hello。
- **write（）**函数是RequestHandler类中一个比较重要的函数，它以一个字符串作为函数的参数，并回写到HTTP响应中。
- RequestHandler对象的基础：如何从一个传入的HTTP请求中获得信息（使用get_argument和传入到get和post的参数）以及写HTTP响应（使用write方法）在这个小程序中都有显示。

```python
if __name__ == "__main__":
    tornado.options.parse_command_line()
    app = tornado.web.Application(handlers=[(r"/", IndexHandler)])
```

 - 这里才是这个小伙子的大脑和手脚，是真正让这个小伙子活蹦乱跳的关键所在，是真正让这个tornado运行起来的语句。首先，我们使用Tornado的options模块来解析命令行。然后我们创建了一个Tornado的**Application**类的实例。**传递给Application类__init__方法的最重要的参数是handlers。它告诉Tornado应该用哪个类来响应请求。**
 - 参数**handlers**是非常重要的，它是由一个元组组成的列表，其中每个元组的第一个元素是一个用于匹配的正则表达式，第二个元素是用于响应这个请求的一个RequesHandler类。

```python
http_server = tornado.httpserver.HTTPServer(app)
http_server.listen(options.port)
tornado.ioloop.IOLoop.instance().start()
```

 - 从这里开始的代码将会被反复使用：**一旦Application对象被创建，我们可以将其传递给Tornado的HTTPServer对象，然后使用我们在命令行指定的端口进行监听（通过options对象取出。）最后，在程序准备好接收HTTP请求后，我们创建一个Tornado的IOLoop的实例**。
以上就是Python家族中tornado大佬小时候刚出生的时候，单纯简单的大脑和四肢，但是注意，虽然简单，却五脏俱全。

了解了上面的上的短小精悍的hello world，接下来的一些tornado系列，我将庖丁解牛般的逐个分析tornado的源码，重点解决一些tornado的一些精华类，并比较一些优缺点。

# tornado的源码目录结构
```shell
├── tornado
│   ├── auth.py
│   ├── autoreload.py
│   ├── concurrent.py
│   ├── curl_httpclient.py
│   ├── escape.py
│   ├── gen.py
│   ├── http1connection.py
│   ├── httpclient.py
│   ├── httpserver.py
│   ├── httputil.py
│   ├── __init__.py
│   ├── ioloop.py
│   ├── iostream.py
│   ├── _locale_data.py
│   ├── locale.py
│   ├── locks.py
│   ├── log.py
│   ├── netutil.py
│   ├── options.py
│   ├── platform
│   │   ├── asyncio.py
│   │   ├── auto.py
│   │   ├── caresresolver.py
│   │   ├── common.py
│   │   ├── epoll.py
│   │   ├── __init__.py
│   │   ├── interface.py
│   │   ├── kqueue.py
│   │   ├── posix.py
│   │   ├── select.py
│   │   ├── twisted.py
│   │   └── windows.py
│   ├── process.py
│   ├── queues.py
│   ├── simple_httpclient.py
│   ├── speedups.c
│   ├── stack_context.py
│   ├── tcpclient.py
│   ├── tcpserver.py
│   ├── template.py
│   ├── test
│   │   ├── asyncio_test.py
│   │   ├── auth_test.py
│   │   ├── concurrent_test.py
│   │   ├── csv_translations
│   │   │   └── fr_FR.csv
│   │   ├── curl_httpclient_test.py
│   │   ├── escape_test.py
│   │   ├── gen_test.py
│   │   ├── gettext_translations
│   │   │   └── fr_FR
│   │   │       └── LC_MESSAGES
│   │   │           ├── tornado_test.mo
│   │   │           └── tornado_test.po
│   │   ├── httpclient_test.py
│   │   ├── httpserver_test.py
│   │   ├── httputil_test.py
│   │   ├── import_test.py
│   │   ├── __init__.py
│   │   ├── ioloop_test.py
│   │   ├── iostream_test.py
│   │   ├── locale_test.py
│   │   ├── locks_test.py
│   │   ├── log_test.py
│   │   ├── __main__.py
│   │   ├── netutil_test.py
│   │   ├── options_test.cfg
│   │   ├── options_test.py
│   │   ├── process_test.py
│   │   ├── queues_test.py
│   │   ├── resolve_test_helper.py
│   │   ├── runtests.py
│   │   ├── simple_httpclient_test.py
│   │   ├── stack_context_test.py
│   │   ├── static
│   │   │   ├── dir
│   │   │   │   └── index.html
│   │   │   ├── robots.txt
│   │   │   ├── sample.xml
│   │   │   ├── sample.xml.bz2
│   │   │   └── sample.xml.gz
│   │   ├── static_foo.txt
│   │   ├── tcpclient_test.py
│   │   ├── tcpserver_test.py
│   │   ├── templates
│   │   │   └── utf8.html
│   │   ├── template_test.py
│   │   ├── test.crt
│   │   ├── testing_test.py
│   │   ├── test.key
│   │   ├── twisted_test.py
│   │   ├── util.py
│   │   ├── util_test.py
│   │   ├── websocket_test.py
│   │   ├── web_test.py
│   │   └── wsgi_test.py
│   ├── testing.py
│   ├── util.py
│   ├── web.py
│   ├── websocket.py
│   └── wsgi.py
└── tornado_version
```

# tornado源码剖析链接
**[tornado源码剖析链接](http://lingyue.github.io/2016/05/04/tornado%E6%BA%90%E7%A0%81%E5%89%96%E6%9E%90%E7%B3%BB%E5%88%97%E6%96%87%E7%AB%A0%E7%9B%AE%E5%BD%95/#more)**

# 链接推荐：
**[tornado翻译](http://demo.pythoner.com/itt2zh/index.html)**
**[tornado翻译](http://demo.pythoner.com/itt2zh/index.html)**
